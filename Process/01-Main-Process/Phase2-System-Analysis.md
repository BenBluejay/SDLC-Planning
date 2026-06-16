# Phase 2: System Analysis & Design (系統分析與設計階段)

> **RUP Phase**: Elaboration  
> **RUP Disciplines**: Analysis & Design, Architecture  
> **里程碑**: Lifecycle Architecture (LCA) — 穩定架構、主要風險已降低  
> **關鍵決策**: 架構可行性確認

## 階段目的

將 Phase 1 產出的 User Stories 轉化為**系統架構**與**領域模型**,透過 Domain-Driven Design (DDD) 方法識別系統邊界、定義領域語言、設計領域模型。

**核心問題**：
- 系統應該如何拆分？邊界在哪裡？
- 領域中有哪些核心概念？它們如何關聯？
- 不同子系統之間如何溝通？
- 外部系統如何整合？

## 輸入 (Inputs)

| 來源 | 輸入物 |
|------|--------|
| Phase 1 | User Story 清單 (BA-09) |
| Phase 1 | 商業目標與 KPI (BA-03) |
| Phase 1 | TO-BE 流程圖 (BA-05) |
| Phase 1 | Ubiquitous Language 詞彙表（初版） |
| Phase 1 | 非功能性需求 (BA-10) |

## 輸出 (Outputs)

| 編號 | 產出物 | 用途 | 格式 |
|------|--------|------|------|
| SA-01 | 子域劃分文件 | 識別核心域、支撐域、通用域 | Markdown |
| SA-02 | **Bounded Context 地圖** | 定義語義邊界與所有權 | Mermaid / Excalidraw |
| SA-03 | **Ubiquitous Language 詞彙表（完整版）** | 每個 Context 的領域術語 | Markdown 表格 |
| SA-04 | **Context Map** | Context 之間的關係圖 | Mermaid / Excalidraw |
| SA-05 | Entity 清單 | 每個 Context 的 Entity 定義 | Markdown |
| SA-06 | Value Object 清單 | 每個 Context 的 Value Object | Markdown |
| SA-07 | **Aggregate 設計文件** | Aggregate Root、邊界、事務範圍 | Markdown |
| SA-08 | **Domain Event 目錄** | 事件定義與生產者/消費者 | Markdown 表格 |
| SA-09 | Repository 介面定義 | 每個 Aggregate Root 的 Repository ABC | Python 程式碼 |
| SA-10 | Domain Service 契約 | 跨 Aggregate 的無狀態服務 | Python Protocol/ABC |
| SA-11 | Anti-Corruption Layer 規格 | 外部系統整合翻譯層 | Markdown + 程式碼 |
| SA-12 | 系統架構文件 | 分層架構、Port-Adapter 模式 | Markdown + 圖 |

## 方法論：Domain-Driven Design (DDD)

DDD 分為兩個階段：

### 戰略設計 (Strategic Design)
- 識別 Bounded Contexts（語義邊界）
- 建立 Ubiquitous Language（通用語言）
- 繪製 Context Map（Context 關係）
- 定義 Anti-Corruption Layer（防腐層）

### 戰術設計 (Tactical Design)
- Entity（有身份的實體）
- Value Object（無身份的值物件）
- Aggregate（一致性邊界）
- Domain Event（領域事件）
- Repository（倉儲）
- Domain Service（領域服務）

詳細方法請參考：
- [`../03-Methodology-Guides/DDD-Strategic-Design.md`](../03-Methodology-Guides/DDD-Strategic-Design.md)
- [`../03-Methodology-Guides/DDD-Tactical-Design.md`](../03-Methodology-Guides/DDD-Tactical-Design.md)
- [`../03-Methodology-Guides/Event-Storming.md`](../03-Methodology-Guides/Event-Storming.md)

---

## Part A: 活動規範 (Activity Specification)

### A.1 為什麼需要系統分析與設計階段

