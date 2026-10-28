# 感知开源雷达 2026-10-29

> 检索范围：GitHub (search API + commits.atom 核实)、arXiv 近 7 日、Hugging Face、OpenDriveLab/NVlabs/OpenGVLab/ori-drs 组织动态。今日整体偏安静：无 watchlist 释码事件；新开源集中在 hous-lab 部署矩阵的两项新 repo。

## 今日重点

### 1. apollo-e2e-closedloop — CARLA + Apollo 闭环 TensorRT 端到端驾驶集成

- **方向**: End-to-End AD 部署 / 仿真闭环验证
- **代码**: https://github.com/hous-lab/apollo-e2e-closedloop （首码 10-24，最近提交 10-26）
- **论文**: 无（工程集成项目）
- **核心亮点**:
  - CARLA 0.9.15 仿真车 + Apollo v8.0 CyberRT 全链闭环：E2E 模型读 CARLA 六路环视相机，输出 ADCTrajectory 到 `/apollo/planning`，Apollo 控制模块按该轨迹闭环开车。
  - 双模型实测接入并通过三道 gate（waypoint sanity / 300s 闭环 / 万帧级延迟统计）：VAD（backbone INT8 + head FP16）p50 191.7ms、plan 5.2Hz；SparseDriveV2（4 引擎 FP16 级联）p50 74.9ms、p99 130ms、plan 13.35Hz —— 均在 RTX 4090 Laptop 单卡上与 CARLA 渲染、Apollo 控制共享 GPU 跑出来的数字。
  - model-agnostic relay 架构：模型跑在 Apollo 容器外的 host TensorRT 10.x，CyberRT↔TCP msgpack 中继，Apollo 侧除小控制 overlay 外全原版；新模型只需 host 侧服务 + engine 指针（`docs/model_contract.md` 定义插入契约）。
  - 测量诚实度拉满：所有 gate 结果落 `run/*.json` 留证，README 明确列出绑定条件（单机、笔记本电源管理、6cam-lowres、sim 时钟 0.115x）并承认无同设备 PyTorch 基线。
- **代码完整度**: 训练 无｜配置 有（`scripts/env.sh` + `check_env.sh` 逐项校验 + CAM_PROFILE 分档）｜权重 无内置（TRT engine 由兄弟 repo `vad-trt-edge` / `sparsedrive-trt-edge` 的 HF ONNX 自建，`ckpts/README.md` 给完整下载/构建表）｜部署 有（TRT 10.16.1 实测，分级 bring-up 脚本 + per-stage 日志）
- **数据集/框架**: CARLA 0.9.15 + Apollo v8.0；模型侧 VAD（nuScenes 系）/ SparseDriveV2（Bench2Drive 系）
- **ONNX/TensorRT/部署**: ONNX（HF hous-lab）→ TRT engine 全链，INT8/FP16 混合已实测；非边缘平台但方法论可平移
- **推荐理由**: 该实验室此前已放出 vad-trt-edge（10-20 已报）、sparsedrive-trt-edge（10-26 已报）、bevdet-metis（10-28 已报）单模型部署，本 repo 补上了"部署产物如何接进 Apollo 并闭环验证"的最后一环——gate 协议 + 插入契约 + 延迟测量规范，是 E2E 模型部署后验证的工程范本。

### 2. orin-qnx-dualrt — AGX Orin 双域确定性推理调度（Linux GPU + QNX 8.0 KVM）

- **方向**: 部署 / 实时系统 × 感知推理调度
- **代码**: https://github.com/hous-lab/orin-qnx-dualrt （首码 10-24，最近提交 10-26）
- **论文**: 无（开源研究项目）
- **核心亮点**:
  - 车载双域拓扑的开源研究复现：Linux 域持 GPU 跑真实 TRT 推理（SparseDrive 4 引擎链 + 3 个 Autoware 官方 ONNX 模型），QNX 8.0 作 KVM guest 提供固定优先级释放调度、看门狗、deadline 监控和安全状态机——调度权威与算力执行分离。
  - 128B contract-frame IPC 带 9 个时间戳；Orin nsys 全核 trace 与 QNX trace 合并为统一 Perfetto 时间线；干扰矩阵实验（GPU 占空比 / CPU 核数 / 任务集 / 电源模式）给出 P50/95/99 分布与逐项机理解释。
  - 亮点工程细节：往未修改的 ROS 2 节点注入 ~90 行 release-gate，真实 CARLA 相机帧按 QNX 节拍驱动完整推理链，闭环巡航并可从 stall 自恢复；ITaskRuntime 双后端（Linux timerfd / QNX timer+sigwaitinfo）同任务表双跑，量化 6 项移植差异。
  - 边界诚实：KVM 非 Type-1、无 ASIL/ISO 26262 主张；QNX NCEULA 限定下量化数字不发布，方法论与代码全放、每用户用自己 SDP 自建二进制。
