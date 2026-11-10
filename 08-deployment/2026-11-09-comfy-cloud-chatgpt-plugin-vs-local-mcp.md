# ComfyUI 成了 ChatGPT 官方插件：云端插件 vs 本地 comfy-mcp 怎么选

> 来源：[ComfyUI成了ChatGPT官方插件：聊天就能出视频，但用的不是你的显卡](https://mp.weixin.qq.com/s/mtQGpcuMZIv3TUtDlgXFGQ)（公众号 Comfyui）· 2026-11-09
> 原文出处：@ComfyUI 2026-11-05 官宣帖；docs.comfy.org Comfy MCP 文档；comfy.org 云端定价页

## 一句话结论

在 ChatGPT 里 @Comfy Cloud 就能出图、出视频、跑工作流。但活儿是在 **Comfy 自家云端的 96GB 卡**上跑的，扣的是 **Comfy Cloud 订阅额度**，跟你本地的显卡无关。网页版 ChatGPT 也连不到本地的 ComfyUI。

- 显存 <8GB 或用 Mac：插件最值得试，相当于花月费租一张 96GB 卡
- 显存 ≥24GB：装本地 **comfy-mcp** 更划算，让 Claude Code、Codex 这类工具驱动自己的 ComfyUI，生成不花钱；只有调用合作模型（可灵、Seedance 等）才扣积分

## 插件能做什么

背后是官方 MCP 云端连接 `cloud.comfy.org/mcp`，在对话里 @Comfy Cloud 后用大白话下指令。

- **跑官方模板**：文生图、图生视频、放大、风格迁移，可以按名字调用、改参数
- **调各家模型**：MiniMax H3、Seedance、Flux、GPT-Image、Nano Banana、可灵、Z-Image、ElevenLabs、混元 3D 等
- **跑自己的工作流**：读取云端存的工作流直接跑，支持批量排队、生成分享链接、做成只露几个输入框的小应用
- **云端硬件**：Blackwell RTX 6000 Pro，96GB 显存；单个工作流最长 30 分钟（Pro 档 1 小时）

## 三个常见误会

| 误会 | 实际 |
|---|---|
| ChatGPT 自己会跑 ComfyUI 了 | ChatGPT 只负责理解指令、调用工具，生成是 Comfy Cloud 做的 |
| 装上就免费 | 搜模板/模型/节点免费；**生成必须有有效订阅**。只充值不订阅，余额再多也不能跑 |
| 能遥控我电脑上的 ComfyUI | 不能。ChatGPT、claude.ai 这类网页端只接受远程连接；本地连接要用能在本机启动程序的客户端 |

早期版本的局限：复杂多节点工作流可能要重试几次；成品不直接存到你电脑，而是给一个有时效的下载链接加一行 curl 命令。

## 按显存选路（官方划线）

| 设备 | 建议 |
|---|---|
| Mac | 大开源模型在苹果 GPU 上速度不可用，交给云端 |
| 独显 <8GB | 直接用云端 |
| 8–24GB | 图本地跑，视频丢云端 |
| ≥24GB | 含视频在内大部分本地可跑，插件可有可无 |

## 价格

- 新用户送 5 次
- **Standard**：月付 $20（年付折合 $16/月）
- **Creator**：月付 $35，导入自己的 LoRA 需要这一档
- 参照：Wan 2.2 图生视频模板默认设置（640×640、5 秒）一条约 11 积分，Standard 一个月够几百条；H3 这类更重的模型、更高分辨率或者调合作模型，消耗明显更高

## 两条路怎么接

**云端（ChatGPT）**
1. ChatGPT 设置 → 插件页装 Comfy Cloud
2. 对话里 @Comfy Cloud，按提示登录 Comfy 账号
3. 先问一句「Comfy Cloud 上能用哪些视频模型」，这一步免费，能确认已连上；再用赠送的 5 次跑个模板，评估画质和等待时间

**本地（自己的显卡）**，需要 Python ≥3.10：

```bash
pip install "comfy-cli>=1.14.0"
comfy install
pip install comfy-mcp
comfy launch
```

然后把 comfy-mcp 加进 Claude Code / Claude Desktop / Codex / Cursor 的 MCP 配置，重启客户端。
- comfy 装在虚拟环境里的话，用 `COMFY_BIN` 写**绝对路径**，否则客户端找不到
- 本地连接能读到本机装了哪些模型、自定义节点、多少显存，这一点云端做不到

**两条可以同时接**：用同一个 Comfy 账号，但要分别登录。之后说一句「这张本地跑，这条视频放云端」，agent 会自己挑连接。

## 关联

- [Comfy Agent 实战：一个人 3 天做完 4 分钟动画短片《YUI》](../07-advanced-workflows/2026-10-18-comfy-agent-yui-short-film-case.md)：同样跑在 Comfy Cloud，按 Credits 计费
- [ComfyUI 官方模板库 workflow_templates 开源](../07-advanced-workflows/2026-10-27-comfy-org-workflow-templates-repo.md)：插件里“跑官方模板”的模板就来自这里
