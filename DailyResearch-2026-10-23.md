# 感知开源雷达 2026-10-23

检索范围：GitHub（search API / trending / commits.atom 核实）、arXiv 近 7 日 submission 及今日 announce、Hugging Face（模型/org 动态）、OpenDriveLab、awesome 列表（Papers with Code 已下线，不查）。已对全部 watchlist 项目逐项复查。

## 今日重点

### 1. ViRA —— watchlist 释码（已跟踪 2 天，今日开源）

- **方向**：End-to-End AD / VFM 表示对齐（DINOv3 alignment）
- **代码**：<https://github.com/OpenDriveLab/ViRA>
- **论文**：<https://arxiv.org/abs/2610.09695>（SEU + OpenDriveLab@HKU + SLAI）
- **释码内容**：10-22 放出 ViRA-Diffusion **推理代码 + 完整权重**（HF `OpenDriveLab-org/ViRA`：`ViRA_Diffusion.ckpt` 820MB + `Resnet34.bin` 87MB），含 navtest 评测脚本、`environment.yml`、轨迹 anchor（`kmeans_navsim_traj_20.npy` 已内置 repo）。释码后连发 4 个修复 commit（nuplan-devkit 1.2.0→2.0.0、HF 物料路径等），README News 明确标注 `2026/10/22 We released the code and checkpoints of ViRA-Diffusion`。
- **核心亮点**：planner-agnostic 的视觉基础模型表示对齐——不改 planner 架构、零推理开销增量，DINOv3 对齐在 NAVSIM v2 navtest 达 **92.3 EPDMS**（无辅助感知监督）；论文同时给出"VFM target 选择影响规划收益、辅助感知监督收窄不同 target 间差距"的实证结论。
- **代码完整度**：训练 **无**（TODO "All Code release" 未勾）| 配置 **有**（env + eval scripts）| 权重 **有**（HF 双 ckpt）| 部署 **无**
- **数据集/框架**：NAVSIM（OpenScene + nuplan-maps v1.0，navtest/navhard metric cache），基于 navsim devkit；HUGSIM zero-shot 结果已列。无 ONNX/TensorRT。
- **推荐理由**：OpenDriveLab 官方、Apache-2.0，NAVSIM v2 头部方案的**可复现推理链路**一次给全（数据组织说明 + 一键 eval 脚本 + 权重），做 NAVSIM 复现/表示对齐研究的首选起点；训练代码仍值得继续蹲守。

### 2. 简讯：torch-pointcloud 补传 nuScenes CenterPoint 权重（非首放）

HuggingFace org [`torch-pointcloud`](https://huggingface.co/torch-pointcloud)（"PyTorch PointCloud"，2026-08-28 建立，153 个模型）10-22 补传了 `centerpoint-voxel01/voxel0075/pillar02` 三个 **nuScenes mmdet3d 权重**。该 org 是点云/LiDAR 模型权重集中化项目（PointNet++/DGCNN/3DETR/Concerto 等室内外模型），org 本身 8 月已建，**不属于 7 天首放**，仅提示：CenterPoint nuScenes 预训练权重现在 HF 上有统一托管，做基线复现方便不少。

## 新开源（近 7 天首次放码）

**今日无合格新开源项目。** 已核查：GitHub `created:>2026-10-16` 覆盖 3D 检测 / BEV / 融合 / E2E / 世界模型 / 4D 雷达 / 在线建图 / VLM / nuScenes 多路搜索 + trending 周榜（当前被 LLM 工具类霸榜）、arXiv 今日 announce 全量关键词过滤、HF 新模型、OpenDriveLab org、Awesome-World-Model（10-22 有更新，仅论文条目无新释码）。候选中 `StreamPETR-DualMotion`（10-19 建）确认为**空壳 repo**（仅一行 README 无代码），`BEVPerception`（10-19 建）为个人汇总型项目无方法代码，均不收。L2RDaS（kaist-avelab，LiDAR→4D 雷达张量合成，CVPRF 2026）确认为 9-29 首放，已出 7 天窗口，不报。

## 持续关注（watchlist）

| 项目 | 方向 | 状态 | 已跟踪 | 备注 |
|---|---|---|---|---|
| AD-LiST-JEPA | LiDAR Occupancy 世界模型 (JEPA) | 等大更新 | 2 天 | 作者承诺 **10-26 前**释完整代码（已推迟过一次），释码窗口仅剩 3 天 |
| SimVTA | World-Action Model 后训练 | 等代码 | 2 天 | 10-22 有 README/修复类提交但 TODO "Code release" 未勾，权重也未放 |
| UNIVERSE | 统一视频-动作 DiT (小米) | 占位中 | 2 天 | 最后提交仍为 10-15，训练/推理代码等小米审批 |
| OccWAM | Occupancy World-Action Model | 等代码 | 2 天 | 暂无 repo |
| InfraWorld | 路侧 4D Occupancy Forecasting | 等代码 | 2 天 | 暂无 repo（同名 GitHub 结果均为无关 web 项目） |
| DriveWorld3D | 3D 世界模型 + 生成式仿真 | 等代码 | 2 天 | 暂无 repo |
| UBD-Bench | 多模态感知鲁棒性基准 | 等代码 | 2 天 | 暂无 repo |
| Lane-Structured Conditioning Fields | Online HD Mapping | 等代码 | 2 天 | 暂无 repo |
| PanoBench | 全景占用 / 4D 占用预测基准 | 等大更新 | 1 天 | 最后提交 10-21，数据集已放、训练/评测代码未放 |
| **Expand-and-Prune** | E2E 测试时候选空间自适应 (WACV) | 占位中 ⚠️ | 1 天 | **repo 昨日起 404**（`iis-essingen/Expand-and-Prune` 消失），WebSearch 未找到新家；承诺 10 月底放码，观察是否迁移至 valadalab |
| FALCON | FPGA 延迟可认证 Occupancy 部署 | 等代码 | 1 天 | 摘要称已释部署配方+HLS 源码但无链接，GitHub 仍未搜到 |
| Think Densely, Act Sparsely | VLA 认知链 | 等代码 | 1 天 | 暂无 repo |
| **FM-Occ** 🆕 | 3D Occupancy Prediction | 等代码 | 今日新增 | 今日 v1（arXiv 2610.29388）：稀疏子体素 Shuffle 上采样 + 记忆感知解码，解决稀疏转置卷积体素响应同质化；无代码无链接 |
| **RAHF-Net** 🆕 | LiDAR-Camera Fusion（LiDAR 辅助 RGB 分割） | 等代码 | 今日新增 | 今日 v2 announce（arXiv 2610.25400，v1 约 10-21 投稿昨日未捕获）：柱面→透视 C2P 投影 + 距离感知异构融合；无代码 |

Dormant（不列入日报正文）：RaGS（4D 雷达+单目 GS 检测）repo 自 2025-10-21 建立后仍零提交，无复活迹象。

## Watchlist 变更记录

- **移入 reported**：ViRA（释码，见今日重点）
- **新增 items**：FM-Occ、RAHF-Net（均 paper-only）
- **异常记录**：Expand-and-Prune repo 404（10-23 发现），保留观察是否迁移
- 其余项复查均无释码/无新提交；无超 30 天需转 dormant 项；reported 内最早已跟踪 10-20，未超 60 天不清理
