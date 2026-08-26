# ComfyUI 漫画分镜完整流程：从一句梗概到多格成稿

> 来源：微信公众号「漫剧AI实验笔记」实验笔记第8期，2026-08
> 原文：https://mp.weixin.qq.com/s/dTg-9LG1xaNQOsesL42yRg

## 核心思路

做 AI 漫画，最难的不是画一张好图，而是**跨格保持一致**：

- 🧑 **角色一致性**：同一人物在不同格里脸型、发色、服装要相同
- 🎨 **风格一致性**：线条粗细、上色风格要统一
- 🏙️ **场景一致性**：同一地点不同角度，透视和细节要对得上

解决方案：**参考图锁定 + 工作流批量生成**——先建「角色参考卡」，再用 IP-Adapter 锚定外貌。

**一句话流程**：角色参考卡 → IP-Adapter锁脸 → ControlNet控姿势 → 固定底模/LoRA保风格 → 批量生图筛选 → Clip Studio排版成稿。

## 🛠️ 必要节点清单

| 节点包 | 用途 | 安装方式 |
|--------|------|---------|
| IP-Adapter | 角色/风格参考图锁定 | ComfyUI Manager 搜索安装 |
| ControlNet | 姿势/构图控制 | — |
| WAS Node Suite | 批量生图、图片拼接 | ComfyUI Manager 搜索 |
| ComfyUI-Impact-Pack | 人脸修复（每格细化） | ComfyUI Manager 搜索 |
| ComfyUI-Frame-Interpolation | 可选：动态分镜 GIF | ComfyUI Manager 搜索 |

## 完整工作流 Step by Step

### Step 1 — 建立「角色参考卡」（地基）

1. 文生图生成一张满意的角色正面全身图（清晰、光线均匀）
2. 保存为 `character_ref.png`，放到 ComfyUI 的 `input` 文件夹
3. 记录这张图的完整正向 prompt（含外貌描述词），后续每格复用

💡 选没有复杂背景的参考图，IP-Adapter 识别纯角色效果更稳。

### Step 2 — IP-Adapter 角色锁定节点链

```
Load Image [character_ref.png]
↓
IP-Adapter Model Loader [ip-adapter-plus-face_sd15.bin]
↓
Apply IP-Adapter (weight: 0.6~0.8)
↓
KSampler → VAE Decode → 输出图像
```

- weight 0.6：相似度适中，保留创作空间
- weight 0.8+：高相似度，画面自由度降低
- 推荐 `ip-adapter-plus-face`，专为人脸一致性设计

### Step 3 — ControlNet 控制分镜构图

| 分镜类型 | ControlNet 方案 |
|---------|----------------|
| 近景对话格 | OpenPose 草图（两人面对面站立） |
| 动作格 | OpenPose 骨架（伸手/跳跃/出拳） |
| 场景环境格 | Scribble 线稿（建筑/街道轮廓） |
| 特写格 | Depth 图（突出主体虚化背景） |

💡 用 Blender / Posemy Art / MagicPoser 快速生成 OpenPose 参考图，不用手画。

### Step 4 — 风格统一：固定模型配方

| 组件 | 推荐选择 | 说明 |
|------|---------|------|
| 底模 | Anything V3 / camelliamix | 漫画线条干净 |
| 风格 LoRA | manga_style / line_art | 强化黑白线稿感 |
| VAE | vae-ft-mse-840000 | 色彩还原标准 |
| Sampler | DPM++ 2M Karras, 25步 | 速度/质量均衡 |

🔒 整本漫画锁定同一套参数不改。

### Step 5 — 批量生成与筛选

- WAS Node Suite Batch 功能，每格 `batch_size = 4` 出候选
- Seed 用随机（变化多一点），人工挑最接近分镜脚本的
- 不满意的细节用**局部重绘（Inpainting）**修正，不要整张重生——只重绘错误的手/脸/背景

### Step 6 — 拼版排格与对话框

- **Clip Studio Paint**（推荐）：专业漫画排版，自带对话框模板
- Canva：简单快速，适合社交媒体短漫
- Illustrator：精确控制，出版级
- 对话框字体：站酷快乐体 / 思源黑体（免费商用）

## 📝 分镜 Prompt 模板

**人物对话格**
```
masterpiece, manga style, two characters talking,
[角色A描述] and [角色B描述],
facing each other, speech bubble area on top,
indoor background, clean lines, black and white
```

**动作爆发格**
```
dynamic action scene, manga panel,
[角色描述] mid-attack, speed lines background,
motion blur, dramatic angle, low camera angle,
impact effect, black and white, high contrast
```

**场景环境格**
```
establishing shot, manga background art,
[场景描述], wide angle, detailed architecture,
no characters, atmospheric, crosshatching shadows,
black and white ink style
```

## 🚫 漫画专用负面词

```
photorealistic, 3D render, color, gradient,
watermark, text, logo, blurry, deformed,
extra limbs, bad anatomy, bad hands,
multiple panels in one image, frame border,
signature, username, artist name
```

⚠️ `multiple panels in one image` 很重要——AI 经常自作主张把多格塞进一张图，加这个词能有效阻止。

## 🏆 进阶技巧

- **Seed 固定保一致性**：满意的角色图记录 Seed；同场景不同格改姿势 Prompt 但保留 Seed，最大程度复用外貌特征
- **Img2Img 格间过渡**：上一格输出作为下一格输入，降噪强度 0.4~0.6，改 Prompt 描述动作/表情，画面连续性更自然
- **表情精确描述**：加 `crying, tears streaming, mouth wide open` 这类具体描述，比模糊的 "sad" 好用得多
- **分辨率**：每格 768×1024（竖）或 1024×768（横），不超过 1024，后期统一 ESRGAN 放大到出版尺寸

## 🎌 4格漫画分镜脚本示例：「少女在废墟城市中发现机器人」

| 格数 | 构图类型 | ControlNet | 关键 Prompt 词 |
|------|---------|-----------|---------------|
| 第1格 | 远景建立镜头 | Scribble 城市废墟 | ruined city, wide shot, no characters |
| 第2格 | 中景角色入场 | OpenPose 站立姿势 | girl walking, back view, ruins background |
| 第3格 | 近景发现时刻 | OpenPose 弯腰看 | girl surprised, eyes wide, looking down at robot |
| 第4格 | 特写表情格 | Depth 人脸特写 | close up face, tears, hopeful expression, glowing eyes |

## 🔧 配套工具推荐

- **Posemy Art**（免费）：在线 3D 人体姿势摆设，导出直接作 OpenPose 参考
- **DW-Pose**（ComfyUI 节点）：从真人照片提取 OpenPose 骨架，比手绘精准
- **Clip Studio Paint**：业界标准漫画排版，无缝添加对话框、音效字、分格线
