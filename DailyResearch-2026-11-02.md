# 感知开源雷达 2026-11-02

> 检索范围：GitHub (search API + commits.atom 核实)、arXiv（10-30 与 11-02 两次公告，10-31/11-01 周末无公告）、Hugging Face、OpenDriveLab/NVlabs 组织动态。**补报说明**：上期日报为 10-29，10-30/31、11-01 三天缺报，本期窗口 10-26~11-02 已覆盖缺报日。今日有两项 watchlist 释码（LECDrive 跟踪 11 天、OpTTA 跟踪 5 天），4D 雷达方向出现本窗口内最完整的新释码；另修正一个跟踪乌龙：Expand-and-Prune 此前连续 7 天 404 竟是 watchlist 里拼写笔误（少个 l），正主 repo 一直都在，但仍为 README 占位、10 月底放码承诺已跳票。

## 今日重点

### 1. LECDrive — NeurIPS 2026 VLA 认知链官方代码（watchlist 释码，跟踪 11 天）

- **方向**: VLM for AD / VLA 认知链
- **代码**: https://github.com/WJ-CV/LECDrive （17 commits，最近提交 10-20；此前连续多日检索遗漏，今日复查捕获）
- **论文**: https://arxiv.org/abs/2610.25734 （Think Densely, Act Sparsely: Latent Expert Cognitive Chains for VLA Models, NeurIPS 2026）
- **核心亮点**:
  - 天津大学（一作 Jie Wang）+ 山西大学 + 小米汽车：Latent Expert Cognitive Chains——密集视觉/语义/几何监督对应"Think Densely"，稀疏动作解码对应"Act Sparsely"。
  - 仓库 vendored 三个基础模型专家（SAM3 / VGGT / Depth Anything 3）+ motion token 聚类管线，`run_scripts/` 含 dist_sft_navsim.sh 等训练/推理入口。
  - 三套评测齐备：NAVSIM v1/v2（轨迹规划）、NuInstruct（风险物体感知与状态预测）、DriveLM（感知-预测-规划问答）。
- **代码完整度**: 训练 有（run_scripts 训练脚本）｜配置 有｜权重 有（HF `wang-jie825/LECDrive`：ckpt + 数据集 + DINOv3/SAM3/DA3 预训练权重）｜部署 无。注意：依赖版本与安装说明 README 标 "coming soon"。
- **数据集/框架**: NAVSIM v1/v2、NuInstruct、DriveLM（nuScenes 系）
- **ONNX/TensorRT/部署**: 无
- **推荐理由**: watchlist 释码（10-22 入表，跟踪 11 天）。认知链式 VLA 的训练范式 + "vendored 专家模型 + motion token 聚类" 的数据构造管线对做 VLA/多模态驾驶模型的同学是难得的完整参考；小米汽车参与，工程味浓。

### 2. 4D-MambaNet — 4D 毫米波雷达 Mamba 3D 检测，VoD radar-only SOTA + 全部种子权重（新开源，10-31 建仓 / 11-01 v1.0）

- **方向**: 4D Radar 3D Object Detection
- **代码**: https://github.com/sheraz007/4D-MambaNet （创建 10-31，最近提交 11-01，Release v1.0 于 11-01）
- **论文**: 无 arXiv；README 自称 "submitted implementation"，基于期刊论文 Radar M3-Net（Expert Systems with Applications, DOI 10.1016/j.eswa.2025.127515）扩展
- **核心亮点**:
  - MambaVoxelBlock 用选择性 SSM 把每帧全部非空 voxel 作为单一序列聚合（线性复杂度替代局部 patch attention），RCS 作为点特征输入（自报 +1.5 mAP）。
  - VoD radar-only：56.08±0.19 mAP (R11, 4 seeds)，自复现基线 Radar M3-Net 54.65；TJ4DRadSet mAP_3D 34.60±0.57；RTX 3090 端到端 10.36 FPS（比基线快 1.4×）。
  - 复现工程做得扎实：4 种子脚本、Release 分发 12 个 checkpoint（4 方法种子 × 2 数据集 + 基线）附 SHA256、配对 t 检验统计脚本；README 诚实标注 R11 指标对 batch size 敏感。
