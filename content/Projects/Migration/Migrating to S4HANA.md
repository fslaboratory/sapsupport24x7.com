Requirements and Benefits
Simplification of the Functionality
Principle of One
Each business requirement has only one implementation in the target architecture.
Case: Credit Management
在 ERP 中:
原始版本 SD-BF-CM（SAP Credit Risk Portfolio Management）
	早期实现，功能有限 
高级版本 | FIN-FSCM-CR（SAP Credit Management）
	后期引入，功能丰富，支持集成外部信用信息提供商

在 SAP S/4HANA 中，**仅保留高级版本 FIN-FSCM-CR** 作为目标架构，未来所有创新也将只基于此功能开展。

> ⚠️ **迁移注意**：若当前使用 SD-BF-CM，该功能在 SAP S/4HANA 中已不可用，迁移前必须规划向 FIN-FSCM-CR 的切换。

这就是为什么在迁移至 SAP S/4HANA 之前，必须确认：

- 当前使用的解决方案在 S/4HANA 中是否仍然可用
- 是否需要迁移至新的替代解决方案
- 自定义开发是否引用了在 S/4HANA 中已变更或移除的功能

S4HANA vs. the TraditionalSAP Business Suite
财富上升速度的增长导致创新不再是竞争优势，而是企业生存的必要条件
市场对企业现在的核心要求是：**最大化敏捷性**。
Intelligent Enterprise

从旧版 ERP 迁移至 SAP S/4HANA，绝不仅仅是技术层面的变更，更是企业重新定位自身未来竞争方式的战略机遇。

从旧版 ERP 迁移到更新版本属于**同一产品的升级**；而迁移到 SAP S/4HANA，则是**引入一个全新产品**。

SAP 提供了完整的功能简化清单，称为 **Simplification Item Catalog for SAP S/4HANA**：

- 网址：http://s-prs.co/v581605
- **规划迁移时应重点参考此目录**，以识别所有受影响的功能点

迁移路径的选择: Greenfield and System Conversion

引入SAP HANA 数据库

数据模型
- 省略聚合表（Omitted Aggregates）
- 重新设计 ABAP Dictionary 表（Redesign of Existing ABAP Dictionary Tables）
- 代码下推（Code Pushdown）

**聚合并未消失，而是"内化"进了数据库层：**

代码下推（Code Pushdown）
原来：大量数据在网络与内存之间搬运，成为性能瓶颈。
```
数据库 → 原始数据加载到应用层 → ABAP 内核执行运算 → 返回结果
```
现在：SAP S/4HANA 将部分数据处理逻辑**直接下推到数据库层**执行
```
数据库（原地计算）→ 仅返回结果 → 应用层
```


影响转换时间的关键因素
转换所需时间主要取决于**待转换数据的体量**。
**最佳实践**：
在迁移前对现有数据进行**归档（Archiving）**。
SAP S/4HANA 内置的兼容模式包含读取模块，支持读取已归档的数据，因此归档操作不会影响历史数据的可访问性。

Sizing 估算经验法则
```
主内存需求 ≈ 压缩后数据量 × 2
```
**SAP 建议**：由于 Sizing 结果高度依赖实际压缩率等特定条件，强烈建议在现有 SAP ERP 系统中运行 **Sizing 报告**以获取更准确的估算值。详细 Sizing 信息参见：https://service.sap.com/siz


SAP Fiori User Interfaces
Transactional Apps
Fact Sheets
Analytical Apps
SAP Fiori 应用参考库：http://s-prs.co/v581603

架构层次说明（从前到后）：
```
浏览器（任意设备）
    ↓
SAP Web Dispatcher        ← 建立连接，指向前端服务器
    ↓
Frontend Server
  ├── SAP Fiori Launchpad
  ├── SAP Fiori Apps
  ├── Search（搜索功能）
  └── SAP GUI for HTML    ← 兼容性用途，特殊情况下直接访问后端
    ↓
SAP Gateway               ← 将前端请求分发至各后端应用系统
    ↓
ABAP Backend in SAP S/4HANA
    ↓
SAP HANA Database
```

对于 on-premise SAP S/4HANA 和私有云版本，可以在迁移过程中**不立即在全系统推行 SAP Fiori**。
SAP 决定在 SAP S/4HANA Cloud 公有版（Public Edition）中独家提供 SAP Fiori 作为唯一 UI，不支持 SAP GUI 访问。

系统集成现状评估
四类集成方式与迁移影响
① SAP Process Integration / SAP Process Orchestration
② 授权接口（Authorized Interfaces）
③ 自定义集成（Proprietary Integration）
④ 第三方应用（Third-Party Applications）

