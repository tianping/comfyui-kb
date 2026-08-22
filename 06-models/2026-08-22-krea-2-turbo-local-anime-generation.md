# Krea 2 Turbo 文生动漫图片 — 本地无限免费生成

> 原文：https://mp.weixin.qq.com/s/j68MmA5XORxeh7QhECCRfw
> 来源：微信公众号文章

## 概述

Krea 2 turbo 模型适合本地生成动漫系列图片，基于 ComfyUI 部署。真人写实系列请使用 Z-Image-Turbo 模型。

- **环境要求**：显存 ≥ 8GB（GPU），需固态硬盘
- **模型来源**：魔搭社区 (ModelScope)

## 模型文件下载

| 文件类型 | 文件名 | ModelScope 目录 |
|----------|--------|----------------|
| Diffusion model | `krea2_turbo_fp8_scaled.safetensors` | `diffusion_models/` |
| Text encoder | `qwen3vl_4b_fp8_scaled.safetensors` | `text_encoders/` |
| VAE | `qwen_image_vae.safetensors` | `vae/` |
| LoRA | `krea2_darkbrush.safetensors` | `loras/` |

下载地址：https://www.modelscope.cn/models/Comfy-Org/Krea-2/tree/master/

## ComfyUI 目录结构

```
📂 ComfyUI/
├── 📂 models/
│   ├── 📂 diffusion_models/
│   │   └── krea2_turbo_fp8_scaled.safetensors
│   ├── 📂 text_encoders/
│   │   └── qwen3vl_4b_fp8_scaled.safetensors
│   ├── 📂 vae/
│   │   └── qwen_image_vae.safetensors
│   └── 📂 loras/
│       └── krea2_darkbrush.safetensors
```

放置完成后重启 ComfyUI，节点即可识别全部模型。

## 使用步骤

1. 打开 ComfyUI 模板库面板（左侧模板按钮）
2. 搜索 `Krea` 关键词
3. 选中 **Krea‑2 文生图模板** 加载
4. 修改提示词生成图片

## 实战示例

### 示例1 — 咖啡馆动漫女孩

Prompt: `masterpiece, best quality, ultra-detailed, 1girl, long flowing auburn reddish-brown hair, soft bangs, beautiful sharp amber brown eyes, gentle subtle smile, delicate drop earrings, thin silver pendant necklace, wearing loose white unbuttoned dress shirt, sleeves rolled up forearms, high-waisted black pencil skirt, black sheer tights, elegant mature young woman, medium full shot, sitting in vintage european cafe booth, leather red-brown sofa seat, round marble table with golden edge, hot steaming coffee cup on saucer on table, large floor-to-ceiling window, heavy rain falling outside window, rainy city street view, wet pavement, blurred street lamps, warm golden yellow ambient indoor lighting, soft window glow, depth of field, bokeh, cel-shading, modern anime illustration style, clean black outlines, cinematic color grading, cozy mood, atmospheric, 16:9 aspect ratio`

### 示例2 — 高达机甲风格

Prompt: `masterpiece, best quality, absurdres, ultra-detailed, intricate mechanical details, 1mecha, solo giant humanoid gundam-style mobile suit robot, full body shot, standing pose, white and deep blue armor paneling, sharp angular mecha design, layered segmented armor plates, visible mechanical joints, exposed hydraulic parts, thin glowing cyan-green energy cables running across torso and arms, bright glowing cyan-green circular chest reactor core, luminous glowing teal visor eyes on angular v-fin helmet, sharp white antenna horns on head, large angular blue-white wing-shaped back thruster units, subtle panel lines across every armor surface, tiny scratch wear marks on armor, small warning decals and unit marking stickers on shoulder plates, right mechanical hand grips large heavy futuristic assault rifle gun, long barrel, detailed magazine and barrel attachments, energy cable connects rifle to mecha torso, left hand clenched into mechanical fist, heavy-duty segmented leg armor, thick mechanical knee joints, large reinforced mecha feet standing firmly on concrete rubble, ruined destroyed city battlefield ground, collapsed broken concrete buildings, twisted steel rebars, scattered concrete debris and broken masonry, thick dust and smoke rising from ground, flying shattered rock fragments and floating metal shrapnel suspended in air, tiny glowing embers and orange spark particles drifting all over scene, dramatic sunset dusk sky, half sky filled with blazing fiery orange-gold sunset clouds, other half covered with heavy dark stormy charcoal-grey cumulus clouds, strong golden backlight rim-lighting outlines the mecha armor, hard dramatic shadows cast by robot onto rubble ground, volumetric atmospheric haze, distant ruined high-rise city buildings background, anime cel-shading, gundam anime art style, clean sharp black outline art, rich saturated colors, cinematic composition, epic battle atmosphere, sharp focus on mecha, depth of field, subtle film grain, 16:9 aspect ratio`

### 示例3 — 热带海滩动漫女孩

Prompt: `masterpiece, best quality, absurdres, ultra-detailed, intricate details, 1girl, solo, young beautiful anime woman, medium-full shot, walking towards viewer along tropical beach, warm sun-kissed tanned smooth skin, subtle skin sheen from sea water, soft natural blush, gentle confident slight smile, glossy soft lips, gorgeous bright amber-gold eyes, highly detailed irises, bright catchlights inside eyes, long dark brown voluminous flowing hair, countless individual hair strands, wind blowing hair wildly in sea breeze, loose wispy flyaway strands framing face, large shiny gold hoop earrings, thin delicate ring on finger, wearing tropical print halter bikini, white bikini fabric printed with green palm trees and pineapple patterns, thin turquoise trim on bikini straps, matching printed wrap sarong tied around hips, fabric billowing and fluttering in ocean wind, soft fabric folds and creases on sarong, toned slim abdomen, subtle body contours, bright sunny tropical beach scenery, soft pale creamy-white sandy shore, scattered footprints imprinted on sand, gentle foamy turquoise ocean waves rolling onto coastline, shimmering sparkling sunlight reflections glinting across sea surface, bright clear vivid blue sky, fluffy cumulus white clouds, tall lush green coconut palm trees line distant coastline, tropical green bushes and foliage far background, distant beach huts, brilliant harsh bright summer sunlight, golden sun rays flare, soft circular lens flare bokeh highlights, strong warm directional sunlight, bright rim-light outlining hair and shoulders, soft natural shadows cast on sand ground, vibrant saturated tropical color palette, modern anime illustration style, clean cel-shading outlines, smooth skin rendering, painterly ocean and sky textures, depth of field, sharp focus on the girl, beautiful seaside atmosphere, summery joyful vibe, 16:9 aspect ratio`

## 相关链接

- 魔搭社区 Krea-2 仓库：https://www.modelscope.cn/models/Comfy-Org/Krea-2/tree/master/
