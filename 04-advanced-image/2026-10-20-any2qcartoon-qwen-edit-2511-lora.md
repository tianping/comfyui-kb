# 角色一键 Q 化：Any2Qcartoon LoRA（Qwen-Image-Edit-2511）

- **日期**：2026-10-20（收藏）
- **来源**：微信公众号「程序员野区」（ComfyUI 图文教程系列）
- **原标题**：角色一键Q化-comfyui图文教程
- **分类**：04-advanced-image（图像编辑 / 风格 LoRA）
- **URL**：https://mp.weixin.qq.com/s/OIY3IYTyQNwrIvYUbJCGrw
- **LoRA 下载**：https://hf-mirror.com/WarmBloodAban/Any2Qcartoon （HF 原站 `WarmBloodAban/Any2Qcartoon`）

## 概述

**QcartoonV1** 是一个通用 IP Q 版画风 LoRA，把任意角色（从写实照片到复杂的动漫风格）一键转成 **Q 版手办（figurine）**风格，作者称人物特征保留得很好。它基于 **Qwen-Image-Edit-2511** 训练，**只能配 2511 使用**。

## 用法

1. 下载 LoRA，放进 `ComfyUI/models/loras/`。
2. 在 Qwen-Image-Edit-2511 图像编辑工作流里加 LoRA 加载节点（连线见原文截图）。
3. 参数：
   - **触发词**：`convert to q-version cartoon figurine style`
   - **权重**：**1.0**（作者推荐，形体重塑效果最好）
   - **画幅**：最好 **1:1**（宽高相等）。模型在 1024×1024 下训练，所以正方形效果最好。

⚠️ **别配 AIO 模型**：用 Qwen-Image-Edit-Rapid-AIO（溶图）跑，出来会是 **2D** 效果，不是立体手办感。

## 训练参数（模型卡）

| 项目 | 值 |
|---|---|
| 底模 | Qwen-Image-Edit-2511 |
| 训练集 | 65 组高精度对比图对（130 张） |
| Network Rank (Dim) | 32 |
| 优化器 | AdamW |
| 分辨率 | 1024 × 1024 |

## 备注

- 原文是该号 ComfyUI 系列教程中的一篇。同系列还有 Qwen 局部重绘/扩图、Z-Image-Turbo 二次元转真人、SeedVR2 高清修复、SAM3 抠图、krea2 去 AI 脸、Rapid-AIO 溶图等，正文只讲了 Q 化 LoRA。
- 原文没有说明 LoRA 的许可证，商用前去 Hugging Face 模型页确认。
- 相关笔记：[AI 角色资产设计标准化提示词](../03-t2i-flow/character-design-prompt-template.md)、[2026 四个值得用的出图模型（含 Qwen-Image）](../2026-09-13-2026-四个值得用的出图模型.md)。
