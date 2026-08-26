# 2026 年 8 月 ComfyUI 生态爆发 · N 卡 / A 卡硬件对照 · 最低配置清单

> 来源：微信公众号「追光的日记」（逗逗逗），2026-08
> 原文：https://mp.weixin.qq.com/s/lJE11JsjTD6sJ9FA6kuipw

## 总览

8 月以来 ComfyUI 生态密集更新，LTX 2.5、海螺 H3、MiniMax Music 3、Qwen-Video-Edit、SAM 3D Body 等模型接连落地。

**一句话结论**：N 卡（CUDA）是「开箱即用的主战场」；A 卡（AMD）能跑，但 Windows 下要走 ROCm/DirectML，且没有 NVFP4 蒸馏加速，同画质需要更多显存。

## 📋 总览速查表

| 模型 | 类型 | ComfyUI 版本 | N 卡最低 / 推荐 | A 卡最低 / 推荐 |
|------|------|-------------|----------------|----------------|
| LTX 2.5 | 文/图/参考生视频 | v0.32.0+ | 8G(FP8) / 24G(4K) | 12G+·ROCm 验证 / 24G |
| MiniMax H3 | 全模态视频+原生音频 | v0.30.0+ | 12G(Q4) / 24G(全质量) | 16G+ · 无 NVFP4 |
| MiniMax Music 3 | 音乐生成 | v0.33.1+ | 8G / 12G | 8G / 12G |
| Qwen-Video-Edit / CoinVE | 视频编辑(Wan2.1)节点包 | — | 12G(GGUF) / 16G | 12G(GGUF) / 16G |
| SAM 3D Body | 3D 人体网格原生核心节点 | — | 8G / 12G | 8G / 12G |

## ① LTX 2.5 — 本地视频生成新标杆

Lightricks 开源，原生零日集成 ComfyUI，4K / 50fps / 最长 20 秒，比 2.3 快约 2 倍。

**环境配置**
- ComfyUI v0.32.0+，无需额外插件，内置 LTX 节点（`LTXVBaseSampler` 等）
- 模型：主模型 + VAE + Gemma-4 文本编码器（约 20–40GB）
- 三模式：T2V（文生视频）、I2V（图生视频）、R2V（参考驱动）

**硬件配置**

| 平台 | 最低可跑 | 流畅配置 | 说明 |
|------|---------|---------|------|
| N 卡 | 8G（FP8/INT8 + TiledSampler） | 24G（RTX 4090，4K） | RTX 3060 12G 可 720P；RTX 4060 8G 尝鲜 |
| A 卡 | 12G（RDNA3，ROCm） | 24G（RX 7900 XTX） | RDNA4(9060/9070) 需 ROCm 6.4+ 验证；Windows 建议 WSL2+ROCm 或 DirectML |

## ② MiniMax H3 — 全模态视频 + 原生音频

单一上下文联合生成视频与立体声音频（语音/音效/音乐一次出），最高 2K / 24fps / 15 秒。8 月生态更新：H3 Director 分镜、Turbo Ref2V 4 步 LoRA、空间物理 LoRA、训练版 2x Latent Upscaler。

**环境配置**
- ComfyUI v0.30.0+，官方已原生支持 H3 工作流
- 下载 H3 checkpoint + 对应 LoRA（Turbo Ref2V / 空间物理 / 提示词重写）
- 内存 32GB 起步（16GB 会爆），系统 Win11 / Linux

**硬件配置**

| 平台 | 最低可跑 | 推荐 | 关键提醒 |
|------|---------|------|---------|
| N 卡 | 12G（RTX 3060，Q4 量化） | 24G（RTX 4090，全质量） | 有 NVFP4 蒸馏，16G 兼顾画质速度 |
| A 卡 | 16G（RX 7900 XTX / 9070） | 24G+ | ⚠️ 无 NVFP4！只能 FP16/INT8，同画质显存更高；RDNA4 需 ROCm 实测 |

## ③ 其余近期模型

### 🎵 MiniMax Music 3（音乐生成）

ComfyUI v0.33.1+ 原生支持，最长 5 分钟带人声歌曲。模型较小，N/A 卡 8–12G 都能跑，不受 NVFP4 影响，适合自动化配乐（对接 H3 的 `non_diegetic_music`）。

### ✂️ Qwen-Video-Edit / CoinVE-Edit（视频编辑）

基于 Wan2.1-14B + Qwen3-VL-8B（约 22B 组合），单步多指令区域编辑。需 16G+，GGUF 量化可降到 12G。N/A 卡均走 ROCm/量化路径。

### 🧍 SAM 3D Body（3D 人体网格）

Meta 原生集成 ComfyUI 核心，全身 3D 网格 + 姿态追踪，可导出 GLB/BVH。相对轻量，8–12G 即可，N/A 卡差异小，适合数字人动作驱动。

## 🟥 A 卡玩家专属要点（以 RX 9060 XT 16GB 为例）

- **驱动后端**：Windows 优先 WSL2 + ROCm 6.4+（或 DirectML/torch-directml）；纯 Windows 原生 ROCm 对消费卡支持有限
- **RDNA4 确认**：RX 9060 XT 属 RDNA4(gfx12)，需 ROCm 6.4+ 才有较好支持（旧版主要覆盖 RDNA3 如 7900 XTX）
- **没有 NVFP4**：H3 的 4 步蒸馏加速是 N 卡专属；A 卡跑 H3 只能 FP16/INT8，同画质显存更高、更慢——优先 LTX 2.5（开源、量化友好）
- **LTX 2.5 是当前 A 卡最友好**：ROCm 实测可行，FP8/INT8 量化后 12–16G 能产出，比 H3 在 A 卡上更现实
- **实测优先**：任何「N 卡教程」不要直接照抄，先确认 ROCm 能否加载模型再定方案

## 选型建议

- **A 卡用户**：优先 LTX 2.5 + ROCm 走视频生成主线，H3 作为辅助（接受 INT8 量化）
- **N 卡用户**：无脑拉满 H3 全家桶 + LTX 2.5
- 配乐交给 MiniMax Music 3，后期编辑交给 Qwen-Video-Edit，动作捕捉交给 SAM 3D Body —— 一条流水线齐活
