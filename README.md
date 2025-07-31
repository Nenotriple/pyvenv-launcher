# Python Virtual Environment Launcher

A Windows batch script that automatically sets up and manages Python virtual environments, then launches your Python application.

## Features

- **Automatic Virtual Environment Management**: Creates and activates Python virtual environments
- **Smart Fast Start**: Skips setup when virtual environment already exists and is valid
- **Requirements Management**: Automatically installs/updates packages from requirements.txt
- **Colored Output**: Enhanced console output with color coding for better readability
- **Hidden Directory Option**: Can automatically hide the virtual environment directory
- **Interactive Shell Mode**: Option to drop into an interactive shell instead of running a script
- **Error Handling**: Comprehensive error checking and user-friendly error messages

## Configuration

Edit the configuration section at the top of `Start.bat` to customize behavior:

### Basic Configuration
- `PYTHON_SCRIPT`: The Python script to launch (default: `app.py`)
- `REQUIREMENTS_FILE`: Requirements file to install packages from (default: `requirements.txt`)
- `VENV_DIR`: Virtual environment directory name (default: `.venv`)

### Behavior Options
- `FAST_START`: Skip setup checks (default: `FALSE`)
- `AUTO_FAST_START`: Automatically use fast start when possible (default: `TRUE`)
- `AUTO_CLOSE_CONSOLE`: Close console after script completion (default: `TRUE`)
- `SET_VENV_HIDDEN`: Set virtual environment directory as hidden (default: `TRUE`)
- `SETUP_ONLY`: Only setup environment, don't run script (default: `FALSE`)

### Display Options
- `ENABLE_COLORS`: Use colored console output (default: `TRUE`)
- `QUIET_MODE`: Suppress pip output messages (default: `FALSE`)

### Advanced Options
- `PIP_TIMEOUT`: Timeout for pip operations in seconds (default: `30`)

## Usage

1. Place `Start.bat` in your project directory
2. Configure the script by editing the variables at the top
3. Double-click `Start.bat` or run it from command line

### Setup Only Mode
Set `SETUP_ONLY=TRUE` to only create/update the virtual environment without running your script. This will drop you into an interactive shell with the virtual environment activated.

### Fast Start Mode
When `AUTO_FAST_START=TRUE`, the script will automatically detect if a valid virtual environment exists and skip the full setup process, only checking if requirements need updating.

## Requirements

- Python 3.3+ (with venv module)
- Windows operating system
- Optional: `requirements.txt` file for automatic package installation

## Example Project Structure

```
your-project/
├── Start.bat
├── app.py
├── requirements.txt
└── .venv/ (created automatically, hidden if SET_VENV_HIDDEN=TRUE)
```

## Error Handling

The script includes comprehensive error checking for:
- Python installation validation
- Virtual environment creation failures
- Package installation errors
- Script execution problems

All errors are logged with clear, color-coded messages to help with troubleshooting.

## Version

Current version: 1.01

Created by: [github.com/Nenotriple](https://github.com/Nenotriple)