**價值**：
- **清晰的系統邊界** — 避免「大泥球」架構，每個 Bounded Context 職責清楚
- **一致的領域語言** — 開發者、領域專家、利害關係人使用相同術語
- **可測試的設計** — Aggregate 邊界清晰，便於單元測試與 TDD
- **可演進的架構** — Context 之間低耦合，可獨立演進

**常見失敗模式**（沒有 SA 階段時）：
- 所有邏輯塞在一個大 Service 類別，難以測試與維護
- 術語不一致，「Request」在不同地方意義不同，溝通成本高
- 資料庫 schema 驅動設計，而非領域模型驅動
- 外部系統耦合過深，一旦外部 API 改變，內部大範圍修改

### A.2 流程步驟

#### 步驟 1: 子域劃分 (Subdomain Decomposition)

| 子步驟 | 如何執行 | 產出 |
|--------|----------|------|
| 1.1 從 User Stories 萃取領域概念 | 提取名詞與動詞，識別業務流程 | 初步領域概念清單 |
| 1.2 劃分核心域/支撐域/通用域 | 根據競爭力與複雜度分類 | 子域分類表 (SA-01) |
| 1.3 確認自建/外購決策 | 通用域考慮外購，核心域自建 | 技術決策記錄 |

**子域分類標準**：

| 子域類型 | 特徵 | 處理策略 | 範例 |
|----------|------|----------|------|
| **核心域 (Core)** | 競爭優勢來源、最複雜 | 自建，投入最多資源 | 審批工作流程 + 業務規則 |
| **支撐域 (Supporting)** | 必要但不是差異化來源 | 自建但簡化 | 預算追蹤、通知歷史 |
| **通用域 (Generic)** | 業界通用解決方案 | 外購或使用 SaaS | 使用者認證、郵件發送 |

#### 步驟 2: Event Storming 工作坊

Event Storming 是快速識別 Bounded Contexts 與 Domain Events 的協作方法。

| 子步驟 | 如何執行 | 產出 |
|--------|----------|------|
| 2.1 準備工作坊 | 邀請領域專家、開發者、產品經理 | 參與者清單、便利貼、白板 |
| 2.2 識別 Domain Events | 橙色便利貼：「發生了什麼事」 | Domain Event 初步清單 |
| 2.3 識別 Commands | 藍色便利貼：「使用者做了什麼」 | Command 清單 |
| 2.4 識別 Aggregates | 黃色便利貼：「誰處理 Command」 | Aggregate 候選清單 |
| 2.5 劃分 Bounded Contexts | 依事件群組與職責邊界切分 | Bounded Context 草圖 |

**Event Storming 產出範例**（人力需求審批系統）：

```
Timeline（時間軸，從左到右）：

[申請人] 填寫表單 → [RequestSubmitted] → [主管] 審核 → [RequestReviewed] 
   → [HR] 審核 → [HRReviewed] → [系統] 檢查預算 → [BudgetChecked] 
   → [RequestApproved] / [RequestRejected]

識別出的 Bounded Contexts:
- Request Context（處理申請生命週期）
- Budget Context（處理預算保留與扣除）
- Notification Context（處理通知發送）
```

詳細工作坊流程參考：[`../03-Methodology-Guides/Event-Storming.md`](../03-Methodology-Guides/Event-Storming.md)

#### 步驟 3: 識別 Bounded Contexts

| 子步驟 | 如何執行 | 產出 |
|--------|----------|------|
| 3.1 從 Event Storming 萃取 Contexts | 依事件群組、職責、所有權切分 | 初步 Context 清單 |
| 3.2 驗證 Context 邊界 | 檢查是否有不同的狀態機、生命週期、規則 | Context 邊界驗證文件 |
| 3.3 定義 Context 所有權 | 每個 Context 指派負責團隊/角色 | Context 所有權表 |
| 3.4 繪製 Bounded Context 地圖 | 視覺化所有 Contexts 與邊界 | Bounded Context 地圖 (SA-02) |

**如何識別 Bounded Context**：

