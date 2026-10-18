* [首页](README.md)

* 一、基础概念
  * [工作流基本原理](01-basics/workflow-fundamentals.md)
  * [ComfyUI 正在成为「AI 内容操作系统」（生态趋势观点）](01-basics/2026-10-17-comfyui-ai-content-os.md) — 五个信号：NVIDIA GDC 2026 视频管线以 ComfyUI 为核心、AI 视频 workflow 多跑在 ComfyUI、创作者转本地、FLUX.2 带动生态、App Mode/App Builder/ComfyHub 走向应用平台；ComfyGPT 自动写 workflow

* 二、核心节点
  * [KSampler 采样器详解](02-core-nodes/ksampler-details.md)

* 三、图像生成流程
  * [图像放大方案对比](03-t2i-flow/upscale-methods.md)
  * [AI角色资产设计标准化提示词（5套风格模板）](03-t2i-flow/character-design-prompt-template.md)
  * [Ollama 图像生成：终端本地出图实战](03-t2i-flow/ollama-image-generation.md)
  * [Z-Image Turbo 在 ComfyUI 中的使用教程](03-t2i-flow/z-image-turbo-comfyui.md)

* 四、进阶技巧（图像）
  * [ControlNet 基础使用教程](04-advanced-image/controlnet-basics.md)
  * [ComfyUI v0.35.0：视频剪辑进工作流，H3 Max、3D 全收进节点（龙行AI实验室第21篇）](04-advanced-image/2026-09-14-comfyui-v0.35-video-trim-h3-max-3d.md) — v0.35.0（2026-09-09）三大块：原生视频剪辑 VideoTrim/VideoCrop（#15637）+ 官方 H3 Max/Max Turbo 进节点（#16025/#16094，走官方 API 别和社区 LoRA 混）+ 3D 网格 File3DToMesh（GLB/GLTF/OBJ/STL #15919）+ TRELLIS 降显存；4060 跑长视频试 Sparse Attention；日期坑：v0.34.0=08-26，v0.35.0=09-09
  * [ControlNet 核心原理拆解：冻结+克隆+零卷积](04-advanced-image/controlnet-principles.md)

* 五、视频生成
  * [AnimateDiff 视频生成入门](05-video-generation/animatediff-basics.md)
  * [Wan2.7 接入 ComfyUI：AI 视频从出片走向工作流](05-video-generation/2026-10-17-wan27-comfyui-partner-nodes.md) — 2026-04 Partner 节点接入；文/图/音/视四模态统一，首末帧+音频驱动 I2V、视频续播、最多 5 人视频参考（形象+音色）、视频编辑；同期 App Mode/ComfyHub、Dynamic VRAM、Wan2.7-Image
  * [ComfyUI-H3-Continuum：MiniMax H3 分块连续生成超长视频](05-video-generation/2026-10-17-comfyui-h3-continuum-long-video.md) — 自定义节点包：约 5 秒/块分块采样，块间传递原生音视频潜变量，断点续跑、单块重试、多参考图按块路由、时间线提示词
  * [Kandinsky 6.0：一句话生成带声音的视频（ComfyUI 音画同步）](05-video-generation/2026-10-12-kandinsky-6-text-to-audio-video.md) — 开源文生音视频+唇形同步，Pro 29B/Lite 3B，MIT 可商用；ComfyUI 装 kandinsky6+sr，显存≥16GB
  * [LTX2.5 本地 ComfyUI 部署教程](05-video-generation/ltx25-local-deployment.md)
  * [MiniMax H3 突破15秒：接力插件实现任意时长](05-video-generation/minimax-h3-arbitrary-duration.md)
  * [ComfyUI + MiniMax H3 图生视频纯小白教程](05-video-generation/minimax-h3-beginner-tutorial.md)
  * [MiniMax H3 Realism People LoRA：告别AI塑料感](05-video-generation/minimax-h3-realism-people-lora.md)
* [GPT-SoVITS 配音工作流：ComfyUI 个性化配音完整流程](05-video-generation/2026-08-21-gptsovits-comfyui-dubbing-workflow.md)
  * [MiniMax H3 多参工作流（REF2VA）详解](05-video-generation/minimax-h3-multi-reference-workflow.md)
  * [MiniMax H3 电影感 LoRA + Sigma Refiner 工作流](05-video-generation/2026-08-28-minimax-h3-cinematic-lora-sigma-refiner.md) — 电影感LoRA改善光影肤质+Sigma Refiner修复高动态拉丝，文戏武戏同一工作流

