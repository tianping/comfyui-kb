# ComfyUI 原生接入 Trellis.2 与 Pixal3D：零依赖本地 3D 生成，6G 显存可跑

- **收藏日期**：2026-10-27
- **来源**：微信公众号「硅基社」
- **原标题**：告别环境噩梦！ComfyUI 原生接入两大 3D 模型，6G 显存也能本地跑
- **分类**：模型与资源管理（3D 生成）
- **URL**：https://mp.weixin.qq.com/s/-yGMDhtRo25PsMTzkcMv0g
- **官方博客**：https://blog.comfy.org/p/trellis2-and-pixal3d-are-now-native
- **部署视频**：PixelArtistry《Best 3D AI Generator Now Runs Natively in ComfyUI!》https://www.youtube.com/watch?v=o7VJnIN31Ds

## 概述

ComfyUI 把微软 **Trellis.2** 和清华 & 腾讯 ARC Lab 的 **Pixal3D** 接进了核心，成为原生节点（文中时间为 2026-09）。图生 3D 的整条流程都能在本地跑完：体素生成 → 网格重构/减面 → UV 展开 → PBR 贴图烘焙 → 导出 GLB。过去本地跑 3D 最头疼的环境配置问题，这次基本解决了。

## 核心突破

- **不用再编译 CUDA 扩展**：原版 Trellis.2 依赖 flash-attention、nvdiffrast、FlexGEMM 等要在本地编译 C++/CUDA 的组件。ComfyUI 团队（开发者 Kiji 等）用纯 PyTorch + SciPy 重写了推理和后处理管线，包括网格重构、减面、展 UV、贴图烘焙。
  - 不用跑安装脚本，也不用装预编译 wheel
  - 不再只能用 NVIDIA CUDA，为 AMD 和 Intel 显卡本地运行打下基础
- **显存门槛降低**：原生支持量化模型，用 INT8 量化版时 **6–8 GB 显存**就能跑完含 PBR 烘焙的全流程。
- **可以商用**：官方 Trellis.2 依赖带非商业限制的 nvdiffrast。原生实现去掉了这类依赖，生成的模型和 PBR 材质可直接用于商业生产。

## 两个模型对比

| 维度 | Trellis.2（Microsoft） | Pixal3D（清华 & 腾讯 ARC Lab） |
|---|---|---|
| 架构 | 4B 参数，O-Voxel 结构化隐空间 + Flow-Matching Transformer | 以 Trellis.2 为骨干，加入像素对齐（pixel-aligned）和反向投影 |
| 擅长 | 复杂拓扑、概念设计、大幅度三维解构，一步出 PBR 材质 | 对参考图还原度极高，几何细节按像素对齐 |
| 条件节点 | `Trellis 2 Conditioning` | `Pixal 3D Conditioning` |
| 什么时候选 | 要灵活性高、拓扑复杂、快速风格化 | 要严格还原原图透视、服装细节、角色结构 |

## 本地部署

1. **更新 ComfyUI 到 GitHub 最新开发版**（关键）：稳定版可能还没有这些节点。
   - Desktop：顶部更新设置 → Update Channel 切到 `latest on GitHub (current)`
   - Portable：运行根目录的 `update_comfyui.bat`
2. **下载权重**（从官方 HuggingFace，按节点提示放置）：
   - 主模型 `trellis2_*.safetensors` / `pixal3d_*.safetensors` → `ComfyUI/models/diffusion_models/`。显存低于 8G 建议用 `pixal3d_int8_comfy.safetensors`
   - DINO v2/v3 权重 → `ComfyUI/models/clip/`
   - 辅助 VAE 和法线模型 → `ComfyUI/models/vae/` 或节点指定的目录
3. **加载原生工作流**：导入图片 → 选择对应的 Conditioning 节点 → 运行 → 在预览节点右下角导出 `.GLB`，可以导入 Blender 继续加工。

## 实用技巧

- **两种 Conditioning 节点不能混用**，混用会直接报错。
- **释放显存**：连续生成导致显存溢出或卡顿时，点 `Edit → Unload models and execution cache`。
- **只要白模时**：框选贴图烘焙（Texture Baking）相关节点，按 `Ctrl+B` 设为 Bypass，生成会快很多。
- **后处理**：节点链里有自动减面（Decimate Mesh，可把几十万面的高模压到 30 万面以内）、自动展 UV、PBR 贴图烘焙（法线、AO、金属度、粗糙度）。

## 备注

- 相关笔记：[ComfyUI v0.35.0：视频剪辑进工作流，H3 Max、3D 全收进节点](../04-advanced-image/2026-09-14-comfyui-v0.35-video-trim-h3-max-3d.md)。v0.35 时 TRELLIS 降了显存需求，这篇是之后 Trellis.2 和 Pixal3D 原生进核心的进展。
- 文中「6G 显存可跑」「可商用」等说法来自原文和官方博客，实际效果以自己的硬件实测为准。
