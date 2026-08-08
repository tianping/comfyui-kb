# KSampler 采样器详解

> 来源：[ComfyUI 官方文档 - KSampler](https://docs.comfy.org/built-in-nodes/sampling/ksampler) · [我要自学网 - Comfyui基础节点教程详解](https://www.51zxw.net/TechArticleDetails.aspx?zid=129&id=2756) · 2026-08-08

## KSampler 是什么

KSampler 是 ComfyUI 中最核心的节点——它是"引擎"，所有工作流都围绕它进行扩散去噪。它对潜空间图像执行多步去噪采样，结合正向和负向条件，使用指定的采样算法和调度器生成高质量 Latent 图像。

## 参数详解

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| model | MODEL | - | 用于去噪的模型（如 Stable Diffusion） |
| seed | INT | 0 | 随机种子，决定噪声生成模式，保证可复现 |
| steps | INT | 20 | 去噪步数，越多细节越精细但越慢 |
| cfg | FLOAT | 8.0 | CFG(Classifier-Free Guidance)引导系数，越高越贴合提示词但过高影响质量 |
| sampler_name | Enum | - | 采样算法名称 |
| scheduler | Enum | - | 调度器，控制噪声逐步移除的方式 |
| positive | CONDITIONING | - | 正向条件（想要的内容） |
| negative | CONDITIONING | - | 负向条件（排除的内容） |
| latent_image | LATENT | - | 要去噪的潜空间图像 |
| denoise | FLOAT | 1.0 | 去噪强度：1.0=完全去噪(文生图)，<1.0=部分保留原图(图生图) |

## 种子(seed)控制
- **fixed**：固定种子，结果可复现
- **increment**：每次+1
- **decrement**：每次-1
- **randomize**：随机选择

## 采样器选择指南

采样器分三大类：
1. **欧拉类**：euler、heun
2. **DPM类**：所有 dpm 开头的
3. **其他类**：ddim、uni_pc、lms 等

### 关键发现
- 名字以 `a` 或 `sde` 结尾的是不收敛的祖先采样器，每步结果都不同
- euler、heun、ddim 在第 9 步就出图，简单图又快又好
- dpmpp_2m 在第 18 步开始收敛，效果很好
- uni_pc 在第 14 步出图
- dpm_adaptive 虽然第 2 步就收敛但速度非常慢

### 选择建议
| 需求 | 推荐采样器 | 步数范围 |
|------|-----------|---------|
| 稳定可复现 | euler, dpmpp_2m | 20-30 |
| 简单图快速生成 | euler, heun | 10-20 |
| 质量与速度平衡 | dpmpp_2m, uni_pc | 15-25 |
| 每次生成不同 | 带a/sde的不收敛采样器 | 20-30 |

## 调度器选择

- **normal**：最常见，但不太适用于 FLUX 生态
- **karras**：以往最常用，新调度器出现后逐渐淡化
- **ddim_uniform**：随步数构图会变化，随机性强
- **exponential**：通常用于扩图放大场景

## 推荐的采样器+调度器组合

| 组合 | 特点 | 生成时间(参考) |
|------|------|------------|
| euler + normal | 快速生成，细节可能不够精细 | ~20s |
| heunpp2 + ddim_uniform | 细节丰富，速度较慢 | ~54s |
| uni_pc + beta | 速度与质量平衡，多数场景推荐 | ~20s |
| DPM2 + SIMPLE | 图片精细度高 | ~37s |
| DEIS + DDIM_UNIFORM | 光影明暗把握好 | ~20s |

## CFG 调参经验
- 默认 8.0 适用于多数 SD1.5 模型
- SDXL 模型建议 5-7
- Flux 模型可能用 CFG=0 + 独立的 Flux Guidance 节点
- 过高会导致画面"烧焦"、色彩过饱和
- 过低会导致不贴合提示词

## 相关笔记
> 相关笔记：[工作流基本原理](../01-basics/workflow-fundamentals.md)