| 信號 | 解讀 | 範例 |
|------|------|------|
| 不同術語指同一概念 | 不同 Context | 「申請人」(HR) vs「員工」(薪資) |
| 不同生命週期/狀態機 | 不同 Context | Request 生命週期 vs Budget 生命週期 |
| 不同利害關係人擁有 | 不同 Context | HR 擁有審批、財務擁有預算 |
| 不同業務規則 | 不同 Context | 審批規則 vs 預算分配規則 |

**Bounded Context 地圖範例**：

```
┌─────────────────────┐     ┌──────────────────────┐
│  Request Context    │     │   Budget Context     │
│                     │     │                      │
│  HeadcountRequest   │     │  DepartmentBudget    │
│  (Aggregate Root)   │     │  (Aggregate Root)    │
│  ApprovalStatus     │     │  BudgetAllocation    │
│  (Value Object)     │     │  (Entity)            │
│                     │     │                      │
│  RequestSubmitted ▶─┼─────┼─▶ BudgetReserved    │
│  RequestApproved ▶──┼─────┼─▶ BudgetCommitted   │
└─────────┬───────────┘     └──────────────────────┘
          │
          │ NotificationContext
          ▼
    ┌──────────────────┐
    │  Notification     │
    │  (Value Object)   │
    └──────────────────┘
```

#### 步驟 4: 建立 Ubiquitous Language 詞彙表

| 子步驟 | 如何執行 | 產出 |
|--------|----------|------|
| 4.1 從 Phase 1 詞彙表延伸 | 補充 Event Storming 發現的術語 | 擴充詞彙表 |
| 4.2 為每個 Context 定義術語 | 每個 Context 有獨立的術語定義 | 分 Context 的詞彙表 (SA-03) |
| 4.3 識別並拒絕別名 | 明確記錄不使用的術語 | 拒絕的別名清單 |
| 4.4 領域專家驗證 | 與領域專家確認術語正確性 | 驗證簽核 |

**Ubiquitous Language 詞彙表格式**：

```markdown
### Request Context

| 術語 | 定義 | 拒絕的別名 |
|------|------|-----------|
| HeadcountRequest | 部門主管提出的人力需求申請，包含人數、成本、理由 | 「HC 申請」、「開缺」、「招募需求」 |
| ApprovalStatus | 審批流程狀態 (DRAFT/SUBMITTED/MANAGER_REVIEWED/HR_REVIEWED/APPROVED/REJECTED) | 「階段」、「狀態」、「進度」 |
| ReviewAction | 單一審批者的決策（同意/退回）+ 意見 + 時間戳 | 「投票」、「簽核」 |

### Budget Context

| 術語 | 定義 | 拒絕的別名 |
|------|------|-----------|
| DepartmentBudget | 部門在一個會計期間的總預算額度 | 「預算線」、「額度」 |
| BudgetReservation | 已提交但未核准的申請所保留的金額 | 「Hold」、「凍結」 |
| BudgetCommitment | 已核准申請的確定支出 | 「實際」、「已用」 |
```

#### 步驟 5: Context Mapping（定義 Context 關係）

| 子步驟 | 如何執行 | 產出 |
|--------|----------|------|
| 5.1 識別 Context 之間的依賴 | 哪個 Context 需要另一個 Context 的資料？ | 依賴關係表 |
| 5.2 定義關係模式 | Upstream/Downstream、Shared Kernel、Conformist、ACL | 關係模式分類 |
| 5.3 繪製 Context Map | 視覺化所有關係 | Context Map (SA-04) |
| 5.4 識別需要 ACL 的地方 | 外部系統或模型衝突的 Context | ACL 需求清單 |

**Context 關係模式**：

