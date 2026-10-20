# 自动驾驶感知开源日报 · 2026-10-20

> 覆盖方向：3D Object Detection / BEV Perception / Occupancy & Forecasting / LiDAR-Camera & Radar-Camera Fusion / 4D Radar / Multi-modal / 3D Tracking / Online Mapping / End-to-End / World Models / VLM-VLA / Vision Foundation Models / 数据集与基准
>
> 核实方式：GitHub API + 仓库页面 + commits.atom feed 实时抓取（今日未认证 API 配额耗尽，日期均经 atom feed / 页面交叉核实，重点条目已二次抽查）；HF 权重经 HuggingFace API 核实。Star 数为当日快照约数。**未验证项均已标注，无凭记忆断言。**

---

## 一、今日重点（近 6 周活跃 × 工程价值最高）

### 1. WorldEngine — 世界模型 × RL Post-Training 框架（OpenDriveLab）
- **方向**：World Models / E2E post-training
- **代码**：<https://github.com/OpenDriveLab/WorldEngine>（522★，最后 push 2026-09-15，同日发布 stable checkpoints）
- **论文**：arXiv:2606.19836
- **核心亮点**：长尾安全场景自动挖掘 + 3DGS 闭环仿真（SimEngine）+ Behavior World Model 场景合成 + RL 后训练（AlgEngine）；宣称仿真碰撞率最高 −45.5%、200 km 路测零接管
- **代码完整度**：训练 有｜配置 有（Hydra）｜权重 有（stable ckpt + VADv2 快测 ckpt，托管 HF/ModelScope `OpenDriveLab/WorldEngine`）｜部署 无
- **数据集**：nuPlan 系、NAVSIM（navtest / navtest_failures / navtrain）；planner 覆盖 VADv2、HydraMDP。代码 Apache-2.0，数据 CC-BY-NC-SA
- **推荐理由**：世界模型从"生成视频"走向"驱动训练数据引擎"的代表作，长尾数据增广与闭环评测可直接复用；OpenDriveLab 出品，持续维护有保障

