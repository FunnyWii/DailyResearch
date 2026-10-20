# 自动驾驶感知开源日报 · 2026-10-21

> 覆盖方向：3D Object Detection / BEV Perception / Occupancy & Forecasting / LiDAR-Camera & Radar-Camera Fusion / 4D Radar / Multi-modal / 3D Tracking / Online Mapping / End-to-End / World Models / VLM-VLA / Vision Foundation Models / 数据集与基准
>
> 核实方式：GitHub search API（created/pushed 过滤）+ commits.atom 逐项核实 + README/文件树确认代码真实存在 + arXiv API 近 7 日检索 + Awesome-World-Model 近三日 commit diff 比对 + HF API。近 7 天窗口内的大部分项目已在昨日（10-20 首期）报道，本期按去重口径只报增量。

---

## 一、今日重点

### CausalSceneNarration (CSN) — VLA 驾驶的推理时因果文本重构（TMLR 2026 官方代码，10-17 首放）
- **方向**：VLM for Autonomous Driving / VLA 推理增强
- **代码**：<https://github.com/tlab-wide/CausalSceneNarration>（created 2026-10-17，最后 commit 2026-10-19，164 个文件，Apache-2.0）
- **论文**：<https://openreview.net/forum?id=sJATIdx6jG>（TMLR 2026，东大 + TIER IV）
- **核心亮点**：不改权重、不占显存、CPU 端把 VLA 驾驶模型的文本输入重写成"因果连接"结构——导航指令与约束（行人/红灯/限速）用 BUT / YIELD_BEFORE / BECAUSE 连接并附上实测距离与速度。LMDrive 在 CARLA 16 路线 × 8 城镇 × 5 次重复上 **驾驶分 32.54 → 42.67（+31.1%）**，LoRA 偏好对齐变体 +24.5%；对 ±5 m 距离误差、±30% 速度噪声、20% 漏检均保持增益
- **代码完整度**：训练 无（推理时方法；LoRA PL-DPO-NLL 训练管线未随仓发布）｜配置 有（leaderboard agent / safety configs）｜权重 无（用 LMDrive 原始权重，LoRA adapter 未见发布）｜部署 无 ONNX/TRT（方法本身 CPU 轻量，无部署需求）
- **框架支持**：CARLA leaderboard；同时给出 LMDrive、DriveLM、DriveVLM 三套 agent 的接入适配；附 `analysis/reproduce_paper_numbers.py` 一键复现论文数字 + 全部 5 次重复的原始结果 JSON
- **推荐关注理由**：VLA 驾驶落地少见的"零训练成本、可直接叠加"的推理时技巧，消融（Flat Text vs 因果连接）与置信区间做得扎实；任何在用 LMDrive 系模型的人都该试一下

---

## 二、新开源（近 7 天首放码，昨日未报道）

### VFM-Assisted-Radar-Perception — 低能见度 SAM 语义先验 + 雷达-相机融合检测（原型，10-18）
- **方向**：Radar-Camera Fusion（低能见度鲁棒检测）
- **代码**：<https://github.com/bella-jy/VFM-Assisted-Radar-Perception>（created 2026-10-18）
- **核心亮点**：三分支设计——退化自适应 SAM（低能见度图 + 对齐伪清晰参考图）产语义先验 → 语义引导的雷达点可靠性过滤（语义支持度 + 中心接近度 + 局部一致性）→ 语义门控融合出 3D 检测；SAM 与检测器分离训练，语义先验离线生成
- **代码完整度**：训练 有（SAM 微调 20 ep + 检测器训练脚本）｜配置 未验证（命令行参数式，无标准 configs）｜权重 无（SAM ViT-B 官方 ckpt 自备，训练后权重未发布）｜部署 部分（`src/convert_onnx.py` ONNX 导出脚本）
- **数据集**：自定义低能见度数据格式（CenterFusion 18×N 雷达输入格式，需自建数据）；**无论文、无公开 benchmark 数字**
- **推荐关注理由**：本周雷达-相机融合方向唯一新仓，SAM 先验引导雷达过滤的思路可借鉴；但属无论文的个人原型（仓里还提交了 `__pycache__`），工程成色需自行甄别——只作思路参考，不作生产依赖