- **代码完整度**: 训练 有（train_4seeds.sh）｜配置 有（VoD/TJ4DRadSet 全套 yaml）｜权重 有（GitHub Release 12 ckpt + SHA256）｜部署 无（无 ONNX/TRT）
- **数据集/框架**: View-of-Delft (VoD)、TJ4DRadSet；OpenPCDet（spconv 2.3.8 + mamba-ssm 2.3.1 + torch 2.5.1），Apache-2.0
- **ONNX/TensorRT/部署**: 无
- **推荐理由**: 4D 雷达方向近一个月释码极稀薄，这是本窗口内最完整的新开源（训练+配置+全种子权重+统计检验）。注意三点：无同行评审论文、指标为 README 自报、0 star 无社区验证——当"高质量可复现基线"用，结论自行验证。

### 3. OpTTA — 测试时世界模型自适应官方代码（watchlist 释码，跟踪 5 天）

- **方向**: World Models / Test-Time Adaptation
- **代码**: https://github.com/akshaymete/optta （10-27 单次初始释码，MIT）
- **论文**: https://arxiv.org/abs/2610.35636 （Optimistic Test-Time World Model Adaptation）
- **核心亮点**:
  - 核心主张：世界模型的测试时适配目标应是**规划收益**而非预测精度（optimistic adaptation），在三个世界模型家族上系统验证。
  - 三套实现齐放：LeWM（PushT/Reacher/TwoRoom）、temporal straightening（PushT/PushObj/PointMaze，CEM + 梯度规划器）、DINO-WM（PushT），每个子目录独立 README 覆盖 setup/data/run。
- **代码完整度**: 训练 有（各任务 setup/run 说明）｜配置 有｜权重 无（依赖第三方 ckpt：AdaJEPA / DINO-WM / DINOv2 / LeWM release）｜部署 无
- **数据集/框架**: 机器人操作域（PushT 等）；方法论直接可迁移到驾驶世界模型评测
- **ONNX/TensorRT/部署**: 无
- **推荐理由**: watchlist 释码（10-28 入表，跟踪 5 天）。"为规划而适配"的评测视角对驾驶世界模型落地（仿真/数据闭环里世界模型何时可信、适配到什么程度够用）有直接启发，是本周方法论向最有意思的释码。

### 4. carla-e2e-harness — 确定性 lockstep 的 CARLA E2E 闭环评测工具链（新开源，10-31 首码）

- **方向**: End-to-End AD 闭环评测
- **代码**: https://github.com/mkhaled87/carla-e2e-harness （创建/最近提交 10-31）
- **论文**: 无（工程工具链）
- **核心亮点**:
  - 纯客户端 RPC 实现，零 UE/C++ 依赖，兼容 CARLA 0.9.14~0.10（UE4 与 UE5 双代）；确定性 lockstep：物理时钟在模型前向期间暂停、按 20Hz 固定步长推进，评测结果与 GPU 推理速度无关、完全可复现。
  - 已适配四个模型家族：AutoE2E（Autoware Foundation, latent BEV）、QwenDrive 1.0（阿里 4B VLA）、Alpamayo 2 Super（NVIDIA VLA）、VAD（OpenDriveLab），另带 OpenDRIVE 拓扑合成后端兜底（无需下载权重）。
  - PyTorch / ONNX Runtime 双执行后端；双向坐标系转换 + Pure Pursuit/Stanley + 抗饱和 PID；8 个测试文件 + dry-run 离线验证模式；支持远程分布式部署。
- **代码完整度**: 训练 不适用｜配置 有｜权重 引用外部模型｜部署 有（远程分布式 + dry-run）
- **数据集/框架**: CARLA 0.9.14/15/16/0.10+ 闭环
- **ONNX/TensorRT/部署**: ONNX Runtime 执行后端原生支持
- **推荐理由**: E2E 模型横评最缺的就是"确定性、可复现"的闭环基建——把 Alpamayo2/VAD/QwenDrive 塞进同一确定性时钟里对比，正是多家在拼的评测缺口。注意成熟度：3 commits、0 star、LICENSE 位置待确认，当早期基建跟踪。

## 新开源（近 7 天首码且未报道过）

### PriorMaskMap — ECCV 2026 在线矢量化建图，先验地图可信度感知（首码 10-28）

