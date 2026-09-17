---
id: TASK-002
status: BLOCKED
priority: high
executor: duanni-dev
requires_terminal: true
requires_network: false
requires_approval: true
blocked_by: TASK-001
---

# TASK-002 — Safe Executor

## Goal
讓 DUANNI DEV 可以在受控 workspace 內執行程式、build、test，但不能取得任意 shell 權限。

## Scope
建立 structured command executor：

```json
{
  "cwd": "<allowed workspace>",
  "command": ["python", "main.py"],
  "timeout_seconds": 60
}
```

## Required Controls
- cwd allowlist
- executable allowlist
- 禁止 shell=True
- timeout
- stdout/stderr/exit code 捕獲
- max output size
- max retries
- audit log
- DUANNI HOME path denylist

## Initial Allowlist
- python / python3
- node
- pytest
- npm test
- npm run build
- git status
- git diff

## Explicit Deny
- sudo / su
- system package manager
- rm -rf outside workspace
- chmod/chown outside workspace
- reading credentials / keychain
- modifying executor policy

## Acceptance Criteria
- 可執行 hello-world Python 並取得 exit=0。
- 可執行一個故意失敗測試並取得 stderr/exit!=0。
- 嘗試 cwd 指向 DUANNI HOME 必須被拒絕。
- 嘗試 sudo 必須被拒絕。
- 全部執行有 audit log。