- **代码完整度**: 训练 无｜配置 有（双后端任务表）｜权重 无（复用兄弟 repo 的 engine）｜部署 有（AGX Orin R36.5 + QNX SDP 8.0 + TRT 10.x 实测；x86 contract 测试任意机器可跑）
- **数据集/框架**: SparseDrive 链 + Autoware 官方 ONNX 模型；CARLA 闭环
- **ONNX/TensorRT/部署**: TRT 10.x 真实负载；Autoware ONNX 直接可用
- **推荐理由**: 把"推理是否按时完成"变成可监督、可归因、可复测的问题——对做车规级部署、实时性验证、多模型调度的工程师是稀缺的开源参考实现；文档密度（porting-diff / no-go 分析 / stability postmortem）本身 就值一读。

## 新开源（近 7 天首码且未报道过）

今日仅上述两项新开源（均首码 10-24），无其他。简评一句：hous-lab 一周内连放 5 个部署向 repo（vad-trt-edge → sparsedrive-trt-edge → bevdet-metis → apollo-e2e-closedloop → orin-qnx-dualrt），已形成"导出→量化→闭环→调度"的完整车载部署矩阵，值得关注整个 org。

## 持续关注（watchlist 33 项）

| 项目 | 方向 | 状态 | 已跟踪 |
|---|---|---|---|
| UNIVERSE | 统一视频-动作 DiT (World-Action, Xiaomi) | 占位中（最后提交 10-15） | 8 天 |
| OccWAM | Occupancy World-Action Model | 等代码 | 8 天 |
| InfraWorld | 路侧 4D Occupancy Forecasting | 等代码 | 8 天 |
| DriveWorld3D | 3D 世界模型 + 生成式仿真 | 等代码 | 8 天 |
| UBD-Bench | 多模态感知鲁棒性基准 | 等代码 | 8 天 |
| Lane-Structured Conditioning Fields | Online HD Mapping | 等代码 | 8 天 |
| PanoBench | Panoptic/4D Occupancy 基准 | 等大更新（仅 configs） | 7 天 |
| Expand-and-Prune | E2E 测试时候选空间自适应 (WACV'27) | repo 404（第 7 天；原承诺 10 月底放码，临近观察） | 7 天 |
| FALCON | FPGA 延迟可认证 Occupancy 网络 | 等代码 | 7 天 |
| Think Densely, Act Sparsely | VLA 认知链 (NeurIPS 2026) | 等代码 | 7 天 |
| FM-Occ | 3D Occupancy (上采样+解码改进) | 等代码 | 6 天 |
| Foundation4D | 统一感知+重建基础模型 | 等代码 | 5 天 |
| CoGaussian | 开放词汇高斯 V2X 协同感知 | 等代码 | 5 天 |
| DriveMLP | E2E（训练策略 vs 复杂度） | 占位中（零提交） | 5 天 |
| SARD | Diffusion Driving VLA | 占位中 | 5 天 |
| ByteCope | V2X 字节约束协同感知 (WACV'27) | 占位中 | 4 天 |
| CLAP-AD | 免模仿速度评分 E2E (WACV'27) | 占位中 | 4 天 |
| VEOcc++ | 具身在线语义占用建图 | 等代码（page 分支活跃） | 4 天 |
| RoMo-GS | 在线 3DGS 建图（运动模糊） | 等代码（仅项目页） | 4 天 |
| RaSFNet | LiDAR 语义分割（射线对齐） | 等代码 | 3 天 |
| RT-Radar-Panoptic | 4D 雷达实时全景理解 (Oxford ORI) | 等代码 | 3 天 |
| FogTTA | 雾天在线测试时适应 | 等代码 | 3 天 |
| SpikeOcc | 脉冲专家多模态 Occupancy | 等代码 | 3 天 |
| RadarGSOcc | 4D 雷达张量 3D Occupancy | 等代码 | 3 天 |
| DriveOSS | 恶劣天气开集分割基准 | 等大更新（数据集 coming soon） | 2 天 |
| LegoOcc++ | 开放词汇 Occupancy（转户外） | 等代码 | 1 天 |
| DriveUniTok | World-Action 统一 tokenizer | 等代码 | 1 天 |
| BEV-Model-Soup | 相机 3D 检测鲁棒性 | 等代码 | 1 天 |
| Adapt-for-Planning | 测试时世界模型自适应 | 等代码 | 1 天 |
| CoopAdverseWeather | 恶劣天气协同感知 | 等代码 | 1 天 |
| GRIP-Fusion | 路侧相机-LiDAR 检测 + 路口数据集 | 占位中（code/models 待 publication；README 已放 TUMTraf 系三基准 SOTA 对比表） | 今日新增 |
| GCSR-OCC | 开放词汇 Occupancy（几何校准+语义路由） | 等代码（10-28 论文 arXiv:2610.37153） | 今日新增 |
| FastBEVFusion (2610) | 实时相机-LiDAR BEV 融合 3D 检测 50FPS (ROBOT2026) | 等代码（10-28 论文 arXiv:2610.37149） | 今日新增 |

**Watchlist 动态**: 今日无释码；Expand-and-Prune 连续第 7 天 404（GitHub search 索引仍残留旧条目，atom/API/raw 全 404，注意不要被索引误导），原承诺 10 月底放码、进入临期窗口；其余各项复查无变化。dormant 1 项（RaGS）仍零提交。

---
*核实方法：repo 活性用 commits.atom 首条 `<updated>`；候选项目逐一核对 README/文件树防空壳；arXiv ID 经 export API 复核。*
