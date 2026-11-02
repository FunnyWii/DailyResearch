# 感知开源雷达 · 2026-11-03

> 检索窗口：2026-10-27 ~ 2026-11-03（7 天滚动）｜数据源：GitHub Search / commits.atom / arXiv API / Hugging Face API / OpenDriveLab
> 今日 watchlist 40 项逐项复查：**无释码、无实质更新**；dormant 1 项（RaGS）仍零提交。今日重点均来自新开源首放。
> 备注：torch-pointcloud 组织 10-30 又有一批新权重上架（HF 代理复查确认约 32 个 AD 感知相关），因 11-02 日报已报该批次，按防重口径今日不再报道；OpenDriveLab 本周仅 WorldEngine 数据缓存公告与 ViRA 榜单文档修正，无新代码发布。

---

## 一、今日重点

### 1. UT-ADL / lidar_pillar_detector —— LiDAR pillar 检测器"ONNX 全家桶"式发布
- **方向**：LiDAR 3D Object Detection（部署向模型发布）
- **代码**：https://github.com/UT-ADL/lidar_pillar_detector
- **论文**：无（Tartu 大学自动驾驶实验室模型发布）
- **首放日期**：2026-11-01（11-02 晚仍在更新）
- **核心亮点**：
  - 检测器 `pillar_film_v5` 在 **7 个公开驾驶数据集 + 自采伪标注行驶数据**上混合训练，单一模型跨数据集使用；
  - **全部预处理与解码封装在 ONNX 计算图内**（体素化/pillar 编码/NMS 解码均无 Python 前后处理依赖），fp16/fp32 双权重直接内置 repo；
  - 单文件推理 `detect.py` + 5 帧示例点云；`--style` 参数做**数据集标注风格 embedding 切换**（默认 nuscenes，共 7 种），`--sweeps` 多帧堆叠输出速度；
  - README 给出 CPU 端实测（95,566 点 + 1 历史帧，CPUExecutionProvider 6.1s，41 个检测含类别/3D 框/速度），附演示视频。
- **代码完整度**：训练 **无**（纯推理侧发布）｜配置 **有**（CLI 参数文档化）｜权重 **有**（fp16/fp32 ONNX 内置 repo）｜部署 **有**（onnxruntime 即跑，示例开箱验证）
- **数据集/框架**：7 个公开驾驶数据集混合训练（nuScenes 风格为默认）；依赖仅 numpy/onnxruntime/pypcd4
- **ONNX/TRT**：ONNX 原生（fp16/fp32）；无 TRT 实测
- **推荐理由**：少见的"权重即下即用、图内全预处理"LiDAR 检测发布，是边缘部署链路与跨数据集泛化测试的理想基准模型；同实验室另有 lidar_panoptic_segmentation 姊妹仓共享示例数据。

### 2. MonoDOR —— T-IV 单目 3D 检测官方全量释码（VMamba + 深度/占用双增强）
- **方向**：Monocular 3D Object Detection（深度引导 + Occupancy 门控）
- **代码**：https://github.com/ho-tae/MonoDOR
- **论文**：IEEE Transactions on Intelligent Vehicles（T-IV）已接收，出版社页 forthcoming
- **首码日期**：2026-10-29
- **核心亮点**：
  - VMamba 骨干提取长程上下文特征，**深度感知增强（DAE）**：用预测深度分布沿深度维加权图像特征构建 frustum 特征体；
  - **占用感知增强（OAE）**：frustum occupancy 块预测空间权重调制特征，经标定引导的 grid sampling 投到 voxel 网格后再做 voxel occupancy 精化，最后坍缩 BEV 出 3D 框；
  - 基于 OpenPCDet/CaDDN 体系改造，训练用 LiDAR 派生深度/占用做监督，推理仅吃 RGB + 标定；
  - 文档规范：复现指南（REPRODUCING）、结果表（RESULTS）、验证状态（VALIDATION）独立成文，环境锁定 CUDA 11.8。
