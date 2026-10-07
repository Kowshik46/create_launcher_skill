# Universal Launcher Skill 🚀

An open-source AI skill and rule compatible with **Cursor**, **Claude Code**, **Claude.ai**, **Windsurf**, and other AI agents. It automatically detects project tech stacks, provisions missing `.env` files, and generates cross-platform runtime scripts (`launch.sh`, `launch.bat`, `launch.ps1`) to bypass local OS execution policies.

---

## 🌟 Key Features

* **Universal AI Compatibility:** Works out of the box with Claude Code, Cursor Rules, and any LLM-powered development environment.
* **Smart Tech Stack Detection:** Automatically recognizes Python, Node.js, and multi-service full-stack configurations.
* **Environment Provisioning:** Automatically copies `.env.example` to `.env` without overwriting existing configuration files.
* **Execution Policy Resilience:** Generates Bash, Batch, and PowerShell scripts simultaneously, ensuring a working fallback when corporate security blocks `.bat` or `.ps1` files.
* **Concurrent Execution:** Sets up background services and process coordination to run frontend and backend applications simultaneously.

---

## 📂 Repository Structure

```text
universal-launcher-skill/
├── .claude/
│   └── skills/
│       └── create-launcher/
│           └── SKILL.md        # For Claude Code & Claude.ai
├── .cursor/
│   └── rules/
│       └── create-launcher.md  # For Cursor & Composer
├── LICENSE
├── README.md
└── .gitignore
```

---

## 📦 Installation

Add this skill to your workspace by copying the instruction file into the appropriate directory for your editor:

### 🚀 For Cursor
1. Create the Cursor rules directory:
   ```bash
   mkdir -p .cursor/rules
   ```
2. Copy `SKILL.md` into `.cursor/rules/create-launcher.md`.

### 🤖 For Claude Code / Claude.ai
1. Create the Claude skills directory:
   ```bash
   mkdir -p .claude/skills/create-launcher
   ```
2. Copy `SKILL.md` into `.claude/skills/create-launcher/SKILL.md`.

---

## ⚡ How to Use

Once installed, open your AI editor's chat interface (Cursor Composer `Cmd/Ctrl + I`, Cursor Chat `Cmd/Ctrl + L`, or the Claude Code CLI) and prompt:

> *"Create launcher scripts for this project"*

The AI will inspect your repository and output three standalone scripts at your project root:
* `launch.sh` (macOS / Linux / WSL / Git Bash)
* `launch.bat` (Windows Command Prompt)
* `launch.ps1` (Windows PowerShell)

---

## 🛡️ Execution Reference Table

If your system security policy blocks a specific script type, use this fallback table:

| Environment / Shell | Primary Command | Policy Bypass / Fallback Command |
| :--- | :--- | :--- |
| **macOS / Linux / WSL** | `./launch.sh` | `bash launch.sh` |
| **Windows PowerShell** | `.\launch.ps1` | `powershell -ExecutionPolicy Bypass -File .\launch.ps1` |
| **Windows CMD** | `launch.bat` | Run Command Prompt as Administrator |

---

## 📄 License

Distributed under the MIT License. See [LICENSE](LICENSE) for details.
