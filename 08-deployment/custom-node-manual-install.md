# ComfyUI 手动安装自定义节点教程

> 来源：[ComfyUI 手动装节点教程：先吃透原理，再用 Manager 解放双手](https://mp.weixin.qq.com/s/gQz7_9blb_DPpU5aS5f3YA) · 2026-08-19

## 概述

本文详细讲解了 ComfyUI 自定义节点的手动安装原理与三种方式，**核心建议：先手动装一次理解原理，再用 ComfyUI Manager 管理节点**。

---

## 自定义节点存放位置

所有自定义节点统一存放在：
```
ComfyUI/custom_nodes/
```

ComfyUI 启动时会扫描该目录下的每个子文件夹，将其作为一个自定义节点加载。手动安装的本质就是把节点文件夹弄进这个目录。

---

## 三种手动安装方式

### 方法一：Git Clone（推荐）

优势：保留 Git 版本信息，后续更新只需 `git pull`。

```bash
# 1. 确认 Git 已安装
git --version

# 2. 进入 custom_nodes 目录
cd /d D:\ComfyUI_windows_portable\ComfyUI\custom_nodes

# 3. 克隆节点仓库（以 ComfyUI-Manager 为例）
git clone https://github.com/Comfy-Org/ComfyUI-Manager.git

# 4. 安装依赖（关键步骤！）
# 必须使用 ComfyUI 自带的 Python 环境
.\python_embeded\python.exe -m pip install -r ComfyUI\custom_nodes\ComfyUI-Manager\requirements.txt

# 5. 重启 ComfyUI
```

> ⚠️ **注意**：如果开启了网络代理，Git 可能报错 "connection reset"。需关闭代理或给 Git 配置代理。

---

### 方法二：下载 ZIP 包解压

适用场景：未安装 Git、或无法访问 GitHub。

1. 打开节点 GitHub 页面（如 https://github.com/Comfy-Org/ComfyUI-Manager）
2. 点击绿色 `Code` → `Download ZIP`
3. 解压得到文件夹（如 `ComfyUI-Manager-main`）
4. **重命名去掉 `-main` 后缀** → `ComfyUI-Manager`
5. 移动到 `ComfyUI/custom_nodes/`
6. 安装依赖（同方法一第 4 步）
7. 重启 ComfyUI

缺点：丢失 Git 版本控制，更新时需重新下载、解压、覆盖。

---

### 方法三：直接拖拽文件夹

适用场景：从他人处获得节点文件夹。

1. 把整个节点文件夹复制到 `custom_nodes/`
2. 检查并安装依赖（若有 `requirements.txt`）
3. 重启 ComfyUI

前提：文件夹需包含完整代码（至少有 `__init__.py` 等入口文件）。

---

## 依赖安装核心坑点

**错误做法**：在系统终端直接运行 `pip install -r requirements.txt`  
→ 依赖装到系统 Python，ComfyUI 用的是内置 `python_embeded`，两个环境不通。

**正确做法**：
```bash
# 在便携版根目录下执行
.\python_embeded\python.exe -m pip install -r ComfyUI\custom_nodes\<节点名>\requirements.txt
```

---

## 手动安装复盘

完整流程：
1. 找到节点仓库地址
2. `cd` 到 `custom_nodes` 目录
3. `git clone` / 下载 ZIP / 拖拽文件夹
4. 用 ComfyUI 自带 Python 安装 `requirements.txt` 依赖
5. 重启 ComfyUI
6. 报错则查日志排查

---

## 为什么几乎没人用纯手动方式？

- 装几十个节点极其繁琐
- 节点更新需逐个手动操作
- 卸载冲突节点麻烦
- 下载工作流缺节点时，需一个个去 GitHub 搜、装

**这就是 ComfyUI Manager 存在的意义**——它解决了上述所有痛点。

---

## 实用建议

1. **观察节点来源**：界面多为英文，复制翻译或用检索工具确认功能
2. **先看 README**：了解特殊要求（需下载模型、特定 Python 版本、已知问题）
3. **养成看启动日志习惯**：CMD 窗口显示加载了哪些节点、有无报错、依赖缺失等关键信息

---

## 相关笔记

- [ComfyUI 0.31 本地视频商用方案：DGX+NAS部署](comfyui-031-local-video-deployment.md) — 包含 ComfyUI Manager 安装与使用
- [工作流基本原理](../01-basics/workflow-fundamentals.md) — 节点/连线机制基础