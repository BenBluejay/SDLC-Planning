# Phase 3: Development (開發階段)

> **RUP Phase**: Construction  
> **RUP Disciplines**: Implementation, Test  
> **里程碑**: Initial Operational Capability (IOC) — 系統準備就緒  
> **關鍵決策**: 系統功能完整性確認

## 階段目的

基於 Phase 2 的領域模型與架構設計，採用 **Harness Engineering** 與 **TDD (Test-Driven Development)** 方法論，迭代式開發出高品質、可測試、可維護的系統。

**核心問題**：
- 如何確保程式碼品質？
- 如何快速驗證業務邏輯正確性？
- 如何建立可重複使用的測試基礎設施？
- 如何進行敏捷迭代開發？

## 輸入 (Inputs)

| 來源 | 輸入物 |
|------|--------|
| Phase 1 | User Story 清單 (BA-09) |
| Phase 2 | Bounded Context 地圖 (SA-02) |
| Phase 2 | Aggregate 設計文件 (SA-07) |
| Phase 2 | Repository 介面定義 (SA-09) |
| Phase 2 | 系統架構文件 (SA-12) |

## 輸出 (Outputs)

| 編號 | 產出物 | 用途 | 格式 |
|------|--------|------|------|
| DEV-01 | Harness Infrastructure | 測試基礎設施（In-Memory Repo, Fake Services） | Python 程式碼 |
| DEV-02 | Domain Model 實作 | Entity, Value Object, Aggregate | Python 程式碼 |
| DEV-03 | Business Rules | 純函數業務規則 | Python 程式碼 |
| DEV-04 | Unit Tests | 單元測試（Domain + Rules） | pytest |
| DEV-05 | Integration Tests | 整合測試（Repository + Service） | pytest |
| DEV-06 | Application Services | Use Case 編排邏輯 | Python 程式碼 |
| DEV-07 | Infrastructure Adapters | SQLite Repository, Event Bus | Python 程式碼 |
| DEV-08 | API Layer | REST API 或 CLI | FastAPI / Click |
| DEV-09 | Test Coverage Report | 測試覆蓋率 >= 80% | pytest-cov |
| DEV-10 | 技術債務清單 | 記錄的 TODOs 與改進項目 | Markdown |

## 方法論：Harness Engineering + TDD

### Harness Engineering 四階段工作流程

```
Phase 5a: Domain Model 定義
  ├─ Entity, Value Object, Aggregate
  ├─ Business Rules (純函數)
  └─ Domain Error 類別
      ↓
Phase 5b: Harness Infrastructure
  ├─ InMemoryRepository（dict-based, CRUD）
  ├─ Fake Services（記錄呼叫，無真實副作用）
  ├─ State Machine Validator（轉移規則）
  └─ Test Fixtures（Factory functions）
      ↓
Phase 5c: TDD Service Layer
  ├─ 寫失敗測試
  ├─ 實作最小程式碼讓測試通過
  ├─ Refactor
  └─ Repeat
      ↓
Phase 5d: Edge Cases
  ├─ 邊界條件
  ├─ 錯誤路徑
  └─ 跨 Entity 約束
```

詳細方法請參考：
- [`../03-Methodology-Guides/Harness-Engineering.md`](../03-Methodology-Guides/Harness-Engineering.md)
- [`../03-Methodology-Guides/TDD-Practice.md`](../03-Methodology-Guides/TDD-Practice.md)

### Agile 迭代開發

採用 **2 週 Sprint** 模式：

```
Sprint Planning → Daily Standup → Development (TDD) → Sprint Review → Sprint Retrospective
     ↓
每個 Sprint 交付可運作的軟體增量
     ↓
Product Backlog Refinement（持續進行）
```

---

## Part A: 活動規範 (Activity Specification)

### A.1 為什麼需要 Harness Engineering + TDD

**價值**：
- **快速回饋** — Harness 讓測試執行速度快（毫秒級），即時驗證邏輯
- **高測試覆蓋率** — TDD 確保每行程式碼都有測試
- **可重構** — 測試保護網讓重構安全
- **減少 Debug 時間** — 問題在測試階段就被發現

**實驗證據**（來自 harness-engineering skill）：
- Harness-first 開發速度比 code-first 快 **2.5x**
- 測試數量多 **78%**
- 但單次修改時間較長（因為保持乾淨分離）

### A.2 流程步驟

