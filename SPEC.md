# DUANNI Control Center — 規格書 V1

## 1. 專案目的

建立一條不需要老爸反覆複製貼上 Terminal 指令的工作流：

> 自然語言需求 → ChatGPT 規格化 → GitHub 任務 → 本地 DUANNI DEV 領取 → 執行/測試/修正 → GitHub 回報 → ChatGPT 驗收。

此系統將「對話決策」與「本機執行」分離：ChatGPT 負責規格、拆解、審核；本地 Hermes/DUANNI DEV 負責在受控工作區執行。

## 2. 已知現況基線

### DUANNI HOME
- Hermes 0.21.3
- Python 3.13.15
- Ollama 0.32.9
- Qwen3 4B Q4_K_M
- 64K context 已驗證
- Dashboard / Web Chat 已可用
- 模型工具數 = 0
- 使用隔離 `HERMES_HOME`
- 人格與核心記憶已開始放入本地 SOUL / MEMORY 層

### DUANNI DEV
- 與 DUANNI HOME 分離
- 有獨立 `HERMES_HOME`
- 工作區與 HOME 分離
- V1 僅開 file 類工具
- 目前可讀、寫、搜尋、patch 檔案
- 目前不應宣稱可實際 Terminal / build / test，直到受控執行層完成

## 3. 使用者體驗目標

老爸只需要說自然語言，例如：

- 「幫我把登入頁做完。」
- 「讓本地端測一下這個 bug。」
- 「把這個需求交給開發精靈。」
- 「查一下現在任務做到哪。」

系統自行完成：

1. 需求澄清（必要時）
2. 產生技術規格
3. 建立 GitHub Issue / TASK
4. 本地領取
5. 建 branch
6. 修改程式碼
7. 執行測試
8. 失敗則修正重試
9. commit / push
10. 開 PR 或回報結果
11. ChatGPT 審核

## 4. GitHub 任務協議

每個任務至少包含：

```yaml
id: TASK-XXX
status: READY
priority: normal
executor: duanni-dev
workspace: <allowed project path>
requires_terminal: false
requires_network: false
requires_approval: false
```

並包含：
- Goal：要完成什麼
- Scope：可以修改什麼
- Do Not Touch：不可修改什麼
- Acceptance Criteria：驗收條件
- Verification：如何驗證
- Report：要回報哪些結果

## 5. 本地 Bridge V1

### 功能
- 每 60 秒讀取 GitHub READY 任務
- 一次只領一個任務
- 以原子方式把狀態改成 WORKING
- 將完整任務落地到本地 inbox
- 啟動/喚醒 DUANNI DEV 執行
- 收集結果並回寫 Issue
- 成功 → REVIEW
- 失敗 → BLOCKED + 錯誤摘要

### 禁止
- 不得碰 DUANNI HOME
- 不得讀取 SOUL / MEMORY 作為開發任務資料
- 不得把本地秘密推上 GitHub
- 不得自行擴權

## 6. 執行層級

### Level 0 — DUANNI HOME
0 tools。人格與記憶安全屋。

### Level 1 — DUANNI DEV File-only
允許：read / write / patch / search。
不允許：shell、套件安裝、build、test。

### Level 2 — Safe Executor
只在指定 workspace 或容器中執行白名單命令。
例如：
- `python ...`
- `npm test`
- `npm run build`
- `pytest`

預設禁止：
- `sudo`
- 修改 HOME 安全屋
- 任意系統級安裝
- 不受控的 `rm -rf`
- 讀取 keychain / credentials

### Level 3 — Autonomous Coding Loop
寫 → 執行 → 看錯 → 修 → 重試 → 驗收。
需要：最大重試次數、timeout、diff review、成本/資源上限。

## 7. 回報格式

本地 Agent 完成後回覆 GitHub：

```markdown
## DUANNI DEV RESULT
Status: REVIEW

### Changed
- ...

### Verification
- command: ...
- result: PASS/FAIL

### Files
- ...

### Risks / Notes
- ...
```

## 8. 長期方向

- GitHub Issue 作為任務佇列
- PR 作為修改審核邊界
- 本地 daemon 自動領任務
- Hermes Desktop 作為自然語言開發介面
- Docker/沙箱作為可執行開發環境
- DUANNI HOME 永遠與開發權限隔離

## 9. V1 成功定義

當老爸只在 ChatGPT 說一句自然語言需求後，無需手動複製程式碼到 Terminal，就能看到：

1. GitHub 自動出現任務
2. 本地 DUANNI DEV 自動領取
3. 實際產生檔案或修改
4. 結果回寫 GitHub
5. ChatGPT 可以讀取並審核結果

達成以上五點，即完成「自然語言 → GitHub → 本地精靈」閉環 V1。
