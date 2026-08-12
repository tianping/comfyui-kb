# Z-Image Turbo 在 ComfyUI 中的使用教程

> 来源：[Z-Image Turbo in ComfyUI (2026)](https://localaimaster.com/blog/z-image-turbo-comfyui) · 2026-08-12

## 模型概述

Z-Image Turbo 是阿里通义实验室（Tongyi-MAI）于 2025年11月27日发布的开源文生图模型：

- **参数量**：6B（蒸馏版）
- **许可**：Apache 2.0（可商用）
- **架构**：S3-DiT（可扩展单流 DiT），将文本 token、视觉语义 token、图像 VAE token 拼接为统一序列
- **速度**：1024×1024 图像仅需 ~8步（8 NFEs），RTX 4090 约2-3秒
- **HuggingFace**：[Tongyi-MAI/Z-Image-Turbo](https://huggingface.co/Tongyi-MAI/Z-Image-Turbo)

同系列还有 Z-Image-Base（非蒸馏，用于微调）和 Z-Image-Edit（编辑版）。

## 为什么快

三重加速叠加：

1. **少步**：8 NFEs vs SDXL 的 20-30 / FLUX dev 的 20-30 → ~3× 减步
2. **小模型**：6B vs FLUX 12B → 每步更便宜
3. **引导关闭**：CFG 1.0（guidance scale 0.0），每步单次前向而非双次

## 显存需求

| 精度 | 约需 VRAM | 典型显卡 | 备注 |
|------|----------|---------|------|
| BF16（全精度） | ~14-16GB | RTX 4080/4090/3090 | 官方 ComfyUI 构建，质量最佳 |
| FP8 (e4m3fn) | ~8GB | RTX 4060 Ti 16GB/3060 12GB | 近 BF16 质量，大幅省显存 |
| GGUF (Q4-Q5) | ~5-6GB | RTX 3050/笔记本 GPU | 社区量化，最小 footprint |

对比：FLUX.1 dev 全精度需 ~24GB，Z-Image Turbo BF16 仅需 16GB，FP8/GGUF 更低。

## ComfyUI 安装步骤

### 1. 下载三个模型文件

| 文件 | 放置目录 | 作用 |
|------|---------|------|
| z_image_turbo_bf16.safetensors | ComfyUI/models/diffusion_models/ | 6B DiT 主体 |
| qwen_3_4b.safetensors | ComfyUI/models/text_encoders/ | 文本编码器（Qwen 3 4B） |
| ae.safetensors | ComfyUI/models/vae/ | VAE 自编码器 |

```
ComfyUI/models/
├── diffusion_models/
│   └── z_image_turbo_bf16.safetensors
├── text_encoders/
│   └── qwen_3_4b.safetensors
└── vae/
    └── ae.safetensors
```

### 2. 加载模板

ComfyUI → Workflow → Browse Templates → Image → 选 Z-Image Turbo 示例。也可拖入官方工作流 JSON。

### 3. 指定加载器节点

模板使用三个加载器：diffusion-model loader / text-encoder loader / VAE loader，分别在各自下拉菜单选对应文件。

### 4. 输入提示词，Queue 出图

低显存方案：BF16 换 FP8（~8GB）或 GGUF（~6GB，需装 ComfyUI-GGUF 自定义节点）。

## 推荐参数

| 参数 | 推荐值 | 原因 |
|------|--------|------|
| Steps | 8-9 | 蒸馏为 ~8 NFEs 优化，多步无益 |
| CFG | 1.0 | 引导已关闭，调高会过曝/烧图 |
| Sampler | res_multistep | 官方工作流默认 |
| Scheduler | simple | 配合 Turbo 步调度 |
| 分辨率 | 1024×1024 | 原生训练分辨率 |

**最大误区**：调高 CFG 来"改善提示词遵循"——在 Turbo 蒸馏模型上通常适得其反。提示词不满意时改措辞或换种子，别动 CFG。

## Z-Image Turbo vs FLUX vs SDXL

| 模型 | 参数 | 步数 | 1024px 时间(4090) | 最低实用 VRAM | 许可证 |
|------|------|------|-------------------|-------------|--------|
| Z-Image Turbo | 6B | 8 | ~2-3s | ~8GB(FP8)/6GB(GGUF) | Apache 2.0 |
| FLUX.1 dev | 12B | 20-30 | ~15-30s(全精度) | ~24GB全/6-8GB GGUF | 非商用 |
| SDXL | 3.5B(UNet) | 20-30 | ~3-8s | ~8GB | OpenRAIL/宽松 |

- **vs FLUX**：Z-Image Turbo 速度数倍快，显存需求大幅降低，商用许可更友好。FLUX dev 在复杂精细场景仍可能胜出（蒸馏总有质量代价）。
- **vs SDXL**：步数减半以上，文字渲染和解剖结构更干净，开箱即用质量更像 FLUX 级。

## 工作流节点链

```
Load Diffusion Model → z_image_turbo_bf16.safetensors
Load CLIP/Text Encoder → qwen_3_4b.safetensors
CLIP Text Encode (Positive) → 你的提示词
  （负向提示词效果很小，留空即可）
Empty Latent Image → 1024×1024
KSampler → steps 9, CFG 1.0, res_multistep, simple
Load VAE → ae.safetensors → VAE Decode → Save Image
```

## RTX 3090 实测参考

- BF16 暖机后 ~3-4秒/张（1024×1024）
- BF16 占 ~16-17GB VRAM（24GB 卡余量充足）
- FP8 降到 ~9-10GB，肉眼无质量差
- 步数加到20无可见改善，只变慢——确认 8步是最优工作点

## 注意事项

1. **蒸馏模型**：最高单图保真度 FLUX dev 或 Z-Image-Base 可能仍胜出
2. **别对抗配方**：高 CFG / 30+步 / 重负向提示词会帮倒忙
3. **文本编码器是 Qwen 3 4B**：额外的磁盘和 VRAM 开销
4. **先更新 ComfyUI**：Z-Image 支持是近期加入的，旧版缺模板/加载器/sampler
5. **GGUF 需装 ComfyUI-GGUF 节点**：最低 VRAM 路径非开箱即用

## 示例提示词

```
Positive: candid editorial portrait of a woman in a rain-soaked
Tokyo alley at night, neon reflections on wet asphalt, 85mm lens,
shallow depth of field, natural skin texture, cinematic color grade

Negative: (留空)
```

因为只有 8 次前向，可以批量生成多个种子挑最好的——这正是 Turbo 的工作方式：多生快选。
