# ComfyUI 官方模板库 workflow_templates 开源：449 个工作流带模型清单

- **收藏日期**：2026-10-27
- **来源**：微信公众号「鹏叔大玩家」
- **原标题**：缺节点、版本不对、折腾一晚：ComfyUI 官方模板库开源了，449 个工作流带模型清单
- **分类**：高级工作流 / 资源
- **URL**：https://mp.weixin.qq.com/s/YmKq5wATSWhcH_F8P7bCig
- **仓库**：https://github.com/Comfy-Org/workflow_templates （文中数据：1041 star、205 fork）
- **在线浏览**：https://comfy.org/workflows

## 概述

Comfy-Org 开放了 `workflow_templates` 仓库。ComfyUI 里点开的官方模板都来自这里。作者建议直接从这个仓库拿模板，不要再去网盘和论坛找来路不明的 workflow JSON。那类 JSON 常见的问题是缺自定义节点、缺模型、版本对不上，装依赖、重启一圈，一晚上一张图都出不来。

## 关键内容

### 1. 模板自带模型清单
- 每个模板都有模型元数据：需要哪个模型文件，放在 `models/` 下的哪个子目录。
- 导入前就能把模型备齐，也能预估要占多少硬盘，开跑一次成功的概率明显提高。

### 2. 节点版本信息写在模板里
- 例：Wan 2.1 模板标明 `SaveWEBM` 节点要 **ComfyUI v0.3.26** 以上才完整支持。跑不通先查版本号。
- 每个模板都配缩略图和 `MarkdownNote` 文档节点，里面的链接指向官方教程页。

### 3. 子图蓝图（Subgraph Blueprints）
- 用的是 ComfyUI 原生子图格式，把一段常用流程（如「加载模型 → 采样 → 放大」）压成单个节点，放进节点面板随时取用。
- 作者把三段常用流程收进节点库后，搭一个新工作流的时间从约 30 分钟降到 10 分钟以内。
- 需要 **ComfyUI 前端 ≥ 1.27.7**，在节点库里直接搜索。

### 4. 质量保障与分发
- CI 自动校验每个模板和蓝图的 JSON 语法、schema、bundle 一致性，并检查节点兼容性。
- 配套静态站（Astro 构建）部署在 comfy.org/workflows：449 个模板按文生图、图生视频、音乐生成等分类，支持 11 种语言，模板描述由 AI 生成。
- 整套模板自动打包成 PyPI 包 `comfyui-workflow-templates` 发版。ComfyUI 前端的模板选择器从这里拉索引，官方模板一更新，前端就同步。

## 用法

1. 从 GitHub 克隆或下载 `templates` 目录，把 JSON 拖进 ComfyUI 画布。
2. 要用子图蓝图：前端升到 1.27.7 以上，在节点库里搜索。
3. 进阶：fork 一份，按自己的模型库改造；定期看仓库提交记录，官方上了新模型的模板就拿下来改用。

## 备注

- 老玩家可以把它当作可拆可改的官方底稿，新手可以靠它跳过搭线的门槛。
- 本库另有第三方整理的模板集：[ComfyUI 短剧工作流模板库](short-drama-workflow-templates.md)。