| 關係模式 | 定義 | 何時使用 | 範例 |
|----------|------|----------|------|
| **Upstream/Downstream** | 下游依賴上游,但有自己的模型 | 下游需要上游資料但有不同規則 | Budget Context 依賴 Request Context |
| **Shared Kernel** | 兩個 Context 共享一小部分模型 | 緊密協作，但保持小範圍共享 | Request ID 在多個 Context 共享 |
| **Conformist** | 下游完全接受上游模型 | 下游沒有話語權，簡化整合 | Notification Context 接受 Request 的事件格式 |
| **Anti-Corruption Layer** | 翻譯層保護下游模型不被上游污染 | 整合外部系統或遺留系統 | 整合外部 HR 系統需要 ACL |

**Context Map 範例**：

```
Request Context (Upstream)
    │ Domain Event: RequestSubmitted, RequestApproved
    ↓
Budget Context (Downstream) ← 需要 Request 的資料但有自己的模型
    │
    └─ BudgetReservation, BudgetCommitment

Request Context
    │ Domain Event: RequestApproved
    ↓
Notification Context (Conformist) ← 完全接受 Request 的事件格式

External HR System
    │ ACL 翻譯
    ↓
Request Context ← 透過 ACL 保護內部模型
```

#### 步驟 6: DDD 戰術設計 — Entity, Value Object, Aggregate

**注意**：戰術設計的詳細方法請參考 [`../03-Methodology-Guides/DDD-Tactical-Design.md`](../03-Methodology-Guides/DDD-Tactical-Design.md)，這裡只列出流程步驟。

##### 6.1 識別 Entity

| 特徵 | 識別方法 | 範例 |
|------|----------|------|
| 有身份延續性 | 問「哪一個？」有意義 | HeadcountRequest (用 ID 識別) |
| 有生命週期 | 會被追蹤、修改、查詢 | Request 從 DRAFT → APPROVED |
| 需要被持久化 | 需要儲存與檢索 | Request, Budget |

**Entity 清單產出 (SA-05)**：

```markdown
### Request Context Entities

| Entity | 識別鍵 | 屬性 | 生命週期 |
|--------|--------|------|----------|
| HeadcountRequest | request_id (UUID) | applicant, department, title, level, headcount, cost, reason, status, created_at, updated_at | DRAFT → SUBMITTED → MANAGER_REVIEWED → HR_REVIEWED → APPROVED/REJECTED |
```

##### 6.2 識別 Value Object

| 特徵 | 識別方法 | 範例 |
|------|----------|------|
| 無身份，值相等即相等 | 只關心「是什麼」不關心「哪一個」 | Money(100, "TWD") |
| 不可變 | 建立後不可修改 | ReviewAction(reviewer, decision, comment, timestamp) |
| 可替換 | 換一個值相同的物件不影響語義 | ApprovalStatus.SUBMITTED |

**Value Object 清單產出 (SA-06)**：

```markdown
### Request Context Value Objects

| Value Object | 屬性 | 不可變性 | 用途 |
|--------------|------|----------|------|
| Money | amount (Decimal), currency (str) | 是 | 表示金額，支援加減運算 |
| ApprovalStatus | Enum (DRAFT, SUBMITTED, ...) | 是 | 表示審批狀態 |
| ReviewAction | reviewer (str), decision (Enum), comment (str), timestamp (datetime) | 是 | 記錄單次審批動作 |
```

##### 6.3 設計 Aggregate

| 原則 | 說明 | 範例 |
|------|------|------|
| 一個 Aggregate = 一個事務 | 保存或載入整個 Aggregate 為原子操作 | HeadcountRequest Aggregate |
| 只透過 Root 存取內部 | 外部不直接修改內部 Entity | Request.submit() 而非直接改 status |
| 跨 Aggregate 只用 ID 引用 | 不持有其他 Aggregate 的物件引用 | Request 引用 department_id,不持有 Budget 物件 |
| 保持小巧 | 避免 10+ Entity 的大 Aggregate | Request 本身是 Root,沒有內部 Entity |

**Aggregate 設計文件產出 (SA-07)**：

