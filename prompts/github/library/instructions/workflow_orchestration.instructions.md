# Workflow Orchestration（工作流程編排）

> 本文件定義 AI Agent 的工作流程編排規範，適用於所有 Agent。
>
> This document defines workflow orchestration rules for all AI Agents.

---

## 📋 Workflow Orchestration（工作流程編排）

### 1. Plan Node Default（預設使用 Plan 節點）

- 對於任何非簡單任務（3+ 步驟或架構決策）進入 plan 模式
- 如果事情開始偏離，停止並立即重新規劃 — 不要繼續硬推
- 在驗證步驟時使用 plan 模式，而不只是建構時
- 事先撰寫詳細規格以減少歧義

---

### 2. Subagent Strategy（子代理策略）

- 積極使用 subagents 以保持主要上下文視窗乾淨
- 將研究、探索與平行分析卸載給 subagents
- 對於複雜問題，透過 subagents 投入更多算力
- 每個 subagent 僅負責一個任務以保持專注執行

---

### 3. Self-Improvement Loop（自我改進迴圈）

- 在使用者提出任何修正後：以該模式更新 `tasks/lessons.md`
- 為自己寫下規則以防止犯下相同錯誤
- 持續無情地迭代這些 lessons，直到錯誤率下降
- 在每次工作階段開始時檢視 lessons（針對相關專案）

---

### 4. Verification Before Done（完成前驗證）

- 在證明可運作之前，絕不要將任務標記為完成
- 在相關時，比對 main 與你的變更差異
- 問自己：「資深工程師會批准這個嗎？」
- 執行測試、檢查日誌、展示正確性

---

### 5. Demand Elegance (Balanced)（要求優雅（平衡））

- 對於非簡單變更：暫停並問「是否有更優雅的方式？」
- 如果修補感覺很 hacky：
  「以我現在所知的一切，實作優雅解法」
- 對於簡單、明顯的修正跳過此步 — 不要過度設計
- 在呈現之前挑戰你自己的工作

---

### 6. Autonomous Bug Fixing（自主錯誤修復）

- 當收到 bug 回報時：直接修復。不要要求手把手指導
- 指出 logs、errors、失敗測試 — 然後解決它們
- 不需要使用者進行上下文切換
- 在未被告知如何做的情況下修復失敗的 CI 測試

---

## 📋 Task Management（任務管理）

1. **Plan First**：將計畫寫入 `tasks/todo.md`，包含可檢查項目
2. **Verify Plan**：在開始實作前先檢查
3. **Track Progress**：隨進度標記項目完成
4. **Explain Changes**：在每一步提供高階摘要
5. **Document Results**：在 `tasks/todo.md` 加入 review 區段
6. **Capture Lessons**：在修正後更新 `tasks/lessons.md`

---

## 📋 Core Principles（核心原則）

- **Simplicity First**：讓每個變更盡可能簡單。影響最小程式碼
- **No Laziness**：找出根本原因。不做暫時修補。資深開發者標準
- **Minimal Impact**：變更只觸及必要部分。避免引入 bug
