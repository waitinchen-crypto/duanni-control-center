---
id: TASK-003
status: BLOCKED
priority: high
executor: duanni-dev
requires_terminal: true
requires_network: true
requires_approval: true
blocked_by: TASK-001,TASK-002
---

# TASK-003 — End-to-End Autonomous Coding Loop

## Goal
完成第一條真正閉環：GitHub READY 任務 → DUANNI DEV 自動領取 → 修改 → 執行驗證 → 回寫結果。

## Test Project
只使用 sandbox/demo 專案，不碰任何正式專案。

## Required Flow
1. Bridge 發現 READY Issue。
2. Claim → WORKING。
3. 將任務寫入 inbox。
4. DUANNI DEV 讀取任務。
5. 修改 demo 專案。
6. Safe Executor 執行測試。
7. 若失敗，允許最多 3 次修正重試。
8. git diff / status 留證據。
9. 建 branch + commit。
10. 回寫 Issue：changed files / tests / result / commit SHA。
11. 狀態 → REVIEW。

## Acceptance Criteria
- 老爸不需要複製任何程式碼到 Terminal。
- GitHub Issue 能看到完整執行紀錄。
- 本地結果可被重現。
- 沒有碰 DUANNI HOME。
- 沒有 secret 外洩。
