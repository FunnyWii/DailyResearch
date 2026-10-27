# 感知开源雷达 2026-10-28

> 检索窗口：2026-10-21 ~ 2026-10-28（7 天滚动）。核实方式：GitHub commits.atom + README/代码树逐一验证。
> 今日概况：watchlist 双释码（AD-LiST-JEPA 跟踪 7 天兑现、RAHFNet 官方仓补获），2 个高价值新开源（IROS 2026 NavControl、bevdet-metis 边缘部署），5 个新 paper-only 项目入 watchlist。

---

## 一、今日重点

### 1. AD-LiST-JEPA —— LiDAR Occupancy 世界模型官方代码首放 🔴（watchlist 释码，已跟踪 7 天）

- **方向**：LiDAR Occupancy 世界模型（JEPA 自监督）
- **代码**：<https://github.com/HaoranZhuExplorer/AD-LiST-JEPA-Preview>
- **论文**：<https://arxiv.org/abs/2602.12540>（Haoran Zhu, Anna Choromanska，Rutgers）
- **核心亮点**：10-27 凌晨提交（标题即 "preview version code release"）释出完整实现。OpenPCDet 风格工程，共 619 文件 / 281 个 .py / 84 个 yaml：自监督表征学习（SSL）与 occupancy completion & forecasting（OCF）两阶段训练脚本、`occ4cast_reproduced/` 基线复现族（conv2d / conv3d / sparse×7 版本 / conv_lstm）及 apolloscape / argoverse / lyft 数据转换脚本，附 docker 与 4 篇 docs（INSTALL / GETTING_STARTED / DEMO / 自定义数据集教程）。作者原承诺 10-26 释码，迟到一天兑现。
- **代码完整度**：训练 **有** | 配置 **有**（84 yaml）| 权重 **无** | 部署 **无**
- **数据集/框架**：Waymo 为主战场（ocf_train/val/test + ssl_pretrain splits），另带 argo2 / kitti / lyft / once / nuscenes / pandaset 数据集配置；PyTorch + OpenPCDet 风格
- **部署**：无 ONNX/TensorRT
- **推荐理由**：JEPA 路线在 LiDAR occupancy 完成与预测任务上的完整可复现参考实现稀少，本仓自带基线族与多数据集数据管线，是研究"自监督预训练 → 预测式 occupancy"的现成骨架。

### 2. RAHFNet —— LiDAR 辅助 RGB 语义分割官方仓补获 🔴（watchlist 释码，已跟踪 5 天）

- **方向**：LiDAR-Camera Fusion（LiDAR 辅助 RGB 语义分割）
- **代码**：<https://github.com/rdlin118/RAHFNet>
- **论文**：<https://arxiv.org/abs/2610.25400>
- **核心亮点**：官方仓身份确认（arXiv 一作 "Lin, R. D." ↔ GitHub `rdlin118`）。仓 10-20 建成即含完整代码（55 文件）：`tools/train_mm.py / val_mm.py / infer_mm.py` 训练评测推理全链，MSPA / PSA / crossatt 注意力模块与 FFM/FRM 特征融合模块，实现论文的柱面→透视 C2P 投影 + 距离感知异构融合。此前连续 5 日检索遗漏，今日复查捕获。
- **代码完整度**：训练 **有** | 配置 **有**（KITTI-360 / DELIVER）| 权重 **无** | 部署 **无**
- **数据集/框架**：KITTI-360、DELIVER；独立 semseg 框架（无 MMSeg 依赖）
- **部署**：无 ONNX/TensorRT；README 为空文件，上手需直接读代码
- **推荐理由**：LiDAR 仅为训练期监督、推理纯相机的分割设置对低成本量产感知有直接参考价值，C2P 投影与距离感知融合模块可单独复用。

### 3. NavControl / NaviHydra —— E2E 自动驾驶"导航合规鸿沟"官方全链路（新开源，首码 10-27）

