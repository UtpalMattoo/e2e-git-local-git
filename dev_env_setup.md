# 🛠️ Local Dev & Build Environment Setup

## ✅ Goal

Enable `make` support to build a playground app locally with proper Python environment management via Poetry

---

## 1. 🔧 Installing Make on Windows

I installed both WSL and a terminal in VS Code, and also Chocolatey.  
For dependency management, **I used Chocolatey to install make**.

### ✅ Option 1: WSL

- Install WSL + Ubuntu → [WSL install guide](https://learn.microsoft.com/en-us/windows/wsl/install)
- Use the `make` command inside the Ubuntu terminal
- VS Code WSL integration → [VSCode WSL Docs](https://code.visualstudio.com/docs/remote/wsl)

### ✅ Option 2: Chocolatey

- Install Chocolatey → [Install Guide](https://chocolatey.org/install)
- Run:  
  ```bash
  choco install make

    Ensure make.exe is added to your system PATH

    In VS Code:
    Go to File > Preferences > Settings > Extensions > Makefile → add the path to make.exe

⚠️ Avoid Manual Installation of Make

Manual installation introduces risks:

    ❌ No auto-updates or dependency resolution

    ❌ Missing PATH integration

    ❌ Harder to uninstall or replicate on another machine

🛡️ Why Use Chocolatey?

Using  Chocolatey for package management avoids issues that occur from manual installations:

    Dependency Management
    Chocolatey automatically installs required dependencies.

    Updates
    Update all packages with one command:

choco upgrade all

PATH Management
Chocolatey configures system PATH automatically.

Clean Uninstallation
Easily remove with:

    choco uninstall make

    Security and Verification
    Chocolatey’s packages are moderated and verified. Manual downloads require you to vet sources yourself.

2. 🐍 Python Environment Setup with Poetry

Run these commands (PowerShell):

Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
iwr -useb get.scoop.sh | iex
scoop install pipx
pipx ensurepath
pipx install poetry

✅ Use Python from python.org

The Python.org version provides:

    ✅ Reliable venv support

    ✅ Compatibility with Poetry, pipx, and gcloud

    https://github.com/orgs/python-poetry/discussions/9674#discussioncomment-11358539

📦 Install Project Dependencies

poetry install --with streamlit,jupyter

✅ Why Use Poetry?

    📦 Dependency Isolation: Keeps your project dependencies separate from global Python

    🔁 Reproducibility: poetry.lock ensures consistent installs across environments

    ☑️ Built-in Virtualenv Support: No manual venv needed

    👥 Team-Friendly: Better than pip + requirements.txt for larger or shared projects

🔗 Helpful Links used during setup

    Install Make on Windows - https://community.chocolatey.org/packages/make

    Download Python from python.org -  https://www.python.org/downloads/

    Poetry: pipx and Scoop Install Discussion - https://github.com/orgs/python-poetry/discussions/9674#discussioncomment-11358539


