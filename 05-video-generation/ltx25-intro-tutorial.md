# LTX-2.5 入门到实操：开源 AI 视频生成全流程教学

> 来源：[追光的日记](https://mp.weixin.qq.com/s/2_NI_HA1BvrFszkyF72l-Q) · 2026-08-12  
> 参考：[LTX 官方文档](https://ltx.io/model/ltx-2-5) · [Steven Video 教程](https://www.stevenvideo.com/blog/ltx-2-open-source-ai-video-guide-zh)

## 核心结论

LTX-2.5 是 LTX 项目最新的开源 AI 视频生成模型，采用扩散变换器（DiT）架构，专为**生产、研究、教育和实验**场景设计。与传统视频生成模型不同，它支持**单次生成多镜头（multi-shot）**，支持**真实素材编辑**（real footage editing），并输出**专业级 EXR 格式**，适合电影级制作。

## 关键特性

| 特性 | 说明 |
|------|------|
| **多镜头生成** | 单次运行即可生成多镜头场景（如：特写→中景→全景），无需多次运行 |
| **真实素材编辑** | 可直接对真实视频素材进行修改、替换、重绘，非合成生成 |
| **EXR 输出** | 原生支持 OpenEXR 格式，支持 10-bit 深度、HDR 元数据，专业后期使用 |
| **4K 画质** | 原生支持 3840×2160 分辨率，帧率最高 50fps |
| **10 秒生成时长** | 单次生成时长限制，可通过拼接实现更长视频 |
| **原生音频** | 生成过程同步输出原生音频，无需后期合成 |
| **本地运行** | 无需云服务，在本地 GPU 上即可运行，支持 8GB 以上显卡 |

---

## 与 LTX-2.3 的区别

| 特性 | LTX-2.3 | LTX-2.5 |
|------|--------|--------|
| **架构** | 传统扩散模型 | 改进的 DiT（Scalable DiT） |
| **生成速度** | 较慢，典型 15-30 秒/10秒 | **2-3 秒/1024px**（RTX 4090） |
| **分辨率** | 1080p 为主 | **4K（3840×2160）** 原生支持 |
| **帧率** | 24-30fps | **50fps**（高帧率流畅感） |
| **音频** | 仅生成静音 | **同步生成原生音频** |
| **格式** | MP4/H.264 | **EXR（OpenEXR）** + 原生音频 |
| **本地部署** | 较复杂，需特定环境 | **更友好，支持 Docker/Colab** |

---

## 如何使用 LTX-2.5

### 1. 系统要求
- **最低配置**：NVIDIA GPU（RTX 3060 及以上推荐）
- **显存**：8GB 以上（4K 生成建议 12GB+）
- **系统**：Linux/Windows/macOS（支持 Docker 部署）

### 2. 安装方式
- **官方 Docker 镜像**：`docker pull ltx/ltx2.5`
- **本地安装**：
  ```bash
  # 克隆仓库
  git clone https://github.com/ltx-io/ltx-video.git
  cd ltx-video
  
  # 安装依赖
  pip install -r requirements.txt
  
  # 下载模型权重（需注册账号）
  wget https://ltx.io/model/ltx-2.5-weights.pth -O weights.pth
  ```
- **Docker 快速启动**：
  ```bash
  docker run -it --gpus all -v $(pwd):/workspace ltx/ltx2.5
  ```

### 3. 工作流程
1. **准备素材**：收集需要生成的视频素材（MP4/MOV/WEBM 等格式）
2. **编写提示词**：描述场景、风格、情绪、构图要求（参考 LTX-2.5 提示词模板）
3. **选择模式**：
   - `text2video`：纯文本生成
   - `img2video`：基于已有图片/视频生成
   - `img2img`：修改已有视频
4. **调整参数**：
   - `--steps`：生成步数（建议 20-30）
   - `--cfg`：引导强度（建议 3.0-5.0）
   - `--seed`：固定随机种子，便于复现
   - `--duration`：指定时长（默认 10 秒）
5. **生成与预览**：
   - 本地渲染后预览
   - 导出 EXR 文件（.exr）用于后期处理
   - 导出 MP4 用于分享

### 4. 示例提示词
```
A cinematic scene of a futuristic city at sunset, 4K, 50fps, dramatic lighting, cinematic color grading, unreal engine 5 render, hyperrealistic, detailed textures, volumetric lighting, film grain, 16:9 aspect ratio
```

### 5. 实用技巧
- **拼接技巧**：多次生成不同镜头，用 DaVinci Resolve 或 Premiere 进行无缝拼接
- **素材替换**：用真实人脸/场景替换生成内容，保持真实感
- **音频同步**：生成音频后，用 Audacity 与视频同步
- **参数调优**：减少 steps 可提升速度，增加 CFG 可增强提示词遵循度

---

## 与 LTX-2.3 的对比

| 维度 | LTX-2.3 | LTX-2.5 |
|------|--------|--------|
| **生成速度** | 15-30 秒/10秒 | **2-3 秒/1024px** |
| **分辨率** | 1080p 为主 | **4K（3840×2160）** |
| **帧率** | 24-30fps | **50fps**（电影级流畅度） |
| **音频** | 无 | **原生音频生成** |
| **格式** | MP4/H.264 | **EXR + 原生音频** |
| **适用场景** | 短视频、社交媒体 | **电影级制作、教育演示、专业展览** |

---

## 实际案例

### 案例 1：教育视频生成
- **需求**：老师需要制作《细胞呼吸过程》动画，时长 8 秒
- **实现**：
  1. 提供细胞微观图片 + 文字描述（"线粒体膜上有氧化还原过程发生"）
  2. 使用 `img2video` 模式，输入提示词 + 参考素材
  3. 生成 8 秒 4K 视频，包含细胞呼吸动画 + 旁白音频
  4. 导出 EXR 格式，后期在 DaVinci Resolve 中调色

### 案例 2：科研宣传片
- **需求**：为生物学实验室制作 15 秒宣传片，展示基因编辑过程
- **实现**：
  - 使用 `img2video` 模式，输入基因编辑示意图 + 文字描述
  - 设置 `--duration 15`，`--steps 25`，`--cfg 4.0`
  - 生成 4K 15fps 视频，内置实验室背景音效
  - 输出 EXR 文件，后期在 Premiere 中与真实实验 footage 合成

---

## 与 ComfyUI 的整合

LTX-2.5 可通过 ComfyUI 的 **Video Nodes** 直接集成：

```mermaid
graph LR
    A[文本提示] --> B[LTX-2.5 节点]
    B --> C[生成视频]
    C --> D[保存 EXR + 音频]
    D --> E[后期处理]
```

具体节点配置：
- **LTX-2.5 Video Generator**：输入提示词和参数
- **Video Loader**：加载真实素材进行编辑
- **Video Mixer**：混合生成视频与素材
- **Audio Sync**：自动同步音频与视频

---

## 资源与社区

- **官方文档**：https://ltx.io/model/ltx-2-5
- **示例项目**：https://ltx.io/examples
- **社区讨论**：Discord 服务器 https://discord.gg/ltx
- **示例素材**：https://ltx.io/examples
- **性能基准**：https://ltx.io/performance

> **提示**：在 ComfyUI 中使用 LTX-2.5 时，建议搭配 [LTX-2.5 ComfyUI Template](https://github.com/ltx-io/ltx-comfyui-templates) 工作流，可实现一键生成。

---

## 注意事项

- **硬件要求**：建议使用 RTX 3060 以上显卡，4GB 显存可跑 1080p，12GB+ 更适合 4K
- **时间限制**：单次生成最长 10 秒，长视频需分段生成并拼接
- **素材限制**：对真实素材的编辑受制于素材分辨率和质量
- **版本更新**：LTX-2.5 为最新版本，建议定期检查官方更新

---

## 资源链接

- 官方主页：https://ltx.io/model/ltx-2-5
- 示例项目：https://ltx.io/examples
- 官方文档：https://ltx.io/model/ltx-2-5
- 社区交流：https://discord.gg/ltx