迁移工作量的决定因素
```
遵循 SAP 标准化建议程度
        ↑ 越高
        │   · 自定义修改（Modifications）越少
        │   · 使用非侵入式增强（Modification-Free Enhancements）越多
        │   · 依赖标准接口（Standard Interfaces）越多
        ↓ 越低
迁移后所需接口适配工作量
        ↑ 越多
```


SAP S/4HANA Embedded Analytics
其核心定位是：**让任何用户**（不仅限于数据分析专家）都能基于 SAP S/4HANA 应用数据创建并执行实时分析。
技术基础：CDS 视图与虚拟数据模型
```
原生应用表（Native Tables）
        ↓
CDS 视图（Core Data Services Views）
        ↓  组织为
虚拟数据模型（VDM, Virtual Data Model）
        ↓  用户在此层面运行
实时查询（Real-Time Queries on Transactional Data）
```


SAP S/4HANA Cloud, Public Edition
- **主版本升级（Upgrade）**：每季度一次（约每年 4 次），SAP 不允许例外或推迟
- **Hotfix Collections**：每两周导入一次
- 版本命名规则：年份 + 月份，例如 `2311` 代表 2023 年 11 月

运营与维护

软件由 **SAP 负责运营和维护**。更新方式：

- **主版本升级（Upgrade）**：每季度一次（约每年 4 次），SAP 不允许例外或推迟
- **Hotfix Collections**：每两周导入一次
- 版本命名规则：年份 + 月份，例如 `2311` 代表 2023 年 11 月

系统景观与实施

提供标准的**三系统景观（Three-System Landscape）**：

```
开发系统 → 测试系统 → 生产系统
```

可额外购买：Starter 系统、Demo 系统、Sandbox 系统。

- 业务流程通过 **SAP Central Business Configuration** 进行配置
- 客户**无法自行创建额外 Tenant**
- 因此，公有云版**只支持新实施（New Implementation）**，不支持系统转换或选择性数据迁移
- 用户界面通过 **SAP Fiori** 访问

| 迁移方式                                   | On-Premise | Private Edition | Public Edition |
| -------------------------------------- | ---------- | --------------- | -------------- |
| 新实施（New Installation）                  | ✓          | ✓               | ✓              |
| 系统转换（System Conversion）                | ✓          | ✓               | —              |
| 选择性数据迁移（Migration with Selective Data） | ✓          | ✓               | —              |

扩展限制

- **不允许**修改 SAP 代码（与 On-Premise 版不同）
- **不允许**安装合作伙伴 App（On-Premise 方式）
- 可用的扩展方式：
    - Key-user extensibility（关键用户扩展）
    - Developer extensibility（开发者扩展）
    - Side-by-side extensibility（旁路扩展）

业务流程实施基于**解决方案包（SAP Best Practices）**，包含预配置内容，可通过配置和扩展进行定制。


RISE with SAP

在技术层面，RISE with SAP 的核心是运行于**私有云**中的 SAP S/4HANA（即 Private Edition）。其最突出的合同特征是：**所有内容由 SAP 作为单一来源（Single Source）提供**，包括超大规模云服务商（Hyperscaler）的选择也已纳入合同，无需与 Hyperscaler 单独签约，所有 SLA 均由 SAP 统一承担。

与 GROW with SAP 的对比

SAP 同时提供面向**中端市场**的类似计划：

| 维度 | RISE with SAP | GROW with SAP |
|---|---|---|
| 目标客户 | 大型企业、现有 SAP 客户 | 中端市场、新兴企业 |
| 核心产品 | SAP S/4HANA Cloud, private edition | SAP S/4HANA Cloud, public edition |
| 包含 SAP BTP | ✓ | ✓ |
| 定位 | 业务转型与云迁移 | 快速上云启动 |

GROW with SAP 主要面向**新企业**，帮助其快速以云的方式使用 SAP。

SAP BTP 的中心角色

```
RISE with SAP
    └── SAP S/4HANA（核心 ERP）
    └── SAP BTP（扩展与集成平台）
            ├── 自定义扩展开发
            ├── 端到端流程集成
            └── 与第三方/合作伙伴系统集成
```

具体演进对应关系：

| 旧有组件                                            | SAP BTP 对应产品                                             |
| ----------------------------------------------- | -------------------------------------------------------- |
| SAP NetWeaver AS ABAP                           | ABAP Cloud（SAP BTP, ABAP Environment）                    |
| SAP Process Integration / Process Orchestration | SAP Integration Suite（前身：SAP Cloud Platform Integration） |