> 其余检索结论：GitHub 近 7 日新仓中 3D 检测 / BEV / occupancy / 4D radar / MOT / online mapping 关键词下**无其他达标项目**（多为 0-star 课程作业或空壳 README 仓，如 StreamPETR-DualMotion 仅 2 行 README，已排除）；HF 上 OpenDriveLab 本周无新权重。

---

## 三、值得持续关注（watchlist · 10 项跟踪中）

**repo 已建、等释码：**
- **ViRA**（OpenDriveLab+SEU）· E2E/VFM 表示对齐 · 等代码（repo 已放结果+Demo）· 跟踪 1 天 · DINOv3 对齐 NAVSIM v2 92.3 EPDMS
- **SimVTA**（OpenDriveLab+上创+理想）· World-Action 后训练 · 等代码 · 跟踪 1 天 · 3DGS 渲染候选轨迹未来帧 + GRPO + flow-matching 视频损失，NAVSIM v1 91.3 PDMS
- **UNIVERSE**（Xiaomi EV）· 统一视频-动作 DiT · 占位中 · 跟踪 1 天 · NeurIPS 2026，训练/推理代码待公司审批
- **AD-LiST-JEPA** · LiDAR occupancy 世界模型 · 等大更新 · 跟踪 1 天 · ⏰ 作者承诺 **10-26 前**释出完整代码（已推迟一次，明日起到期）
- **RaGS**（CVPR'26）· 4D 雷达+单目高斯泼溅 · 占位已 12 个月 → 移入 dormant，每日轻查

**纯论文、暂无 repo（今日新增蹲守）：**
- **OccWAM**（arXiv:2610.17688）· Occupancy World-Action Model（scene-to-ego 联合 flow matching）· 等代码 · 1 天
- **InfraWorld**（arXiv:2610.14544）· 路侧 4D occupancy forecasting · 等代码 · 1 天
- **DriveWorld3D**（arXiv:2610.22450）· 证据约束 3D 世界建模 + 生成式仿真 · 等代码 · 1 天
- **UBD-Bench**（arXiv:2610.25448）· 多模态融合单模态分支退化基准 · 等代码 · 1 天
- **Lane-Structured Conditioning Fields**（arXiv:2610.25284）· 在线高精建图 · 等代码 · 1 天
- **SafeDriveVLA**（CoRL 2026，arXiv:2610.20609）· 世界模型 dreaming × 冲突感知 E2E · 等代码 · 1 天

---

## 四、趋势信号（第 2 期）

1. **"论文+结果先行、代码 TODO"成建制出现**：本周 ViRA、SimVTA、UNIVERSE 三连占位，分属 OpenDriveLab 双弹与小米 EV——蹲守名单（watchlist）从 0 涨到 10 项，比单日放码更能预示 11-12 月的释码潮。
2. **World-Action Model (WAM) 成为 10 月论文爆发点**：Awesome-World-Model 10-14~16 三天收录约 26 篇新条目（OccWAM / InfraWorld / 1Step-WAM / StreamingWAM / UniW(A)M…），occupancy 与 world-action 的交叉是新热点；与我们跟踪的 SimWAM / WorldEngine / SimVTA 构成同一条技术线。
3. **雷达融合与 3D MOT 新开源持续真空**：连续两期无达标新仓，4D radar 检索（created/pushed 双口径）零结果——该方向开源主力仍停留在 2026 上半年那批（R4Det / MSSF / L4DR）。
4. **明日期待点**：AD-LiST-JEPA 承诺 10-26 释码（LiDAR occupancy 世界模型 + JEPA 自监督，方向稀缺）；ViRA/SimVTA 论文热度高，释码随时可能。

---

*生成：ZCode · 2026-10-21 · 检索路径：GitHub search（4 批 22 查询）→ 候选 commits.atom/文件树逐项核实 → arXiv API 近 7 日 7 组关键词 → Awesome-World-Model 近 3 日 commit diff → HF API；去重基线：10-20 首期日报（已回填 reported 列表 23 项）*
