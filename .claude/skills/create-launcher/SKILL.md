---
name: create-launcher
description: Use when the user asks to create a launcher, setup script, or run configuration for a project. Detects the stack, provisions a missing .env, and generates launch.sh, launch.bat and launch.ps1 that start services concurrently.
---

When the user asks for a launcher, setup script, or run configuration, generate three scripts at the project root: `launch.sh`, `launch.bat`, `launch.ps1`. Do not launch the project yourself unless asked.

## Step 1: Detect services

Inspect the project root and its immediate subdirectories. Build a list of **services**, each with: directory, type, install command, start command.

**Stack detection**
- Python: `requirements.txt`, `pyproject.toml`, or `Pipfile`.
- Node.js: `package.json`. Package manager from lockfile: `pnpm-lock.yaml` → pnpm, `yarn.lock` → yarn, otherwise npm.
- Multi-service: separate subfolders such as `frontend/` + `backend/` or `client/` + `server/`. Each is its own service. A single-app project is one service with directory `.`.

**Install command**
- `requirements.txt` → `pip install -r requirements.txt`
- `pyproject.toml` (no requirements.txt) → `pip install -e .`
- `Pipfile` → `pipenv install`, and run commands via `pipenv run`
- Node → `<pm> install`

**Start command** (never guess silently)
- Node: `scripts.dev` in `package.json`, else `scripts.start`.
- Python: `manage.py` → `python manage.py runserver`; `uvicorn`/`fastapi` in dependencies → `uvicorn <module>:app --reload` (module from the file that defines `app`); `flask` in dependencies → `flask run`; otherwise `python main.py` or `python app.py` if one exists.
- If no start command can be determined, write a clearly marked `TODO` placeholder in the scripts and list it in the final report.

## Step 2: Environment files

For the root and each service directory: if `.env` is missing and a template (`.env.example`, `.env.template`, `.env.dist`) exists, copy it to `.env` and print a notice telling the user to fill in real values. If `.env` exists, never overwrite or modify it. Never print `.env` contents.

## Step 3: Rules that apply to all three scripts

- **Run from the script's own directory**, so the launcher works from any current directory.
- **Check prerequisites first**: `python`/`python3` and `node`/package manager on PATH for the stacks detected. Exit with a clear message if missing.
- **Call the venv's interpreter directly**, never an activate script (`Activate.ps1` is exactly what restrictive execution policies block):
  - macOS/Linux: `.venv/bin/python -m pip ...`
  - Windows: `.venv\Scripts\python.exe -m pip ...`
  - `launch.sh` also runs under Git Bash on Windows, where the venv uses `Scripts/`: use `.venv/bin/python` if it exists, else `.venv/Scripts/python.exe`.
- **Create the venv only if missing** (`python3 -m venv .venv`, or `python -m venv .venv` on Windows), one per Python service directory.
- **Install dependencies on every run** (installs are idempotent, so changes to `requirements.txt` or `package.json` are picked up). Allow skipping with `SKIP_INSTALL=1`.
- **Run each service from its own directory.**
- **Stop everything on Ctrl+C** and leave no orphaned processes.
- Never delete files, never hardcode secrets.

## Step 4: Script specifics

### `launch.sh` (macOS, Linux, WSL, Git Bash)
- Start with `#!/usr/bin/env bash` and `set -euo pipefail`; `cd "$(dirname "$0")"`.
- Copy env templates only when `.env` is missing: `[ -f .env ] || { [ -f .env.example ] && cp .env.example .env && echo "Created .env from .env.example, fill in real values"; }`.
- Start each service in a background subshell `( cd <dir> && <start command> ) &`, record its PID, then:
  `trap 'kill "${PIDS[@]}" 2>/dev/null || true' INT TERM EXIT` and `wait`.
- Do not depend on `npx concurrently`.
- Write with LF line endings. Then run `chmod +x launch.sh` where the filesystem supports it (skip on Windows).

### `launch.bat` (Windows Command Prompt)
- Start with `@echo off`, `setlocal`, `cd /d "%~dp0"`.
- Env copy: `if not exist ".env" if exist ".env.example" (copy ".env.example" ".env" >nul & echo Created .env from .env.example, fill in real values)`.
- Venv: `if not exist ".venv" python -m venv .venv`; use `.venv\Scripts\python.exe` directly. Check failures with `if errorlevel 1 exit /b 1`.
- Node installs: `call npm install` (`call` is required for `.cmd` tools).
- Launch each service in its own window: `start "<name>" /D "<service dir>" cmd /k "<start command>"`. Closing a window stops that service.
- Write with CRLF line endings.

### `launch.ps1` (Windows PowerShell)
- Start with `$ErrorActionPreference = 'Stop'` and `Set-Location $PSScriptRoot`.
- Env copy, guarded so it never overwrites: `if (-not (Test-Path .env) -and (Test-Path .env.example)) { Copy-Item .env.example .env; Write-Host 'Created .env from .env.example, fill in real values' }`.
- Venv: `if (-not (Test-Path .venv)) { python -m venv .venv }`; call `.venv\Scripts\python.exe` directly.
- Use `npm.cmd` (not `npm`) when starting Node processes with `Start-Process`.
- Launch each service with `Start-Process -FilePath <exe> -ArgumentList <args> -WorkingDirectory <dir> -NoNewWindow -PassThru` and collect the process objects. Do not use `Start-Job`: in Windows PowerShell 5.1 it starts in the wrong directory, hides output, and dies with the script.
- Wrap in `try { $procs | Wait-Process } finally { foreach ($p in $procs) { taskkill /PID $p.Id /T /F 2>$null | Out-Null } }` so child processes are cleaned up on Ctrl+C.

## Step 5: Verify

- Run `bash -n launch.sh` if bash is available.
- Parse-check `launch.ps1` if `pwsh` is available.
- `launch.bat` cannot be run on Linux/macOS; say it was not executed.
- Do not start the services unless the user asks.

## Step 6: Report

Reply with: files generated, services detected (directory, type, start command), any `TODO` placeholders, any `.env` files created that need real values, and this table:

| Environment | Primary | Fallback |
| :--- | :--- | :--- |
| macOS / Linux / WSL / Git Bash | `./launch.sh` | `bash launch.sh` |
| Windows PowerShell | `.\launch.ps1` | `powershell -ExecutionPolicy Bypass -File .\launch.ps1` (this run only; follow your organization's policy) |
| Windows CMD | `launch.bat` | `cmd /c launch.bat`, or use `launch.ps1` |