SAP BTP 今天与 SAP NetWeaver 昔日的角色相同：**所有 SAP 产品的基础技术底座**——一切产品要么构建于其上，要么可通过它进行扩展。


SAP BTP 对于 SAP S/4HANA 迁移项目具有直接价值：

- 可在 ERP 迁移**之前**，将不兼容 SAP HANA 数据库的自定义代码迁移至 SAP BTP，使 ABAP 系统代码线保持干净和兼容
- SAP BTP 的 ABAP Cloud 环境使扩展开发能够以云模型运行，同时保持 SAP S/4HANA 核心系统的"Clean Core"特性
- 支持将遗留流程迁移至 SAP BTP，避免这些流程在迁移至 SAP S/4HANA 时丢失

SAP BTP 可理解为一个综合工具箱，按功能域分类如下：

```
SAP BTP 工具全景
├── 应用开发（Application Development）
│   ├── SAP Build Apps
│   ├── SAP Build Work Zone
│   ├── SAP Business Application Studio
│   └── ABAP Cloud（SAP BTP, ABAP Environment）
├── 自动化（Automation）
│   ├── SAP Build Process Automation
│   └── SAP Task Center
├── 集成（Integration）
│   ├── SAP Integration Suite
│   └── SAP Master Data Integration
├── 数据与分析（Data & Analytics）
│   ├── SAP Analytics Cloud
│   ├── SAP Datasphere
│   ├── SAP Data Intelligence Cloud
│   ├── SAP Master Data Governance
│   └── SAP HANA Cloud
└── AI
    ├── SAP AI Services
    └── SAP AI Core
```


三阶段任务划分

阶段一：准备（Preparation）

- 分析现有业务流程实施情况
- 与 SAP S/4HANA 创新功能进行对比
- 识别必要的集成场景
- 在源系统中执行预检查（已使用功能、行业增强、客制代码、第三方增强）
- 在源系统中执行必要的预转换

阶段二：技术实施（Technical Implementation）

- 安装 SAP S/4HANA、SAP HANA 数据库及相关应用
- 适配技术基础设施
- Customizing 配置

阶段三：流程适配（Process Adaptation）

- 在 SAP S/4HANA 中适配客制程序
- 开发新的或增强的业务流程以利用 SAP S/4HANA 创新
- 适配集成场景
- 定制 SAP Fiori UI


新实施检查清单

1. 确定目标状态（运营模式与实例分布）——支持 On-Premise、SAP HANA Enterprise Cloud、SaaS 云
2. 识别所需的新业务功能，参考 Simplification Item Catalog（需 SAP S-user，https://me.sap.com/#sic），统计各功能的用户数量
3. 对现有 SAP ERP 源系统：以模拟模式执行 Simplification Item Check（见 SAP Note 2502552）
4. 使用 Custom Code Migration Worklist 分析客制增强（http://s-prs.co/v581621；更多信息见第 3 章 3.6 节）
5. （仅 On-Premise）：执行 Sizing（https://service.sap.com/sizing）
6. 在源系统中执行数据清洗与归档（如可行）
7. 调整项目容量规划并确认迁移场景
8. 搭建目标系统
9. 启动 SAP S/4HANA Migration Cockpit 并传输数据
10. 检查结果
11. （仅 On-Premise）：搭建 SAP Fiori 前端服务器
12. 执行增量配置（Delta Configuration）
13. 执行最终测试
14. 向用户推出新流程


系统转换检查清单

1. 确定目标状态（运营模式与实例分布）——系统转换仅支持 On-Premise 或 SAP HANA Enterprise Cloud
2. 识别所需的新业务功能
3. 对比 Simplification Item Catalog 中当前使用的功能（https://launchpad.support.sap.com/#sic），统计各功能用户数量
4. 对现有 SAP ERP 源系统：以模拟模式执行 Simplification Item Check（见 SAP Note 2502552）
5. 使用 Custom Code Migration Worklist 分析客制增强（http://s-prs.co/v581624；更多信息见第 3 章 3.6 节）
6. 执行 Sizing（https://service.sap.com/sizing）
7. 在源系统中执行数据清洗与归档（如可行）
8. 调整项目容量规划并确认迁移场景
9. 在 Maintenance Planner 中规划系统转换（http://s-prs.co/v581625）
10. 在 SUM 中选择标准转换或停机优化转换，按需调整 Sizing
11. 执行维护事务
12. 检查结果
13. 搭建 SAP Fiori 前端服务器
14. 执行增量配置（Delta Configuration）
15. 执行最终测试
16. 向用户推出新流程


