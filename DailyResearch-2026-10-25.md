# 📡 感知开源雷达 2026-10-25

> 方向覆盖:3D Object Detection / BEV Perception / Occupancy / LiDAR-Camera & Radar-Camera Fusion / Multi-modal Perception / 3D Tracking / Online Mapping / End-to-End AD / World Models / VLM for AD / 4D Radar / Vision Foundation Models
> 窗口口径:近 7 天 (2026-10-18 起) 新开源;已报道项目不重复。核实方法:GitHub commits.atom + README/文件树 + arXiv API + HF API;今日 GitHub API 配额吃紧,部分检索经 HTML 页/arXiv/HF 绕行完成。

---

## 一、今日重点

### 1. WildDepth-v2 —— watchlist 跟踪 1 天,官方代码仓已释出(此前检索遗漏,今日复查发现)

- **方向**:RGB-LiDAR-Radar 三模态同步数据集与基准(度量深度 / Doppler 运动 / 度量 3D 跟踪)
- **代码**:https://github.com/Morphocam/wilddepth_v2(repo 09-30 建,最后提交 10-12,MIT License,11 文件层级:wd2 库 + scripts + tests + results)
- **论文**:https://arxiv.org/abs/2610.31123(10-22 v1;IJCV 特刊 "Computer Vision Approaches for Animal Tracking and Modeling",WildDepth ECCV 2026 扩展版)
- **机构**:Morphocam(野生动物感知组)
- **核心亮点**:
  - **37 段同步 snippet**:RGB / LiDAR / radar / metric-depth(`depthm/`)/ 精修外参逐段配套,10 Hz 全局帧号对齐,三模态同时非空才入帧;
  - **论文数字全部可复现**:每个 paper claim 对应 `scripts/` 里一个脚本(E1-E11),`results/RESULTS.md` 做 claim→文件映射;含 Doppler→LiDAR 运动掩码迁移(E4)、Doppler-gated 点云累积(E5)、按深度源分解的度量 3D 跟踪(E6,IoU 跟踪 + CV Kalman + RTS 平滑);
  - **公平协议设计值得抄**:E3 度量尺度评估把 mask 内 LiDAR 点对半分(anchor/评测)避免自证;radar 外参高程误差 1-2 m 故只做 object 级关联——数据缺陷与补偿策略全部写进 README;
  - `run_all.sh` 单张 RTX 级 GPU ~1.5h + CPU ~1h 全量跑通,含 pytest 单测。
- **代码完整度**:训练 无(评测基准定位)| 配置 **有**(`configs/calib/` radar→LiDAR 外参、RealSense 内参)| 权重 **部分**(MegaDetector V6 + SAM2.1 环境变量接入,MoGe-2 与 Track-A 基线从 HF 自动拉)| 部署 无;测试 **有**
- **数据集/框架**:自有 WildDepth-v2 数据集(README 未给出公开下载 URL,需按 `WD2_RADAR_ROOT` 目录约定自备,获取入口待确认);PyTorch;依赖 ultralytics / SAM2.1 / MoGe-2
- **部署支持**:无 ONNX/TensorRT
- **推荐理由**:watchlist 加入 1 天即"等到了"——官方代码完整度在数据集论文里属第一梯队(全实验脚本+单测+claim 映射)。域虽是野生动物而非 AD,但 **radar 度量深度/运动评估的公平协议 + Doppler 关联的工程取舍**对 radar-camera fusion 与 4D radar 工程实践直接可迁移;此前两日将其误记为"无 repo",今日经 GitHub 检索纠正,已如实记录。

---

## 二、新开源(近 7 天首放代码,未报道过)

**今日无符合口径的新首发代码项目。**

检索说明:GitHub `created:>2026-10-18` 覆盖 occupancy / 3D 检测 / BEV / fusion / 4D radar / online mapping / E2E / VLA / world model / tracking / nuScenes / Waymo 各方向 + OpenDriveLab、NVlabs、valeoai 三组织页 + HF 新模型 + arXiv 近 7 日,新建 repo 经逐个核验多为练习/作业型项目或纯项目页;NVlabs `physical_ai_av` 10-23 为 v0.2.3 存量小版本更新(devkit 自 2026-02 起持续发版,数据集 2025-05 上线),不属首放,不收。

---

## 三、持续关注(watchlist)