- **方向**：End-to-End Autonomous Driving（导航合规）
- **代码**：<https://github.com/hanfeng-wu/NavControl>
- **论文**：<https://arxiv.org/abs/2512.10660>（IROS 2026，项目页 hanfeng-wu.github.io/closing/）
- **核心亮点**：指出当前 E2E planner 普遍存在"导航合规鸿沟"——路口不遵守导航指令。三项产出：**NAVI 指标**（量化导航合规）、**NaviHydra** 轨迹打分 planner（BEV-based trajectory gathering + 对比训练）、**NavControl 数据集**（路口对比训练/评测）。基于 NAVSIM 框架，322 文件：PyTorch Lightning DDP 训练（混合精度、逐 epoch 存 ckpt）、PDM Score 与可控性双评测链路，HF 放出 ~38GB 预计算 navtrain trajectory-simulation 缓存及路口 metric cache，可跳过最贵的仿真步骤。
- **代码完整度**：训练 **有** | 配置 **有**（agent/training/scoring yaml 全套）| 权重 **无**（HF 仅数据缓存）| 部署 **无**
- **数据集/框架**：nuScenes / OpenScene（NAVSIM navtrain / navtest）+ 自建 NavControl
- **部署**：无 ONNX/TensorRT
- **推荐理由**：NAVI 指标直击当下 E2E 规划评测盲区（PDM Score 测不出"听不听导航"），NavControl 数据生成管线 + 预计算缓存让复现成本大幅降低，适合做 planner 评测体系的人跟进。

### 4. bevdet-metis —— FastBEV++ 六相机 3D 检测 INT8 边缘部署（新开源，首码 10-22）

- **方向**：BEV Perception × 边缘部署
- **代码**：<https://github.com/sanket-pixel/bevdet-metis>
- **核心亮点**：把纯视觉 BEV 检测器 FastBEV++ 拆成 **4 个 INT8 CNN stage 部署到 Axelera Metis AIPU**，相机投影、feature lifting、box 解码等几何运算留在 C++ host——因为 Voyager 编译器不支持 `align_corners` resize，这个"AIPU + host 分工"的拆分方案本身就是工程干货。Python 侧 export（PyTorch→ONNX）→ PTQ 校准 → 编译全工具链；README 给出 per-stage 实测延迟表（六相机共享 backbone INT8 30.8ms、host 图像预处理 32.2ms 等）；`make models data build compile` 四步在 nuScenes mini 上完整复现。
- **代码完整度**：训练 **无**（部署向项目）| 配置 **有** | 权重 **有**（官方 ckpt 下载 + ONNX 导出，weights/onnx 带 provenance 记录）| 部署 **有**
- **数据集/框架**：nuScenes（v1.0-mini demo）；C++17 host + AxRuntime 1.7.0
- **部署**：ONNX 导出 **有**、INT8 PTQ **有**、TensorRT **无**（Axelera Metis 专用工具链）
- **推荐理由**：与此前报道的 vad-trt-edge、SparseDrive-TRT 同属"BEV/E2E 上边缘"系列，但本篇把"算子不支持时的 host/device 切分决策"写得很透，做低功耗边缘感知落地的工程师可直接抄作业。

---

## 二、新开源（近 7 天首码，简条）