#### 步驟 1: Sprint Planning（Sprint 規劃）

| 子步驟 | 如何執行 | 產出 |
|--------|----------|------|
| 1.1 選擇 User Stories | 從 Product Backlog 選出本 Sprint 要完成的 Stories | Sprint Backlog |
| 1.2 拆分為 Tasks | 每個 Story 拆分為 2-5 分鐘的小任務 | Task List |
| 1.3 估算 Story Points | 團隊共同估算複雜度 | Story Points 估算 |
| 1.4 定義 Done 標準 | 何時算「完成」（測試通過、Code Review、文件更新） | Definition of Done |

#### 步驟 2: Phase 5a — Domain Model 實作

**TDD Cycle**: RED → GREEN → REFACTOR

| 子步驟 | 如何執行 | 產出 |
|--------|----------|------|
| 2.1 寫失敗測試（RED） | 先寫測試描述期望行為 | 失敗的測試 |
| 2.2 執行確認失敗 | `pytest` 確認測試失敗 | 測試報告（FAIL） |
| 2.3 實作最小程式碼（GREEN） | 寫最少的程式碼讓測試通過 | Domain Model 程式碼 |
| 2.4 執行確認通過 | `pytest` 確認測試通過 | 測試報告（PASS） |
| 2.5 Refactor | 改善程式碼品質（不改變行為） | 重構後的程式碼 |
| 2.6 Commit | `git commit` | Git 歷史 |

**範例: 測試驅動 Business Rule**

```python
# tests/domain/test_rules.py
def test_validate_headcount_within_range():
    errors = validate_headcount(5)
    assert errors == []

def test_validate_headcount_too_high():
    errors = validate_headcount(11)
    assert "Headcount must be between 1 and 10" in errors[0]

# domain/rules.py
def validate_headcount(count: int) -> list[str]:
    errors = []
    if count < 1 or count > 10:
        errors.append(f"Headcount must be between 1 and 10, got {count}")
    return errors
```

#### 步驟 3: Phase 5b — Harness Infrastructure

| 子步驟 | 如何執行 | 產出 |
|--------|----------|------|
| 3.1 實作 InMemoryRepository | dict-based, 支援 CRUD | InMemoryRepository 類別 |
| 3.2 實作 Fake Services | 記錄呼叫，無真實副作用 | FakeBudgetService, FakeNotificationService |
| 3.3 實作 State Machine Validator | 定義合法轉移規則 | validate_transition() |
| 3.4 實作 Test Fixtures | Factory functions 產生測試資料 | make_request(), make_budget() |

**範例: InMemoryRepository**

```python
# tests/harness/repositories.py
class InMemoryRequestRepository:
    def __init__(self):
        self._store: dict[str, HeadcountRequest] = {}

    def save(self, request: HeadcountRequest) -> None:
        self._store[request.id] = request

    def get(self, request_id: str) -> HeadcountRequest | None:
        return self._store.get(request_id)

    def list_by_department(self, department: str) -> list[HeadcountRequest]:
        return [r for r in self._store.values() if r.department == department]
```

#### 步驟 4: Phase 5c — TDD Service Layer

使用 Harness 進行快速 TDD：

| 子步驟 | 如何執行 | 產出 |
|--------|----------|------|
| 4.1 寫 Service 測試（使用 Harness） | 使用 InMemoryRepo + Fake Services | 測試檔案 |
| 4.2 實作 Application Service | 編排 Domain 邏輯 | Application Service 程式碼 |
| 4.3 驗證副作用 | 檢查 Fake Services 記錄的呼叫 | 通過的測試 |
| 4.4 測試所有正常流程 | Happy Path 全覆蓋 | 測試覆蓋率報告 |

**範例: Service 測試**

```python
# tests/application/test_request_service.py
def test_submit_request_happy_path():
    # Arrange: 準備 Harness
    repo = InMemoryRequestRepository()
    budget = FakeBudgetService()
    budget.set_budget("Engineering", 1_000_000)
    notifications = FakeNotificationService()
    service = RequestService(repo, budget, notifications)

    request = make_request(department="Engineering", headcount=2, cost_per_head=50_000)
    repo.save(request)

    # Act: 執行
    result = service.submit_request(request.id)

    # Assert: 驗證
    assert result.status == ApprovalStatus.SUBMITTED
    assert budget.get_reserved("Engineering") == 100_000  # 2 * 50k
    assert len(notifications.get_notifications()) == 1
```

