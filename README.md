# Universal Launcher Skill 🚀

An open-source AI skill and rule compatible with **Cursor**, **Claude Code**, **Claude.ai**, **Windsurf**, and other AI agents. It automatically detects project tech stacks, provisions missing `.env` files, and generates cross-platform runtime scripts (`launch.sh`, `launch.bat`, `launch.ps1`) so you can start a project on any OS without changing system execution policy.

---

## 🌟 Key Features

* **Universal AI Compatibility:** Works out of the box with Claude Code, Cursor Rules, and any LLM-powered development environment.
* **Smart Tech Stack Detection:** Automatically recognizes Python, Node.js, and multi-service full-stack configurations.
* **Environment Provisioning:** Automatically copies `.env.example` to `.env` without overwriting existing configuration files.
* **Execution Policy Resilience:** Generates Bash, Batch, and PowerShell scripts simultaneously, so at least one works when a machine's policy restricts `.bat` or `.ps1` files.
* **Concurrent Execution:** Sets up background services and process coordination to run frontend and backend applications simultaneously.

---

## 📂 Repository Structure

```text
universal-launcher-skill/
├── .claude/
│   └── skills/
│       └── create-launcher/
│           └── SKILL.md        # For Claude Code & Claude.ai
├── .claude-plugin/
│   └── marketplace.json        # Claude Code plugin marketplace
├── .cursor-plugin/
│   └── marketplace.json        # Cursor plugin marketplace
├── plugins/
│   └── create-launcher/        # Claude Code and Cursor plugin
│       ├── .claude-plugin/plugin.json
│       ├── .cursor-plugin/plugin.json
│       └── skills/create-launcher/SKILL.md
├── .cursor/
│   └── rules/
│       └── create-launcher.mdc # For Cursor & Composer
├── LICENSE
├── README.md
└── .gitignore
```

---

## 📦 Installation

### 🔌 Claude Code plugin (recommended)
```bash
claude plugin marketplace add Kowshik46/create_launcher_skill
claude plugin install create-launcher@kowshik-tools
```
Inside a session, use `/plugin marketplace add Kowshik46/create_launcher_skill` instead. Run it with `/create-launcher:create-launcher` or just ask "Create launcher scripts for this project". Keep `plugins/create-launcher/skills/create-launcher/SKILL.md` in sync with `.claude/skills/create-launcher/SKILL.md` when editing.

### 🧩 Cursor plugin
The same `plugins/create-launcher/` folder also has a `.cursor-plugin/plugin.json`, and the repo root has `.cursor-plugin/marketplace.json`, so it can be listed in Cursor's plugin marketplace. Submit the repo at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish) (reviewed by the Cursor team), or use the manual rule below. *Not yet tested inside Cursor.*

### Manual install
Clone this repo (or download it), then copy the file for your editor into your own project:

### 🚀 For Cursor
```bash
mkdir -p .cursor/rules
cp /path/to/create_launcher_skill/.cursor/rules/create-launcher.mdc .cursor/rules/
```

### 🤖 For Claude Code
```bash
mkdir -p .claude/skills/create-launcher
cp /path/to/create_launcher_skill/.claude/skills/create-launcher/SKILL.md .claude/skills/create-launcher/
```
Use `~/.claude/skills/create-launcher/` instead to install it for all projects.

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

If a script won't run directly, use the fallback. Where your organization's policy forbids running local scripts, follow that policy instead.

| Environment / Shell | Primary Command | Fallback Command |
| :--- | :--- | :--- |
| **macOS / Linux / WSL / Git Bash** | `./launch.sh` | `bash launch.sh` |
| **Windows PowerShell** | `.\launch.ps1` | `powershell -ExecutionPolicy Bypass -File .\launch.ps1` (affects this run only) |
| **Windows CMD** | `launch.bat` | `cmd /c launch.bat`, or use `launch.ps1` |

---

## 📄 License

Distributed under the MIT License. See [LICENSE](LICENSE) for details.
