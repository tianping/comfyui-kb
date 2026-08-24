# MiniMax H3 多参工作流（REF2VA）详解

> 来源：[【玩转ComfyUI】Minimax-H3多参工作流（AI漫剧制作首选）](https://mp.weixin.qq.com/s/CD7Nr7pnykptbBvvzft6ZQ) · 公众号「小马辨东西」 · 2026-08-24

## 模型介绍

魔搭社区可找到官方 MiniMax H3 模型库，原始模型在 HuggingFace。全系列约 500G，主要两款：

- **FL2VA**：文生视频、图生视频
- **REF2VA**：多参考图生视频——制作复杂视频的主力工作流

## REF2VA 模型下载

需下载的目录：

- `diffusion_models`：主模型
- `Loras`：加速 LoRA
- `text_encoders`：CLIP 编码
- `Vae`：音频与视频 VAE

## 官方多参工作流

官方模板：[video_minimax_h3_r2v.json](https://github.com/Comfy-Org/workflow_templates/blob/main/templates/video_minimax_h3_r2v.json)

### 主模型加载区

加载主模型 + 加速 LoRA + CLIP 编码 + VAE（音频与视频分开解码）。

### if/else 开关

官方工作流内置条件判断：
- 加速 LoRA 开关：false 不启用 / true 启用
- 步数联动：不启用加速 = 20 步；启用后 = 4 步

### 核心节点：Reference to Video

输入参考图片、参考音频、参考视频。上限：**9 张参考图片、3 个参考音频、3 个参考视频**。

比例参数：
- 宽高比 16:9（可改）
- 0.4 = 400 万像素
- 32 = 最终生成宽高均为 32 的倍数
- 时长直接填秒数，公式自动换算总帧数

帧数公式示例（a=5 秒）：`round(5*24)=120` → `max(5,120)=120` → `120%17=1` → `5-1=4` → `4%17=4` → 总帧数 `120+4=124`。

### 采样与解码

自定义采样器，名称 **res_multistep**。解码时对视频和音频都解码，最后创建视频输出。

## 提示词规范（重要）

多参工作流的提示词有**规范模板**，靠官方 skill 生成。不用模板也能跑，但效果有差距。

用法：下载 skill 模板 → 上传参考图片/视频 → 让大模型生成规范提示词 → 复制进 prompt。

## 实测体验

- 本地笔记本跑一段 20 分钟以上，作者最终用 RunningHub 云端跑
- H3 比 LTX 2.3 省资源一些
- 开篇示例视频即多参工作流生成，剪映拼接

相关笔记：
- [MiniMax H3 突破15秒](minimax-h3-arbitrary-duration.md)
- [ComfyUI + MiniMax H3 图生视频纯小白教程](minimax-h3-beginner-tutorial.md)
- [短剧工作流模板库](../07-advanced-workflows/short-drama-workflow-templates.md)
