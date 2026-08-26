# ComfyUI + Stable Diffusion 3.5 对比实测：它真的比 SDXL 强吗？

> 来源：微信公众号「漫剧AI实验笔记」ComfyUI 核心技术系列第11期，2026-06
> 原文：https://mp.weixin.qq.com/s/Y6s4T-wMMciv86CO8NoP4g

⚠️ SD 3.5 对显卡要求高，6GB 显存以下基本无法运行。

## 🧬 架构革命：MM-DiT

| 项目 | SD 1.5 / SDXL | SD 3.5 |
|------|--------------|--------|
| 核心架构 | 单一 UNet | 多模态扩散 Transformer（MM-DiT） |
| 文本编码器 | CLIP only | CLIP + T5-XXL 双编码器 |
| 参数量 | SDXL: 2.6B | Large: 8B |
| 原生分辨率 | SDXL: 1024×1024 | 最高 2048×2048 |

**一句话理解 MM-DiT**：传统 SD 是「文本→UNet→图片」的单行道；SD 3.5 是「文本和图片在 Transformer 里互相看、互相改」的双向对话——所以文字理解能力暴涨。

## 📦 三个版本，别下错了

| 版本 | 参数量 | 显存需求 | 推荐人群 |
|------|-------|---------|---------|
| SD 3.5 Large | 8B | ≥16 GB | 旗舰体验，质量最高 |
| SD 3.5 Large Turbo | 8B | ≥16 GB | 4步出图，速度极快 |
| SD 3.5 Medium ⭐ | 2.5B | ≥8 GB | 消费级显卡首选 |

💡 大多数人选 **Medium**：8GB 显存就能跑，画质和 SDXL 相当，文字理解明显更强。

## ⚙️ ComfyUI 部署步骤

**Step 1：下载模型文件**（Hugging Face，需 Stability AI 授权）

```
sd3.5_medium.safetensors          → ComfyUI/models/checkpoints/
clip_g.safetensors                → ComfyUI/models/clip/
t5xxl_fp8_e4m3fn.safetensors      → ComfyUI/models/clip/（显存不够选这个）
t5xxl_fp16.safetensors            → ComfyUI/models/clip/（显存够用）
sd3.5_vae.safetensors             → ComfyUI/models/vae/（自带VAE最稳）
```

T5-XXL 有 fp16（9.6GB）和 fp8（4.8GB）两版，显存不够选 fp8_e4m3fn，质量损失很小。

**Step 2：节点连接（与 SDXL 的核心区别）**

SD 3.5 需要**两个 CLIP 编码器**：
- `Load Checkpoint(SD3)` → MODEL → KSampler（SD3）
- CLIP → `CLIP Text Encode（SD3）`×2（分别连 CLIP 和 T5）
- 还需单独加载 T5-XXL + CLIP-G
- VAE → VAE Decode

**Step 3：官方工作流模板（最快）**

ComfyUI 右上角「Load Default」→ 选「SD3」模板 → 刷新模型列表 → 选择 sd3.5_medium → Queue Prompt。

## 🔬 实测对比（RTX 4070 Ti 12GB，同提示词）

| 评测项目 | SD 1.5 | SDXL | SD 3.5 Medium | SD 3.5 Large |
|---------|--------|------|---------------|--------------|
| 画质（人物） | 6/10 | 8/10 | 8.5/10 | 9/10 |
| 画质（风景） | 5/10 | 8/10 | 9/10 | 9.5/10 |
| 文字理解 | 4/10 | 6/10 | 8/10 | 9/10 |
| 手部准确度 | 3/10 | 6/10 | 7/10 | 8/10 |
| 显存占用(512) | 4 GB | 7 GB | 8 GB | 16 GB |

🏆 **结论**：SD 3.5 Medium 是 SDXL 的正向升级——画质更好、文字理解更强，显存只多 1GB；Large 需 16GB，消费级基本无缘。

## ✍️ Prompt 写法变了！

SD 3.5 支持自然语言描述 + 最长 **500 token**：

| 模型 | 推荐写法 |
|------|---------|
| SD 1.5 | `1girl, long hair, blue dress, forest, sunlight, masterpiece, best quality` |
| SDXL | `A girl with long hair wearing a blue dress, standing in a forest with sunlight filtering through trees, highly detailed` |
| SD 3.5 | `A young woman with flowing long hair in a blue dress stands in a sunlit forest. Golden light filters through the canopy, casting dappled shadows...` |

**负面提示词也用自然语言**：

```python
# 旧式（SD 1.5）
negative_prompt = "low quality, blurry, worst quality, bad anatomy"

# SD 3.5 推荐（自然语言）
negative_prompt = """The image should not be blurry or low resolution.
Avoid distorted anatomy, extra fingers, or unnatural lighting.
No text artifacts or watermark."""
```

## ⚡ Large Turbo — 4 步出图

| 项目 | Medium | Large Turbo |
|------|--------|-------------|
| 推荐步数 | 20~30 | **4 步** |
| 出图速度(512) | ~15秒 | ~3秒 |
| 画质损失 | 无 | 轻微（可接受） |
| 采样器 | Euler / DPM++ | Euler a（固定） |

💡 高效工作流：Turbo 批量出 20 张筛选 → 最好的用 Medium/Large Img2Img 重绘提画质。

## 🚨 常见坑 & 解决方案

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| 显存溢出 OOM | T5-XXL fp16 太大 | 换 T5-XXL fp8 版；或 `--lowvram` |
| 出图偏色 | VAE 没配对 | 加载 SD 3.5 专用 VAE（自带最稳） |
| 提示词不生效 | CLIP 连错 | 需两个 `CLIP Text Encode（SD3）`节点，分别连 CLIP 和 T5 |
| 出图很慢 | 没用 Turbo / 步数太多 | 切 Large Turbo；或降到 20 步内 |
| 报错 "unknown model" | ComfyUI 太旧 | 更新到支持 SD3 架构的版本 |

## 🎯 升级建议（按显存对号入座）

- ✅ **8GB 以上**：推荐升级 Medium，值得
- ⚠️ **12GB 以上**：可考虑 Large（理想 16GB），12G 开 `--lowvram` 较慢
- ❌ **6GB 及以下**：继续用 SDXL + LoRA 组合，效果更好
