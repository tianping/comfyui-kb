# ComfyUI 电商产品主图-自动匹配场景（Flux Context 工作流）

## 元数据
- 日期：2026-09-19
- 来源：苏北沪漂小二子
- 分类：04-advanced-image（电商产品图）
- URL：https://mp.weixin.qq.com/s/7x1jTEsf7cssuKseEXuTww

## 概述
使用 Flux Context 模型做电商产品主图合成：把【产品实拍图】+【场景背景图】一起喂给模型，指令控制产品位置、形态不变，融合进背景环境。参考图布局生成，不改变产品外形。

## 关键内容

### 适用场景
- 产品图融合场景图
- 商品位置保留
- 电商主图合成（参考图布局生成，不改变产品外形）

### 工作流整体逻辑
双图输入 → 图像拼接预处理 → Flux Context 条件编码 → 潜空间生成 → VAE 解码出图

核心原理：利用 Flux Context 支持多图上下文输入，把产品实拍图 + 场景背景图一起喂给模型，用指令控制产品位置、形态不变，融合进背景。

### 节点拆解清单

1. **双 CLIP 加载器**
   - CLIP1：clip_l.safetensors
   - CLIP2：t5xxl_fp8_e4m3fn.safetensors
   - 类型选择：flux（Flux 模型专用 CLIP 组合）

2. **模型加载核心**
   - UNet 加载器：flux1-context-dev-fp8-e4m3fn（Flux Context Dev 权重）
   - 加载 VAE：ae.safetensors（Flux 原生 VAE，不要混用 SD VAE）

3. **提示词**（控制产品位置/形态/场景融合指令）

4. **显存优化（8G 显卡）**
   - 使用 fp8 量化版 Flux1-Context-dev，禁止 fp16 原版
   - CFG 固定 1.0～1.5，不要大于 2
   - 采样步数 18~24 区间足够，没必要 30 步以上

## 影响与备注
- Flux Context 的核心价值是"参考图布局生成"：输入多张图作为上下文条件，保留主体外形融合进新场景，特别适合电商主图批量生产。
- 本工作流与本库 04-advanced-image 的 controlnet-basics 等可互链参照（同属"参考图/条件约束"路线）。

## 相关链接
- 工作流仓库：https://github.com/ssabc/workFlows