# ComfyUI 知识库

> 边学边整理，边整理边分享。每篇笔记用自己的话写，复制粘贴等于没学。

## 知识地图

### 一、基础概念
- [出图总是糊？换 2026 四个值得用的出图模型（Z-Image Turbo / FLUX.1 dev / SDXL / Qwen-Image）](2026-09-13-2026-四个值得用的出图模型.md) — ComfyUI 默认 SD 1.5 太老；四模型对号入座：Z-Image Turbo 快速迭代/商用、FLUX dev 写实、SDXL 画风LoRA、Qwen-Image 中文字；低显存走 FP8/GGUF 量化，核心是塞进显存；Z-Image Turbo 装法三步（三个文件三个目录 + 官方模板 + 加载器指向）
- [工作流基本原理](01-basics/workflow-fundamentals.md) — 节点/连线机制、五大基础节点类别、三种基本工作流模式
- 节点(Node)与连线(Link)机制
- 工作流(Workflow)概念
- 模型类型（Checkpoint / LoRA / VAE / CLIP）
- ComfyUI 与 A1111 WebUI 的区别

### 二、核心节点
- [KSampler 采样器详解](02-core-nodes/ksampler-details.md) — 参数说明、采样器选择指南、调度器组合推荐
- 加载器类（Load Checkpoint / LoRA / VAE）
- 采样器类（KSampler 及参数详解）
- 编解码类（VAE Encode / Decode）
- 条件类（CLIP Text Encode / Conditioning）
- 图像后处理（Upscale / Save Image）

### 三、图像生成流程
- [图像放大方案对比](03-t2i-flow/upscale-methods.md) — 各类放大算法特点、组合策略、适用场景
- [AI角色资产设计标准化提示词（5套风格模板）](03-t2i-flow/character-design-prompt-template.md) — 5套完整角色设定模板：正脸特写/三视图/6表情/服装配饰拆解/色彩色卡，法式初恋·暗夜蔷薇·暖阳治愈·暗夜红酒·中式水墨旗袍
- [Ollama 图像生成：终端本地出图实战](03-t2i-flow/ollama-image-generation.md) — 2026年1月Ollama加入实验性出图，Z-Image-Turbo+FLUX.2 Klein两模型，终端一条命令出图，附显存需求与ComfyUI对比
- [Z-Image Turbo 在 ComfyUI 中的使用教程](03-t2i-flow/z-image-turbo-comfyui.md) — 阿里通义6B distilled模型，8步出图2-3秒，BF16/FP8/GGUF三档显存，三文件安装+推荐参数+vs FLUX/SDXL对比
- 文生图(T2I)标准流程
- 图生图(I2I)与重绘
- 批量生成与种子控制
- 提示词工程（正向/负向/权重）

### 四、进阶技巧（图像）
- [ControlNet 基础使用教程](04-advanced-image/controlnet-basics.md) — 概念、安装、标准工作流、多ControlNet叠加、实用技巧
- [ControlNet 核心原理拆解：冻结+克隆+零卷积](04-advanced-image/controlnet-principles.md) — 为什么克隆编码器不克隆整个UNet、零卷积为何从零开始增长、ControlNet与底模强耦合不可跨模型通用
- IPAdapter / 风格迁移
- 区域控制(Regional Prompter)
- 遮罩与局部重绘(Inpaint)
- LoRA 叠加与权重混调

### 五、视频生成
- [AnimateDiff 视频生成入门](05-video-generation/animatediff-basics.md) — Text2Vid/Vid2Vid工作流、安装、核心节点、进阶技巧
- [LTX2.5 本地 ComfyUI 部署教程](05-video-generation/ltx25-local-deployment.md) — 系统需求、5个核心权重下载、目录配置、工作流获取
- [MiniMax H3 突破15秒：接力插件实现任意时长](05-video-generation/minimax-h3-arbitrary-duration.md) — Motion Context接力+Director导演台，段间清显存、分段落盘、局部重跑
- [ComfyUI + MiniMax H3 图生视频纯小白教程](05-video-generation/minimax-h3-beginner-tutorial.md) — 三步上手：下载ComfyUI→软件内自动下载H3模型→图生视频，16G显存可跑
- [MiniMax H3 Realism People LoRA：告别AI塑料感，生成影视级真人](05-video-generation/minimax-h3-realism-people-lora.md) — 针对H3的写实增强LoRA，不改风格而是重组物理光影，解决动态人像恐怖谷，HuggingFace已开源
- [GPT-SoVITS 配音工作流：ComfyUI 个性化配音完整流程](05-video-generation/2026-08-21-gptsovits-comfyui-dubbing-workflow.md) — 3~10秒参考音频克隆音色，分场景参数速查表（口播/种草/剧情/科普），RNNoise降噪+EQ+响度归一化全流程
- [MiniMax H3 多参工作流（REF2VA）详解](05-video-generation/minimax-h3-multi-reference-workflow.md) — FL2VA vs REF2VA、9图+3音频+3视频参考上限、if/else加速LoRA开关（20步→4步）、帧数换算公式、res_multistep采样器、skill提示词模板
- [MiniMax H3 电影感 LoRA + Sigma Refiner 工作流](05-video-generation/2026-08-28-minimax-h3-cinematic-lora-sigma-refiner.md) — 电影感LoRA改善光影肤质+Sigma Refiner修复高动态拉丝，文戏武戏同一工作流，720P→Topaz放大方案
- AnimateDiff 进阶（运动模块 / LoRA / CFG）
- SVD(Stable Video Diffusion) 工作流
- 视频帧处理（帧率 / 插帧 / 抽帧）
- 首尾帧控制与关键帧动画
- Deforum 风格动态视频
- 视频后处理（放大 / 补帧 / 调色）
- 音画同步与剪辑输出
- 视频生成常见问题（闪烁 / 抖动 / 画质崩坏）

