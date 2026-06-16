# 現代軟體發展流程 (Modern SDLC Framework)

> 融合 PMI BA、PMBOK、RUP、Agile、DDD 與 TDD 的完整軟體發展流程

## 專案目的

本專案定義了一套完整的軟體發展流程規範（Software Development Lifecycle, SDLC），融合了業界成熟方法論的精神與最佳實踐：

1. **PMI Business Analysis** — 從企業痛點到商業目標的完整商業分析方法
2. **PMBOK 專案管理** — 全流程的控管原則與方法
3. **RUP (Rational Unified Process)** — 9大工作流程與4個發展階段
4. **Agile 開發** — 迭代、增量、持續交付的敏捷精神
5. **Domain-Driven Design (DDD)** — 戰略設計與戰術設計的領域建模
6. **Test-Driven Development (TDD)** — 測試先行的開發流程

## 核心設計原則

### 1. 端到端完整循環
從企業痛點發現，經過目標設定、解決方案設計、系統開發、上線部署，到成效評估與持續改善，形成完整的發展循環。

### 2. 分階段控管
採用 RUP 四階段模型（Inception → Elaboration → Construction → Transition），每個階段有明確的里程碑與交付物。

### 3. 角色與工作流程明確
基於 RUP 9大工作流程（Business Modeling、Requirements、Analysis & Design、Implementation、Test、Deployment、Configuration & Change Management、Project Management、Environment），定義各階段的角色責任與產出。

### 4. 敏捷迭代精神
在 Construction 階段採用迭代式開發，每次迭代都交付可運作的軟體增量，持續整合與持續交付。

### 5. 領域驅動設計
透過 DDD 戰略設計（Event Storming、Bounded Context、Ubiquitous Language）與戰術設計（Aggregate、Entity、Value Object、Domain Service）建立清晰的領域模型。

### 6. 測試驅動開發
在實作階段採用 TDD 與 Harness Engineering，先建立測試基礎設施，再開發業務邏輯，確保高品質與可維護性。

## 流程架構

本流程包含**主流程**（Main Process）與**支援性流程/活動**（Supporting Processes）：

### 主流程（Main Process）

```
Phase 1: Business Analysis          (RUP Inception)
   ↓
Phase 2: System Analysis & Design   (RUP Elaboration)
   ↓
Phase 3: Development                (RUP Construction)
   ↓
Phase 4: Deployment & Transition    (RUP Transition)
   ↓
Phase 5: Production & Evaluation    (Continuous)
```

### 支援性流程（Supporting Processes）

貫穿整個生命週期的控管與支援活動：

- **專案管理** (Project Management) — 基於 PMBOK 的計畫、執行、監控、收尾
- **配置管理** (Configuration & Change Management) — Git 策略、分支模型、版本控制
- **品質管理** (Quality Assurance) — 程式碼審查、自動化測試、品質閘門
- **風險管理** (Risk Management) — 識別、評估、應對、監控
- **環境管理** (Environment Management) — 開發、測試、預生產、生產環境

## 文件結構

```
Process/
├── README.md                           ← 本文件（總覽）
│
├── 01-Main-Process/                    ← 主流程規範
│   ├── Phase1-Business-Analysis.md     ← 商業分析階段
│   ├── Phase2-System-Analysis.md       ← 系統分析階段
│   ├── Phase3-Development.md           ← 開發階段
│   ├── Phase4-Deployment.md            ← 部署與轉移階段
│   └── Phase5-Production-Evaluation.md ← 生產與評估階段
│
├── 02-Supporting-Processes/            ← 支援性流程規範
│   ├── Project-Management.md           ← 專案管理流程
│   ├── Configuration-Management.md     ← 配置管理流程
│   ├── Quality-Assurance.md            ← 品質保證流程
│   ├── Risk-Management.md              ← 風險管理流程
│   └── Environment-Management.md       ← 環境管理流程
│
├── 03-Methodology-Guides/              ← 方法論詳細指引
│   ├── BA-PGOW-Framework.md            ← P/G/O/W 商業分析框架
│   ├── DDD-Strategic-Design.md         ← DDD 戰略設計指引
│   ├── DDD-Tactical-Design.md          ← DDD 戰術設計指引
│   ├── TDD-Practice.md                 ← TDD 實踐指引
│   ├── Harness-Engineering.md          ← Harness Engineering 方法
│   └── Event-Storming.md               ← Event Storming 工作坊指引
│
├── 04-Templates/                       ← 可重複使用的範本
│   ├── business-analysis/              ← 商業分析範本
│   ├── system-analysis/                ← 系統分析範本
│   ├── architecture/                   ← 架構設計範本
│   ├── development/                    ← 開發階段範本
│   └── deployment/                     ← 部署階段範本
│
└── 05-Adaptations/                     ← 不同系統類型的適配指引
    ├── Web-Application-SDLC.md         ← Web 應用系統適配
    ├── Data-Analytics-SDLC.md          ← 資料分析系統適配
    ├── ML-AI-System-SDLC.md            ← ML/AI 系統適配
    └── Integration-System-SDLC.md      ← 整合系統適配
```

## 適用場景

### 本流程完整適用於：
- 中大型企業應用系統
- 複雜業務邏輯系統
- 需要長期維護演進的系統
- 多團隊協作的系統

### 可簡化的場景：
- **快速原型驗證 (Spike)** — 跳過 Elaboration，直接從最小化的 BA 進入 Construction
- **單一功能微服務** — 簡化 BA 層級，但保留 DDD 戰術設計與 TDD
- **維護性變更** — 僅執行受影響的階段活動

