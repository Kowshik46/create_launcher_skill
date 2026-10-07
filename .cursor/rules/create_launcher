---
name: create-launcher
description: Inspects the workspace stack, provisions missing .env files, and generates cross-platform, multi-format launch scripts (launch.sh, launch.bat, launch.ps1) to bypass OS execution policies and launch services concurrently.
---

When the user requests to create a launcher, setup script, or execution configuration:

### Step 1: Detect Tech Stack & Configuration Files
Inspect the workspace root and immediate subdirectories:
- **Python Check:** Look for `requirements.txt`, `Pipfile`, or `pyproject.toml`.
- **Node.js Check:** Look for `package.json`.
- **Multi-Service Check:** Check if frontend and backend exist in separate subfolders (e.g., `frontend/` and `backend/` or `client/` and `server/`).
- **Environment Check:** Check if `.env` exists. If not, look for `.env.example`, `.env.template`, or `.env.dist`.

### Step 2: Environment Provisioning Logic
The generated scripts must run environment setup first:
- If `.env` exists: Do not overwrite or alter it.
- If `.env` is missing: Copy `.env.example` (or other detected template) to `.env` and log a notice in terminal output.

### Step 3: Generate All Three Platform Scripts
Write all three scripts directly to the root directory to guarantee fallback availability across all environments:

#### 1. `launch.sh` (macOS, Linux, WSL, Git Bash)
- Standard Bash layout starting with `#!/bin/bash`.
- Handle `.env` copying safely (`cp -n .env.example .env 2>/dev/null || true`).
- If Python: Check if `.venv` exists; if missing, run `python3 -m venv .venv`, activate via `source .venv/bin/activate`, and install packages.
- If Node.js: Check if `node_modules` exists; if missing, run `npm install`.
- Launch command: Run services concurrently using `npx concurrently` or background jobs (`&`).
- Automatically execute `chmod +x launch.sh` in the workspace terminal.

#### 2. `launch.bat` (Windows Command Prompt)
- Batch syntax starting with `@echo off`.
- Handle `.env` copying: `if not exist ".env" if exist ".env.example" copy ".env.example" ".env"`.
- If Python: Check `if not exist ".venv"`, run `python -m venv .venv`, activate via `call .venv\Scripts\activate.bat`, and install packages.
- If Node.js: Check `if not exist "node_modules"`, run `call npm install`.
- Launch command: Launch services in independent windows via `start cmd /k "..."`.

#### 3. `launch.ps1` (Windows PowerShell)
- PowerShell syntax.
- Handle `.env` copying: `Copy-Item -Path ".env.example" -Destination ".env" -ErrorAction SilentlyContinue`.
- If Python: Check `if (-not (Test-Path ".venv"))`, run `python -m venv .venv`, activate via `& .venv\Scripts\Activate.ps1`, and install dependencies.
- If Node.js: Check `if (-not (Test-Path "node_modules"))`, run `npm install`.
- Launch command: Launch processes using `Start-Job` or background commands.

### Step 4: Output Execution Matrix
Provide a clear status report confirming the files were generated, along with an execution matrix detailing primary commands and execution policy bypass flags.
