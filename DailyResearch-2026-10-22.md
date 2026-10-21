# 感知开源雷达 2026-10-22

> 覆盖方向：3D Object Detection / BEV Perception / Occupancy & Forecasting / LiDAR-Camera & Radar-Camera Fusion / Multi-modal Perception / 3D Tracking / Online Mapping / End-to-End AD / World Models / VLM for AD / 4D Radar / Vision Foundation Models
> 时间口径：7 天滚动窗口（2026-10-15 起），已报道项目不重复。

---

## 一、今日重点

### 1. SafeDriveVLA —— watchlist 释码（已跟踪 1 天）

- **方向**：VLM for AD / World Model × VLA（安全方向）
- **机构**：UC Irvine（Alfred Chen 组）+ UCSD + U. Cincinnati + NUS，CoRL 2026
- **代码**：https://github.com/ASGuard-UCI/safedrive-vla
- **论文**：https://arxiv.org/abs/2610.20609
- **权重**：https://huggingface.co/Danielxsy/safedrive-vla（baseline / baseline_mode / safedrive_vla / world_model 共 3 组 ckpt + config，10-16 上线）

**核心亮点**：把 VLA 的自由文本导航接口当成新的攻击面/安全面来研究。两点方法贡献：① 每帧专家动作重标为离散驾驶模式 token（`<STRICT>` / `<CAUTIOUS>` / `<FALLBACK>`），在动作前显式输出，冲突感知被直接监督；② 冻结 V-JEPA 2 编码器 + 动作条件预测器做"导航条件世界模型 dreaming"，在执行指令前先在潜空间预演机动是否可行。基准侧贡献 CARLA-F（导航跟随）、B2D-C / NavSim-C（导航-场景冲突）、B2D-Adv（对抗指令）四套评测。

**代码完整度**：训练 **有**（`safedrive_vla/` 完整包：configs / models / data / action_tokenizer）｜配置 **有**（hydra 风格 experiment configs）｜权重 **有**（HF 10-16 上线，含世界模型 ckpt）｜部署 **无**
**框架/数据支持**：CARLA Bench2Drive 体系（benchmark 生成脚本全套：路线构建、冲突 meta-command 生成、对抗场景 XML）；ONNX/TensorRT **无**
**核实说明**：repo 共 800 文件，commits.atom 最后提交 10-15，HF 权重 10-16。昨日入 watchlist 时判"暂无 repo"系当时检索遗漏，今日复核确认——按"watchlist 释码"如实记录。
**推荐理由**：安全视角独特的 VLA 工作，对抗性指令与导航-场景冲突是语言接口上车前必须回答的问题；四套基准生成脚本全开源，可单独复用做红队测试。

### 2. robust-efficient-lidar-perception —— 论文 10-20 新出，代码全量可用

- **方向**：3D Object Detection（LiDAR）/ 鲁棒性 × 部署效率
- **代码**：https://github.com/drunklibai/robust-efficient-lidar-perception（384 文件）
- **论文**：https://arxiv.org/abs/2610.27617（10-20）

**核心亮点**：首个把 LiDAR 检测的**点云腐蚀鲁棒性**与**延迟/显存效率**放在同一框架里解决的工作。路线：离线用 7 个固定参考检测器在干净/腐蚀双域出预测并融合 → 按扰动敏感度对 1:1 训练混合数据排序 → 按训练统计生成 point/voxel 预算瘦身 PointRCNN / SECOND → 保序训练紧凑检测器。效果：腐蚀下 AP 平均 **+4.03**，平均推理延迟 **-14.4%**，峰值 GPU 显存 **-38%**。部署时单检测器，无蒸馏、无运行时开销。

**代码完整度**：训练 **有**（`tools/train.py` + `paper_pipeline.py` 全流程编排）｜配置 **有**（KITTI/ONCE 复现 recipe + 7 参考检测器）｜权重 **无**｜部署 **部分**（`test_speed.py` 测延迟/p95/峰值显存，非 TRT）
**框架/数据支持**：OpenPCDet 改造版（含 CUDA 算子）；KITTI、ONCE；ONNX/TensorRT **无**
**核实说明**：repo 09-29 即预建全量代码（pushed 09-30），论文 10-20 放出后才进入视野——严格"首码日期"在 7 天窗口外，因未被报道过且论文在窗口内，列入今日重点并如实标注。
**推荐理由**：工程视角稀缺——同时报 AP、p95 尾延迟和峰值显存，且复现文档（REPRODUCING / BASELINES / RELEASE_NOTES）罕见地完备，做 LiDAR 部署的同学可直接拿来当腐蚀鲁棒性测试床。

---

## 二、新开源（近 7 天首放、此前未报道）

### 3. PanoBench（首放 10-16，数据集 10-21 更新）—— 全景占用 + 4D 占用预测基准

- **方向**：Occupancy / Panoptic Occupancy / **Occupancy Forecasting 基准**
- **代码**：https://github.com/fengyi233/PanoBench
- **论文**：https://arxiv.org/abs/2610.23537
- **数据**：https://huggingface.co/datasets/fengyi233/PanoBench（136 文件，10-21 更新）
- **主页**：https://mias.group/PanoBench/

