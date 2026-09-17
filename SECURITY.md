# DUANNI Control Center — Security V1

## 最高原則

開發能力與人格/記憶本體必須分離。

### DUANNI HOME
- 0 tools
- 不接受 GitHub 任務直接修改
- 不允許 bridge 讀寫其 SOUL / MEMORY / config
- 不允許 Safe Executor 以其目錄為 cwd

### DUANNI DEV
- 只在指定 workspace 工作
- 權限分級開啟
- 任何擴權都必須是顯式設定，不得由模型自行修改

## Repo 安全

本 repository 只放：
- 技術規格
- 任務
- 狀態
- 程式碼（若未來加入）

嚴禁提交：
- API keys
- access tokens
- cookies
- passwords
- private SSH keys
- Keychain exports
- ChatGPT conversations export
- SOUL / MEMORY 私人內容
- 個人財務、醫療、法律等私人資料

## Safe Executor 規則

### 預設 deny
所有命令預設禁止，只有 allowlist 才可執行。

### 初始 allowlist 建議
- `python`
- `python3`
- `node`
- `npm test`
- `npm run build`
- `pytest`
- `git status`
- `git diff`

實際允許項目由 executor wrapper 以 structured command 判斷，不以字串拼接直接交 shell。

### 明確禁止
- `sudo`
- `su`
- `rm -rf /`
- `chmod`/`chown` 對 workspace 外部
- 修改 `/etc`、`/System`、使用者 HOME 安全資料
- 存取 DUANNI HOME
- 自行讀取環境秘密
- 自行修改 executor policy

## 網路

V1 可以完全不開 network。
需要 `git` / package registry 時再分別開：
- GitHub
- npm registry
- PyPI

不要一開始就給無限制 outbound network。

## Git 操作

推薦：
- 每任務獨立 branch
- 不直接 push main
- 產生 PR
- ChatGPT 或人工 review 後 merge

## Kill Switch

本地 Bridge 必須有：
- 單一 stop command
- PID file
- max runtime
- stale task recovery
- 每任務 max retry

## 稽核

所有本地任務必須留下：
- GitHub issue id
- task id
- claimed_at
- executed commands
- exit codes
- changed files
- commit SHA
- final status

這些紀錄用於確認 Agent 沒有「假裝完成」。
