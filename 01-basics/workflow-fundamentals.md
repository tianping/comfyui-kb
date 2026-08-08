# ComfyUI 工作流基本原理

> 来源：[知乎 - 保姆级教程！全面掌握ComfyUI工作流的组成元素](https://zhuanlan.zhihu.com/p/21439609297) · [博客园 - ComfyUI 基础教程(三)](https://www.cnblogs.com/joy99/p/18402051) · [我要自学网 - Comfyui基础节点教程详解](https://www.51zxw.net/TechArticleDetails.aspx?zid=129&id=2756) · 2026-08-08

## 什么是 ComfyUI

ComfyUI 是 Stable Diffusion 的节点式图形界面。与 A1111 WebUI 相比：

- **WebUI**：开箱即用，功能齐全，但可定制性差，作品不易复现
- **ComfyUI**：可定制性强，工作流可复现可分享，自动化程度高，但上手门槛较高

> 📷 [图：ComfyUI 默认界面，左侧节点库面板 + 中央画布 + 右侧生成历史]
> 来源：https://zhuanlan.zhihu.com/p/21591679893

## 工作流的核心组成

工作流由**节点(Node)**和**连线(Link)**组成。节点是矩形块，执行某种任务；连线连接节点的输入和输出，共同完成 AI 绘画任务。

### 节点三大要素
- **输入(Input)**：左侧接入点，接收上游数据
- **输出(Output)**：右侧接出点，向下游传递结果
- **参数(Parameter)**：节点上的可调设置项

> 📷 [图：节点结构示意，展示一个 Checkpoint 加载器节点的输入/输出/参数三部分]
> 来源：https://zhuanlan.zhihu.com/p/21439609297

## 五大基础节点类别

几乎每个工作流都会用到这五类：

### 1. 加载器类
工作流的起点，提供各种模型数据：
- **Checkpoint 加载器(简易)**：加载大模型，输出 MODEL、CLIP、VAE 三个数据流
  - MODEL → 传给采样器做去噪
  - CLIP → 传给文本编码器
  - VAE → 传给编解码器
- **VAE 加载器**：不想用大模型自带 VAE 时单独加载
- **LoRA 加载器**：串联在大模型后面，微调模型风格
  - 模型强度：LoRA 对模型的影响程度
  - CLIP 强度：对 CLIP 模型的影响程度
  - 多个 LoRA 可串联，也可用 LoRA 堆节点统一管理
- **ControlNet 加载器**：加载 ControlNet 模型
- **放大模型加载器**：加载超分辨率模型

### 2. 条件类
加在模型与采样器之间，充当"指挥官"：
- **CLIP 文本编码器**：将提示词编码为条件信息
  - 正向提示词 → 想要的内容
  - 负向提示词 → 不想要的内容

### 3. 潜空间类
- **空 Latent 图像**：文生图时用，设置生成图片的宽高
- **VAE 编码器**：图生图时用，将真实图片编码为 Latent
- **VAE 内补编码器**：局部重绘时用，需要传入图像+遮罩
  - 遮罩延展参数类似 PS 的羽化，让重绘区域与原图融合更自然

### 4. 采样器类
工作流的核心引擎，围绕它进行扩散去噪：
- **K采样器(KSampler)**：详见 [ksampler-详解](../02-core-nodes/ksampler-details.md)

### 5. 编解码类
- **VAE 解码器**：将 Latent 解码为可见图像
- **VAE 编码器**：将图像编码为 Latent

## 三种基本工作流模式

### 文生图(T2I)
```
Checkpoint加载器 → CLIP文本编码 → K采样器 ← 空Latent图像
                                     ↓
                              VAE解码 → 保存图像
```

> 🔧 工作流示例：ComfyUI 默认启动即加载此工作流，可直接在 ComfyUI 中体验

### 图生图(I2I)
与文生图基本一致，区别是 Latent 不为空，而是用 VAE 编码器将参考图编码后传入。denoise 参数此时相当于重绘幅度。

### 局部重绘
通过遮罩指定重绘区域：
- 系统自带方案：VAE 内补编码器 + Latent 噪波遮罩
  - 缺点：会对全图重绘，高分辨率时又慢又容易爆显存
- 进阶方案：Segment Anything + BrushNet（效果更好）
- 高效方案：Impact Pack（裁剪局部区域重绘，速度快融合好）

> 📷 [图：遮罩编辑器界面，左下角工具条含清除/画笔宽度/透明度/颜色，右下角保存按钮]
> 来源：https://www.cnblogs.com/joy99/p/18402051

## 三大空间概念

- **潜空间(Latent Space)**：图像以压缩形式存在的空间，K采样器在此工作
- **像素空间(Pixel Space)**：我们看到的实际图像
- **条件空间(Conditioning)**：CLIP 编码后的提示词条件
- Checkpoint 加载器是独立准备节点，为三大空间提供计算基础

> 📷 [图：WebUI 上的 ControlNet 面板示意，展示加载图像/选择预处理器/加载模型/控制权重等操作]
> 来源：https://www.uisdc.com/stable-diffusion-comfyui-8

## 相关节点
> 相关笔记：[KSampler 采样器详解](../02-core-nodes/ksampler-details.md) · [ControlNet 使用教程](../04-advanced-image/controlnet-basics.md)
