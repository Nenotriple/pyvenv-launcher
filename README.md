# Python Virtual Environment Launcher (Windows)

A robust Windows batch script that creates, activates, and manages a Python virtual environment
for your project, installs dependencies, and launches your Python application.

## Features

- Automatic venv creation and activation
- Fast Start to skip full setup when a valid venv exists
- Optional requirements installation/update
- Colored, readable logs
- Option to hide the venv directory
- Interactive shell mode (`LAUNCH_SCRIPT=FALSE`)
- Manual interpreter override via `PYTHON_OVERRIDE`
- Safe error handling and clear messaging
- Automatic project name detection and terminal window title
- Quiet mode for less verbose pip output
- Configurable pip timeout

## Prerequisites

- Windows (cmd.exe)
- Python 3 installed (exposed via `python` on `PATH` **or** the Windows `py` launcher)

## Quick start

1. Put `Start.bat` in your project root.
2. Optionally create `requirements.txt` and your app script (default: `app.py`).
3. Double‑click `Start.bat` (or run it from a terminal).

> Note:
>
> - `Start.bat` always operates from its own directory (`pushd %~dp0`).
> - The launcher prefers the Windows `py` command (e.g., `py -3`) when available and
>   falls back to `python` on `PATH`.
> - Set `PYTHON_OVERRIDE` to force a specific interpreter (useful when multiple Python installs exist).
> - The script detects your project name from the folder containing `Start.bat`
>   and sets the terminal window title accordingly. The detected name is also
>   printed in the console header.

### Example structure

```plaintext
your-project/
├── Start.bat
├── app.py
├── requirements.txt
└── .venv/         # auto-created; hidden if enabled
```

## Configuration

Set variables at the top of `Start.bat` to control behavior.

| Variable                        | Default            | Description                                                            |
| ------------------------------- | ------------------ | ---------------------------------------------------------------------- |
| `PYTHON_SCRIPT`                 | `app.py`           | Script to run after setup.                                             |
| `PYTHON_OVERRIDE`               | *(empty)*          | Absolute path to the Python interpreter. Skips auto-detection if set.  |
| `REQUIREMENTS_FILE`             | `requirements.txt` | Requirements file to install from.                                     |
| `VENV_DIR`                      | `.venv`            | Virtual environment directory name.                                    |
| `FAST_START`                    | `FALSE`            | Forces Fast Start if venv is valid.                                    |
| `AUTO_FAST_START`               | `TRUE`             | Auto‑enables Fast Start when venv exists and responds.                 |
| `UPDATE_REQUIREMENTS_ON_LAUNCH` | `FALSE`            | When in Fast Start, also update requirements.                          |
| `AUTO_CLOSE_CONSOLE`            | `TRUE`             | After script completion, close the console.                            |
| `LAUNCH_SCRIPT`                 | `TRUE`             | If `TRUE`, run the Python script; if `FALSE`, open an activated shell. |
| `SET_VENV_HIDDEN`               | `TRUE`             | Sets venv directory attribute to Hidden.                               |
| `ENABLE_COLORS`                 | `TRUE`             | Enables ANSI colors for log messages.                                  |
| `QUIET_MODE`                    | `FALSE`            | Reduces pip output verbosity.                                          |
| `PIP_TIMEOUT`                   | `30`               | Timeout (seconds) for pip operations.                                  |

## Common usage

- First run in a new repository:
  - Creates the venv, upgrades pip, installs requirements, then runs your script.

- Subsequent runs (no changes):
  - With `AUTO_FAST_START=TRUE` (default), activates the existing venv and runs quickly.
  - Set `UPDATE_REQUIREMENTS_ON_LAUNCH=TRUE` to also check and install requirements in Fast Start.

- Interactive shell only:
  - Set `LAUNCH_SCRIPT=FALSE` to prepare the venv and drop into an activated shell.

## How it works

The flowchart below shows the main execution path.

### Main execution path

