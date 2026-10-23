# 📡 感知开源雷达 2026-10-24

> 方向覆盖:3D Object Detection / BEV Perception / Occupancy / LiDAR-Camera & Radar-Camera Fusion / Multi-modal Perception / 3D Tracking / Online Mapping / End-to-End AD / World Models / VLM for AD / 4D Radar / Vision Foundation Models
> 窗口口径:近 7 天 (2026-10-17 起) 新开源;已报道项目不重复。核实方法:GitHub commits.atom + README/文件树 + arXiv 页 + HF API。

---

## 一、今日重点

### 1. SimVTA —— 论文已跟踪 3 天,今日开源(推理+权重)

- **方向**:World-Action Model 后训练(世界模型 × 规划对齐)
- **代码**:https://github.com/OpenDriveLab/SimVTA(repo 10-06 建,13MB,**10-23 放出推理/评测代码 + checkpoints**)
- **论文**:https://arxiv.org/abs/2610.13781(10-12)
- **权重**:https://huggingface.co/OpenDriveLab-org/SimVTA(`simvta.pkl`,已验证存在)
- **机构**:OpenDriveLab@HKU + 上海创新研究院 + 理想 + 同济 + 清华
- **核心亮点**:
  - 仿真锚定对齐:在重建真实场景里为**候选轨迹**用 3DGS 渲染匹配的未来帧视频,把"视频预测监督"从重放日志扩展到任意候选动作;
  - 选择性视频监督:只对高奖励候选中误差更高的未来施加视频损失,联合训练轨迹规划与视频预测;推理时直接出轨迹(不需要视频采样);
  - 结果:NAVSIM v1 **91.3 PDMS** / v2 **88.2 EPDMS** / HUGSIM **38.8 HD-Score**,属当前 World-Action 模型第一梯队。
- **代码完整度**:训练 **无**(TODO 未勾)| 配置 有(`configs/navsim_v1.py`)| 权重 **有**(HF,另需 Epona DCAE 外链权重)| 部署 无
- **数据集/框架**:NAVSIM v1 全套评测链路内置(`navsim/` 含 PDM-score、metric cache);需自行准备 OpenScene navtest + nuPlan 地图
- **部署支持**:无 ONNX/TensorRT(研究代码定位)
- **推荐理由**:watchlist 跟踪 3 天释码。视频监督 × 候选轨迹渲染是当前 WAM 后训练的关键分歧点,本作给出可复现的推理+评测基线;训练代码放出到时可跟进其选择性监督的实现细节。

---

## 二、新开源(近 7 天首放代码,未报道过)

### 1. NeuralSRender —— G-Buffer 引导的 Sim-to-Real 视频扩散(10-21 首放)

- **方向**:仿真数据照片级化 / 生成式仿真(World Models 周边工程线)
- **代码**:https://github.com/WHU-USI3DV/NeuralSRender(repo 10-21 建,22.7MB,含完整 diffsynth 训练框架)
- **论文**:https://arxiv.org/abs/2610.29200(10-21)
- **权重**:https://huggingface.co/stardustlu/NeuralSRender(两阶段 LoRA `synthetic_city_lora` + `real_dv_lora`,已验证存在)
- **机构**:武汉大学 USI3DV
- **核心亮点**:
  - 用 G-Buffer(几何+材质先验)轻量 projector 引导预训练视频 DiT,把合成驾驶视频翻译成照片级,同时保持与源场景几何一致;
  - 两阶段训练:先合成视频学 G-buffer 条件,再真实视频适配外观(第二阶段只训新 LoRA),**仅加 1.45% 参数、24GB 显存可训**;
  - 附 **GenReal** 大规模驾驶视频数据集(合成+真实+对应 G-buffer);
  - 硬指标:用翻译后视频训练感知模型,nuImages 检测 AP50 **35.1→40.6**,Cityscapes 分割 mIoU **32.4→40.8**。
- **代码完整度**:训练 **有**(两阶段全流程)| 配置 有 | 权重 **有** | 部署 无(未验证)
- **数据集/框架**:GenReal(自带)/ nuImages / Cityscapes;基于 DiffSynth 风格工程化代码库,含 NPU 兼容层
- **部署支持**:无 ONNX/TensorRT
- **推荐理由**:sim-to-real 是仿真数据真正可用的最后一公里,该作把"数据翻译收益"做成了可量化闭环(检测+分割双双提升),且训练门槛压到单卡 24G,工程可上手性在同类视频扩散工作里少见。