- **代码完整度**：训练 **有**（完整训练管线，2,247 文件含 vendored VMamba CUDA kernel）｜配置 **有**（合并模型 1 份配置；历史消融不提供开关）｜权重 **无**（README 明示不附带检测器权重）｜部署 **无**
- **数据集/框架**：KITTI（README 给 Pedestrian/Cyclist AP3D/BEV R40 结果）；PyTorch 2.1 + spconv 2.3.8 + MMCV 2.1 + MMSeg/MMDet
- **ONNX/TRT**：无
- **推荐理由**：T-IV 官方全量代码，DAE/OAE 两个模块（深度分布加权 + frustum/voxel 两级 occupancy 门控）可单独摘出移植到其它单目/BEV 检测器；诚实标注了"论文数值非本快照复测"。

---

## 二、新开源（近 7 天首放代码，此前未报道）

### 3. MoCaFe —— NeurIPS 2026 官方：运动引导的无监督识别域适应
- **方向**：Multi-modal Perception / 无监督模型自适应（识别）
- **代码**：https://github.com/yanan-m/mocafe
- **论文**：https://arxiv.org/abs/2610.15846（NeurIPS 2026）
- **首码日期**：2026-11-02
- **核心亮点**：利用**运动本身作为免费监督**——车辆/机器人移动时对同一目标形成远-近多视角观测序列（远处难、近处易），MoCaFe/p-MoCaFe 借此在无人工标注下提升新环境目标识别精度；提供函数式 API（`mocafe()`/`p_mocafe()` 一行调用）。
- **代码完整度**：训练 **有**（src/mocafe 包 + scripts/run_swin_nuscenes.py）｜配置 **有**｜权重 **无**｜部署 **无**；带单元测试与 CI，包结构规范
- **数据集/框架**：nuScenes（相机 keyframe 按实例 token 裁剪成序列）+ 机器人数据集；PyTorch 2.0+
- **ONNX/TRT**：无
- **推荐理由**："运动即标注"的思路对车队级自监督在线适应（新城市/新传感器部署）有直接启发，NeurIPS 官方实现代码质量高。

### 4. mech-interp-e2e-driving —— CoRL 2026 官方：E2E 规划器的机制可解释性（SAE）【补报】
- **方向**：End-to-End Autonomous Driving 可解释性/安全审计
- **代码**：https://github.com/mgagvani/mech-interp-e2e-driving
- **论文**：https://arxiv.org/abs/2610.41185（CoRL 2026）
- **首码日期**：2026-10-24（repo 10-12 建；此前日报检索遗漏，今日补报，此后不再重复）
- **核心亮点**：对单相机 E2E 规划模型激活训练**稀疏自编码器（SAE）**：无监督恢复路线意图 98.3%；SAE 特征对红绿灯/停止标志呈因果响应，可通过特征编辑**直接操控规划输出**——E2E 黑盒的安全归因与操控实验工具链。
- **代码完整度**：训练 **有**（planner 训练 + SAE 训练 + 全套 analyze 脚本）｜配置 **有**（Slurm 分阶段脚本 + paths.env）｜权重 **有**（论文 SAE checkpoints sae_block_*.pt 内置）｜部署 **无**
- **数据集/框架**：Waymo Open Dataset E2E（TFRecord 直读 + 预建索引）；PyTorch Lightning
- **ONNX/TRT**：无
- **推荐理由**：含权重的 SAE 分析全链路，做 E2E 安全审计、特征归因、规划器行为干预的现成起点；WOD-E2E 读取器亦可复用。