### 六、模型与资源管理
- [Hermes全自动LoRA训练实战](06-models/lora-training-hermes-automated.md) — 从装环境到出图零代码，512张图10小时训练，数据集决定上限
- [一文看懂GGUF：原理、等级、安装、实战](06-models/2026-08-21-gguf-principles-guide.md) — 量化压缩+内存映射，显存12GB→4GB，FLUX.1 Dev各等级量化文件对照表+ComfyUI使用方法
- [2026年8月 ComfyUI 生态爆发：新模型速览 + N/A 卡硬件对照](06-models/2026-08-26-comfyui-august-ecosystem-hardware-guide.md) — LTX 2.5 / MiniMax H3 / Music 3 / Qwen-Video-Edit / SAM 3D Body 最低显存速查表，A卡ROCm要点与选型建议
- [ComfyUI + SD 3.5 对比实测：它真的比 SDXL 强吗？](06-models/2026-08-26-sd35-comfyui-comparison.md) — MM-DiT架构+CLIP/T5双编码器节点连法，三版本选型（Medium 8G⭐/Large 16G），自然语言Prompt写法与常见坑速查
- 模型下载与存放规范
- LoRA 训练入门
- 模型合并(Model Merge)
- 资源网站与社区推荐
- Civitai 模型选型指南

### 七、高级工作流
- [ComfyUI 短剧工作流模板库：5 个开箱即用的工作流](07-advanced-workflows/short-drama-workflow-templates.md) — 角色一致性（IP-Adapter+LoRA参数）/批量分镜/Prompt List/Wan2.2·H3·LTX三方案图生视频/修复流水线（去闪→超分→插帧→口型）/多角色同框三方案
- [ComfyUI 漫画分镜完整流程：从一句梗概到多格成稿](07-advanced-workflows/2026-08-26-comfyui-manga-storyboard-workflow.md) — 角色参考卡+IP-Adapter锁脸+ControlNet控构图+固定模型配方保风格，批量生图筛选与分镜Prompt模板
- [ComfyUI+Dify自动化：从一句话到批量出图，搭建你的AI内容工厂](07-advanced-workflows/2026-08-30-comfyui-dify-automation-content-factory.md) — Dify编排+ComfyUI执行，三个场景：智能提示词→批量分镜→全自动短剧生产线；含API对接、角色一致性、kt-ai-Studio开源实现
- [ComfyUI实战营收官：把14篇技能串成流水线，一个人就是一个内容团队](07-advanced-workflows/2026-08-28-comfyui-fullstack-content-factory.md) — AI全栈内容工厂七环节全链路拆解（选题→脚本→分镜→出图→视频→后期→发布）、双轨并行模式、L1→L3升级路径、开源工具推荐
- [MiniMax Music 3：ComfyUI 音乐生成工作流全拆解](07-advanced-workflows/2026-09-07-minimax-music3-comfyui-workflow.md) — 子图封装扩散音频流水线，caption三段式+歌词结构标签，three models(DiT/text encoder/VAE)，低显存tiled decode方案
- 多模型级联(SDXL → Refiner)
- 动态工作流与 API 调用
- 自定义节点开发(Node Development)
- 工作流模板复用与分享
- 视频批处理自动化工作流