### 需額外適配的場景：
- **資料分析/ML 系統** — 需加強資料探索、特徵工程、模型訓練與評估流程
- **純整合系統** — 強化 ACL (Anti-Corruption Layer) 設計與外部系統契約管理
- **嵌入式/IoT 系統** — 加入硬體限制分析與即時性需求分析

## RUP 四階段與里程碑

| RUP Phase | 中文名稱 | 主要目標 | 里程碑 | 關鍵決策 |
|-----------|---------|----------|--------|----------|
| **Inception** | 初始階段 | 建立業務案例與系統範圍 | Lifecycle Objectives (LCO) | Go/No-Go 決策 |
| **Elaboration** | 細化階段 | 建立穩定架構，降低主要風險 | Lifecycle Architecture (LCA) | 架構可行性確認 |
| **Construction** | 建構階段 | 迭代開發，交付可用系統 | Initial Operational Capability (IOC) | 系統準備就緒 |
| **Transition** | 轉移階段 | 部署上線，使用者培訓 | Product Release (PR) | 生產環境上線 |

## PMBOK 知識領域映射

本流程覆蓋 PMBOK 10大知識領域的關鍵活動：

| PMBOK 知識領域 | 本流程對應活動 | 主要文件 |
|---------------|---------------|----------|
| 整合管理 (Integration) | 專案章程、階段評審、變更控制 | Project-Management.md |
| 範圍管理 (Scope) | P/G/O/W 框架、User Story、MoSCoW | Phase1-Business-Analysis.md |
| 時程管理 (Schedule) | 迭代計畫、Sprint Planning | Project-Management.md |
| 成本管理 (Cost) | 預算評估、ROI 分析 | Phase1-Business-Analysis.md |
| 品質管理 (Quality) | TDD、Code Review、Quality Gates | Quality-Assurance.md |
| 資源管理 (Resource) | 團隊組成、RACI 矩陣 | Project-Management.md |
| 溝通管理 (Communications) | Ubiquitous Language、文件標準 | DDD-Strategic-Design.md |
| 風險管理 (Risk) | 風險識別、應對計畫 | Risk-Management.md |
| 採購管理 (Procurement) | 技術選型、外部服務評估 | Phase2-System-Analysis.md |
| 利害關係人管理 (Stakeholder) | 利害關係人分析、訪談 | Phase1-Business-Analysis.md |

## 使用指引

### 第一次使用本流程

1. **閱讀本 README** — 了解整體架構與設計原則
2. **評估專案特性** — 確認是否需要完整流程或可簡化
3. **選擇適配指引** — 根據系統類型參考 `05-Adaptations/` 中的指引
4. **依序執行主流程** — 從 Phase 1 開始，依序完成各階段
5. **應用支援性流程** — 全程執行專案管理、配置管理等支援活動
6. **參考方法論指引** — 在執行特定方法時（如 Event Storming、TDD），參考 `03-Methodology-Guides/`
7. **使用範本加速** — 從 `04-Templates/` 取用適合的範本

### 快速檢索

- **我需要做商業分析** → `01-Main-Process/Phase1-Business-Analysis.md` + `03-Methodology-Guides/BA-PGOW-Framework.md`
- **我需要做領域建模** → `01-Main-Process/Phase2-System-Analysis.md` + `03-Methodology-Guides/DDD-Strategic-Design.md`
- **我需要開始寫程式** → `01-Main-Process/Phase3-Development.md` + `03-Methodology-Guides/TDD-Practice.md`
- **我需要設定 Git 流程** → `02-Supporting-Processes/Configuration-Management.md`
- **我需要規劃專案時程** → `02-Supporting-Processes/Project-Management.md`

## 版本與維護

**當前版本**: v1.0.0  
**最後更新**: 2025-01-24  
**維護者**: Ben Lin  

### 版本歷史

- **v1.0.0** (2025-01-24) — 初始版本，定義完整流程框架

### 貢獻與回饋

本流程基於實務經驗持續改進。如有建議或發現問題，請透過以下方式回饋：

1. 建立 GitHub Issue 描述問題或建議
2. 提交 Pull Request 改善文件
3. 在實際專案中試用並記錄經驗

## 授權

MIT License - 自由使用、修改與分享，但請保留原作者資訊。

---

## 快速開始範例

假設你要開發一個「人力需求審批系統」：

```
1. Phase 1: Business Analysis (Week 1-2)
   - 訪談 HR、部門主管、財務
   - 執行 P/G/O/W 分析
   - 產出: 問題陳述、商業目標、User Stories
   
2. Phase 2: System Analysis (Week 3-4)
   - Event Storming 工作坊
   - 識別 Bounded Contexts (Request Context, Budget Context, Notification Context)
   - 戰術設計: Entity, Value Object, Aggregate
   - 產出: 領域模型、Context Map、Ubiquitous Language Glossary
   
3. Phase 3: Development (Week 5-10)
   - Sprint 1: Harness Infrastructure + 提交請求功能
   - Sprint 2: 審批工作流程
   - Sprint 3: 預算檢查整合
   - Sprint 4: 通知與報表
   - 每個 Sprint 採用 TDD，測試先行
   
4. Phase 4: Deployment (Week 11)
   - CI/CD Pipeline 建置
   - Docker 容器化
   - 部署到測試環境 → UAT → 生產環境
   
5. Phase 5: Production (Continuous)
   - 監控與告警
   - 使用者回饋收集
   - 每月評估成效 vs 商業目標 KPI
   - 規劃下一階段改進
```

---

**下一步**: 開始閱讀 [Phase 1: Business Analysis](01-Main-Process/Phase1-Business-Analysis.md)