**核心亮点**：200K+ 帧、约 1K 序列的相机中心全景占用基准，三子集统一标注协议：PanoBench-CarlaOcc（合成、mesh 导出完整几何）、PanoBench-nuScenes（真实环视、完整前景标注）、PanoBench-KITTI360（前视、持久实例 ID），体素 0.1/0.2/0.4m 三档。支持三任务：语义占用、**全景占用**（几何+语义+实例 ID 统一评测）、**4D occupancy forecasting / occupancy world modeling**。

**代码完整度**：训练 **无**（repo 仅 baseline 实验配置，无任何 .py，代码待放）｜配置 **有**（FlashOcc / GaussianFormer / SparseOcc / OPUS 四组 baseline × 三子集全配置）｜数据 **有**（HF 数据集）｜权重 **无**｜部署 **无**
**核实说明**：repo 10-16 建、10-21 仍活跃推送；已入 watchlist 等大更新（等训练/评测代码），后续只报代码释出事件。
**推荐理由**：现有占用基准普遍缺实例 ID 与完整前景几何，occupancy forecasting 更是缺高质量评测——这个基准正好补痛点，先下数据、等代码。

### 4. Scene-Adaptive-VLA（首码 10-13 建仓，活跃至 10-20）—— 场景自适应层路由

- **方向**：VLM for AD / VLA 推理效率
- **机构**：北邮，NeurIPS 2026
- **代码**：https://github.com/yang-luvu/Scene-Adaptive-VLA
- **论文**：https://arxiv.org/abs/2610.24590

**核心亮点**：实时估计模型感知的场景复杂度，逐帧动态调整激活层（动态层路由）：简单场景走浅层，复杂场景开深层。Bench2Drive 上推理 FLOPs **-53.5%**、延迟 **-49.0%**，驾驶与语言能力几乎无损；路由器端到端可训。

**代码完整度**：训练 **无**（方法本体训练代码未见，注意 repo 4234 文件大部分是 Bench2Drive + scenario_runner 评测框架）｜配置 **部分**（stage2_eval 等）｜数据生成 **有**（CARLA VQA/解说/梦境数据全链路脚本）｜评测 **有**（Bench2Drive harness）｜权重 **无**｜部署 **无**
**支持**：CARLA Bench2Drive；ONNX/TensorRT **无**
**推荐理由**：VLA 推理成本是上车核心瓶颈，"按场景难度分配算力"是轻量、可迁移到其他 VLA 的思路；当前 repo 更适合当数据生成 + 评测脚手架用，方法训练代码到了再精读。

---

## 三、持续关注（watchlist）

| 项目 | 方向 | 状态 | 已跟踪 |
| --- | --- | --- | --- |
| ViRA | E2E / VFM 表示对齐（DINOv3→NAVSIM v2 92.3 EPDMS） | 等代码 | 1 天 |
| SimVTA | World-Action Model 后训练（3DGS+GRPO） | 等代码 | 1 天 |
| UNIVERSE | 统一视频-动作 DiT（小米，代码审批中） | 占位中 | 1 天 |
| AD-LiST-JEPA | LiDAR Occupancy 世界模型 | 等大更新（**承诺 10-26 前释码，窗口仅剩 4 天**） | 1 天 |
| OccWAM | Occupancy World-Action Model | 等代码 | 1 天 |
| InfraWorld | 路侧 4D Occupancy Forecasting | 等代码 | 1 天 |
| DriveWorld3D | 证据约束 3D 世界模型 | 等代码 | 1 天 |
| UBD-Bench | 多模态融合单模态退化基准 | 等代码 | 1 天 |
| Lane-Structured Conditioning Fields | Online HD Mapping | 等代码 | 1 天 |
| PanoBench | 全景占用基准 | 等大更新（数据已放等代码） | 今日新增 |
| Expand-and-Prune | E2E 测试时候选空间自适应（WACV 2027） | 占位中（承诺 10 月底放码） | 今日新增 |
| FALCON | FPGA 延迟可认证占用网络 | 等代码（论文称已释 HLS 但未见 repo） | 今日新增 |
| Think Densely, Act Sparsely | VLA 认知链 | 等代码 | 今日新增 |

今日复查：ViRA（最后提交 10-13）、SimVTA（10-12）、UNIVERSE（10-15）、AD-LiST-JEPA（10-19）均无新提交；dormant 的 RaGS 仍零提交（今日不列）。

---

## 编注

- 本日 GitHub 主题检索新建成 repo 多为 0 star 个人项目（BEV 教程、CARLA 练手等），无入选；SPA-junghokim/SafeDrive（CVPR 2026 Highlight）代码 8 月已放、超出窗口，未报。
- VEOcc++（10-20，室内 embodied occupancy mapping，wryzju/VEOccpp 10-14 建）与 TriSAR-3D（10-15，搜救机器人三模态数据集）偏具身/救援场景，超出本雷达 AD 感知口径，未列。
- GitHub API 无认证限额（60 次/时）今日耗尽，核心核实均改经 commits.atom / raw 文件完成，不影响结论。