```markdown
### Request Context Aggregates

#### Aggregate: HeadcountRequest

- **Aggregate Root**: HeadcountRequest (Entity)
- **內部 Entities**: 無
- **Value Objects**: ApprovalStatus, ReviewAction, Money
- **事務邊界**: 建立、提交、審批、核准/退回都是原子操作
- **Repository**: RequestRepository (save, get, list_by_department, ...)
- **Domain Events**: RequestSubmitted, RequestReviewed, RequestApproved, RequestRejected
```

##### 6.4 定義 Domain Event

| 子步驟 | 如何執行 | 產出 |
|--------|----------|------|
| 6.4.1 從 Event Storming 萃取 | 橙色便利貼 → Domain Event | Event 清單 |
| 6.4.2 定義事件結構 | 每個事件的欄位、時間戳 | Event schema |
| 6.4.3 定義生產者與消費者 | 誰發出、誰訂閱 | 生產者-消費者對照表 (SA-08) |
| 6.4.4 設計事件版本化策略 | Event 是契約，需版本化 | 版本化規則 |

**Domain Event 目錄產出 (SA-08)**：

```markdown
| Event | 生產者 Context | 消費者 Context | Payload 欄位 | 用途 |
|-------|----------------|----------------|--------------|------|
| RequestSubmitted | Request | Budget, Notification | request_id, department, total_cost, occurred_at | 預算保留、通知主管 |
| RequestApproved | Request | Budget, Notification | request_id, department, total_cost, occurred_at | 預算扣除、通知申請人 |
| RequestRejected | Request | Budget, Notification | request_id, reason, occurred_at | 預算釋放、通知申請人 |
| BudgetExceeded | Budget | Notification | department, required, available, occurred_at | 通知財務 |
```

##### 6.5 定義 Repository 介面

| 原則 | 說明 |
|------|------|
| 每個 Aggregate Root 一個 Repository | Request Repository, Budget Repository |
| 介面定義在領域層 | ABC in `domain/` |
| 實作在基礎設施層 | InMemoryRepository, SQLiteRepository in `infrastructure/` |
| 表達集合語義 | save(), get(), list_*() 方法 |

**Repository 介面產出 (SA-09)** — Python ABC 程式碼：

```python
# domain/repositories.py
from abc import ABC, abstractmethod
from domain.entities import HeadcountRequest

class RequestRepository(ABC):
    @abstractmethod
    def save(self, request: HeadcountRequest) -> None:
        """儲存或更新 Request Aggregate"""
        ...

    @abstractmethod
    def get(self, request_id: str) -> HeadcountRequest | None:
        """根據 ID 取得 Request"""
        ...

    @abstractmethod
    def list_by_department(self, department: str) -> list[HeadcountRequest]:
        """查詢部門的所有 Requests"""
        ...

    @abstractmethod
    def list_approved_in_month(
        self, department: str, year: int, month: int
    ) -> list[HeadcountRequest]:
        """查詢部門在指定月份已核准的 Requests（用於月度預算上限檢查）"""
        ...
```

##### 6.6 定義 Domain Service

Domain Service 是無狀態的領域邏輯，不屬於單一 Entity 或 Value Object。

| 何時需要 Domain Service | 範例 |
|------------------------|------|
| 跨 Aggregate 的業務邏輯 | BudgetValidationService（檢查部門預算是否足夠，需要 DepartmentBudget 和 pending Requests） |
| 複雜的領域計算 | TaxCalculationService |
| 領域層的協調邏輯 | 不是 Application Service（那個在應用層） |

**Domain Service 契約產出 (SA-10)** — Python Protocol/ABC：

```python
# domain/services.py
from abc import ABC, abstractmethod
from domain.entities import DepartmentBudget, HeadcountRequest

class BudgetValidationService(ABC):
    """領域服務：驗證部門預算是否足以支應新申請"""

    @abstractmethod
    def can_allocate(
        self,
        budget: DepartmentBudget,
        pending_requests: list[HeadcountRequest],
        new_request_cost: Decimal,
    ) -> bool:
        """
        檢查剩餘預算 - 已保留金額 是否 >= 新申請成本
        """
        ...
```

#### 步驟 7: Anti-Corruption Layer (ACL) 設計

