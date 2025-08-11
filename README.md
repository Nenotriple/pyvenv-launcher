# Python Virtual Environment Launcher (Windows)

A robust Windows batch script that creates, activates, and manages a Python virtual environment
for your project, installs dependencies, and launches your Python application.

## Highlights

- Automatic venv creation and activation
- Smart Fast Start to skip full setup when a valid venv exists
- Optional requirements installation/update
- Colored, readable logs
- Option to hide the venv directory
- Setup-only interactive shell mode
- Safe error handling and clear messaging

## Quick Start

1. Put `Start.bat` in your project root.
2. Optionally create `requirements.txt` and your app script (default: `app.py`).
3. Double-click `Start.bat` (or run it from a terminal).

> **Note:**
> - `Start.bat` always operates from its own directory (`pushd %~dp0`), so relative paths are evaluated from the script’s location.
> - The venv is created using `python -m venv` with the Python found in `PATH`.

**Example structure:**

```plaintext
your-project/
├── Start.bat
├── app.py
├── requirements.txt
└── .venv/         # auto-created; hidden if enabled
```

## Configuration

Edit the variables at the top of `Start.bat` to match your needs.

| Variable                         | Default            | Description                                                                                      |
|----------------------------------|--------------------|--------------------------------------------------------------------------------------------------|
| `PYTHON_SCRIPT`                  | `app.py`           | Script to run after setup. If empty, no script is launched. Path is relative to `Start.bat`. |
| `REQUIREMENTS_FILE`              | `requirements.txt` | Requirements file to install from. If missing, install step is skipped. |
| `VENV_DIR`                       | `.venv`            | Virtual environment directory name, created in the script directory. |
| `FAST_START`                     | `FALSE`            | Forces fast start mode (skip full setup) if the venv is valid. If activation fails, falls back to full setup. |
| `AUTO_FAST_START`                | `TRUE`             | Auto-enables fast start when a valid venv exists and responds to `python --version`. |
| `UPDATE_REQUIREMENTS_ON_LAUNCH`  | `FALSE`            | When in fast start, also install/update requirements from `REQUIREMENTS_FILE`. |
| `AUTO_CLOSE_CONSOLE`             | `TRUE`             | After script completion, close the console. If `FALSE`, keeps an interactive shell open. On errors, a “Press any key” prompt is shown only when `TRUE`. |
| `SETUP_ONLY`                     | `FALSE`            | Only setup the environment and drop into an interactive shell (cmd) with the venv activated; does not run `PYTHON_SCRIPT`. |
| `SET_VENV_HIDDEN`                | `TRUE`             | Sets the venv directory attribute to Hidden after creation. |
| `ENABLE_COLORS`                  | `TRUE`             | Enables ANSI colors for log messages. |
| `QUIET_MODE`                     | `FALSE`            | Reduces pip output verbosity (applies to pip upgrade and requirements install). |
| `PIP_TIMEOUT`                    | `30`               | Timeout (seconds) for pip operations. |

## How It Works

1. **Validate Python**

   - Verifies Python is available in `PATH` and logs its version.

2. **Detect Fast Start**

   - If `AUTO_FAST_START` is `TRUE` and `%VENV_DIR%\Scripts\python.exe` exists and responds to `--version`:
     - `FAST_START` is set to `TRUE` automatically.
   - If `FAST_START` is `TRUE`:
     - Activate the venv.
     - If `UPDATE_REQUIREMENTS_ON_LAUNCH` is `TRUE`, install/update requirements.
     - Skip full setup.

3. **Full Setup** (when fast start is not used)

   - Create venv if absent (removes a non-functional directory if needed).
   - Optionally mark venv directory as hidden.
   - Activate the venv.
   - Upgrade pip (with timeout and optional quiet mode).
   - Install requirements from `REQUIREMENTS_FILE` (if present).

4. **Run or Shell**

   - If `SETUP_ONLY` is `TRUE`: start an interactive shell (cmd) with the venv activated.
   - Otherwise, launch `PYTHON_SCRIPT` if specified and present.

5. **Console Behavior**

   - If `AUTO_CLOSE_CONSOLE` is `FALSE`, the console remains open (`cmd /k`) after the script completes.
   - On errors, a “Press any key to exit…” prompt appears only when `AUTO_CLOSE_CONSOLE` is `TRUE`.

## Usage Scenarios

- **First run in a new repo:**
  - The script creates the venv, upgrades pip, installs requirements, and runs your script.

- **Subsequent runs with no changes:**
  - With `AUTO_FAST_START=TRUE` (default), it activates the existing venv and runs quickly.
  - Set `UPDATE_REQUIREMENTS_ON_LAUNCH=TRUE` if you want fast start to also check and install requirements each time.

- **Setup only:**
  - Set `SETUP_ONLY=TRUE` to prepare the venv and drop into an activated interactive shell.