确定适配工作范围的主要工具

在各转换项目阶段，以下工具帮助识别所需的适配工作：

**Simplification List（简化列表）**
描述将 SAP ERP 转换至 SAP S/4HANA 时，各功能潜在的适配需求。详见 Section 7.2.3。

**Maintenance Planner**
在组件级别检查源系统，提供必要的软件归档文件，尤其核查 Add-on（SAP 及合作伙伴）与业务功能是否与 S/4HANA 目标版本兼容。详见 Section 7.2.4。

**Simplification Item Checks（SI Checks）**
通过 SAP Notes 提供的程序，在 SAP ERP 源系统中运行，识别出简化列表中**实际需要**在本系统中处理的适配项。详见 Section 7.2.5。

**Custom Code Analysis（自定义代码分析）**
识别自定义程序在迁移至 SAP S/4HANA 时所需的适配工作，包括因数据结构变更或功能范围变化引发的代码修改。详见 Section 7.2.6 及 Chapter 8, Section 8.2.3。

**Software Update Manager（SUM）**
用于在系统转换期间安装 SAP S/4HANA 软件，以及后续更新和升级。提供减少迁移项目停机时间的选项，通过 SL Toolsets 定期更新。详见 Chapter 8, Section 8.1。

**SAP Readiness Check for SAP S/4HANA**
免费服务，通过清晰的仪表板呈现系统转换中最关键的方面，可在生产和开发系统（或对应的系统副本）上运行。主要功能：
- 相关简化项及自定义开发适配信息
- 数据库大小调整信息和建议
- 活跃业务功能与 S/4HANA 的兼容性评估
- 基于使用数据的 SAP Fiori 应用推荐



三大项目阶段概览

准备与规划阶段（Prepare and Planning Phase）
适配与测试阶段（Adaptation and Test Phase）
执行阶段（Execution Phase）


五轮转换循环

**第 1 轮：初始测试系统转换（沙箱）**
- 获取技术转换知识，供后续轮次使用
- 最终用户熟悉 SAP S/4HANA 业务功能
- 对自定义开发进行基于新软件的分析与测试
- **建议**：保留已转换的沙箱系统，用于 Custom Code Migration 应用的 ABAP 代码适配分析（Section 7.2.6）
- **建议**：同步建立系统转换操作手册，记录每个周期的技术和功能活动及所需时间

**第 2 轮：开发系统转换（DEV）**
- 将自定义开发适配至 S/4HANA 解决方案范围和数据结构
- 实施强制性适配（未在源系统中提前实施的部分）
- 实施可选适配以最大化转换收益
- **重要**：转换后尽量减少进一步变更，需提前协调并通知"系统冻结（system freeze）"

**第 3 轮：QA 系统转换**
- 起草 cut-over 计划（列出 cut-over 周末从头到尾的所有活动）
- 从 DEV 系统导入适配内容（业务流程调整和自定义开发适配）
- 测试适配后的业务流程和新 SAP Fiori UI

**第 4 轮：生产系统转换测试（沙箱）**
- 在沙箱中以与 PRD 系统相同的条件测试生产转换
- 准备减少停机时间的优化措施
- 最终确定 cut-over 计划
- 建议进行多次测试迭代

**第 5 轮：生产系统转换（PRD）**
- 按照 cut-over 计划执行


双开发系统过渡期管理

从 DEV 系统转换开始，直到 PRD 生产系统转换完成，需要同时维护**两套开发系统**：一套在原 SAP Business Suite 架构中，一套在新 SAP S/4HANA 架构中。

**过渡期策略要点：**
- 须制定策略：SAP ERP 系统组在此期间的变更如何同步到 SAP S/4HANA 系统组
- 若两个开发系统都需要 ABAP 开发，可参考 **SAP Note 2652106** 评估是否可使用 SAP Solution Manager 的 **Re


架构层面的关键决策

以下几项架构决策需要在项目准备阶段明确：

- **SAP Fiori 前端服务器部署方式**：嵌入式（embedded）还是独立 Hub，详见 Chapter 8, Section 8.2.4
- **应用服务器操作系统**：确认 SAP ERP 系统的操作系统在 S/4HANA 中受支持（SAP Note 2696472）
- **Hub 系统互操作性**：如 SAP Process Integration，SAP Note 2251604 提供版本支持信息
- **运行环境**：自建数据中心（on-premise）还是私有云（IaaS）