#### 步驟 5: Phase 5d — Edge Cases（邊界案例）

| 測試類型 | 範例 | 產出 |
|----------|------|------|
| 邊界值 | headcount = 0, 1, 10, 11 | 邊界測試 |
| 錯誤路徑 | 預算不足、狀態轉移非法 | 錯誤處理測試 |
| 並發安全 | 同時提交多個申請 | 並發測試（如需要） |
| 跨 Aggregate 約束 | 部門月度上限 | 跨 Entity 測試 |

#### 步驟 6: Infrastructure Adapters 實作

| 子步驟 | 如何執行 | 產出 |
|--------|----------|------|
| 6.1 實作 SQLiteRepository | 實作 Repository ABC | SQLiteRepository 類別 |
| 6.2 撰寫整合測試 | 使用 SQLite `:memory:` 測試 | 整合測試 |
| 6.3 實作 Event Bus | Domain Event 發布/訂閱機制 | EventBus 類別 |
| 6.4 實作外部 Adapters | ACL, 外部 API Client | Adapter 類別 |

#### 步驟 7: API Layer 實作

| 子步驟 | 如何執行 | 產出 |
|--------|----------|------|
| 7.1 定義 API 端點 | REST API 路由規劃 | API 規格文件 |
| 7.2 實作端點處理 | FastAPI / Flask | API 程式碼 |
| 7.3 撰寫 API 測試 | 使用 TestClient | API 測試 |
| 7.4 API 文件產生 | OpenAPI / Swagger | API 文件 |

#### 步驟 8: Sprint Review & Retrospective

| 子步驟 | 如何執行 | 產出 |
|--------|----------|------|
| 8.1 Demo 可運作軟體 | 展示給利害關係人 | Demo 記錄 |
| 8.2 收集回饋 | 記錄使用者回饋與改進建議 | 回饋清單 |
| 8.3 Retrospective | 團隊反思：做得好、需改進、行動項目 | Retrospective 記錄 |
| 8.4 更新 Product Backlog | 根據回饋調整優先序 | 更新後的 Backlog |

### A.3 設計原則與最佳實踐

#### 原則 1: RED-GREEN-REFACTOR 循環

永遠遵守 TDD 三步驟：
1. **RED** — 先寫失敗測試
2. **GREEN** — 寫最少程式碼讓測試通過
3. **REFACTOR** — 改善程式碼品質

**不可跳過 RED 步驟** — 先寫程式碼再寫測試不是 TDD。

#### 原則 2: Harness Code 與 Production Code 分離

```
tests/
├── harness/                    ← Harness Infrastructure
│   ├── repositories.py         ← InMemoryRepository
│   ├── fake_services.py        ← Fake Services
│   └── fixtures.py             ← Test Fixtures
├── domain/                     ← Domain 測試
├── application/                ← Application 測試
└── integration/                ← Integration 測試

src/
├── domain/                     ← Production Domain Code
├── application/                ← Production Application Code
└── infrastructure/             ← Production Infrastructure Code
```

#### 原則 3: 測試要快

- Harness 測試應該 < 1ms
- 單元測試應該 < 10ms
- 整合測試應該 < 100ms
- 如果慢，代表測試太複雜或用了真實 I/O

#### 原則 4: 一次只做一件事

每個 Task 應該 2-5 分鐘，頻繁 commit：

```bash
git add domain/rules.py tests/domain/test_rules.py
git commit -m "feat: add headcount validation rule"
```

#### 原則 5: Code Review 必須

所有程式碼合併前需經過 Code Review：
- 檢查測試覆蓋率
- 檢查 Ubiquitous Language 一致性
- 檢查 Domain Layer 無外部依賴
- 檢查錯誤處理完整性

### A.4 輸出物規格與範本

使用範本加速產出，所有範本位於：[`../04-Templates/development/`](../04-Templates/development/)

| 輸出物 | 範本檔案 |
|--------|----------|
| InMemoryRepository | `inmemory-repository-template.py` |
| Test Fixtures | `test-fixtures-template.py` |
| Unit Test | `unit-test-template.py` |
| Sprint Planning | `sprint-planning-template.md` |

---

## Part B: 參考範例 (Reference Examples)

> **⚠️ 警告**: 本節內容為示例參考，實際專案應根據實際情況實作。

### B.1 範例：完整 TDD Cycle

