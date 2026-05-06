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
SAP 决定在 **SAP S/4HANA Cloud 公有版（Public Edition）**中**独家提供 SAP Fiori 作为唯一 UI**，不支持 SAP GUI 访问。

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
