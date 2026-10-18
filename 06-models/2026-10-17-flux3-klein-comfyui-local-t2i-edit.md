# ComfyUI 本地跑 FLUX.3 Klein：文生图 + 指令改图（官方四套模板）

> 来源：[ComfyUI装上FLUX.3 Klein：本地文生图和指令改图](https://mp.weixin.qq.com/s/PnEu0bPCbT1JO03I22vDMA)（公众号 Comfyui）· 收藏 2026-10-17
> 依据：docs.comfy.org FLUX 3 Image Klein 教程、Changelog v0.41.0、Comfy-Org/workflow_templates
> 模型发布背景见 ai-news-kb：`01-models/2026-10-17-flux-3-image-klein-open-weights.md`

## 概述

Black Forest Labs 的 FLUX.3 [klein] 开源权重已原生进 ComfyUI：同一家族既能文生图，也能按自然语言指令改图。本地跑只需下载一次权重，**无 API key、不按张扣 credits**。许可为 FLUX Kommunity License，权重仓在 Hugging Face 上门控，同意许可后才能下载。

## 本地 Klein vs Partner 托管 FLUX 3 Image

| | 本地 klein | Partner FLUX 3 Image |
|---|---|---|
| 运行方式 | 开源权重，本机 GPU 推理 | 画布上的托管节点，登录 Comfy 账号 |
| 计费 | 电费和硬盘，无按张积分 | 按生成图扣 Comfy credits |
| 分辨率 | 模板 / Empty Flux 2 Latent 自控 | 约 0.75K–4K 档位 |
| 参考图点名 | `<ref_image_0>`、`<ref_image_1>`… | `image 1`…`image 10`（最多 10 张） |
| 额外能力 | — | 多参考拼合、bounding box 布局、默认开 web/image search grounding（2026-07-23 Early Access） |
| 适合 | 离线交付、像素级可复现 | 不想下大包、快速验证观感 |

两者可串用：托管节点先定构图/材质，定稿后回本地 klein 做可复现精修。

## 能力与组件

- 同一家族权重覆盖**文生图**与**指令驱动的图像编辑**（指令提示 + 参考图）。
- 两档检查点：**full（base）** 和 **step-distilled**（4–16 步、guidance-free）。
- 可选 **Qwen3.5 9B prompt upsampler**：把短请求扩写成完整 caption 再采样。
- 文本编码器 Qwen3-VL-4B；VAE 用 FLUX.2 的 flux2-vae。
- 单文件检查点提供 bf16 / int8(convrot) / nvfp4，模板默认 int8 convrot。
- **版本门槛**：ComfyUI ≥ 0.41.0（0.41.1 backport，ComfyUI#17070）；workflow_templates v0.11.84 起含相关模板。

## base 与 Distilled 参数（模板默认）

| 模板 | steps | cfg | 备注 |
|---|---|---|---|
| base · 文生图 | 50 | 3.5 | upsampler 默认开 |
| base · 编辑 | — | guidance 1 | 画布用 Empty Flux 2 Latent（例 1024×1024） |
| Distilled · 文生图 | 4–16（默认 16） | 固定 1 | 抬 cfg 没用 |
| Distilled · 编辑 | 4–16 | 1 | 一张参考 + 一条指令即可试跑 |

用法：**先 Distilled 跑通构图和主体，再换 base 拉到 50 步精修**。Distilled 不是"更差的 base"，而是专为短步、guidance-free 采样的档。

## 多参考写法

- 参考图接到 **Set Reference Multi Latent** 节点：第一张对应 `<ref_image_0>`，下一张 `<ref_image_1>`，依次类推。
- 额外参考要按顺序接入，并像参考管线那样，把每张同时加进 positive 与 negative conditioning。
- **写结果全貌，不写"改一下"**：把改动嵌进完整描述。官方例：`In <ref_image_0>, change the dress color <obj_1> to red`。
  ```
  在 <ref_image_0> 中，将连衣裙颜色改为深红色丝绒质感；
  保持人物姿态、发型、背景墙面与原有光位不变。
  ```
- 配饰替换（如换耳环）：描述新配饰的形状、材质、反光，并写明"不要出现旧款特征"。
- 参考分辨率是成本旋钮：模板把参考缩到约 1MP；BFL 自家管线可保留到约 16MP 换取更多细节，代价更高。
- 模板带 **Create Bounding Boxes**：在源图上画框做区域规划，框列表在 Preview Any 里查看后写进提示。
- 先对好接线顺序再写标签：标签指错对象，堆再多形容词也救不回来。

## 权重下载与目录

门控仓：`black-forest-labs/flux-3-image-klein-comfy`（打开页面 → 同意许可 → 下载）。

```
ComfyUI/models/
├── diffusion_models/
│   ├── flux3_image_klein_base_int8_convrot.safetensors   # full
│   └── flux3_image_klein_sd_int8_convrot.safetensors     # distilled
├── text_encoders/
│   ├── qwen3vl_4b_int8_convrot.safetensors               # CLIPLoader 类型 flux3
│   └── qwen3.5_9b_flux3_prompt_upsampler_w4a8.safetensors # 可选，文生图模板用
└── vae/
    └── flux2-vae.safetensors
```

文件名与模板默认一致，就不用改加载节点。

## 四套官方模板

Templates 搜索 **"FLUX 3 Image Klein"**（或菜单 Workflow → Browse Workflow Templates；也可把 JSON 拖进画布）：

1. **FLUX 3 Image Klein: Text to Image**：改 positive prompt、宽高或 ResolutionSelector（默认 1:1）；upsampler 默认开，已写好细描述时关掉 `enable_prompt_upsampler`。
2. **FLUX 3 Image Klein Distilled: Text to Image**：短提示即可，steps 4–16，cfg 保持 1。
3. **FLUX 3 Image Klein: Image Edit**：LoadImage → Set Reference Multi Latent；ImageCompare 对照输入输出。
4. **FLUX 3 Image Klein Distilled: Image Edit**：一参考 + 一指令，4–16 步，cfg 1。

模板 JSON 文件名：`image_flux3_klein_t2i.json`、`image_flux3_klein_image_edit.json`、`image_flux3_klein_distilled_t2i.json`、`image_flux3_klein_distilled_image_edit.json`（来源 Comfy-Org/workflow_templates）。文生图结果由 SaveImage 存到 `ComfyUI/output/`。

**试跑顺序**：Distilled 文生图确认链路通 → Distilled 编辑换一张清晰人像/产品图 → 满意后切 base 精修。每次只改一处（提示、参考或步数）。

## 踩坑清单

- 模板搜不到：先确认 ComfyUI ≥ 0.41.0 和模板包版本，别装来路不明的 fork。
- 没在 HF 同意许可：权重下不了，加载报缺文件。
- 文件名/目录放错：模板默认找 int8 convrot 文件名，改名要同步改加载节点。
- Distilled 上抬 cfg：无效，保持 1。
- 编辑提示只写"改一下"：要写结果全貌；标签指错参考会改错区域。
- 别把 Partner 的 credits 单价套进本地报价。
- Cloud 用户暂时看不到新核心节点时，等稳定版同步。

## 五步清单

1. 升级 ComfyUI 到 0.41.0+，模板包跟上；
2. HF 同意 FLUX Kommunity License，下载 base/sd、Qwen3-VL-4B、VAE（及可选 upsampler）；
3. 加载 "FLUX 3 Image Klein Distilled: Text to Image" 模板；
4. 短提示 + Distilled 4–16 步试跑，通了再试编辑模板；
5. 要离线交付留在 klein，只想免装快验再开 Partner FLUX 3 Image。

## 备注

- 原文刻意不写显存占用、出图耗时和画质排名（官方未公开）；ai-news-kb 发布笔记中"8GB 显存可跑"来自 BFL 宣传。