- **方向**: Online HD Mapping
- **代码**: https://github.com/healenrens/PriorMaskMap （最近提交 10-28，4 commits，MIT）
- **论文**: https://arxiv.org/abs/2610.39283 （ECCV 2026）
- **核心亮点**: PACE（先验感知置信估计器）判断先验地图是否过时/临时改道（biased prials），先验有偏时在线矢量化建图不掉点；基于 HRMapNet，先验生成沿用作者 Syn-D-maps (IV 2025)。
- **代码完整度**: 训练 有（tools/dist_train.sh、test.py）｜配置 有（1 份 priormaskmap_r50_24e）｜权重 无（README 明言不含）｜部署 无
- **数据集/框架**: nuScenes；mmdet3d / HRMapNet 基座
- **ONNX/TensorRT/部署**: 无
- **推荐理由**: "地图先验过时怎么办"是车端在线建图绕不开的工程问题，切入角度实用。最小化释码（无指标无权重），当方法论参考。

### HF 简讯（三条）

- **SCU-VIP-Lab/Pretrained_BEVFusion**（10-26 上架）：BEVFusion（相机+LiDAR 多模态 BEV 融合）预训练权重直接上架 HF，**无 model card、无 license、裸 checkpoint**——用前自行验证输入约定与评测协议。https://huggingface.co/SCU-VIP-Lab/Pretrained_BEVFusion
- **torch-pointcloud 10-30 新批次**（组织首批 10-28 已报，本批为跟进）：约 40 个新权重——nuScenes 的 PointPillars(+FPN)/TransFusion-Lidar/VoT-S/LitePT-S/L/Cylinder3D-0.5x/OpenScene-OpenSeg，KITTI 的 SECOND/PointRCNN-IoU/Voxel-R-CNN，SemanticKITTI 的 MinkUNet 全系列（spconv/torchsparse × w32 × AMP 组合）。https://huggingface.co/torch-pointcloud
- **Sta8is nuScenes 深度伪标签 + 法向量标签**（10-26 发布）：CAM_FRONT 深度伪标签（Depth Anything V2 ViT-L，4164 帧/142MB）+ 表面法向量标签（Lotus，~4014 帧/~65GB .npy），DINO-Foresight (NeurIPS 2025) 未来预测评测配套，CC BY-NC-SA 4.0；做 BEV/深度/未来预测评测的现成监督源。https://huggingface.co/datasets/Sta8is/Nuscenes_depthv2 与 https://huggingface.co/datasets/Sta8is/Nuscenes_normals

## 持续关注（watchlist 40 项；dormant 1 项不列）

