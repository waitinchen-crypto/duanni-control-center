---
id: TASK-001
status: READY
priority: high
executor: duanni-dev
requires_terminal: true
requires_network: true
requires_approval: true
---

# TASK-001 — Bootstrap Local GitHub Bridge

## Goal
建立一個常駐於本地 Mac 的小型 Bridge，使本機能自動讀取 GitHub Control Center 的 READY 任務，並將任務安全落地到 DUANNI DEV inbox。

## Scope
允許建立：
- `${DUANNI_DEV}/bridge/`
- `${DUANNI_DEV}/inbox/`
- `${DUANNI_DEV}/outbox/`
- bridge log / pid / state files

## Do Not Touch
- DUANNI HOME
- DUANNI HOME 的 SOUL / MEMORY / config
- 使用者其他專案
- 系統層設定

## Functional Requirements
1. 每 60 秒查詢本 repo 的 open Issues。
2. 只處理 body 中含 `Status: READY` 或 title 以 `[READY]` 開頭的任務。
3. 一次只 claim 一個任務。
4. claim 成功後：
   - Issue 留言 `CLAIMED BY DUANNI DEV`
   - 本地建立 `inbox/issue-<n>.md`
   - 本地 state 記錄 issue number / claim time
5. 不得執行 Issue 內任意 shell；V1 只負責搬運任務。
6. 支援 dry-run。
7. 支援 stop / start / status。
8. 所有錯誤寫 log。

## Authentication
優先使用 GitHub CLI (`gh auth`) 或最小權限 token；不得把 token 寫入 repo 或 log。

## Acceptance Criteria
- 建立一個測試 READY Issue 後，60 秒內在本地 inbox 出現任務檔。
- Issue 留下 claim 訊息。
- Bridge 重啟後不重複 claim 同一任務。
- Stop 後不再輪詢。
- 無 token/key 出現在 GitHub commit、Issue comment 或 log。

## Verification
回報：
- Bridge process 狀態
- 測試 Issue 編號
- inbox 檔案路徑
- claim comment
- log 最後 20 行（需去除秘密）

## Report
完成後將 Issue 狀態改為 REVIEW，並貼上 DUANNI DEV RESULT 格式報告。