| 项目 | 方向 | 状态 | 已跟踪 |
|---|---|---|---|
| AD-LiST-JEPA | LiDAR Occupancy 世界模型 (JEPA) | 等大更新(**明日 10-26 为作者承诺释码 deadline**,已推迟过一次) | 4 天 |
| UNIVERSE (xiaomi-mlab) | 统一视频-动作 DiT (NeurIPS 2026) | 占位中(最后提交 10-15,代码等小米审批) | 4 天 |
| OccWAM | Occupancy World-Action Model | 等代码(无 repo) | 4 天 |
| InfraWorld | 路侧 4D Occupancy Forecasting | 等代码(无 repo) | 4 天 |
| DriveWorld3D | 3D 世界模型+生成式仿真 | 等代码(无 repo) | 4 天 |
| UBD-Bench | 多模态感知单分支退化基准 | 等代码(无 repo) | 4 天 |
| Lane-Structured Conditioning Fields | Online HD Mapping | 等代码(无 repo) | 4 天 |
| PanoBench | 全景占用+4D Forecasting 基准 | 等大更新(数据已放,最后提交 10-21) | 3 天 |
| Expand-and-Prune | E2E 候选空间自适应 (WACV 2027) | 占位中(repo 404 第 3 天,疑迁移,原承诺 10 月底放码) | 3 天 |
| FALCON | FPGA 延迟可认证 Occupancy (WACV 2027) | 等代码(arXiv v2 页仍无链接) | 3 天 |
| Think Densely, Act Sparsely | VLA 认知链 | 等代码(**已中 NeurIPS 2026**,无 repo) | 3 天 |
| FM-Occ | 3D Occupancy (子体素 Shuffle+解码) | 等代码(无 repo) | 2 天 |
| RAHF-Net | LiDAR 辅助 RGB 语义分割 | 等代码(无 repo) | 2 天 |
| Foundation4D | 统一感知+重建基础模型 | 等代码(无 repo) | 1 天 |
| CoGaussian | 协同开放词汇高斯感知 (V2X) | 等代码(无 repo) | 1 天 |
| DriveMLP | 训练策略 vs 模型复杂度 (E2E) | 占位中(repo 仅 LICENSE 零提交) | 1 天 |
| SARD | Diffusion Driving VLA 场景自适应读出 | 占位中(论文未挂 arXiv) | 1 天 |
| **ByteCope** 🆕 | V2X 字节约束协同感知 (WACV 2027) | 占位中(官方仓 10-24 建仅 README) | 今日加入 |
| **CLAP-AD** 🆕 | E2E 规划:路径模仿+免模仿速度评分 (WACV 2027) | 占位中(官方仓 "Code being prepared") | 今日加入 |
| **VEOcc++** 🆕 | 具身在线语义占用建图(持久体素特征图) | 等代码(官方仓仅项目页模板) | 今日加入 |
| **RoMo-GS** 🆕 | 在线 3DGS 建图(运动模糊解析积分) | 等代码(Code TBD,偏机器人侧) | 今日加入 |

**今日新增 watchlist 说明**:
- **ByteCope**(arXiv 2609.29456,FZI+KIT):V2X 带宽受限协同感知的"粗层全覆盖 + 稀疏高价值块精化"设计,任务感知收益选择器按下游效用分配字节预算,免重训练适配带宽变化;DAIR-V2X 上 0.60 AP@0.7 @1.87KB/agent(SimVQ 均匀压缩 0.52 @4.61KB);WACV 2027 一轮直接录用,官方仓已建等码;
- **CLAP-AD**(WACV 2027,项目页 clap-ad.github.io):解耦"路径靠模仿、速度靠多准则免模仿打分"的规划头,部署期用户可指定期望速度且无需重训,Bench2Drive 与 NAVSIM v2 大幅提升;与本栏长期跟踪的规划侧项目互补,官方仓 10-20 已建;
- **VEOcc++**(arXiv 2610.27704):持久体素特征图支撑具身在线语义占用建图与离线再处理;官方仓 VEOccpp 目前只是项目页模板,但前作 wryzju/VEOcc(⭐8)有完整代码可先行参照;
- **RoMo-GS**(arXiv 2610.24605):用误差函数解析积分每个高斯在曝光区间内的运动,单次 CUDA 光栅化搞定运动模糊,联合在线优化位姿/速度/高斯,比离线去模糊基线快 15-150×;域为机器人建图,但其"在线建图 × 运动模糊"解法对车载在线建图同样对症,标注 Code TBD。

**Dormant**(轻量复查):RaGS(4D 雷达+单目 GS 检测)——今日复查仍无提交,继续休眠。

---

*核实说明:WildDepth-v2 释码由 repo README 全文 + 文件布局 + commits.atom(10-12)三重确认,数据获取入口未见公开 URL 已如实标注;四个新增 watchlist 均经官方仓 README/项目页逐个核验占位状态;所有"无 repo"结论来自 arXiv API + GitHub 检索双通道。*
