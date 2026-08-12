# Ollama 图像生成：终端本地出图实战（2026）

> 来源：[Ollama Image Generation: Run Z-Image & FLUX.2 Locally](https://localaimaster.com/blog/ollama-image-generation-models) · 2026-08-12

## 核心结论

2026年1月20日，Ollama 正式加入实验性图像生成功能（仅 macOS，Win/Linux 待来）。在此之前 Ollama 只能「读图」（vision），不能「生图」。这是全新能力，不是旧功能改名。

## 可用模型

| 模型 | 来源 | 参数量 | 默认量化大小 | 许可证 | 适合 |
|------|------|--------|-------------|--------|------|
| x/z-image-turbo | 阿里通义实验室 | 6B | fp8 ~13GB / bf16 ~33GB | Apache 2.0 | 写实、中英双语文字渲染 |
| x/flux2-klein:4b | Black Forest Labs | 4B | ~5.7GB | Apache 2.0 | 快速、可商用 |
| x/flux2-klein:9b | Black Forest Labs | 9B | ~12GB | FLUX 非商用 v2.1 | 更高质量（仅非商用） |

### Z-Image-Turbo 亮点
- 6B 参数，8步出图（8 NFEs），1024×1024
- 中英双语文字渲染准确——大多数开源模型搞不定的文字问题，它搞定
- fp8 ~13GB 可塞进 16GB Mac；bf16 需 36GB+

### FLUX.2 Klein 亮点
- 4B 版本仅 5.7GB 下载，16GB Mac 轻松跑
- Apache 2.0 可商用
- 9B 更清晰但非商用许可

## 用法（终端命令）

```bash
# Z-Image-Turbo — 质量+双语文字
ollama run x/z-image-turbo "a cat holding a sign that says hello world"

# FLUX.2 Klein 4B — 快速+商用
ollama run x/flux2-klein "a neon-lit Tokyo street at night, photorealistic"

# FLUX.2 Klein 9B — 更高质量（非商用）
ollama run x/flux2-klein:9b "a watercolor fox in a misty forest"
```

图片默认保存到当前目录，Ghostty/iTerm2 等终端可内联预览。

交互式会话内可调参数：
```
/set width 1024
/set height 1024
```
也可控制步数、随机种子、负向提示词。

## 显存需求

| 模型 | 下载大小 | 实际显存下限 | 舒适运行 |
|------|---------|-------------|---------|
| x/flux2-klein:4b | ~5.7GB | ~10-12GB | 16GB Mac |
| x/z-image-turbo (fp8) | ~13GB | ~16GB | 24GB+ Mac |
| x/flux2-klein:9b | ~12GB | ~16GB | 24GB+ Mac |
| x/z-image-turbo (bf16) | ~33GB | ~36GB+ | 48/64GB Mac |

## Ollama vs ComfyUI 对比

| 能力 | Ollama（实验性） | ComfyUI / Forge |
|------|-----------------|-----------------|
| 平台 | 仅 macOS | Win/Linux/macOS |
| 界面 | 终端 | 节点图/Web UI |
| 模型选择 | 2个精选模型 | 数百个 |
| LoRA | 不支持 | 支持 |
| ControlNet | 不支持 | 支持 |
| Inpainting/Outpainting | 不支持 | 支持 |
| img2img | 不支持 | 支持 |
| 批量/自动化 | 有限 | 丰富 |
| 安装难度 | 极低（一条命令） | 中等 |

**何时选 Ollama**：终端快速出图、已在用 Ollama 跑文本模型、Mac 用户、不需要精细控制。

**何时选 ComfyUI**：需要 LoRA/ControlNet/重绘/img2img/特定 checkpoint，或在 Win/Linux 上。任何严肃的、可重复的图像工作，ComfyUI 仍是工具。

## 选型建议

- **追求质量** → x/z-image-turbo（写实+文字渲染）
- **追求速度** → x/flux2-klein:4b（小、快、Apache 2.0）
- **非商用高质量** → x/flux2-klein:9b（注意许可证限制）