### 新开源简讯
- **ForesightSafety-VLA**（10-29 首码，IROS 2026，⭐87）：RoboTwin 2.0 上的 VLA 安全基准——66 个安全增强场景、13 类安全 taxonomy、结构/语言/视觉三维诊断、过程级指标（累计安全代价 CC/风险暴露时间 RET/安全调整成功率 SASR），1,119 文件全量官方码（中科院自动化所等）。定位：**机器人操作 VLA 非 AD**，但安全评测方法学对 E2E 驾驶评测可迁移。https://github.com/tuotuoLyu/ForesightSafety-VLA
- **jordykieto/carla-driving-demos + carla-imitation-learning**（11-02 数据集 / 11-01 策略，成对发布）：CARLA 0.9.16 仿真数据集——448 段/238,674 帧 @20Hz 同步 RGB（224², FOV 90°）+ 32 线 LiDAR 的 64×64 BEV 直方图 + 遥测/车道中心/控制量，TFRecord 格式（CC-BY-4.0），文档完善；配套 Keras 行为克隆策略含 ONNX 导出。局限：仿真数据、限速 ≤10m/s、RGB 含路线 debug 标记。https://huggingface.co/datasets/jordykieto/carla-driving-demos
- **TaozheLi/BEV-Map-Annotator**（10-28 首放 v4.4）：桌面端 BEV 地图标注工具——连续点云构建地面反射率地图后标注可行驶区域/分隔线/人行横道/路沿/停止线，Tk 实现无浏览器无 GPU 依赖，打包发行（Python 源码在 ZIP 内），适合自采数据快速产 BEV 真值。https://github.com/TaozheLi/BEV-Map-Annotator

---

## 三、持续关注（watchlist，43 项）

> 今日无释码/无大更新；距 last_change 超 30 天移入 dormant 的有：无。★ 为今日新增。

| 项目 | 方向 | 状态 | 已跟踪 |
|---|---|---|---|
| UNIVERSE | 统一视频-动作 DiT (World-Action) | 占位中 | 13 天 |
| OccWAM | Occupancy World-Action Model | 等代码 | 13 天 |
| InfraWorld | 路侧 4D Occupancy Forecasting | 等代码 | 13 天 |
| DriveWorld3D | 3D 世界模型 + 生成式仿真 | 等代码 | 13 天 |
| UBD-Bench | 多模态感知鲁棒性基准 | 等代码 | 13 天 |
| Lane-Structured Conditioning Fields | Online HD Mapping | 等代码 | 13 天 |
| PanoBench | Panoptic Occupancy / 4D Forecasting 基准 | 等大更新 | 12 天 |
| Expand-and-Prune | E2E 测试时候选空间自适应 | 占位中（10 月底放码承诺已跳票） | 12 天 |
| FALCON | FPGA 延迟可认证 Occupancy 网络 | 等代码 | 12 天 |
| FM-Occ | 3D Occupancy Prediction | 等代码 | 11 天 |
| Foundation4D | 统一感知+重建基础模型 | 等代码 | 10 天 |
| CoGaussian | 协同开放词汇高斯感知 (V2X) | 等代码 | 10 天 |
| DriveMLP | E2E（训练策略 vs 模型复杂度） | 占位中 | 10 天 |
| SARD | Diffusion Driving VLA | 占位中 | 10 天 |
| ByteCope | V2X 字节约束协同感知 | 占位中 | 9 天 |
| CLAP-AD | E2E 规划（免模仿速度评分） | 占位中 | 9 天 |
| VEOcc++ | 具身在线语义占用建图 | 等代码 | 9 天 |
| RoMo-GS | 在线 3DGS 建图（运动模糊） | 等代码 | 9 天 |
| RaSFNet | LiDAR 语义分割（射线对齐） | 等代码 | 8 天 |
| RT-Radar-Panoptic | 4D 雷达语义+全景场景理解 | 等代码（ori-drs 组织 11-03 复查无新雷达仓） | 8 天 |
| FogTTA | 雾天在线测试时适应 | 等代码 | 8 天 |
| SpikeOcc | 脉冲专家多模态 3D Occupancy | 等代码 | 8 天 |
| RadarGSOcc | 4D 雷达张量 3D 语义 Occupancy | 等代码 | 8 天 |
| DriveOSS | 恶劣天气 3D 开集分割基准 | 等大更新（数据集仍 coming soon） | 7 天 |
| LegoOcc++ | 开放词汇 3D Occupancy | 等代码 | 6 天 |
| DriveUniTok | World-Action 统一多模态 tokenizer | 等代码 | 6 天 |
| BEV-Model-Soup | 相机 3D 检测鲁棒性 | 等代码 | 6 天 |
| CoopAdverseWeather | 恶劣天气协同感知 | 等代码 | 6 天 |
| GRIP-Fusion | 路侧相机-LiDAR 3D 检测+数据集 | 占位中 | 5 天 |
| GCSR-OCC | 开放词汇 3D Occupancy | 等代码 | 5 天 |
| FastBEVFusion (2610) | 实时相机-LiDAR BEV 融合 (50 FPS) | 等代码 | 5 天 |
| WorkIng | 高速施工区 360° 多模态基准 | 占位中 | 1 天 |
| MotionFirst | 世界模型残差运动规划 | 占位中 | 1 天 |
| CaRaPlan | 相机-雷达稀疏 E2E | 占位中 | 1 天 |
| UFMF | 雷达引导 4D Occupancy Forecasting | 等代码 | 1 天 |
| GATS | LiDAR 几何 token 选择（世界模型效率） | 等代码 | 1 天 |
| ObjectGuidedMap | BEV 地图+3D 目标联合预测 | 等代码 | 1 天 |
| CalibSwitch | Camera-LiDAR 外参在线恢复 | 等代码 | 1 天 |
| TextRadar | 全景毫米波 3D 占用-语言场 | 等代码 | 1 天 |
| KITScenes Aerial | 空地协同在线建图基准 | 等数据集/代码（项目页 11-03 仍 404） | 1 天 |
| ★ TRIM | 3D 检测运行时监控（激活统计+时序） | 等代码 | 今日新增 |
| ★ AgentNorm | V2X 协作方降质容错测试时归一化 | 等代码 | 今日新增 |
| ★ GenSight | 统一视频生成+4D 感知世界模型 | 等代码（项目页已上） | 今日新增 |

