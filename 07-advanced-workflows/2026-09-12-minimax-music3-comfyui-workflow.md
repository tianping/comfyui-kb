# 从零搭建 MiniMax Music 3 音乐生成工作流：从提示词到完整歌曲

- **日期**：2026-09-12
- **来源**：老王自学ComfyUI（资源包 088）
- **分类**：ComfyUI / 音频生成
- **URL**：https://mp.weixin.qq.com/s/9F-fSgbtzNReoalTy-iggA

## 学习目标

在 ComfyUI 中搭建 MiniMax Music 3 音乐生成工作流，理解 Caption（音乐描述）与 Lyrics（歌词）编写规范，使用 MiniMax Music3 Text Encode 节点及音频 VAE 解码流程，实现从文本提示词到完整歌曲的生成。

## 认识模型

MiniMax Music 3 可根据结构化的音乐描述和歌词生成最长 **5 分钟（300s）** 的完整歌曲，生成富有表现力的人声、不断演变的编曲和稳定的长音频质量。

架构（分层自回归）：
- 8B 全局 LLM：负责长程音乐结构
- 0.6B 局部 LLM：负责帧级声学细节
- 基于 Flow Matching + Flow-VAE 的连续隐藏状态合成系统
- 输出 32 kHz、16 位立体声音频

官方发布页：huggingface.co/MiniMaxAI/MiniMax-Music3；ComfyUI 配套模型：huggingface.co/Comfy-Org/MiniMax-Music-3（含底模、CLIP、VAE，可按显存选量化版本）。

## 工作流搭建（7 步）

1. **加载模型**：UNet 加载器 + 加载 CLIP + 加载 VAE，分别加载三个模型
2. **MiniMax Music3 Text Encode 节点**：把音乐描述和歌词结构转成声学条件信号，并输出音频最终时长
   - `caption`：音乐描述（曲风/情绪/乐器/人声质感），**必须英文**
   - `lyrics`：歌词，控制演唱内容和段落结构
   - `seed`：随机种子（可复现）
   - `max_duration`：生成时长上限（≤300s）
   - `cfg_scale`：引导强度，官方推荐 **1.7**（平衡创造力与提示词遵从）
   - `top_k`：采样阈值，限制候选词汇保证连贯性
3. **Empty MiniMax Music3 Latent Audio**：创建空的（零填充）潜在音频张量；音频需指定时间长度，且要和 Text Encode 的 `max_duration` 一致——把 Text Encode 的 `seconds` 输出连到本节点 `seconds` 参数
4. **负向条件**：H3 系列训练用了指导蒸馏，不需要负向条件；用「条件零化」节点生成
5. **K 采样器**：种子和 Text Encode 保持一致（拖一个种子节点同时连到两处）；再用「浮点数」节点把 CFG 值同时输出到 Text Encode 和 K 采样器
6. **切换节点**（控制是否用分块解码）：
   - `VAE 解码（音频）`：一次性整体解码，显存占用高（时长越长越高），但解码速度快、质量最好（无拼接痕迹）
   - `VAE 解码音频（分块）`：长音频切多段逐块解码再无缝拼接，显存占用固定（分块大小决定），总耗时更长
   - 显存不够或音频较长 → 打开切换用分块
7. **保存音频（高级）** 节点输出

完整工作流：资源包 088_1.json

## Caption 与 Lyrics 编写规范

**Caption（音乐描述）**——定义风格/情绪/人声/编曲，**强制英文**，官方推荐三段式：
1. **Global Metadata（全局元数据）**：流派、BPM、调性、音阶、情绪推进、聆听场景、制作风格
2. **Vocal Details（人声细节）**：人声性别、音色、演唱风格、和声、人声效果
3. **Arrangement（编曲）**：主/次乐器、段落级乐器演变、律动、低音、打击乐、空间效果
描述越具体，结果越接近预期

**Lyrics（歌词）**——定义演唱文字，必须用方括号段落标签标注结构，支持标签：
`[Intro] [Verse] [Pre-Chorus] [Chorus] [Post-Chorus] [Bridge] [Instrumental] [Solo] [Outro]`

- `[Intro]` 前奏引子，可留空或写氛围词，为整首歌定基调
- `[Verse]` 主歌，具体歌词，通常 2 段以上（旋律相同内容不同），为副歌铺垫
- `[Pre-Chorus]` 预副歌，连接主歌与副歌的过渡段，制造情绪张力
- `[Chorus]` 副歌，歌曲金句/情绪最高点，旋律最抓耳，通常重复多遍
- `[Post-Chorus]` 后副歌
- `[Bridge]` 桥段
- `[Instrumental]` 纯器乐、`[Solo]` 独奏、`[Outro]` 尾奏

## 实战提示

- 需根据歌词内容**预先判断歌曲时长**：太短→模型压缩前奏/间奏、段落转换仓促、歌词唱不完就断；太长→前奏/间奏/尾奏过长、整首歌空洞
- **Caption 必须英文**；**Lyrics 支持中文歌**（本文演示即为中文歌）
- 不要试图用其他方式控制歌曲结构，正确用法是只用标签
- 可把文章喂给 AI，让它按规范改写指定风格的歌曲提示词