```mermaid
flowchart TD
    %% ===== Layout & global styles =====
    classDef proc fill:#eef2ff,stroke:#6366f1,color:#111827;
    classDef decision fill:#fff7ed,stroke:#f59e0b,color:#111827;
    classDef success fill:#ecfdf5,stroke:#10b981,color:#065f46;
    classDef error fill:#fef2f2,stroke:#ef4444,color:#7f1d1d;
    classDef start fill:#e0f2fe,stroke:#0284c7,color:#0c4a6e;
    classDef terminal fill:#e0f2fe,stroke:#0284c7,color:#0c4a6e;

    %% ===== Shared requirements installation block =====
    subgraph REQS[Install requirements]
        direction TB
        reqexists{requirements.txt exists?}:::decision
        reqexists -- Yes --> install["pip install -r requirements.txt"]:::success
        reqexists -- No --> reqskip[Skip requirements install]:::proc
        install --> reqdone[(Requirements step complete)]:::success
        reqskip --> reqdone
    end

    %% ===== Entry =====
    start([Start]):::start --> chk{Python found in PATH?}:::decision
    chk -- No --> err[Show error and exit]:::error
    chk -- Yes --> auto{AUTO_FAST_START is TRUE<br/>and venv Python responds?}:::decision

    auto -- Yes --> setfast[Set FAST_START=TRUE]:::proc
    auto -- No --> usecfg[Use configured FAST_START]:::proc
    setfast --> isfast{FAST_START?}:::decision
    usecfg --> isfast

    %% ===== Fast Start path (compact) =====
    subgraph FAST_START_Path [Fast Start]
        direction TB
        act[Activate venv]:::proc --> uprq{UPDATE_REQUIREMENTS_ON_LAUNCH?}:::decision
        uprq -- Yes --> reqexists
        uprq -- No --> join[Skip full setup]:::proc
    end

    isfast -- Yes --> act
    isfast -- No --> fullsetup

    %% ===== Full Setup path (compact) =====
    subgraph FULL_SETUP_Path [Full Setup]
        direction TB
        fullsetup[Begin full setup]:::proc --> valid{venv exists and valid?}:::decision
        valid -- No --> create[Create venv]:::proc --> hide{SET_VENV_HIDDEN?}:::decision
        hide -- Yes --> hide1[Set Hidden attribute]:::proc
        hide -- No --> hide2[Leave visible]:::proc
        hide1 --> act2[Activate venv]:::proc
        hide2 --> act2
        valid -- Yes --> act2
        act2 --> uppip["Upgrade pip (timeout & quiet mode)"]:::proc --> reqexists
        setupdone[Setup complete]:::success
    end

    %% ===== Wiring shared block exits =====
    reqdone --> setupdone
    reqdone --> join

    %% ===== Run or Shell =====
    join --> next
    setupdone --> next

    next --> launchScript{LAUNCH_SCRIPT?}:::decision
    launchScript -- No --> shell["Open cmd with venv activated"]:::proc --> done([Completion]):::terminal
    launchScript -- Yes --> hasScript{PYTHON_SCRIPT specified and exists?}:::decision
    hasScript -- Yes --> run["Run PYTHON_SCRIPT"]:::success --> done
    hasScript -- No --> skipRun[Skip app launch]:::proc --> done
```

### Console behavior

```mermaid
flowchart TD
    classDef proc fill:#eef2ff,stroke:#6366f1,color:#111827;
    classDef decision fill:#fff7ed,stroke:#f59e0b,color:#111827;
    classDef success fill:#ecfdf5,stroke:#10b981,color:#065f46;
    classDef start fill:#e0f2fe,stroke:#0284c7,color:#0c4a6e;
    classDef terminal fill:#e0f2fe,stroke:#0284c7,color:#0c4a6e;

    s([On completion or error]):::start --> errq{Error occurred?}:::decision
    errq -- Yes --> ac1{AUTO_CLOSE_CONSOLE=TRUE?}:::decision
    ac1 -- Yes --> pause[Show 'Press any key…' then close]:::proc --> e1([End]):::terminal
    ac1 -- No --> keep1[Keep console open]:::proc --> e1

    errq -- No --> ac2{AUTO_CLOSE_CONSOLE=TRUE?}:::decision
    ac2 -- Yes --> close[Close console]:::success --> e1
    ac2 -- No --> keep2[Keep console open]:::proc --> e1
```

## Project name detection and terminal title

- The script determines your project name from the parent directory of `Start.bat`
  (e.g., `C:\Repos\myproject\Start.bat` → `myproject`).
- The detected project name is printed in the console header, visually distinct from the launcher header.
- The terminal window title is automatically set to the project name.

## Troubleshooting

- Python not found:
  - Install Python 3 or enable the Windows `py` launcher. See [https://python.org](https://python.org).
  - If you installed Python via the Microsoft Store, ensure the `py` launcher component is included.
  - When using `PYTHON_OVERRIDE`, verify the path points to a valid Python 3 executable.

- Activation failed:
  - Delete `.venv` and re‑run `Start.bat` to recreate the environment.

- Requirements not installed:
  - Confirm `requirements.txt` exists next to `Start.bat`, or update `REQUIREMENTS_FILE`.

- Slow installs or timeouts:
  - Increase `PIP_TIMEOUT`, or set `QUIET_MODE=FALSE` to see more details.

- Reset everything:
  - Close terminals using the venv, delete `.venv`, then run `Start.bat` again.