當整合外部系統或遺留系統時，使用 ACL 保護內部領域模型。

| 子步驟 | 如何執行 | 產出 |
|--------|----------|------|
| 7.1 識別外部系統 | 列出所有外部依賴（API、資料庫、遺留系統） | 外部系統清單 |
| 7.2 評估模型衝突 | 外部模型與內部模型的術語、結構差異 | 衝突分析表 |
| 7.3 設計翻譯規則 | 外部 → 內部的映射規則 | 翻譯規則文件 (SA-11) |
| 7.4 實作 ACL 介面 | Adapter 模式實作 | ACL 程式碼 |

**ACL 規格範例 (SA-11)**：

```markdown
### ACL: External HR System → Request Context

**外部系統**: Legacy HR System API  
**衝突點**:
- 外部稱「hireRequestor」，內部稱「applicant_name」
- 外部稱「costCenter」，內部稱「department」
- 外部稱「positionTitle」，內部稱「title」

**翻譯規則**:
```python
class HrSystemAcl:
    """翻譯外部 HR 系統模型到內部 Request Context 模型"""

    def __init__(self, external_client: ExternalHrApiClient):
        self._client = external_client

    def get_employee_as_applicant(self, external_id: str) -> ApplicantInfo:
        raw = self._client.fetch_employee(external_id)
        return ApplicantInfo(
            applicant_name=raw["hireRequestor"],
            department=raw["costCenter"],
            title=raw["positionTitle"],
        )
```
```

#### 步驟 8: 系統架構設計

| 子步驟 | 如何執行 | 產出 |
|--------|----------|------|
| 8.1 定義分層架構 | Domain / Application / Infrastructure / Presentation | 分層定義文件 |
| 8.2 應用 Port-Adapter 模式 | Repository、Service 介面為 Port，實作為 Adapter | Port-Adapter 設計圖 |
| 8.3 定義目錄結構 | 程式碼組織方式 | 目錄結構範本 |
| 8.4 定義依賴注入策略 | 如何注入 Repository、Service 實作 | DI 設計文件 |

**分層架構 (SA-12)**：

```
┌─────────────────────────────────────────────┐
│ Presentation Layer (API / CLI / GUI)       │ ← 對外介面
├─────────────────────────────────────────────┤
│ Application Layer (Use Case / Service)     │ ← 編排領域邏輯
├─────────────────────────────────────────────┤
│ Domain Layer (Entity / VO / Aggregate)     │ ← 核心業務邏輯（無依賴）
├─────────────────────────────────────────────┤
│ Infrastructure Layer (Repository Impl / DB)│ ← 技術實作細節
└─────────────────────────────────────────────┘

依賴方向: Presentation → Application → Domain ← Infrastructure
（Infrastructure 實作 Domain 定義的 Port 介面）
```

**目錄結構範本**：

```
src/
├── domain/                  ← 領域層（無外部依賴）
│   ├── entities.py          ← HeadcountRequest, DepartmentBudget
│   ├── value_objects.py     ← Money, ApprovalStatus, ReviewAction
│   ├── aggregates.py        ← Aggregate 設計（可選，或直接在 entities.py）
│   ├── events.py            ← Domain Events
│   ├── repositories.py      ← Repository ABC (Port)
│   ├── services.py          ← Domain Service ABC
│   └── rules.py             ← Business Rules (純函數)
│
├── application/             ← 應用層（編排）
│   ├── services.py          ← Application Service (Use Case)
│   └── dto.py               ← Data Transfer Objects
│
├── infrastructure/          ← 基礎設施層（實作）
│   ├── repositories.py      ← Repository 實作 (InMemory / SQLite)
│   ├── event_bus.py         ← Event Bus 實作
│   └── external_adapters.py ← ACL 實作
│
└── presentation/            ← 展示層
    ├── api.py               ← REST API (FastAPI / Flask)
    ├── cli.py               ← CLI (Click / Typer)
    └── web/                 ← Web GUI
```

