# 感知开源雷达 2026-10-27

> 检索范围：GitHub / arXiv（近 7 日，今日索引至 10-23 批次）/ Hugging Face / OpenDriveLab。去重基线：近 60 天 reported 列表（37 项）。
> 今日小结：watchlist 无释码事件；**AD-LiST-JEPA 作者承诺的 10-26 释码 deadline 已过 1 天，仓库仍无新提交**；VEOcc++ 项目页分支 10-26 有更新但代码未放；Expand-and-Prune repo 连续 404 第 5 天。新开源收获：VoD 雷达-相机融合检测（含 LiDAR 训练期特权监督）、WACV 2027 恶劣天气开集分割基准、IROS 2026 闭环 E2E 官方实现。

---

## 一、今日重点

### 1. radar-camera-fusion-vod —— VoD 雷达-相机 3D 检测：LiDAR 只在训练期当"几何老师"（新开源，10-26 首码）

- **方向**：Radar-Camera Fusion × 3D Object Detection
- **代码**：https://github.com/TerryBTF/radar-camera-fusion-vod（10-26 建仓首码，当天完成整理）
- **论文**：无（TU Delft RO47020 *Advanced Machine Perception* 课程项目，附完整实验报告）
- **核心亮点**：
  - 雷达 + 单目相机 3D 检测（CenterPoint pillar 基座，View-of-Delft 数据集），**LiDAR 仅训练期使用**：辅助分支预测 LiDAR BEV 占用/密度目标做几何监督，推理时整个分支移除——"LiDAR 特权学习、推理零成本"在雷达检测上的完整落地。
  - 一系列雷达适配改造：垂直回归降权、Circle-NMS 与高斯热图半径按行人/骑行者放宽（对抗多径鬼影）、多帧雷达 RCS/径向速度 recency 加权、RCS Top-K 柱内选点、3D 径向距离作输入特征。
  - 跨模态融合两级：ResNet-18 layer2/3 特征采样到雷达投影点（point fusion）+ BEV 融合模块；再加 ROI-aware 微调（驾驶走廊内目标 loss 加权）。
  - 实测（val split）：ROI mAP **53.59 → 65.75**，全区域 mAP 31.96 → 42.80；消融表里每一步（雷达 backbone 改造 / BEV 融合 / 雷达增强 / 高分辨率图像特征 / LiDAR 几何监督 / ROI 微调）都有独立数字，还有"尝试了但没用"的负结果表（特征蒸馏、camera prior、自注意力）。
  - 数据分析扎实：VoD 中仅 **~0.2% 雷达点落入 GT 框**、pillar 占用率 ~0.75%——雷达稀疏性的第一手量化。
- **代码完整度**：训练 **部分有**（src 全量 + patches 覆盖基座，但依赖课程 starter code 作底座，该基座不随仓分发）｜配置 **有**（15+ Hydra YAML，逐实验对应）｜权重 **无**（download_weights.py 仅拉 ResNet-18 ImageNet 权重，训练 ckpt 未放）｜部署 **无**
- **数据集/框架**：View-of-Delft（VoD，需官网申请）；PyTorch 2.7 + Lightning 2.6 + Hydra；CenterPoint
- **部署支持**：无 ONNX/TensorRT
- **推荐理由**：4D/车载雷达+相机融合方向少见的"每步消融都给数字"的工程级参考：多帧雷达怎么加权、LiDAR 特权监督怎么加、ROI 加权怎么定，都是可直接搬到自家雷达管线的经验；附中文实验日志（docs/experiment-log-zh.md）。注意：课程项目非论文，复现需向课程方获取 starter code 基座，训练 ckpt 未放。

### 2. DriveOSS —— 恶劣天气 3D 开集语义分割数据集 + 基准（新开源，benchmark 代码 10-21~23 首放；WACV 2027）

