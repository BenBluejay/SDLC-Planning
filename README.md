# SDLC-Planning

現代軟體發展流程規劃 (Modern Software Development Lifecycle Planning)

融合了以下方法論的完整 SDLC 框架：

1. **PMI Business Analysis** — 從企業痛點到商業目標的完整商業分析
2. **PMBOK 專案管理** — 全流程的控管原則與方法
3. **RUP (Rational Unified Process)** — 9大工作流程與4個發展階段
4. **Agile 開發** — 迭代、增量、持續交付
5. **Domain-Driven Design (DDD)** — 戰略設計與戰術設計
6. **Test-Driven Development (TDD)** — 測試驅動開發

## 快速開始

詳細內容請參閱 [Process/README.md](Process/README.md)

## 專案結構

```
Process/
├── README.md                        ← 總覽文件
├── 01-Main-Process/                 ← 主流程規範
│   ├── Phase1-Business-Analysis.md
│   ├── Phase2-System-Analysis.md
│   ├── Phase3-Development.md
│   ├── Phase4-Deployment.md
│   └── Phase5-Production-Evaluation.md
├── 02-Supporting-Processes/         ← 支援性流程
├── 03-Methodology-Guides/           ← 方法論詳細指引
├── 04-Templates/                    ← 可重複使用的範本
└── 05-Adaptations/                  ← 不同系統類型的適配指引
```

## RUP 四階段流程

| 階段 | 目標 | 主要方法論 |
|------|------|-----------|
| **Inception** (初始) | 定義商業案例與範圍 | PMI BA (P/G/O/W) |
| **Elaboration** (細化) | 建立穩定架構 | DDD (Strategic + Tactical Design) |
| **Construction** (建構) | 迭代開發 | TDD + Harness Engineering |
| **Transition** (轉移) | 部署上線 | CI/CD + DevOps |

## 授權

MIT License

## 作者

Ben Lin - 2025