* 七、高级工作流
  * [ComfyUI 短剧工作流模板库：5 个开箱即用的工作流](07-advanced-workflows/short-drama-workflow-templates.md)
  * [ComfyUI 漫画分镜完整流程：从一句梗概到多格成稿](07-advanced-workflows/2026-08-26-comfyui-manga-storyboard-workflow.md)
  * [ComfyUI+Dify自动化：从一句话到批量出图，搭建你的AI内容工厂](07-advanced-workflows/2026-08-30-comfyui-dify-automation-content-factory.md) — Dify编排+ComfyUI执行，三个场景：智能提示词→批量分镜→全自动短剧生产线；含API对接、角色一致性、kt-ai-Studio开源实现
  * [ComfyUI实战营收官：把14篇技能串成流水线，一个人就是一个内容团队](07-advanced-workflows/2026-08-28-comfyui-fullstack-content-factory.md) — AI全栈内容工厂七环节全链路拆解、双轨并行模式、L1→L3升级路径、开源工具推荐
  * [MiniMax Music 3：ComfyUI 音乐生成工作流全拆解](07-advanced-workflows/2026-09-07-minimax-music3-comfyui-workflow.md) — 子图封装扩散音频流水线，caption三段式+歌词结构标签，three models (DiT/text encoder/VAE)，低显存tiled decode方案
  * [从零搭建 MiniMax Music 3 音乐生成工作流](07-advanced-workflows/2026-09-12-minimax-music3-comfyui-workflow.md) — 7步搭建（三模型加载→Text Encode→空潜空间→零化→K采样→分块/整体VAE解码→保存），caption三段式英文规范+歌词9种结构标签，cfg1.7推荐值，300s上限
  * [Comfy Agent 实战：一个人 3 天做完 4 分钟动画短片《YUI》](07-advanced-workflows/2026-10-18-comfy-agent-yui-short-film-case.md) — 官方 Agent（Comfy Cloud Beta，Credits 计费，共用画布）；852話 Hakoniwa 4:20 短片约 20 万日元/3 天；交给 Agent：重跑镜头、Wan/MiniMax H3/Seedance 并排比模型、对照设定图查一致性；故事与审美作者自留

* 六、模型与资源管理
  * [Hermes全自动LoRA训练实战](06-models/lora-training-hermes-automated.md)
  * [ComfyUI 本地跑 FLUX.3 Klein：文生图 + 指令改图（官方四套模板）](06-models/2026-10-17-flux3-klein-comfyui-local-t2i-edit.md) — 本地 vs Partner 托管对比；base 50 步 cfg3.5 / Distilled 4–16 步 cfg1；<ref_image_N> 多参考写结果全貌；HF 门控权重目录；ComfyUI ≥0.41.0；踩坑清单
  * [一文看懂GGUF：原理、等级、安装、实战](06-models/2026-08-21-gguf-principles-guide.md)
  * [Krea 2 Turbo 文生动漫图片 — 本地无限免费生成](06-models/2026-08-22-krea-2-turbo-local-anime-generation.md)
  * [2026年8月 ComfyUI 生态爆发：新模型速览 + N/A 卡硬件对照](06-models/2026-08-26-comfyui-august-ecosystem-hardware-guide.md)
  * [ComfyUI + SD 3.5 对比实测：它真的比 SDXL 强吗？](06-models/2026-08-26-sd35-comfyui-comparison.md)

* 八、环境与部署
  * [ComfyUI 0.31 本地视频商用方案：DGX+NAS部署](08-deployment/comfyui-031-local-video-deployment.md)
  * [ComfyUI 手动安装自定义节点教程](08-deployment/custom-node-manual-install.md)

* [inbox（待整理）](inbox/)

  * [出图总是糊？换 2026 四个值得用的出图模型（Z-Image Turbo / FLUX.1 dev / SDXL / Qwen-Image）](2026-09-13-2026-四个值得用的出图模型.md) — ComfyUI 默认 SD 1.5 太老；四模型对号入座：Z-Image Turbo 快速迭代/商用、FLUX dev 写实、SDXL 画风LoRA、Qwen-Image 中文字；低显存走 FP8/GGUF 量化，核心是塞进显存；Z-Image Turbo 装法三步（三个文件三个目录 + 官方模板 + 加载器指向）