- **方向**：3D 开集/异常分割 × Occupancy 前的语义鲁棒性 × 恶劣天气（数据集 + 基准）
- **代码**：https://github.com/chrisyan/DriveOSS（repo 10-17 建，benchmark 代码 10-21~10-23 落地）
- **论文**：https://arxiv.org/abs/2610.32612（WACV 2027 dataset track，round 2 接收）
- **核心亮点**：
  - 真采集多模态数据集：**12,661 同步标注帧、52 个真实驾驶场景**，6 环视相机 + 128 线 LiDAR + GPS/IMU；覆盖**晴/夜/云/雨/雪**五种条件，按 ID（闭集）与 OOD（开集）组织。
  - OOD test 标注不公开（防过拟合）：现阶段邮件提交预测评测，官方用仓内 `evaluate_multi_sequence_openset.py` 统一协议跑分，评测服务器在规划中。
  - 基准代码已放：LCPS 基线（LiDAR-Camera 全景分割 [zhangzw12319/lcps](https://github.com/zhangzw12319/lcps) 改造）+ DriveOSS/KITTI/nuScenes 三套配置 + 多种 anomaly scoring 的开集评测脚本。
  - 论文分析了天气相关的 LiDAR 回波退化（雨天路面反射显著减少等），对恶劣天气感知工程有直接参考价值。
- **代码完整度**：训练 **有**（LCPS benchmark 训练管线）｜配置 **有**（DriveOSS/KITTI/nuScenes 全套 YAML）｜权重 **无**（README 明示 dataset + pretrained models "soon"）｜部署 **无**
- **数据集/框架**：DriveOSS（**coming soon**）+ KITTI + nuScenes（mini/trainval/val 配置齐）；LCPS 基线
- **部署支持**：无
- **推荐理由**：开集/异常分割方向此前多在 nuScenes 上靠人工贴异常物体"仿造"OOD，真车多天气采集的 3D 开集基准稀缺；雨雪条件下 LiDAR 回波分布的真实变化对做鲁棒融合/Occupancy 的人是重要先验。数据集与预训练模型放出是下一个关键节点，已入 watchlist（等大更新）。

---

## 二、新开源（近 7 天首放码，未报道过）

### 3. HyTraj —— IROS 2026 闭环 E2E：统一自回归与非自回归规划（10-26 首码）

- **方向**：End-to-End 自动驾驶（闭环规划）
- **代码**：https://github.com/sii-pilab/HyTraj（10-26 建仓首码，同济 sii-pilab，Guang Chen 组）
- **论文**：IROS 2026（Tao Xu, Guang Chen 等；README 仅给 bibtex，暂未见 arXiv 挂网）
- **核心亮点**：
  - 混合 AR/NAR 轨迹规划：自回归分支多步迭代保精度，非自回归分支单步并行保时延，统一在闭环 E2E 框架内调度。
  - 评测链路完整：CARLA 0.9.10.1 + Leaderboard v1（沿 TCP 路线），Town05 Short/Long 评测脚本齐备；训练用 189K Roach 专家帧（TCP 数据集，HF/GDrive/百度云三镜像）。
  - 训练管线开放：`hytraj/train.py`（4×GPU、batch 32、80 epochs）、模型/数据增强/控制器全模块在仓；value heads 从 Roach ckpt 初始化。
- **代码完整度**：训练 **有**｜配置 **有**（environment.yml + config.py）｜权重 **无**（训练 ckpt 未随仓放出，Roach ckpt 为外链）｜部署 **无**（闭环评测链路有）
- **数据集/框架**：TCP CARLA 数据集；CARLA Leaderboard v1；Python 3.7 + PyTorch 1.10 + pytorch-lightning 1.2.5（链路较老，复现需锁版本）
- **部署支持**：无
- **推荐理由**：CARLA 闭环 E2E 里训练+评测全链路可复现的官方实现，TCP 系生态环境成熟、算力门槛低于大模型系 E2E；AR/NAR 混合的时延-精度权衡思路对规划侧工程有参考。

### 简讯

- **UniAction**（[KBRASK/UniAction](https://github.com/KBRASK/UniAction)，10-26 首码）：统一动作条件化世界模型——把力、相机控制、手部位姿、机器人位姿四类异构动作统一为"离散语义身份 + 连续时空坐标"的稀疏动作元素，注入 Wan2.2-I2V-A14B 的原生位置空间；推理管线 + HF 权重 + 训练数据集均已放（[arXiv 2610.32977](https://arxiv.org/abs/2610.32977)）。方向偏通用视频/具身世界模型而非 AD，但单一联合模型胜过动作专属专家的结论，对 World-Action 模型的条件化设计有直接参考。
- **chirp**（[Aneesh495/chirp](https://github.com/aneesh495/chirp)，搜索显示 10-26 建仓但提交时间戳停在 2022，疑仓库改名/迁移，非全新项目）：MATLAB + C MEX 的 4D FMCW 雷达信号处理引擎——MIMO 虚拟阵列、Range-Doppler、AoA 超分辨、2D CFAR、Doppler 感知 DBSCAN 聚类、IMM-UKF/GNN/JPDA 跟踪、SAR 成像、微多普勒步态分类，15 项单元测试通过。做 4D 雷达底层链路（ADC→点云→航迹）的工程参考。
- **perception-eval-platform**（[KopalliAditya/perception-eval-platform](https://github.com/KopalliAditya/perception-eval-platform)，10-21 首码）：C++17 从零实现的 3D 检测（AP/APH，精确旋转框 IoU）+ MOT（CLEAR-MOT 全家桶）评测平台，Waymo 标签 schema 兼容，带 {类×距离×难度×场景} 切片级回归 triage；当前以自带合成 WOD 形状数据演示，接真数据需自行走转换器。
- **nuscenes-lidar-camera-fusion-eval**（[blakpantherg2127-debug/nuscenes-lidar-camera-fusion-eval](https://github.com/blakpantherg2127-debug/nuscenes-lidar-camera-fusion-eval)，10-21 首码）：nuScenes v1.0-mini 全量 18,538 条标注的 LiDAR 点数-距离-相机可见性统计：**53.5% 标注不足 5 个 LiDAR 点**（23.1% 零点），40m 外中位数 1 点，行人 30-40m 处 92% 稀疏；稀疏目标中 36% 相机高可见（融合有收益）、45% 双侧遮挡。做融合策略或按证据量加权评测时的现成量化依据。

---

## 三、持续关注（watchlist，27 项）

| 项目 | 方向 | 状态 | 已跟踪 |
|---|---|---|---|
| AD-LiST-JEPA | LiDAR Occupancy 世界模型 (JEPA) | 等大更新 | 6 天 ⚠️ 承诺 deadline(10-26)已过 1 天仍未释码 |
| UNIVERSE | 统一视频-动作 DiT (小米) | 占位中 | 6 天 |
| OccWAM | Occupancy World-Action Model | 等代码 | 6 天 |
| InfraWorld | 路侧 4D Occupancy Forecasting | 等代码 | 6 天 |
| DriveWorld3D | 3D 世界模型 + 生成式仿真 | 等代码 | 6 天 |
| UBD-Bench | 多模态感知单模态退化基准 | 等代码 | 6 天 |
| Lane-Structured Conditioning Fields | Online HD Mapping | 等代码 | 6 天 |
| PanoBench | 全景占用+4D Occ Forecasting 基准 | 等大更新 | 5 天 |
| Expand-and-Prune | E2E 测试时候选空间自适应 | 占位中 | 5 天 ⚠️ repo 连续 404 第 5 天 |
| FALCON | FPGA 延迟可认证 Occupancy 网络 | 等代码 | 5 天 |
| Think Densely, Act Sparsely | VLA 认知链 (VLM for AD) | 等代码 | 5 天 |
| FM-Occ | 3D Occupancy Prediction | 等代码 | 4 天 |
| RAHF-Net | LiDAR-Camera Fusion (语义分割) | 等代码 | 4 天 |
| Foundation4D | 统一感知+重建基础模型 | 等代码 | 3 天（出现第三方随机权重 Rust 演示，非释码） |
| CoGaussian | 协同开放词汇高斯感知 (V2X) | 等代码 | 3 天 |
| DriveMLP | E2E（训练策略 vs 复杂度） | 占位中 | 3 天 |
| SARD | Diffusion Driving VLA | 占位中 | 3 天 |
| ByteCope | V2X 字节约束协同感知 | 占位中 | 2 天 |
| CLAP-AD | E2E 规划（免模仿速度评分，闭环） | 占位中 | 2 天 |
| VEOcc++ | 具身在线语义占用建图 | 等代码 | 2 天（项目页分支 10-26 更新，代码未放） |
| RoMo-GS | 在线 3DGS 建图（运动模糊） | 等代码 | 2 天 |
| RaSFNet | LiDAR 语义分割（射线对齐融合） | 等代码 | 1 天 |
| RT-Radar-Panoptic | 4D 雷达语义+全景理解（实时） | 等代码 | 1 天 |
| FogTTA | 雾天在线测试时适应 | 等代码 | 1 天 |
| SpikeOcc | 脉冲专家多模态 3D Occupancy | 等代码 | 1 天 |
| RadarGSOcc | 4D 雷达张量 3D 语义 Occupancy | 等代码 | 1 天 |
| DriveOSS | 恶劣天气 3D 开集分割 数据集+基准 | 等大更新 | 今日新入（等数据集/预训练模型放出） |

---

*生成于 2026-10-27 · 感知开源雷达 · 去重基线：60 天 reported（37 项）*
