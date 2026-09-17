# DUANNI Control Center — Architecture V1

## 核心原則

把三個角色拆開：

1. **ChatGPT / 規格層**：理解自然語言、寫規格、拆任務、審核結果。
2. **GitHub / 協作層**：保存技術文件、Issue、branch、commit、PR、狀態。
3. **DUANNI DEV / 本地執行層**：在受控 workspace 讀取任務、修改程式、執行驗證並回報。

DUANNI HOME 不參與開發執行。

## 資料流

```text
老爸自然語言
    ↓
ChatGPT
    ↓ 產生 SPEC / Issue
GitHub Control Center
    ↓ Bridge 輪詢
Local Task Bridge
    ↓ task.json / task.md
DUANNI DEV
    ↓
Safe Executor
    ↓
workspace changes / tests
    ↓
commit / PR / Issue comment
    ↓
GitHub
    ↓
ChatGPT review
```

## 本地目錄建議

```text
${DUANNI_ROOT}/
├── duanni-home/              # 本體安全屋，0 tools
├── duanni-dev-home/          # 開發分身
│   ├── home/
│   ├── workspace/
│   ├── inbox/
│   ├── outbox/
│   └── bridge/
└── control-center/           # Git clone（可選）
```

## Bridge 狀態機

```text
READY
  ↓ claim
WORKING
  ↓ execute
REVIEW ──驗收成功──> DONE
  └─失敗/缺條件──> BLOCKED
```

### Claim 規則

- 同一時間只允許一個本地 worker claim 同一任務。
- claim 後立即把 Issue 狀態或任務 metadata 改為 `WORKING`。
- local bridge 記錄 GitHub issue number + task id + claim timestamp。
- worker crash 後需有 stale claim 回收機制。

## Safe Executor

### 第一版

執行器不直接給模型任意 shell；模型產生「執行請求」，由 wrapper 判斷：

```json
{
  "cwd": "${ALLOWED_WORKSPACE}",
  "command": ["python", "main.py"],
  "timeout_seconds": 60
}
```

wrapper 驗證：
- cwd 必須位於 allowlist
- executable 必須在 allowlist
- 禁止 sudo
- 禁止存取 DUANNI HOME
- timeout
- stdout/stderr 捕獲
- exit code 回傳

### 第二版

改到 Docker / sandbox：
- workspace mount
- HOME 不掛入
- credentials 不掛入
- 可選擇關閉 network
- CPU/RAM/time limit

## GitHub 任務來源

推薦主來源：GitHub Issues。

原因：
- 有狀態與留言
- 容易被 ChatGPT 建立/讀取
- 本地可輪詢 API
- 容易審計

TASKS/*.md 用於規格模板與較大型固定任務；Issue 作為實際 dispatch queue。

## Result Contract

本地 worker 每次必須回報：
- task id
- start/end time
- branch/commit
- changed files
- commands executed
- exit codes
- tests
- unresolved problems
- whether human approval is needed

禁止只說「完成」而沒有可驗證證據。