### 2. NVlabs Alpamayo 家族 — 驾驶 VLA 全链路（本月最活跃）
- **方向**：VLM/VLA for AD + 车端部署
- **代码**：
  - [alpamayo-recipes](https://github.com/NVlabs/alpamayo-recipes)（211★，push **2026-10-16**）：SFT（HF Trainer+DeepSpeed）→ 开环 RL（Cosmos-RL+GRPO）→ FP8/NVFP4 量化 → **DRIVE AGX Thor 车端部署** 成套 recipes，另含 ckpt 转换与 Chain-of-Causation 自动标注管线
  - [alpamayo2](https://github.com/NVlabs/alpamayo2)（282★，push 2026-10-07）：34B 多任务驾驶基座（32B VLM + 2B diffusion action expert），推理 only、开箱即用；HF 权重 gated（OpenMDW-1.1）
  - [alpamayo](https://github.com/NVlabs/alpamayo) / alpamayo1.5（2047★ 级，push 2026-09-09）：10B reasoning VLA，HF 下载 44k+；论文 arXiv:2511.00088
- **数据**：PhysicalAI-AV（1700+ 小时、306K clips）+ AlpaSim 闭环挑战赛
- **推荐理由**：驾驶 VLA 领域罕见覆盖"训练→RL 后训练→量化→车端"全链路的开源工程参考，且 10 月仍在密集更新

### 3. ReSMap — 相机+卫星图在线建图（NeurIPS 2026，本周开源）
- **方向**：Online Mapping
- **代码**：<https://github.com/dehsurc/ReSMap>（最后 push 2026-10-14）
- **论文/主页**：<https://dehsurc.github.io/resmap-page/>
- **核心亮点**：卫星图既作先验又作相机全失效时的冗余——六相机全关仍 mAP 32.9；基于 MapTracker 代码库扩展
- **代码完整度**：三阶段训练（6+6+12 ep）有｜configs 有｜权重 有（OneDrive，可复现 mAP 54.9）｜部署 无
- **数据集**：nuScenes（地理不相交新划分）；GPL-3.0 + Google Earth ToS 限制
- **推荐理由**：本周刚开源、方向对口（先验增强的鲁棒在线建图），链路完整可复现

### 4. MAGneT-3D — 单目时序 3D 检测域泛化（TUM Cremers 组，10 月新开源）
- **方向**：Camera 3D Detection / Domain Generalization
- **代码**：<https://github.com/mo-sameh/MAGneT-3D>（created 2026-10-07，push 2026-10-18）
- **论文**：arXiv:2608.14282
- **核心亮点**：基于 StreamPETR，DRAG 域鲁棒锚点生成器替换静态 query + TRIM 时序精化与身份合并，主打跨数据集零样本泛化
- **代码完整度**：训练 有（train.py / dist / slurm）｜configs 有｜权重 有（Google Drive，含复现 Table 2 的 ckpt）｜部署 未验证
- **数据集**：nuScenes / Waymo / Lyft / ONCE，跨域 benchmark 单独开仓（MAGneT-3D-Benchmark）；Apache-2.0
- **推荐理由**：10 月刚开源，四数据集跨域 benchmark + 权重齐全，相机 3D 检测域泛化方向必看

### 5. vad-trt-edge — VAD 端到端模型 Jetson 边缘部署全工程
- **方向**：E2E 部署（ONNX / TensorRT / Jetson）
- **代码**：<https://github.com/hous-lab/vad-trt-edge>（新仓，push 2026-10-18）
- **核心亮点**：MSDeformAttn/Rotate/SelectAndPad 自定义 TensorRT 插件、ONNX 导出、PTQ INT8 + QAT 混合精度；RTX 4090 上 FP32 233ms → FP16 93.5ms；**AGX Orin 实测 VAD-Small 61.3ms vs VAD-Base 726.7ms**
- **代码完整度**：训练 无（部署向）｜配置 有｜权重 有（Bench2DriveZoo ckpt + 自研 VAD-Small）｜部署 有（核心价值）
- **数据集**：Bench2Drive（mini 验证集）
- **推荐理由**：罕见的"BEV 系端到端模型如何真正落到 Orin"完整参考；4 万条 BEV query 在边缘端受内存带宽限制的量化结论对工程实践价值极高

### 6. SAM 3 / SAM 3.1 — Promptable Concept Segmentation（Meta）
- **方向**：Vision Foundation Model / 开放词汇分割跟踪
- **代码**：<https://github.com/facebookresearch/sam3>（~12k★，push 2026-10-14）
- **核心亮点**：一条文本提示穷举分割/跟踪全部同类实例（DETR 式检测器 + SAM2 式 tracker，848M 参数）；3.1（2026-03）引入 Object Multiplex 多目标共享内存，显著提速
- **代码完整度**：训练/微调 有（README_TRAIN）｜configs 有｜权重 有但 **gated（HF 申请）**｜ONNX/TensorRT 无
- **License**：自定义 SAM License（非 OSI 标准，商用受限）
- **推荐理由**：长尾障碍物识别与自动标注管线的核心候选；license 与 gated 权重需先做法务评估

---

## 二、新开源 / 近期首发放码（2026-07 以来）

### SimWAM — World-Action Model（occupancy forecasting 关联）
- <https://github.com/H-EmbodVis/SimWAM>｜~197★｜代码+权重 2026-08-07 发布｜arXiv:2608.07468
- Wan2.2-5B 视频专家 + 轻量 action expert 做 flow-matching 联合训练，未来视频生成仅作训练期监督、**推理时不生成未来帧**；NAVSIM 91.9 PDMS，nuScenes 零样本迁移
- 训练（含 FlowGRPO LoRA RL）有｜configs 有｜HF 权重 有｜部署 无｜NAVSIM v1/v2、Waymo E2E、PhysicalAI-AV

### DrivoR — Driving on Registers（CVPR 2026，valeoai）
- <https://github.com/valeoai/DrivoR>｜~296★｜push 2026-08-27｜arXiv:2601.05083
- DINOv2 带 register tokens 的 ViT 直接从视觉特征回归轨迹，NAVSIM v1 **94.6 PDMS**（人类 GT 94.8）；其 scorer 被 TOAD 用于 test-time 轨迹搜索
- 训练 有（4×A100 命令）｜configs 有｜权重 有（GitHub Releases，含 SimScale scaling 实验 ckpt）｜部署 无｜NAVSIM v1/v2 + nuPlan devkit

### SimScale — 仿真数据规模化 E2E（CVPR 2026 Oral，OpenDriveLab）
- <https://github.com/OpenDriveLab/SimScale>｜335★｜push 2026-09-18
- 大规模真实仿真 + sim-real 协同训练（LTF / DiffusionDrive / GTRS-Dense），NAVSIM v2 EPDMS 评测；8 月追加 AlpaSim E2E Challenge 评测脚本
- 训练/配置/权重（Model Zoo）有｜部署 无｜nuPlan / NAVSIM v2

### RV-SDTM — Range-View 语义注入 LiDAR 检测
- <https://github.com/huhuhu301/Cross-View-Semantic-Injection-and-Sparse-Dual-Path-Token-Mixer-for-3-D-Object-Detection>｜push 2026-09-26
- range-view 语义注入 + 稀疏双路 Token Mixer，针对远距/遮挡弱目标；基于 OpenPCDet
- 训练 有｜三数据集 configs 有（**Waymo / nuScenes / Argoverse2**——AV2 支持的新实现很少见）｜权重 未验证｜部署 未验证

### LaserPerception — LiDAR 部署工具链 + CPU 确定性 3D MOT
- <https://github.com/muhammadmahadazher/laserperception>｜push **2026-10-19**（v0.4.0 活跃中）
- MMDet3D PointPillars 预训练模型 + 确定性体素化 + TensorRT FP16 + ROS 2 Humble 多帧重构；附 CPU 确定性 MOT 与语义评测框架
- 训练 无（封装预训练模型）｜配置 有｜权重 有（包装官方）｜部署 有（TensorRT/ROS 2/CI）｜nuScenes｜Apache-2.0

### RC-BEV（bevformer-efficiency-analysis）— 单 ckpt 多分辨率 BEV
- <https://github.com/Naya-996/bevformer-efficiency-analysis>｜push 2026-10-10
- 物理坐标驱动的连续 BEV query 生成器，一个 checkpoint 在 100–200 五档网格分辨率间切换（100×100: NDS 0.510 / 4.6 FPS）；BEVFormer 代码系，nuScenes 6019 帧完整验证
- 训练 有｜configs 有｜权重 部分｜部署 部分（FP16 + 延迟测试）｜注意：单种子结果、多种子统计未完成

### 其他新仓速览
- **offroad-bevfusion**（push 10-09）：BEVFusion 多任务（3D det / BEV seg / occupancy）+ TensorRT + ROS 2，GOOSE 越野数据
- **GaugeAlign-AICity2026**（push 09-29）：AI City Challenge 2026 Track 1 冠军方案（ECCVW 2026）
- **multi-camera-bev**（08-29）：六相机→BEV 的 PyTorch→ONNX→TensorRT→Triton 教学级全链路（nuScenes mini，含 HF Spaces demo）——部署起手模板
- **Hyper-Diffusion-Planner**（push 07-09，略超窗口）：diffusion planner "监督 + RL 后训练"双链路官方实现，88.6 PDMS 监督 ckpt；NAVSIM/nuPlan；⚠️ 无 License 文件
- **mot3d-nuscenes**（08-27→10-15）：C++17 EKF+CTRV 匈牙利关联 3D MOT，CI 规范；尚无评测数字，早期跟踪
- **VGGT-Det**（push 09-22，~163★）：VGGT 几何先验做多视角检测——**室内**（ScanNet/ARKitScenes），训练+权重+数据齐备，跨领域参考

---

## 三、值得持续关注

### 数据集 / 基准
- **Bench2Drive v0.0.4**（~2.0k★，2026-08-06）：44 场景 × 25 路线严格均匀训练分布 + **新增 3D occupancy 模态** + 220 条独立验证路线；年内另有 Robust（05-21，相机失效/延迟扰动）与 Speed（03-27）子基准；HF `rethinklab/Bench2Drive` 开放下载；⚠️ CC-BY-NC-ND
- **UniOcc**（ICCV 2025，2025-09 后未更新）：统一 occupancy prediction / forecasting / flow 三任务评测，多数据集标签统一，MIT——forecasting 评测首选基础设施
- **Cam4DOcc / OpenScene**：camera-only 4D occ forecasting 标准基线（含权重）/ nuPlan 120+ 小时最大 occupancy 基准；活跃度低但是数据基座
- **K-Radar**（push 2026-06-26）：4D radar 数据集基座，仍维护

### 4D Radar / 多模态融合集群（2026 上半年集中开源，Q3 静默）
- **R4Det**（CVPR 2026，push 2026-05-30）：全景深度融合 + 免位姿时序对齐的 radar-camera BEV 检测；训练/configs/HF 权重齐；TJ4DRadSet + VoD；⚠️ 学术限定 License
- **MSSF**（T-ITS 2025）：VoD EAA mAP 63.31；训练 + HF 权重 + 扩展数据全家桶；⚠️ 无 License 文件
- **L4DR**（AAAI 2025 Oral）：LiDAR + 4D radar 全天候检测，OpenPCDet，HF 权重，Apache-2.0
- **TEOcc**（VDIGPKU）：radar × occupancy 交叉方向少数有完整代码+权重的工作
- **RAF**（ECCV 2026）：cam+LiDAR+4D radar 三模态恶劣天气融合，K-Radar 上最高 +6.5 AP_BEV / +7.4 AP_3D；训练细节未验证，7 月后未更新
- **Sparse4D-Radar**（push 2026-03-22）：Sparse4D 架构 × camera-4D radar，观察名单

### Online Mapping 基线（已验证不活跃，作基座引用）
- **MapTracker**（ECCV 2024 Oral，push 2025-10-07）：tracking 式时序一致矢量地图，ReSMap 的直接基座；nuScenes + Argoverse2
- **MapTR / MapTRv2**（push 2025-03-03）：引用最广的矢量化建图基线，MIT（商用最友好）
- FlexMap / SatMap 等 2026 论文暂未放出官方代码

### Vision Foundation Models
- **Depth Anything 3**（~6.5k★，push 2026-07-27）：depth-ray 统一表示，多视输入恢复空间一致几何（深度+位姿+3DGS），环视多目直接适用；推理/configs/权重 有（BASE/SMALL Apache-2.0），**官方训练代码无**；社区已有 ROS2 C++ TensorRT 节点
- **DINOv3**（push 2026-07-15，略早于窗口）：下期观察

### 检测
- **MonoCoP**（CVPR 2026 Highlight，push 2026-08-03）：Chain-of-Prediction 建模深度/尺寸/朝向属性相关性；代码 + HF 权重 + 训练日志齐；KITTI configs 完整，nuScenes/Waymo 待补；MIT
- **InfoCom**（AAAI 2026）：信息瓶颈协同感知（KB 级通信）

### 清单与信源（持续更新中）
- **LMD0311/Awesome-World-Model**（2283★，10-16 更新）：当前最活跃的世界模型索引；近期新增 OccStress（NeurIPS'26，4D occupancy 压力测试）、RAYNOVA、WorldLens（CVPR'26 Oral）等
- **worldbench/awesome-vla-for-ad**（10-17 更新）：驾驶 VLA 模型/权重/数据追踪
- szenergy/awesome-lidar（09-30）、autonomousvision/transfuser（09-21）、autoware_universe（**10-20 当天仍在 push**）
- gotonote/Autopilot-Notes（840★，10-19）：中文 BEV/Transformer 模块笔记
- HF 观察：commaai/openpilot_driving_models（ONNX 权重，10-17 更新）

### 已验证休眠的常用框架（选型时注意）
- **OpenPCDet**：最后 push 2025-10-08，近一年未动（但仍是 RV-SDTM / L4DR 等新项目的基座）
- **MMDetection3D**：最后 push 2024-01-08，休眠近三年
- **Vista**（OpenDriveLab 世界模型）：最后 commit 2025-07-02；navsim devkit：2025-10-27（作为评测环境安装即可）
- **Papers with Code 已下线**：302 → HuggingFace papers/trending；系统性检索改用 arXiv + GitHub topic/search

---

## 四、本期趋势信号

1. **大框架休眠，活跃主体转向论文官方仓 + 部署工程仓**：OpenPCDet / MMDetection3D 停更，但 CVPR/ECCV 2026 官方仓（MonoCoP、DrivoR、R4Det、VGGT-Det）与部署仓（vad-trt-edge、laserperception、offroad-bevfusion、multi-camera-bev）训练-部署链路完整——日报应区分这两类。
2. **Occupancy 纯预测方向热度向世界模型迁移**：GitHub topic:occupancy 里新 AD repo 稀少，交叉条目（OccStress、SimWAM、RadarGSOcc）集中出现在 Awesome-World-Model；occupancy forecasting 的"方法-数据-评测"链路（Drive-OccWorld/IR-WM + Cam4DOcc/OpenScene + UniOcc）已成型。
3. **VLA 与世界模型成为 OpenDriveLab / NVIDIA 主战场**：WorldEngine + SimScale 构成仿真数据栈，Alpamayo 家族构成模型+部署栈；NVIDIA 以 PhysicalAI-AV 数据 + AlpaSim 挑战赛绑定生态。
4. **端到端开环竞争聚焦 NAVSIM**：v1 PDMS 已逼近人类上限（DrivoR 94.6 vs 94.8），竞争转向 v2 EPDMS 与 navhard。
5. **3D MOT 是本期明显低谷**：近 3 个月无高质量新开源，仅 C++ 工程化尝试（mot3d-nuscenes）。
6. **ICCV 2026（10 月中旬闭幕）代码潮将至**：ViRA（NAVSIM v2 92.3 EPDMS，OpenDriveLab，代码 TODO）、RaGS（CVPR'26 radar GS）等"论文先行"项目值得蹲守，预计 11-12 月放码。

---

*生成：ZCode · 2026-10-20 · 五方向并行检索（3D 检测/融合/跟踪、BEV/Occupancy/Radar、E2E/世界模型/VLM、建图/VFM/数据集、组织与平台扫描），重点条目经 commits.atom 二次抽查*
