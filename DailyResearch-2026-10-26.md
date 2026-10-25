# 感知开源雷达 2026-10-26

> 检索范围：GitHub / arXiv（近 7 日）/ Hugging Face / OpenDriveLab / NVlabs。去重基线：近 60 天 reported 列表（33 项）。
> 今日小结：watchlist 无释码事件；**AD-LiST-JEPA 作者承诺的 10-26 释码 deadline 今日已过，仓库仍无新提交**。新开源收获 1 个高质量部署工程 + 1 个 V2X 参考实现。

---

## 一、今日重点

### 1. sparsedrive-trt-edge —— SparseDriveV2 端到端模型全链路 TensorRT 边缘部署（新开源，10-21 首码）

- **方向**：End-to-End 自动驾驶 × 模型部署（ONNX / TensorRT / Jetson）
- **代码**：https://github.com/hous-lab/sparsedrive-trt-edge（10-21 建仓首码，持续迭代至 10-25，工程成熟度高）
- **模型权重**：https://huggingface.co/hous-lab/sparsedrive-trt-edge（4 个部署 ONNX 免导出直接用；训练 ckpt 走上游链接不二次分发）
- **论文**：无独立论文（部署工程），上游为 [SparseDriveV2](https://github.com/swc-17/SparseDriveV2/tree/bench2drive)
- **核心亮点**：
  - SparseDriveV2 全链路拆解部署：Backbone / 检测头 / 在线建图 / 运动预测四段引擎级联，x86（RTX 4090 Laptop）与 **Jetson AGX Orin（JetPack 6.2.2, TRT 10.3）** 双平台实测。
  - 关键实测结论：**INT8 只有 Backbone 有效**（x86 级联 21.1→18.2ms，Orin MAXN 86.2→75.5ms，约 1.9–2.1x）；检测头里 deformable aggregation 插件被强制 FP16，头 INT8 量化对关键路径无收益——这类"量化哪里才有效"的一手数据在 E2E 部署里非常稀缺。
  - 精度验证完整：per-tensor 余弦相似度 gates（FP16 全线 0.999+，INT8 backbone 特征 0.981–0.989），且 **Orin INT8 与 x86 INT8 退化逐位一致**（跨平台复现性）；`mp_lon_anchor` 的 WARN 定位为近并列 argmax 敏感而非部署缺陷（docs/lessons.md §7.6）。
  - 工程化程度高：`run_pipeline.sh` 8 步幂等（plugin→ONNX→FP16 engine→demo 数据→gates→渲染），Orin 侧 5 步 + `sync_to_orin.sh`；tegrastats 功耗模式扫描。
- **代码完整度**：训练 **无**（部署仓，训练环境指向上游）｜配置 **有**｜权重 **部分**（部署 ONNX 有·HF；训练 ckpt 上游链接）｜部署 **有**（FP16/INT8 双档 + 自定义 plugin）
- **数据集/框架**：Bench2Drive val（demo 帧 + 量化校准）；PyTorch 2.0.1 + mmcv 1.7.1 → ONNX 1.15 → TRT 10.3/10.4
- **部署支持**：ONNX **有**（HF 官方导出物）｜TensorRT **有**（10.x + deformable aggregation plugin，sm_87/sm_89 双平台）｜Jetson **有**（AGX Orin 实测）
- **推荐理由**：E2E 感知-规划模型"上 Orin"的完整参考实现。此前社区只有 VAD（vad-trt-edge，10-20 已报）一条链路，这次补上 SparseDrive 系；其 INT8 收益边界、插件约束、跨平台一致性验证方法论可直接迁移到自家模型部署。

---

## 二、新开源（近 7 天首放码，未报道过）

### 2. CSCWD-DEER-SBR —— V2X-R 雾雪场景 LiDAR-Radar 协同感知（10-21 首码）

- **方向**：V2X 协同感知 × Radar 融合 × 恶劣天气鲁棒
- **代码**：https://github.com/Hqa77880011/CSCWD-DEER-SBR（10-21 建，10-24 完成整理）
- **论文**：repo 内附 PDF + LaTeX 源码（**匿名作者**，CSCWD 会议投稿，未上 arXiv）
- **核心亮点**：DEER 从可追溯的删除/注入监督中分别预测"漏检证据"与"虚警证据"并做特征路由；SBR 从邻近实测雷达 donor（含 null 选项）预测受观测支撑约束的残差修正。V2X-R 上 Snow AP3D@0.7 相对基线 **+3.52pp**（45.67→49.19），雾雪均值 +1.67pp。
- **代码完整度**：训练 **有**（服务器训练命令）｜配置 **有**｜权重 **有**（Release v1.0.0，7 个配置：S0/A/B/AB/SUP/N1/N2）｜部署 **无**
- **数据集/框架**：V2X-R（fog/snow/normal 三天气协议，每条件 3,166 协同样本）；AttFuse 协同 backbone + Vehicle 检测头；PyTorch 2.1.2+cu121
- **部署支持**：无
- **推荐理由**：LiDAR 缺失回波与雷达虚警"分别处置"的思路对雷达融合工程有参考价值；V2X-R 全天气协议 + 7 配置对照实验 + 中文复现文档，适合做协同感知 baseline 复现。注意其为匿名投稿版本，正式发表后可能换官方仓。

### 简讯

- **torch-pointcloud**（HF org，10-17 起批量上传）：系统性重发布 mmdet3d / OpenPCDet / Pointcept / Open3D 生态的预训练权重（CenterPoint-nuScenes、PV-RCNN-KITTI、PTv3-nuScenes、RandLA-Net/KPFCNN-各分割数据集等 30+），免去找官网 ckpt 的麻烦，做 baseline 或蒸馏教师时可直接 `from_pretrained`。属权重镜像集而非新项目，不计入新开源。

---

## 三、持续关注（watchlist，26 项）

| 项目 | 方向 | 状态 | 已跟踪 |
|---|---|---|---|
| AD-LiST-JEPA | LiDAR Occupancy 世界模型 (JEPA) | 等大更新 | 5 天 ⚠️ 承诺 deadline(10-26)今日已过仍未释码 |
| UNIVERSE | 统一视频-动作 DiT (小米) | 占位中 | 5 天 |
| OccWAM | Occupancy World-Action Model | 等代码 | 5 天 |
| InfraWorld | 路侧 4D Occupancy Forecasting | 等代码 | 5 天 |
| DriveWorld3D | 3D 世界模型 + 生成式仿真 | 等代码 | 5 天 |
| UBD-Bench | 多模态感知单模态退化基准 | 等代码 | 5 天 |
| Lane-Structured Conditioning Fields | Online HD Mapping | 等代码 | 5 天 |
| PanoBench | 全景占用+4D Occ Forecasting 基准 | 等大更新 | 4 天 |
| Expand-and-Prune | E2E 测试时候选空间自适应 | 占位中 | 4 天 ⚠️ repo 连续 404 第 4 天 |
| FALCON | FPGA 延迟可认证 Occupancy 网络 | 等代码 | 4 天 |
| Think Densely, Act Sparsely | VLA 认知链 (NeurIPS 2026) | 等代码 | 4 天 |
| FM-Occ | 3D Occupancy Prediction | 等代码 | 3 天 |
| RAHF-Net | LiDAR-Camera Fusion 语义分割 | 等代码 | 3 天 |
| Foundation4D | 统一感知+重建基础模型 | 等代码 | 2 天 |
| CoGaussian | 协同开放词汇高斯感知 (V2X) | 等代码 | 2 天 |
| DriveMLP | E2E 训练策略研究 | 占位中 | 2 天 |
| SARD | Diffusion Driving VLA | 占位中 | 2 天 |
| ByteCope | V2X 字节约束协同感知 (WACV 2027) | 占位中 | 1 天 |
| CLAP-AD | E2E 闭环规划 (WACV 2027) | 占位中 | 1 天 |
| VEOcc++ | 具身在线语义占用建图 | 等代码 | 1 天 |
| RoMo-GS | 在线 3DGS 建图 (运动模糊) | 等代码 | 1 天（10-23 仅建项目页，代码 TBD） |
| RaSFNet 🆕 | LiDAR 语义分割（射线对齐+扫描感知） | 等代码 | 今日加入 |
| RT-Radar-Panoptic 🆕 | 4D 雷达语义+全景分割（Oxford ORI） | 等代码 | 今日加入 |
| FogTTA 🆕 | 雾天在线 TTA（RF-DETR，Canadian AI 2026） | 等代码 | 今日加入 |
| SpikeOcc 🆕 | 脉冲专家多模态 Occupancy（北大清华） | 等代码 | 今日加入 |
| RadarGSOcc 🆕 | 4D 雷达张量语义 Occupancy（KAIST） | 等代码 | 今日加入 |

dormant（>30 天无变化，轻量复查中）：RaGS（4D 雷达+单目高斯 3D 检测，repo 零提交）——今日复查仍无提交。

---

## 四、检索与核查记录

- watchlist 10 个在册 repo 全部经 `commits.atom` 复查：**均无新提交**（UNIVERSE 10-15 / AD-LiST-JEPA 10-19 / PanoBench 10-21 / DriveMLP 09-09 / SARD 10-22 / ByteCope 10-24 / CLAP-AD 10-20 / VEOcc++ 10-19 / RaGS 2025-10-21 / Expand-and-Prune 持续 404）。
- 12 个 paper-only 项经 GitHub 名字检索 + WebSearch 复查均仍无 repo；RoMo-GS 发现仅新增 github.io 项目页（10-23 建），代码 repo 未出。
- arXiv 近 7 日 cs.CV "autonomous driving" 40 篇过筛：RaSFNet / TerraTransfer++ / FogTTA / Real-Time Radar Panoptic 无代码；RaSFNet、FogTTA、Radar Panoptic 入 watchlist；TerraTransfer++（Applied Intuition，工业界开源意愿低）与 HorizonComposer / GeoV2V（偏视频编辑生成，非感知重点）不入册。
- Drive3R（SJTU 流式几何基础模型）有官方代码，但首码 10-10~10-14，在 7 天窗口外且此前未被报道，按口径不补报。
- GitHub 新建仓库两轮关键词 + topic 检索（occupancy/BEV/3D detection/4D radar/E2E/world model/VLM/融合/online mapping/forecasting）+ OpenDriveLab / NVlabs org 扫描：除上述两项新开源外无达标项目（其余为个人作业/课程项目/无关重名）。
- HuggingFace 扫描：除 sparsedrive-trt-edge 的 ONNX 与 torch-pointcloud 权重集外无新感知模型。

*明日关注：AD-LiST-JEPA 是否兑现顺延承诺；Expand-and-Prune 是否迁库复活。*
