# AnimateDiff 视频生成入门

> 来源：[AIToolGo - 掌握 ComfyUI 中的 AnimateDiff](https://www.aitoolgo.com/zh/learning/detail/mastering-animatediff-in-comfyui-a-comprehensive-guide-to-ai-video-creation) · 2026-08-08

## 什么是 AnimateDiff

AnimateDiff 是 ComfyUI 中生成 AI 视频的强大工具。它在 Stable Diffusion 基础上添加运动模块，让静态图像生成扩展为视频生成。

## 两种主要工作流

### Text2Vid（文生视频）
从文本提示词直接生成视频：
```
Checkpoint加载器 → AnimateDiff加载器 → CLIP文本编码 → K采样器 ← 运动模块
                                                                    ↓
                                                           VAE解码 → 视频合成
```

### Vid2Vid（视频生视频）
使用 ControlNet 从现有视频中提取运动并指导转换：
```
视频输入 → 帧拆分 → ControlNet预处理 → ControlNet应用
                                        ↓
                AnimateDiff加载器 → K采样器 → VAE解码 → 视频合成
```

## 系统要求

- **最低显存**：8GB（适合较小分辨率或 Txt2Vid）
- **推荐显存**：10GB+
- **依赖工具**：Git（下载扩展）、FFmpeg（合成视频，可选但推荐）、7zip

## 安装步骤

### 需要安装的自定义节点
1. **ComfyUI-AnimateDiff-Evolved**：AnimateDiff 核心插件
   - `git clone https://github.com/Kosinkadink/ComfyUI-AnimateDiff-Evolved.git`
   - 放到 `ComfyUI/custom_nodes/` 目录
2. **ComfyUI-Manager**：节点管理器
3. **ComfyUI-Advanced-ControlNet**：Vid2Vid 需要
4. **ComfyUI-VideoHelperSuite**：视频导入导出
5. **FizzNodes**：提示词调度（可选，做动态提示用）
6. **ControlNet 预处理器**：通过 ComfyUI Manager 安装

### 模型下载
| 模型类型 | 说明 | 存放位置 |
|---------|------|---------|
| Checkpoint | 基于 SD1.5 的检查点 | `models/checkpoints/` |
| VAE | 变分自编码器 | `models/vae/` |
| 运动模块(Motion Module) | AnimateDiff 核心，如 mm_sd_v15_v2.ckpt | `custom_nodes/ComfyUI-AnimateDiff-Evolved/models/` |
| ControlNet | 用于 Vid2Vid | `models/controlnet/` |

## 核心节点说明

| 节点 | 作用 |
|------|------|
| ADE_AnimateDiffLoaderGen1 | 加载运动模块 |
| ADE_UseEvolvedSampling | 应用 AnimateDiff 采样设置 |
| KSampler | 标准采样器（与图像生成一致） |
| VAE Decode | 解码 Latent 为帧图像 |
| VHS_VideoCombine | 将帧合成为视频 |
| Load Image | Vid2Vid 时导入帧 |
| Uniform Context Options | 管理动画长度和一致性 |
| Batch Prompt Schedule | 动态提示词调度 |

## 五种工作流模式

1. **基本 Vid2Vid（1个ControlNet）**：最简单的视频转绘
2. **多 ControlNet 的 Vid2Vid**：多种控制叠加
3. **基本 Txt2Vid**：纯文本生成视频
4. **带提示调度的 Vid2Vid**：视频中场景随时间变化
5. **带提示调度的 Txt2Vid**：文本生成 + 场景变化

## 进阶技巧

- **更改视频输入**：尝试不同素材，效果差异大
- **调整参数**：帧数、FPS、CFG、步数都会影响结果
- **添加/删除 ControlNet**：灵活组合控制方式
- **Lora + Motion Lora**：风格 + 运动风格叠加
- **高分辨率修复**：先生成再放大
- **遮罩/区域提示**：控制视频中不同区域

## 常见问题

- **空类型错误**：通常是节点版本冲突
- **与其他库冲突**：注意自定义节点之间的兼容性
- **模型不匹配**：运动模块要与 Checkpoint 版本对应（SD1.5 ↔ SD1.5）
- **视频闪烁/抖动**：帧数太少或步数太低，尝试增加帧数、调整 CFG

## 相关注意事项

- AnimateDiff 技术更新快，某些教程可能过时
- 安装路径要准确，否则节点无法识别模型
- 生成视频比图像更耗时，耐心等待
- 8fps 是常用的起始帧率设置

## 相关笔记
> 相关笔记：[工作流基本原理](../01-basics/workflow-fundamentals.md) · [KSampler 详解](../02-core-nodes/ksampler-details.md) · [ControlNet 基础](../04-advanced-image/controlnet-basics.md)
