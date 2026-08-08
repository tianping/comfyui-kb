# ComfyUI 知识库

> 边学边整理，边整理边分享。每篇笔记用自己的话写，复制粘贴等于没学。

## 知识地图

### 一、基础概念
- 节点(Node)与连线(Link)机制
- 工作流(Workflow)概念
- 模型类型（Checkpoint / LoRA / VAE / CLIP）
- ComfyUI 与 A1111 WebUI 的区别

### 二、核心节点
- 加载器类（Load Checkpoint / LoRA / VAE）
- 采样器类（KSampler 及参数详解）
- 编解码类（VAE Encode / Decode）
- 条件类（CLIP Text Encode / Conditioning）
- 图像后处理（Upscale / Save Image）

### 三、图像生成流程
- 文生图(T2I)标准流程
- 图生图(I2I)与重绘
- 图像放大(Upscale)方案对比
- 批量生成与种子控制
- 提示词工程（正向/负向/权重）

### 四、进阶技巧（图像）
- ControlNet 全家族应用
- IPAdapter / 风格迁移
- 区域控制(Regional Prompter)
- 遮罩与局部重绘(Inpaint)
- 图像融合与合成
- LoRA 叠加与权重混调

### 五、视频生成
- AnimateDiff 基础与工作流
- AnimateDiff 进阶（运动模块 / LoRA / CFG）
- SVD(Stable Video Diffusion) 工作流
- 视频帧处理（帧率 / 插帧 / 抽帧）
- 视频生成分辨率与时长控制
- 首尾帧控制与关键帧动画
- Deforum 风格动态视频
- 视频后处理（放大 / 补帧 / 调色）
- 音画同步与剪辑输出
- 视频生成常见问题（闪烁 / 抖动 / 画质崩坏）

### 六、模型与资源管理
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