新增依据（arXiv 近 7 日扫描，均为论文已出、代码未放）：
- **TRIM**（arXiv:2610.38613）：通道级激活统计摘要 + TCN 在线监测 3D 检测漏检失效，不改检测器不重训，CPU 0.159ms——部署侧运行时监控方向稀缺，工程价值高。
- **AgentNorm**（arXiv:2610.39505）：按 agent 估计 BN 统计的测试时归一化，浓雾/模糊降质的 V2X 协作方不再污染自车特征，免训练免梯度——V2X 鲁棒性实用技巧。
- **GenSight**（arXiv:2610.39232）：单一 DiT 骨干统一未来视频生成与深度/分割/光流稠密 4D 感知，生成与单步感知双模式——World Models 方向；项目页 https://gensight-rbs.github.io/ 已上、代码未放。

---

## 四、窗口内其它动态（一句话）
- OpenDriveLab：WorldEngine（10-27 数据缓存更新公告+README 修复，无新代码）、ViRA（10-27 榜单 README 修正）——均为此前已报项目的文档级更新。
- NVlabs：窗口内无新感知 repo；alpajudge（AD 评测奖励模型，9 月建仓）10-29 有例行推送，非首放不报。
- HF 排除项备忘：SelfDriving-VLA/laq-ad-backup（10-30，LAQ-AD 原始备份仓 1264 文件无 README，正式发布后再报）；pengxiang/navsim-coc-13k（NAVSIM CoT 标注，README 空，质量存疑暂不报）；V2XSim 同名空壳数据集（仅 .gitattributes）。

---
*生成时间：2026-11-03 07:00 CST ｜ 感知开源雷达 · 每日自动检索整理*