### A.3 設計原則與最佳實踐

#### 原則 1: Bounded Context 是語義邊界，不是技術邊界

**錯誤**：依資料庫 schema 或微服務劃分 Context  
**正確**：依領域語言與業務職責劃分

#### 原則 2: Ubiquitous Language 強制執行

- 程式碼中的類別名、方法名必須與詞彙表一致
- 拒絕使用的別名不可出現在程式碼中
- Code Review 檢查術語一致性

#### 原則 3: Aggregate 保持小巧

- 避免 10+ Entity 的大 Aggregate
- 一個 Aggregate = 一個事務
- 跨 Aggregate 引用只用 ID

#### 原則 4: Domain Layer 零外部依賴

- Domain 層不依賴 Infrastructure（如資料庫、框架）
- 使用 Dependency Inversion：Domain 定義介面，Infrastructure 實作

#### 原則 5: Domain Event 是契約

- Event 結構一旦發布，需版本化策略
- 新增欄位可以，但不可破壞既有欄位
- 考慮 Event Versioning (v1, v2...)

### A.4 輸出物規格與範本

使用範本加速產出，所有範本位於：[`../04-Templates/system-analysis/`](../04-Templates/system-analysis/)

| 輸出物 | 範本檔案 |
|--------|----------|
| Bounded Context 地圖 | `bounded-context-map-template.md` |
| Ubiquitous Language 詞彙表 | `ubiquitous-language-template.md` |
| Context Map | `context-map-template.md` |
| Aggregate 設計文件 | `aggregate-design-template.md` |
| Domain Event 目錄 | `domain-event-catalog-template.md` |
| Repository 介面 | `repository-interface-template.py` |

---

## Part B: 參考範例 (Reference Examples)

> **⚠️ 警告**: 本節內容為示例參考，實際專案產出應根據真實情況設計。

### B.1 範例：人力需求審批系統的 DDD 設計

#### B.1.1 Bounded Contexts

```
Request Context
  - 負責：申請的生命週期管理、審批流程
  - Aggregate Root: HeadcountRequest
  - 所有權：HR 團隊

Budget Context
  - 負責：預算額度管理、保留、扣除
  - Aggregate Root: DepartmentBudget
  - 所有權：財務團隊

Notification Context
  - 負責：各類通知發送
  - 無 Aggregate Root（Conformist，接受其他 Context 的事件）
  - 所有權：Platform 團隊
```

#### B.1.2 Ubiquitous Language（Request Context）

| 術語 | 定義 | 拒絕的別名 |
|------|------|-----------|
| HeadcountRequest | 部門主管提出的人力需求申請 | 「HC 申請」、「開缺」 |
| ApprovalStatus | 審批流程狀態 (DRAFT/SUBMITTED/MANAGER_REVIEWED/HR_REVIEWED/APPROVED/REJECTED) | 「階段」 |
| ReviewAction | 單一審批者的決策 + 意見 | 「投票」 |
| BusinessRule | 純函數驗證約束條件 | 「Validation」 |

#### B.1.3 Aggregate 設計（Request Context）

```python
class HeadcountRequest:
    """
    Aggregate Root: 人力需求申請

    Invariants（不變量）:
    - status 只能依合法路徑轉移
    - 每次狀態轉移必須記錄 Domain Event
    """

    def __init__(self, request_id: str, applicant: str, ...):
        self._id = request_id
        self._applicant = applicant
        self._status = ApprovalStatus.DRAFT
        self._events: list[DomainEvent] = []

    def submit(self) -> None:
        """提交申請（DRAFT → SUBMITTED）"""
        self._transition_to(ApprovalStatus.SUBMITTED)
        self._events.append(RequestSubmitted(self._id, ...))

    def manager_review(self, approve: bool, comment: str) -> None:
        """主管審核"""
        if approve:
            self._transition_to(ApprovalStatus.MANAGER_REVIEWED)
            self._events.append(RequestReviewed(self._id, "manager", True))
        else:
            self._transition_to(ApprovalStatus.REJECTED)
            self._events.append(RequestRejected(self._id, comment))

    def _transition_to(self, target: ApprovalStatus) -> None:
        validate_transition(self._status, target)  # 檢查合法性
        self._status = target

    def pop_events(self) -> list[DomainEvent]:
        events = self._events.copy()
        self._events.clear()
        return events
```

