# 图像放大方案对比

> 来源：[我要自学网 - Comfyui基础节点教程详解](https://www.51zxw.net/TechArticleDetails.aspx?zid=129&id=2756) · 2026-08-08

## 放大算法一览

| 算法 | 类型 | 特点 | 适用场景 |
|------|------|------|---------|
| None | 纯数学 | 无优化，直接拉伸 | 不推荐 |
| Lanczos | 纯数学 | 加权平均插值，利用原图像素信息 | 传统算法，效果一般 |
| Nearest | 纯数学 | 最近邻插值 | 效果不如 Lanczos |
| LDSR | 深度学习 | 轻量网络+残差学习，速度快 | 效果最好但非常耗时 |
| ESRGAN | 深度学习 | 生成对抗网络，增加真实纹理 | 可能过度拟合 |
| 4x-UltraSharp | 深度学习 | ESRGAN 优化版 | 适合处理真人 |
| R-ESRGAN 4x+ | 深度学习 | Real ESRGAN 优化 | 适合真实风格 |
| R-ESRGAN 4x+ Anime6B | 深度学习 | 动漫专用 | 适合动漫风格 |
| ScuNET GAN | 深度学习 | GAN 训练 | 视觉效果提升 |
| ScuNET PSNR | 深度学习 | 均方误差训练 | 客观质量提升 |
| SwinIR_4x | 深度学习 | Swin Transformer | 通用超分 |

> 📷 [图：各放大算法效果对比，从左到右依次展示 None/Lanczos/ESRGAN/R-ESRGAN 4x+/4x-UltraSharp 的放大效果差异]
> 来源：https://www.51zxw.net/TechArticleDetails.aspx?zid=129&id=2756

## Upscaler1 + Upscaler2 组合策略

Upscaler2 用于避免 Upscaler1 过度处理（如磨皮太严重）。可见度参数 0=不用，1=完全用 Upscaler2。

### 出图锐度优先
- Upscaler1：4x-UltraSharp 或 R-ESGAN 4x+
- Upscaler2：Lanczos（细节补充）

### 出图细节优先
- Upscaler1：Lanczos
- Upscaler2：Lanczos（锐度补充）

### 测试结论
- LDSR 效果最好但非常耗时，慎重使用
- 4x-UltraSharp、BSRGAN、R-ESRGAN 4x+ 锐化偏重，搭配 Upscaler2 平衡效果更好
- R-ESRGAN 4x+ Anime6B 动漫推荐，用在真人会有重涂抹感

## 模型存放位置
`ComfyUI/models/upscale_models/`
