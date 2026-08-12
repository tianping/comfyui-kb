# LTX2.5 本地 ComfyUI 部署教程

> 来源：[LTX2.5 重磅登场！官方直接免费开源商用](https://mp.weixin.qq.com/s/DXwwVpnj29mbB6Lbx2ltZw) · 2026-08-12

## 概述

Lightricks 官方发布 LTX2.5 视频生成模型，免费开源可商用。本文记录本地 ComfyUI 搭建 LTX2.5 的完整流程，包括系统需求、模型下载、目录配置和工作流获取。

> ⚠️ LTX2.5 原生硬件门槛偏高（最低 32GB 显存），有条件可尝试。社区低显存方案持续关注中。

## 一、系统需求

来源：[官方 API 文档](https://docs.ltx.io/open-source-model/getting-started/system-requirements)

| 项目 | 最低要求 |
|------|----------|
| 显卡 | NVIDIA，最低 32GB+ 显存 |
| 内存 | 32GB 系统内存 |
| 存储 | 100GB 空闲空间 |
| CUDA | 12.7 及以上 |
| Python | 3.12 或更高 |

## 二、模型文件下载

共需下载 **4 组 5 个核心权重文件**，推荐使用 hf-mirror.com 镜像。

### 1. text_encoders（2个文件）

| 文件名 | 下载地址 |
|--------|----------|
| `gemma4_e2b_it_bf16.safetensors` | [hf-mirror.com/google/gemma-4-E2B-it](https://hf-mirror.com/google/gemma-4-E2B-it/tree/main) → 下载 `model.safetensors` 后重命名 |
| `gemma4-12b-with-proj-ltx-2.5-comfy-int8-convrot.safetensors` | [hf-mirror.com/Lightricks/LTX-2.5/text_encoders](https://hf-mirror.com/Lightricks/LTX-2.5/tree/main/text_encoders) |

### 2. diffusion_models（1个文件）

| 文件名 | 下载地址 |
|--------|----------|
| `ltx-2.5-22b-distilled-transformer-comfy-int8-convrot.safetensors` | [hf-mirror.com/Lightricks/LTX-2.5/diffusion_models](https://hf-mirror.com/Lightricks/LTX-2.5/tree/main/diffusion_models) |

### 3. vae（2个文件）

| 文件名 | 下载地址 |
|--------|----------|
| `ltx-2.5-video-vae-bf16.safetensors` | [hf-mirror.com/Lightricks/LTX-2.5/vae](https://hf-mirror.com/Lightricks/LTX-2.5/tree/main/vae) |
| `ltx-2.5-audio-vae-bf16.safetensors` | 同上 |

### 4. latent_upscale_models（1个文件）

| 文件名 | 下载地址 |
|--------|----------|
| `ltx-2.5-latent-spatial-upscaler-x2-bf16-1.0.safetensors` | [hf-mirror.com/Lightricks/LTX-2.5/latent_upscale_models](https://hf-mirror.com/Lightricks/LTX-2.5/tree/main/latent_upscale_models) |

## 三、目录配置

将下载的模型文件按以下结构放入 ComfyUI 目录：

```
ComfyUI/
├── models/
│   ├── text_encoders/
│   │   ├── gemma4_e2b_it_bf16.safetensors
│   │   └── gemma4-12b-with-proj-ltx-2.5-comfy-int8-convrot.safetensors
│   ├── diffusion_models/
│   │   └── ltx-2.5-22b-distilled-transformer-comfy-int8-convrot.safetensors
│   ├── vae/
│   │   ├── ltx-2.5-video-vae-bf16.safetensors
│   │   └── ltx-2.5-audio-vae-bf16.safetensors
│   └── latent_upscale_models/
│       └── ltx-2.5-latent-spatial-upscaler-x2-bf16-1.0.safetensors
```

## 四、工作流

作者从 GitHub 官网收集了 LTX2.5 相关工作流并保存至网盘：

- **百度网盘**：https://pan.baidu.com/s/1QFtYDZek9F27yTPD13qaSw?pwd=6t4t （提取码：6t4t）
- **夸克网盘**：https://pan.quark.cn/s/9fee753c07ec

## 备注

- LTX2.5 支持视频+音频生成（含独立 audio VAE）
- 使用 int8 量化版本（convrot）降低显存需求
- latent spatial upscaler 可用于 2 倍空间放大
- 文本编码器使用 Gemma 4 系列（E2B + 12B 双编码器）
