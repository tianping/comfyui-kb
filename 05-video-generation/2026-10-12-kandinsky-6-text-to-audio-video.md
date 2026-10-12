# Kandinsky 6.0：一句话生成带声音的视频（ComfyUI 音画同步）

- **收藏日期**：2026-10-12
- **来源**：微信公众号「龙行AI实验室」，《ComfyUI 从安装入门到精通》第 25 篇
- **分类**：05-video-generation / 文生音视频（T2AV）
- **URL**：https://mp.weixin.qq.com/s/5xHrPZlJkdrIszcnKe-loA
- **技术报告**：arXiv:2610.05608（2026-10-04）
- **作者声明**：未亲自实跑（作者用的是 8G 显卡），速度和显存数据都是官方自测口径

## 概述

Kandinsky 6.0 是 Kandinsky Lab 出品的开源**文生音视频**模型：画面和声音一起生成，自带唇形同步，不再需要"生视频 → 配音 → 对口型"几步分开做。2026-10-06 开源权重，同时作为 ComfyUI Registry 官方扩展发布，Diffusers、vLLM-Omni、SGLang、FastVideo 同步支持。**MIT 许可，可商用。**

## 规格与能力

- **Pro 29B**：19B 视频 + 5B 音频 + 5B 交叉注意力
- **Lite 3B**：轻量版（但"轻量"是相对的，官方最低门槛 12 GB，需开 offload）
- **T2AV**：提示词 → 5 秒、24 帧视频，带 44 kHz 同步音频（含唇形对齐）
- **TI2AV**：提示词 + 图片 → 基于这张图生成音视频（适合角色口播）
- 另有独立超分模型（kandinsky6-sr），可上采样到 Full HD

## ComfyUI 安装步骤

前提：ComfyUI ≥ 0.38.0，NVIDIA 显卡。

1. Manager → Install Custom Nodes → 装 `kandinsky6`
2. 再装 `kandinsky6-sr`（超分，**必装**，不装会报红框错误）
3. **重启 ComfyUI 后端进程**（不是刷新网页）
4. Workflow → Browse Templates → Load Kandinsky 6（或用扩展自带的示例工作流）
5. 写提示词，同时描述画面和声音，如"卡通小猫在桌前开心介绍 AI 工具"
6. Queue Prompt → 输出的 mp4 自带音轨，嘴型已对齐

**常见报错**：只装了 kandinsky6 → 超分节点报错；显存不够 → CUDA out of memory，后端崩溃；ComfyUI 版本低于 0.38.0 → 模板加载失败。

## 参数速查表（官方自测口径）

| 场景 | 模型 | 模式 | 显存建议 | 备注 |
|---|---|---|---|---|
| 本地尝鲜 | Lite 3B | T2AV | ≥16 GB 或上云 | 非蒸馏版 4090 约 437 秒出一段 5 秒片 |
| 高质量成片 | Pro 29B | T2AV / TI2AV | 16 GB 可跑标清档；Full HD 建议 24 GB | 开 block offload 后标清峰值约 21.7 GiB；Full HD 4090 约 1247 秒，5090 约 854 秒 |
| 快速出片 | 任一（蒸馏版） | T2AV | 同非蒸馏版 | 10 步推理，速度大幅提升 |
| 提高分辨率 | kandinsky6-sr | 后处理 | 单独占显存 | 必须和主模型一起装 |

## 显存真相

- Pro 29B 完整模型不开优化时，峰值显存高达 **72.8 GiB**
- 8 GB 显卡（4060 之类）本地基本跑不动，Lite 也吃力
- 作者给的出路：上云按秒计费用蒸馏版试水，或者等社区出 GGUF 之类的激进量化

## 备注

- **对漫剧流程的意义**：它能把"镜头视频 + 配音 + 口型"合成一步，正好解决那个付费漫剧工作台主打的"视频乱说话"（口型对不上）问题。限制是每段只有 5 秒，生成也慢。
- **HPC 可行性**：McCleary 的 A100（40/80 GB）能跑 Pro 29B，80 GB 卡甚至不开 offload 也行；A5000（24 GB）能跑 Pro 标清档或 Lite。
- 同系列笔记：`04-advanced-image/2026-09-14-comfyui-v0.35-video-trim-h3-max-3d.md`（第 21 篇）
- 作者预告下一篇讲"把 Kandinsky 接进 ComfyUI 视频工作流，串成稳定出片流水线"
