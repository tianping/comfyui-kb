# Wan2.1 文生视频工作流（text_to_video_wan.json，1.3B 怀旧乡村）

## 元数据
- 日期：2026-09-19
- 来源：GitHub 工作流（ssabc/workFlows）+ 同批微信收藏
- 分类：05-video-generation（文生视频）
- 工作流地址：https://github.com/ssabc/workFlows/blob/62e235ec1071498c51b78e75fa5d109907f5a86c/text_to_video_wan.json

## 概述
一个 Wan2.1-T2V-1.3B 的 ComfyUI 文生视频工作流（text_to_video_wan.json），配了"80/90后童年乡村夏日"怀旧风格的正面提示词，适合本地轻量文生视频测试。

## 工作流节点结构（解析自 JSON）

**Step 1 - 加载模型**
- UNet 加载器：`wan2.1_t2v_1.3B_fp16.safetensors`（Wan2.1-T2V-1.3B-FP16，diffusion_models 目录）
- CLIP 加载器：`umt5_xxl_fp8_e4m3fn_scaled.safetensors`（text_encoders，类型 wan）
- VAE 加载器：`wan_2.1_vae.safetensors`

**Step 2 - 视频尺寸**
- `EmptyHunyuanLatentVideo`：宽 544、高 960、长度 33、批量 1（竖屏短视频）

**Step 3 - Prompt**
- 正面：怀旧中国乡村夏日（详见下方）
- 负面：ugly, blurry, low resolution, jpeg artifacts, deformed hands...（标准反提示词）

**采样与输出**
- `ModelSamplingSD3` shift=8 → KSampler（cfg 5.2, 步数 30, sampler uni_pc, scheduler simple, seed 固定）
- VAEDecode → CreateVideo（16fps, 8bit）→ SaveVideo

### 正面提示词（可复用为怀旧乡土短视频模板）
"90后童年记忆里的中国乡村夏天，2000年代初农村生活，怀旧胶片电影质感，暖黄柔和色调，轻微胶片颗粒，自然柔光，纪实生活化，低对比度，静谧治愈，朴素农家小院，老式平房，房屋朴素完好、带有生活痕迹，不过度破旧，80/90后童年回忆，无新潮现代物件，电影运镜，全景，农家老式平房，正午阳光，梧桐树影洒在泥土地，安静空旷院前，浓郁夏日氛围"

## 影响与备注
- Wan2.1-T2V-1.3B 是轻量级文生视频模型，1.3B 参数量适合本地低显存部署，搭配 fp8 文本编码器。
- 竖屏 544×960 33帧 ≈ 2 秒短视频（16fps），适合短视频创作起步。
- 与 ComfyUI 官方 repackaged 模型源对应（Comfy-Org/Wan_2.1_ComfyUI_repackaged）。

## 相关链接
- 工作流仓库：https://github.com/ssabc/workFlows
- 模型源：https://huggingface.co/Comfy-Org/Wan_2.1_ComfyUI_repackaged