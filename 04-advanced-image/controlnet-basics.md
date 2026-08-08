# ControlNet 基础使用教程

> 来源：[ComfyUI 官方文档 - ControlNet 使用示例](https://docs.comfy.org/zh/tutorials/controlnet/controlnet) · [博客园 - ComfyUI 完全入门：ControlNet 使用教程](https://www.cnblogs.com/Jcloud/p/18228969) · [优设 - Stable Diffusion ComfyUI 基础教程（八）](https://www.uisdc.com/stable-diffusion-comfyui-8) · [博客园 - ComfyUI 基础教程(三)](https://www.cnblogs.com/joy99/p/18402051) · 2026-08-08

## 什么是 ControlNet

ControlNet 是一种基于扩散模型的条件控制生成模型，由 Lvmin Zhang 等人于 2023 年提出。它通过引入多模态输入条件（边缘检测图、深度图、姿势关键点等），显著提升图像生成的可控性和细节还原能力。

**没有 ControlNet**：每次生成充满随机性，只能靠反复抽卡碰运气
**有了 ControlNet**：可以用涂鸦、姿势参考、深度图等精确控制画面

> 📷 [图：随机种子生成的图片 vs 用涂鸦 ControlNet 控制生成的图片对比]
> 来源：https://docs.comfy.org/zh/tutorials/controlnet/controlnet

> 📷 [图：ControlNet 各类预处理器效果一览，涵盖线条/姿态/深度/分割/颜色等类型]
> 来源：https://github.com/Fannovel16/comfyui_controlnet_aux/blob/main/examples/CNAuxBanner.jpg

## ControlNet 的三大组成

### 1. ControlNet 加载器
加载 ControlNet 模型文件（位于 `ComfyUI/models/controlnet`）。
- **ControlNet加载器**：加载标准格式
- **DiffControlNet加载器**：更通用，还支持 diffusers 格式
- ⚠️ 模型区分 SD1.5 和 SDXL，必须与基础大模型对应

### 2. 参考图片 + 预处理器
ControlNet 需要参考图来实施控制：
- **已有处理好的图**（如手绘线稿、深度图）→ 直接用
- **需要从原图提取**→ 使用预处理器
  - 线条类：Canny、HED、Scribble
  - 面部与姿态：OpenPose、DW姿态
  - 法向与深度：Depth、Normal
  - 语义分割：Segment Anything
  - 颜色、Tile平铺等

### 3. ControlNet 应用节点
封装 ControlNet，汇集模型、参考图和参数，计算新的条件：

> 📷 [图：Apply ControlNet 节点界面，展示 positive/negative/control_net/image/vae/strength/start_percent/end_percent 参数]
> 来源：https://docs.comfy.org/zh/tutorials/controlnet/controlnet

> 📷 [图：Load ControlNet 节点界面，从 models/controlnet 目录加载模型]
> 来源：https://docs.comfy.org/zh/tutorials/controlnet/controlnet

**关键参数**：
| 参数 | 作用 |
|------|------|
| strength | ControlNet 强度，越大参考特征越明显，太大可能过强 |
| start_percent | 开始应用的时机（0-1），0.2 表示生成过程 20% 时开始 |
| end_percent | 结束应用的时机（0-1），0.8 表示生成过程 80% 时停止 |

**ControlNet应用 vs ControlNet应用(高级)**：
- 普通版：只能控制 strength，开始/结束时间默认 0/1
- 高级版：可控制负面条件 + 开始/结束时间

## 安装插件

### 必装插件
1. **ComfyUI ControlNet aux**：预处理器合集
   - `https://github.com/Fannovel16/comfyui_controlnet_aux`
2. **ComfyUI-Advanced-ControlNet**：高级特性（调度、遮罩等）
   - `https://github.com/Kosinkadink/ComfyUI-Advanced-ControlNet`

通过 ComfyUI 管理器 → Git URL 安装 → 重启 ComfyUI

### 模型下载
- SD1.5 ControlNet：`https://huggingface.co/lllyasviel/ControlNet-v1-1/tree/main`
- fp16 精简版：`https://huggingface.co/comfyanonymous/ControlNet-v1-1_fp16_safetensors`
- SDXL 多合一模型：`https://huggingface.co/xinsir/controlnet-union-sdxl-1.0`
  - 集成 12 种类型，更节约内存
  - 使用时需配合"设置 UnionControlNet 类型"节点

> 🔧 工作流文件（可直接拖入 ComfyUI 加载）：
> https://raw.githubusercontent.com/Comfy-Org/example_workflows/main/controlnet/scribble_controlnet.png
> 该图片包含工作流 JSON 元数据，拖入 ComfyUI 会自动提示下载所需模型

> 📷 [图：ControlNet 涂鸦输入示例图，可作为工作流的输入图片]
> 来源：https://raw.githubusercontent.com/Comfy-Org/example_workflows/main/controlnet/scribble_input.png

## 标准工作流连接方式

> 📷 [图：ControlNet 标准工作流流程图，展示从加载图像到最终输出的完整节点连接]
> 来源：https://docs.comfy.org/zh/tutorials/controlnet/controlnet

```
加载图像 → 预处理器 → ControlNet应用 ← ControlNet加载器 ← 模型
                        ↑                  ↑
              CLIP文本编码(正向)     Checkpoint加载器
              CLIP文本编码(负向)
                        ↓
                    K采样器 → VAE解码 → 保存图像
```

**连线逻辑**：
1. CLIP文本编码器的正向/负向条件 → ControlNet应用的 positive/negative
2. ControlNet应用输出的正向/负向条件 → K采样器的 positive/negative
3. ControlNet加载器需要连接模型（来自Checkpoint加载器）

## 多个 ControlNet 叠加

> 📷 [图：多个 ControlNet 链式连接示意图，展示 Apply ControlNet 节点串联使用]
> 来源：https://docs.comfy.org/zh/tutorials/controlnet/controlnet

### 串联方式
在"ControlNet应用"后面再串联一个"ControlNet应用"即可。每个ControlNet各自连模型和预处理器。

### ControlNet 堆节点
更简洁的方式，最多支持 3 个 ControlNet 同时控制，整合了加载器和各参数。不够用可以继续串联堆节点。

## 实用技巧

### 完美像素节点
用于计算预处理器最优输出分辨率，匹配参考图和生成图的尺寸。

### 遮罩编辑器
在图像上右键 → "在遮罩编辑器中打开"，可手绘遮罩用于局部重绘。

### Inpaint 预处理的坑
Inpaint 预处理器输出的图像不能直接输入到 ControlNet 应用节点，需要先用"图像转RGB"节点转换（WAS 插件下的，不是其他插件的）。

> ⚠️ 踩坑提醒：必须是 WAS 插件下的"图像到RGB"节点，其他插件的 Convert Image To RGB 亲测不生效
> 来源：https://www.cnblogs.com/joy99/p/18402051

### VAE内补编码器做局部重绘
将原图 + 遮罩传入 VAE 内补编码器，输出 Latent 直连采样器。遮罩延展参数类似 PS 羽化，让重绘区域与原图融合更自然。

> 📷 [图：完整局部重绘工作流，展示原图→遮罩编辑器→VAE内补编码器→K采样器→VAE解码→输出的完整流程]
> 来源：https://www.cnblogs.com/joy99/p/18402051

## 相关系目
> 相关笔记：[工作流基本原理](../01-basics/workflow-fundamentals.md) · [KSampler 详解](../02-core-nodes/ksampler-details.md)
