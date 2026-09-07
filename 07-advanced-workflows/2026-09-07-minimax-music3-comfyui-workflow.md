# MiniMax Music 3：ComfyUI 音乐生成工作流全拆解

- **来源**: [微信公众号文章](https://mp.weixin.qq.com/s/ayn414c_FyCR9On9IFx9uA)
- **日期**: 2026-09-07
- **分类**: 高级工作流 / 音频生成

## 核心思路

ComfyUI 不只能画图，还能直接生成长达数分钟、结构完整的带人声歌曲。一个子图封装整个流水线，对外只暴露 8 个输入：caption、lyrics、max_duration、seed、unet_name、clip_name、vae_name、tiled_decode。

## 三个模型（扩散生成"三位一体"）

| 角色 | 文件 | 作用 |
|------|------|------|
| 扩散模型(DiT) | minimax_music3_dit_fp16.safetensors | 潜空间"降噪"主力 |
| 文本编码器 | minimax_music3_text_encoder_pruned_int8_convrot.safetensors | caption/lyrics→条件向量 |
| 音频 VAE | minimax_music3_dav.safetensors | 潜空间↔波形互转 |

低显存方案：用 int8 量化版 `minimax_music3_dit_int8_convrot.safetensors`。

模型放置路径：
```
ComfyUI/models/
├── diffusion_models/
│   ├── minimax_music3_dit_fp16.safetensors
│   └── minimax_music3_dit_int8_convrot.safetensors  # 低显存版
├── text_encoders/
│   └── minimax_music3_text_encoder_pruned_int8_convrot.safetensors
└── vae/
    └── minimax_music3_dav.safetensors
```

## 双文本输入设计

**Caption（三段式 Prompt）**：
1. Global Metadata：风格、BPM、调式、情绪、使用场景
2. Vocal Details：音色、唱法、混音位置、和声
3. Arrangement：配器、Intro/Verse/Bridge/Outro 各段内容

示例（Lo-fi hip-hop）：
> "78 BPM, D flat major, major scale with jazzy extensions. Laid-back and dreamy throughout… Bedroom production: muddy warm texture, heavy vinyl crackle, tape hiss and wow-flutter pitch wobble, low-passed dusty mix…"

**Lyrics（歌词+结构标签）**：
- 标签是唯一可执行指令：`[intro] [verse] [chorus] [bridge] [outro] [Instrumental]`
- 歌词文字只负责传递情绪
- 括号内容如 `(rain on the window)` 是拟声提示

## 流水线（子图内部）

1. **文本编码**：`MiniMaxMusic3TextEncode` — 接收 clip/caption/lyrics/seed/max_duration，产出正面条件 + 估算秒数；内部 cfg_scale=1.7, top_k=50
2. **造空白画布**：`EmptyMiniMaxMusic3LatentAudio` — 按秒数生成纯噪声音频潜空间
3. **扩散采样**：`KSampler` — steps=30, cfg=1.7, scheduler=simple, denoise=1.0；负面条件清零；seed 同时喂给文本编码器和采样器（固定 seed=复现，换 seed=新演绎）
4. **VAE 解码**：`VAEDecodeAudio`（整体）或 `VAEDecodeAudioTiled`（tile=1536, overlap=64，低显存）→ `ComfySwitchNode` 按 tiled_decode 开关选择
5. **保存**：MP3 V0 格式

## 关键参数

| 参数 | 默认 | 说明 |
|------|------|------|
| max_duration | 120s | 模型支持约 300s/5分钟；越长越耗显存 |
| seed | 固定 | 固定=复现，换=新演绎版 |
| cfg_scale | 1.7 | 一般不动 |
| tiled_decode | false | 低显存开 true |

## 实用提示

- 官方还提供 Music Caption Rewriter Skill，帮把模糊想法改写成模型最"听得懂"的三段式描述
- 遇到问题：更新 ComfyUI 到最新版；运行时错误报 `ComfyUI/issues`，前端报 `ComfyUI_frontend/issues`，工作流模板报 `workflow_templates/issues`
- 作者建议 max_duration 调小一点，别让显卡陪你熬到天亮