### 八、环境与部署
- [ComfyUI 0.31 本地视频商用方案：DGX+NAS部署](08-deployment/comfyui-031-local-video-deployment.md) — 0.31核心更新、本地视频五大流派(MiniMax H3/Wan Animate 2/Wan 2.2 MoE/LTX-2/云端API)、2026静态绘图模型格局、DGX Spark+QNAP NFS算力存储分离架构
- [ComfyUI 手动安装自定义节点教程](08-deployment/custom-node-manual-install.md) — 三种手动安装方式、依赖安装核心坑点、为何弃手动改用 Manager
- 本地安装与配置(Windows / Linux / Mac)
- 云端部署(Google Colab / RunPod / AutoDL)
- 性能优化与显存管理
- 常见问题排查与日志分析
- ComfyUI Manager 安装与使用

---

## 已收录笔记索引

| 分类 | 笔记 | 来源数 |
|------|------|--------|
| 01-basics | 工作流基本原理 | 4 |
| 02-core-nodes | KSampler 采样器详解 | 2 |
| 03-t2i-flow | 图像放大方案对比 | 1 |
| 03-t2i-flow | AI角色资产设计标准化提示词（5套风格模板） | 1 |
| 03-t2i-flow | Ollama 图像生成：终端本地出图实战 | 1 |
| 03-t2i-flow | Z-Image Turbo 在 ComfyUI 中的使用教程 | 1 |
| 04-advanced-image | ControlNet 基础使用教程 | 4 |
| 04-advanced-image | ControlNet 核心原理拆解：冻结+克隆+零卷积 | 1 |
| 05-video-generation | AnimateDiff 视频生成入门 | 1 |
| 05-video-generation | LTX2.5 本地 ComfyUI 部署教程 | 1 |
| 05-video-generation | MiniMax H3 突破15秒：接力插件实现任意时长 | 1 |
| 05-video-generation | ComfyUI + MiniMax H3 图生视频纯小白教程 | 1 |
| 05-video-generation | MiniMax H3 Realism People LoRA | 1 |
| 05-video-generation | GPT-SoVITS 配音工作流：ComfyUI 个性化配音完整流程 | 1 |
| 05-video-generation | MiniMax H3 多参工作流（REF2VA）详解 | 1 |
| 07-advanced-workflows | ComfyUI 短剧工作流模板库：5 个开箱即用的工作流 | 1 |
| 07-advanced-workflows | ComfyUI 漫画分镜完整流程：从一句梗概到多格成稿 | 1 |
| 06-models | Hermes全自动LoRA训练实战 | 1 |
| 06-models | 一文看懂GGUF：原理、等级、安装、实战 | 1 |
| 06-models | Krea 2 Turbo 文生动漫图片 — 本地无限免费生成 | 1 |
| 06-models | 2026年8月 ComfyUI 生态爆发：新模型速览 + N/A 卡硬件对照 | 1 |
| 06-models | ComfyUI + SD 3.5 对比实测：它真的比 SDXL 强吗？ | 1 |
| 08-deployment | ComfyUI 0.31 本地视频商用方案：DGX+NAS部署 | 1 |
| 08-deployment | ComfyUI 手动安装自定义节点教程 | 1 |
| 07-advanced-workflows | MiniMax Music 3：ComfyUI 音乐生成工作流全拆解 | 1 |

---

## 使用方式

1. **收集**：看到好文章，发链接给我，我来抓取归档到 `inbox/`
2. **消化**：每周整理，把 inbox 里的内容归入对应分类，用自己的话写笔记
3. **输出**：某个分类有 3-5 篇笔记后，合并成文章分享
4. **迭代**：框架本身持续进化，该加加该拆拆

## 文件命名约定

- 笔记文件：`小写英文-连字符.md`（如 `controlnet-basics.md`）
- 剪藏文件：`inbox/原文章标题简写.md`
- 每篇笔记顶部保留：
  ```
  # 标题
  > 来源：[文章名](链接) · 日期
  ```

## 去重规则

- **链接重复**：搜索已有 URL，不重复存
- **内容重复无新信息**：跳过
- **内容有部分新信息**：合并进已有笔记，追加来源
- **角度不同各有价值**：各自保留，互相引用
- **内容冲突**：标注冲突，留给用户判断

- [ComfyUI v0.35.0：视频剪辑进工作流，H3 Max、3D 全收进节点（龙行AI实验室第21篇）](04-advanced-image/2026-09-14-comfyui-v0.35-video-trim-h3-max-3d.md) — v0.35.0（2026-09-09）三大块：原生视频剪辑 VideoTrim/VideoCrop（#15637）+ 官方 H3 Max/Max Turbo 进节点（#16025/#16094，走官方 API 别和社区 LoRA 混）+ 3D 网格 File3DToMesh（GLB/GLTF/OBJ/STL #15919）+ TRELLIS 降显存；4060 跑长视频试 Sparse Attention；日期坑：v0.34.0=08-26，v0.35.0=09-09 (2026-09-14)
