# DUANNI Control Center

這個 repository 是 DUANNI 系統的「任務中樞」，不是人格記憶庫。

## 目標

讓老爸只需要用自然語言描述需求：

1. ChatGPT 將需求整理成規格與 GitHub 任務。
2. 本地 `DUANNI DEV` 自動領取 `READY` 任務。
3. 本地執行、測試、修正。
4. 結果回寫 GitHub（Issue / branch / commit / PR）。
5. ChatGPT 再檢查結果並繼續派工。

## 安全邊界

- `DUANNI HOME`：人格 / 記憶本體，維持 **0 tools**，不由本 repo 直接修改。
- `DUANNI DEV`：開發分身，只在受控 workspace 工作。
- GitHub：只保存技術規格、任務與開發紀錄。
- **禁止**把 SOUL、MEMORY、聊天匯出、API Key、token、密碼或其他私人資料提交到本 repo。

## 文件

- `SPEC.md`：整體產品與功能規格
- `ARCHITECTURE.md`：技術架構與資料流
- `SECURITY.md`：安全邊界與權限規則
- `STATUS.md`：目前進度
- `TASKS/`：可被本地執行器領取的任務規格

## 任務狀態

任務使用以下狀態：

- `READY`：可以領取
- `WORKING`：本地 Agent 執行中
- `BLOCKED`：等待條件
- `REVIEW`：完成待審
- `DONE`：驗收完成

> 本 repo 目前為技術協作層；DUANNI HOME 的人格與長期記憶另行保存在本地安全屋。