```python
# Step 1: RED — 寫失敗測試
def test_manager_review_approve():
    repo = InMemoryRequestRepository()
    service = RequestService(repo)
    request = make_request(status=ApprovalStatus.SUBMITTED)
    repo.save(request)

    result = service.manager_review(request.id, approve=True, comment="Looks good")
    
    assert result.status == ApprovalStatus.MANAGER_REVIEWED
    assert result.manager_comment == "Looks good"

# Step 2: GREEN — 實作最小程式碼
class RequestService:
    def __init__(self, repo: RequestRepository):
        self._repo = repo

    def manager_review(self, request_id: str, approve: bool, comment: str):
        request = self._repo.get(request_id)
        if approve:
            request.status = ApprovalStatus.MANAGER_REVIEWED
            request.manager_comment = comment
        self._repo.save(request)
        return request

# Step 3: REFACTOR — 改善（移到 Aggregate）
class HeadcountRequest:
    def manager_review(self, approve: bool, comment: str):
        if approve:
            self._transition_to(ApprovalStatus.MANAGER_REVIEWED)
            self.manager_comment = comment
        else:
            self._transition_to(ApprovalStatus.REJECTED)
```

### B.2 範例：Harness Infrastructure

```python
# tests/harness/fake_services.py
class FakeBudgetService:
    def __init__(self):
        self._budgets: dict[str, Decimal] = {}
        self._reservations: dict[str, Decimal] = {}

    def set_budget(self, department: str, amount: Decimal):
        self._budgets[department] = amount

    def reserve(self, department: str, amount: Decimal, request_id: str) -> bool:
        remaining = self.get_remaining(department)
        if remaining < amount:
            return False
        self._reservations[request_id] = amount
        return True

    def get_remaining(self, department: str) -> Decimal:
        budget = self._budgets.get(department, Decimal(0))
        reserved = sum(self._reservations.values())
        return budget - reserved
```

---

## 常見陷阱 (Pitfalls)

### ❌ 先寫程式碼再寫測試

**症狀**: 「我知道怎麼寫，先實作再補測試」  
**後果**: 測試變成形式，只測試已有行為，無法驅動設計  
**解法**: 嚴格執行 RED-GREEN-REFACTOR

### ❌ Harness 太複雜

**症狀**: InMemoryRepository 用了檔案 I/O  
**後果**: 測試變慢，失去 Harness 的速度優勢  
**解法**: Harness 必須 in-memory、無 I/O

### ❌ 測試覆蓋率不足

**症狀**: 只測 Happy Path，不測 Edge Cases  
**後果**: Production 遇到邊界條件崩潰  
**解法**: 每個 Story 必須包含 Edge Case 測試

### ❌ 忽略 Code Review

**症狀**: 「我們小團隊不需要 Review」  
**後果**: 技術債務累積，品質下降  
**解法**: 即使單人團隊也要 Review（可以用 AI 輔助）

---

## 階段檢查清單 (Checklist)

在進入 Phase 4 之前，確認以下項目：

- [ ] 所有 MVP User Stories 已完成
- [ ] 測試覆蓋率 >= 80% (DEV-09)
- [ ] Harness Infrastructure 完整 (DEV-01)
- [ ] Domain Model 實作完成 (DEV-02)
- [ ] Business Rules 有測試覆蓋 (DEV-03, DEV-04)
- [ ] Integration Tests 通過 (DEV-05)
- [ ] Application Services 實作完成 (DEV-06)
- [ ] Infrastructure Adapters 實作完成 (DEV-07)
- [ ] API Layer 實作完成 (DEV-08)
- [ ] 所有測試通過（Unit + Integration）
- [ ] Code Review 已完成
- [ ] 技術債務已記錄 (DEV-10)
- [ ] **系統可在本地環境運行** ← Initial Operational Capability 里程碑

---

## 下一步

完成 Phase 3 後，進入 [Phase 4: Deployment & Transition](Phase4-Deployment.md)，將系統容器化並部署到生產環境。

---

**相關文件**:
- [Harness Engineering 方法](../03-Methodology-Guides/Harness-Engineering.md)
- [TDD 實踐指引](../03-Methodology-Guides/TDD-Practice.md)
- [品質保證流程](../02-Supporting-Processes/Quality-Assurance.md)
- [配置管理流程](../02-Supporting-Processes/Configuration-Management.md)
