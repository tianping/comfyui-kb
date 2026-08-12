# ComfyUI 知识库

> 边学边整理，边整理边分享。每篇笔记用自己的话写，复制粘贴等于没学。

## 知识地图

### 一、基础概念
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
- 文生图(T2I)标准流程
- 图生图(I2I)与重绘
- 批量生成与种子控制
- 提示词工程（正向/负向/权重）

### 四、进阶技巧（图像）
- [ControlNet 基础使用教程](04-advanced-image/controlnet-basics.md) — 概念、安装、标准工作流、多ControlNet叠加、实用技巧
- IPAdapter / 风格迁移
- 区域控制(Regional Prompter)
- 遮罩与局部重绘(Inpaint)
- LoRA 叠加与权重混调

### 五、视频生成
- [AnimateDiff 视频生成入门](05-video-generation/animatediff-basics.md) — Text2Vid/Vid2Vid工作流、安装、核心节点、进阶技巧
- [LTX2.5 本地 ComfyUI 部署教程](05-video-generation/ltx25-local-deployment.md) — 系统需求、5个核心权重下载、目录配置、工作流获取
- [MiniMax H3 突破15秒：接力插件实现任意时长](05-video-generation/minimax-h3-arbitrary-duration.md) — Motion Context接力+Director导演台，段间清显存、分段落盘、局部重跑
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
- 模型下载与存放规范
- LoRA 训练入门
- 模型合并(Model Merge)
- 资源网站与社区推荐
- Civitai 模型选型指南

### 七、高级工作流
- 多模型级联(SDXL → Refiner)
- 动态工作流与 API 调用
- 自定义节点开发(Node Development)
- 工作流模板复用与分享
- 视频批处理自动化工作流

### 八、环境与部署
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
| 04-advanced-image | ControlNet 基础使用教程 | 4 |
| 05-video-generation | AnimateDiff 视频生成入门 | 1 |
| 05-video-generation | LTX2.5 本地 ComfyUI 部署教程 | 1 |
| 05-video-generation | MiniMax H3 突破15秒：接力插件实现任意时长 | 1 |
| 06-models | Hermes全自动LoRA训练实战 | 1 |

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