> 本周其余新建 repo 经筛查多为个人练习/作业型项目(0-star BEV demo、教学链路等),未达收录门槛。

---

## 三、持续关注(watchlist)

| 项目 | 方向 | 状态 | 已跟踪 |
|---|---|---|---|
| AD-LiST-JEPA | LiDAR Occupancy 世界模型 (JEPA) | 等大更新(作者承诺 10-26 前释码,剩 2 天) | 3 天 |
| UNIVERSE (xiaomi-mlab) | 统一视频-动作 DiT (NeurIPS 2026) | 占位中(最后提交 10-15,代码等审批) | 3 天 |
| OccWAM | Occupancy World-Action Model | 等代码(无 repo) | 3 天 |
| InfraWorld | 路侧 4D Occupancy Forecasting | 等代码(无 repo) | 3 天 |
| DriveWorld3D | 3D 世界模型+生成式仿真 | 等代码(无 repo) | 3 天 |
| UBD-Bench | 多模态感知单分支退化基准 | 等代码(无 repo) | 3 天 |
| Lane-Structured Conditioning Fields | Online HD Mapping | 等代码(无 repo) | 3 天 |
| PanoBench | 全景占用+4D Forecasting 基准 | 等大更新(数据已放,代码未放) | 2 天 |
| Expand-and-Prune | E2E 候选空间自适应 (WACV 2027) | 占位中(repo 连续两日 404,观察迁移) | 2 天 |
| FALCON | FPGA 延迟可认证 Occupancy | 等代码(摘要称已释但未找到 repo) | 2 天 |
| Think Densely, Act Sparsely | VLA 认知链 | 等代码(无 repo) | 2 天 |
| FM-Occ | 3D Occupancy (子体素 Shuffle+解码) | 等代码(无 repo) | 1 天 |
| RAHF-Net | LiDAR 辅助 RGB 语义分割 | 等代码(无 repo) | 1 天 |
| **Foundation4D** 🆕 | 统一感知+重建基础模型(几何/3D 检测/语义 Occ/高斯) | 等代码(无 repo) | 今日加入 |
| **CoGaussian** 🆕 | 协同开放词汇高斯感知 (V2X) | 等代码(无 repo) | 今日加入 |
| **WildDepth-v2** 🆕 | RGB-LiDAR-Radar 同步数据集基准 | 等数据/代码(无 repo) | 今日加入 |
| **DriveMLP** 🆕 | 训练策略 vs 模型复杂度 (E2E) | 占位中(repo 仅 LICENSE,论文 10-21 出) | 今日加入 |
| **SARD** 🆕 | Diffusion Driving VLA 场景自适应读出 | 占位中(code coming soon,论文未出) | 今日加入 |

**今日新增 watchlist 说明**:
- **Foundation4D**(arXiv 2610.30340):一个前馈模型统一几何估计、3D 检测、语义 Occupancy 与高斯重建,因子化时空注意力融合多相机多帧,位姿图优化精修轨迹——多任务感知基础模型路线,值得等码;
- **CoGaussian**(arXiv 2610.28530):协同感知×开放词汇×高斯场景表示,摆脱稠密 3D 体素标注依赖,V2X 方向;
- **WildDepth-v2**(arXiv 2610.31123):RGB-LiDAR-Radar 三模态同步数据集,度量深度+运动+透明物体基准,4D radar 数据稀缺背景下的新供给;
- **DriveMLP**(arXiv 2610.29676):论证训练策略收益可超模型复杂度,repo 空占位;
- **SARD**(github sjyu001/SARD):KAIST,diffusion VLA 的场景自适应读出/解码,论文尚未挂 arXiv 即建 repo 占位。

**Dormant**(轻量复查):RaGS(4D 雷达+单目 GS 检测)——今日复查仍无提交,继续休眠。

---

*核实说明:SimVTA 释码由 README News `[2026/10/23]` + TODO 勾选 + HF API 文件列表三重确认;NeuralSRender 由 repo 文件树 + HF safetensors 文件确认;所有"无 repo"结论来自 arXiv abs 页链接扫描 + GitHub 检索。*