---

## 常見陷阱 (Pitfalls)

### ❌ Bounded Context 過度拆分

**症狀**: 定義了 10+ 個 Contexts  
**後果**: 協調成本高，Context 之間大量通訊  
**解法**: 初期保持 2-4 個 Context，未來需要時再拆分

### ❌ Domain Layer 依賴 Infrastructure

**症狀**: Domain Entity 直接 import SQLAlchemy 或 FastAPI  
**後果**: 領域邏輯與技術耦合，難以測試  
**解法**: 使用 Dependency Inversion，Domain 定義 Port，Infrastructure 實作

### ❌ Aggregate 設計過大

**症狀**: 一個 Aggregate 包含 10+ 個 Entity  
**後果**: 載入慢、事務範圍過大、難以測試  
**解法**: 拆分為多個小 Aggregate，跨 Aggregate 只用 ID 引用

### ❌ 忽略 Ubiquitous Language

**症狀**: 程式碼用 `RecruitRequest`，文件寫 `HeadcountRequest`  
**後果**: 溝通成本高，新人看程式碼與文件對不上  
**解法**: 建立詞彙表，Code Review 檢查一致性

### ❌ Domain Event 當作內部實作細節

**症狀**: Event 只用於內部狀態轉移  
**後果**: 失去跨 Context 解耦的機會  
**解法**: Event 是 Context 之間的契約，設計時考慮其他 Context 的需求

---

## 階段檢查清單 (Checklist)

在進入 Phase 3 之前，確認以下項目：

- [ ] 子域已劃分為核心域/支撐域/通用域 (SA-01)
- [ ] Event Storming 工作坊已完成
- [ ] Bounded Context 地圖已繪製 (SA-02)
- [ ] 每個 Context 的 Ubiquitous Language 詞彙表已完成 (SA-03)
- [ ] Context Map 已繪製，關係模式已定義 (SA-04)
- [ ] 所有 Entity 已識別並定義 (SA-05)
- [ ] 所有 Value Object 已識別並定義 (SA-06)
- [ ] Aggregate 設計文件已完成，邊界清晰 (SA-07)
- [ ] Domain Event 目錄已建立 (SA-08)
- [ ] Repository 介面已定義為 ABC (SA-09)
- [ ] Domain Service 契約已定義 (SA-10)
- [ ] 需要 ACL 的外部整合已設計 (SA-11)
- [ ] 系統架構文件已完成，分層清晰 (SA-12)
- [ ] 技術風險已識別並有應對計畫
- [ ] **架構原型已實作並驗證** ← Lifecycle Architecture 里程碑

---

## PMBOK 對應

| PMBOK 知識領域 | 本階段活動 |
|---------------|-----------|
| 範圍管理 | Bounded Context 邊界確認 |
| 溝通管理 | Ubiquitous Language 建立與強制執行 |
| 風險管理 | 技術風險識別（架構原型驗證） |
| 品質管理 | 架構品質屬性定義（可測試性、可維護性） |
| 採購管理 | 通用域外購決策 |

---

## 下一步

完成 Phase 2 後，進入 [Phase 3: Development](Phase3-Development.md)，透過 Harness Engineering 與 TDD 實作領域模型與業務邏輯。

---

**相關文件**:
- [DDD 戰略設計指引](../03-Methodology-Guides/DDD-Strategic-Design.md)
- [DDD 戰術設計指引](../03-Methodology-Guides/DDD-Tactical-Design.md)
- [Event Storming 工作坊指引](../03-Methodology-Guides/Event-Storming.md)
- [Harness Engineering 方法](../03-Methodology-Guides/Harness-Engineering.md)