| 项目 | 方向 | 状态 | 已跟踪 |
|---|---|---|---|
| UNIVERSE | 统一视频-动作 DiT (World-Action, Xiaomi) | 占位中（最后提交仍 10-15） | 12 天 |
| OccWAM | Occupancy World-Action Model | 等代码 | 12 天 |
| InfraWorld | 路侧 4D Occupancy Forecasting | 等代码 | 12 天 |
| DriveWorld3D | 3D 世界模型 + 生成式仿真 | 等代码 | 12 天 |
| UBD-Bench | 多模态感知鲁棒性基准 | 等代码 | 12 天 |
| Lane-Structured Conditioning Fields (LaneField) | Online HD Mapping | 等代码（今日找到一作项目页 repo andberto/LaneField，仅网站源码无算法代码，最后提交 10-16） | 12 天 |
| PanoBench | Panoptic/4D Occupancy 基准 | 等大更新（仅 configs，最后提交仍 10-21） | 11 天 |
| Expand-and-Prune | E2E 测试时候选空间自适应 (WACV'27) | 占位中（**今日修正**：此前 7 天 404 系笔误，正主 repo 为 iis-esslingen/Expand-and-Prune，仅 README 零代码，最后提交 10-19；10 月底放码承诺已跳票） | 11 天 |
| FALCON | FPGA 延迟可认证 Occupancy 网络 | 等代码 | 11 天 |
| FM-Occ | 3D Occupancy (上采样+解码改进) | 等代码 | 10 天 |
| Foundation4D | 统一感知+重建基础模型 | 等代码（**数据集已上** NVIDIA HF `PhysicalAI-Autonomous-Vehicles-Foundation4D`，10-15 建仓窗口外未单列，下载 2.4k；代码 repo 仍无） | 9 天 |
| CoGaussian | 开放词汇高斯 V2X 协同感知 | 等代码 | 9 天 |
| DriveMLP | E2E（训练策略 vs 复杂度） | 占位中（仍 09-09 零提交） | 9 天 |
| SARD | Diffusion Driving VLA | 占位中（最后提交仍 10-22） | 9 天 |
| ByteCope | V2X 字节约束协同感知 (WACV'27) | 占位中（最后提交仍 10-24） | 8 天 |
| CLAP-AD | E2E 规划（路径模仿+速度打分, WACV'27） | 占位中（最后提交仍 10-20） | 8 天 |
| VEOcc++ | 具身在线语义占用建图 | 等代码（默认分支仍 page，10-26 更新） | 8 天 |
| RoMo-GS | 在线 3DGS 建图（运动模糊解析积分） | 等代码（仍仅项目页） | 8 天 |
| RaSFNet | LiDAR 语义分割（射线对齐） | 等代码 | 7 天 |
| RT-Radar-Panoptic | 4D 雷达语义+全景场景理解 | 等代码 | 7 天 |
| FogTTA | 雾天在线测试时适应 | 等代码 | 7 天 |
| SpikeOcc | 脉冲专家多模态 3D Occupancy | 等代码 | 7 天 |
| RadarGSOcc | 4D 雷达张量 3D 语义 Occupancy (KAIST) | 等代码（摘要承诺公开） | 7 天 |
| DriveOSS | 恶劣天气 3D 开集分割基准 (WACV'27) | 等大更新（代码已放，数据集仍 coming soon，最后提交 10-27） | 6 天 |
| LegoOcc++ | 开放词汇 3D Occupancy | 等代码（repo 最后提交仍 10-16） | 5 天 |
| DriveUniTok | World-Action 统一多模态 tokenizer | 等代码 | 5 天 |
| BEV-Model-Soup | 相机 3D 检测鲁棒性（稀疏恢复+模型汤） | 等代码 | 5 天 |
| CoopAdverseWeather | 恶劣天气 V2X 协同感知 | 等代码 | 5 天 |
| GRIP-Fusion | 路侧相机-LiDAR 3D 检测+数据集 | 占位中（最后提交 10-28） | 4 天 |
| GCSR-OCC | 开放词汇 Occupancy（几何校准+语义路由） | 等代码（摘要承诺公开） | 4 天 |
| FastBEVFusion (2610) | 实时相机-LiDAR BEV 融合 50 FPS | 等代码 | 4 天 |
| **WorkIng**（今日新增） | 高速施工区 360° 多模态基准 (WACV'27, TUM cvims) | 占位中（repo 10-26 仅 README+LICENSE，128 线 LiDAR+6 RGB+RTK、12k 标注帧、22.7 万跟踪框、CARLA 合成域，论文 2610.41772） | 今日入表 |
| **MotionFirst**（今日新增） | 世界模型残差运动规划（高德 amap-cvlab, WACV'27） | 占位中（repo 10-30 建，"open source coming soon"） | 今日入表 |
| **CaRaPlan**（今日新增） | 相机-雷达稀疏 E2E 驾驶 | 占位中（官方仓 10-30 建，仅 README+LICENSE） | 今日入表 |
| **UFMF**（今日新增） | 雷达引导运动学先验实时 4D Occupancy Forecasting | 等代码（论文 2610.40947，Ottawa） | 今日入表 |
| **GATS**（今日新增） | LiDAR 几何测量 token 选择（世界模型效率，随机丢 75% token 反超全量） | 等代码（论文 2610.40935） | 今日入表 |
| **ObjectGuidedMap**（今日新增） | BEV 地图+3D 目标联合多任务（Mask2Map 后续, Fraunhofer） | 等代码（论文 2610.41834） | 今日入表 |
| **CalibSwitch**（今日新增） | Camera-LiDAR 外参免初始化恢复+选择性维护（南开） | 等代码（论文 2610.41352） | 今日入表 |
| **TextRadar**（今日新增） | 全景毫米波 3D 占用-语言场（开放文本查询, CMU） | 等代码（论文 2610.37929） | 今日入表 |
| **KITScenes Aerial**（今日新增） | 空地协同在线建图基准 (WACV'27, FZI/KIT) | 等数据集/代码（论文 2610.40455，项目页 kitscenes.com/aerial 目前 404，数据未上） | 今日入表 |

### 排除备注（窗口外/不符口径，防止后续误收）

- mech-interp-e2e-driving（CoRL 2026, SAE 可解释性, repo 最后提交 10-12 窗口外）；RoSAMDepth（CVPR 2026, repo 03-23 旧仓）；LiDAR-SHIELD（对抗鲁棒性框架, 08-05 旧仓）；InterDigital 语义图压缩（08-20 旧仓）；WorldFlow（机器人域非驾驶）；DrivingBench preview（官方要求暂不引用）。

---

*数据口径：首码/首放时间以 commits.atom、GitHub Release、HF API createdAt 为准；去重依据本地 watchlist reported 列表（滚动 60 天）。*