| 项目 | 方向 | 首码 | 说明 |
| --- | --- | --- | --- |
| [Monocular-BEV-Perception](https://github.com/Cabbage-Charls/Monocular-BEV-Perception) | 单目 BEV（轻量） | 10-27 | YOLOv8 + Depth-Anything-V2 + SegFormer → anchor MLP 尺度回归 → BEV 投影，LiDAR 监督训练、纯相机推理；个人项目、无论文，胜在链路简单可读 |
| [torch-pointcloud HF 权重动物园](https://huggingface.co/torch-pointcloud) | LiDAR 感知即用权重 | 10-22/23 | HF 组织集中上架 CenterPoint（nuScenes，mmdet3d 格式）、PV-RCNN / Part-A2（KITTI）、GeoTransformer（配准）即用权重，每个含下载量；做基线对齐省去训练成本 |

---

## 三、持续关注（watchlist，共 30 项）

| 项目 | 方向 | 状态 | 已跟踪 |
| --- | --- | --- | --- |
| UNIVERSE | 统一视频-动作 DiT (World-Action) | 占位中 | 7 天 |
| OccWAM | Occupancy World-Action Model | 等代码 | 7 天 |
| InfraWorld | 路侧 4D Occupancy Forecasting | 等代码 | 7 天 |
| DriveWorld3D | 3D 世界模型 + 生成式仿真 | 等代码 | 7 天 |
| UBD-Bench | 多模态感知鲁棒性基准 | 等代码 | 7 天 |
| Lane-Structured Conditioning Fields | Online HD Mapping | 等代码 | 7 天 |
| PanoBench | Panoptic/4D Occ Forecasting 基准 | 等大更新 | 6 天 |
| Expand-and-Prune | E2E 测试时候选空间自适应 | 占位中（repo 连续 6 天不可访问） | 6 天 |
| FALCON | FPGA 延迟可认证 Occupancy 网络 | 等代码 | 6 天 |
| Think Densely, Act Sparsely | VLA 认知链 (VLM for AD) | 等代码 | 6 天 |
| FM-Occ | 3D Occupancy Prediction | 等代码 | 5 天 |
| Foundation4D | 统一感知+重建基础模型 | 等代码 | 4 天 |
| CoGaussian | 协同开放词汇高斯感知 (V2X) | 等代码 | 4 天 |
| DriveMLP | E2E 训练策略研究 | 占位中（零提交） | 4 天 |
| SARD | Diffusion Driving VLA | 占位中 | 4 天 |
| ByteCope | V2X 字节约束协同感知 | 占位中 | 3 天 |
| CLAP-AD | E2E 规划（免模仿速度评分） | 占位中 | 3 天 |
| VEOcc++ | 具身在线语义占用建图 | 等代码 | 3 天 |
| RoMo-GS | 在线 3DGS 建图（运动模糊） | 等代码 | 3 天 |
| RaSFNet | LiDAR 语义分割 | 等代码 | 2 天 |
| RT-Radar-Panoptic | 4D 雷达语义+全景理解 | 等代码 | 2 天 |
| FogTTA | 雾天在线测试时适应 | 等代码 | 2 天 |
| SpikeOcc | 脉冲专家多模态 3D Occupancy | 等代码 | 2 天 |
| RadarGSOcc | 4D 雷达张量 3D 语义 Occupancy | 等代码 | 2 天 |
| DriveOSS | 恶劣天气 3D 开集分割基准 | 等大更新（数据集 coming soon） | 1 天 |
| LegoOcc++ 🆕 | 开放词汇 3D Occupancy（一致性） | 等代码 | 今日入 |
| DriveUniTok 🆕 | World-Action Model 统一 tokenizer | 等代码 | 今日入 |
| BEV-Model-Soup 🆕 | 相机 3D 检测鲁棒性（稀疏恢复+模型汤） | 等代码 | 今日入 |
| Adapt-for-Planning 🆕 | 测试时世界模型自适应 | 等代码 | 今日入 |
| CoopAdverseWeather 🆕 | 恶劣天气协同感知（V2X） | 等代码 | 今日入 |

**备注**：
- AD-LiST-JEPA（跟踪 7 天）与 RAHF-Net（跟踪 5 天）今日释码，移入 reported，详见今日重点。
- DriveOSS 10-27 午后有三次提交，均为 README/citation 维护，数据集仍 coming soon。
- Expand-and-Prune 搜索索引仍残留条目，但 atom/代码树均不可访问，按第 6 天不可见记录。
- dormant：RaGS（零提交占位）每日轻量复查，无变化。

---

*数据来源：GitHub Search API（created/pushed 窗口过滤）、arXiv API（近 7 日 submission）、Hugging Face API、OpenDriveLab org、commits.atom 逐项核实。Papers with Code 已下线未使用。*
