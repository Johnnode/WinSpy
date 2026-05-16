<div align="center">

```
██╗    ██╗██╗███╗   ██╗███████╗██████╗ ██╗   ██╗
██║    ██║██║████╗  ██║██╔════╝██╔══██╗╚██╗ ██╔╝
██║ █╗ ██║██║██╔██╗ ██║███████╗██████╔╝ ╚████╔╝ 
██║███╗██║██║██║╚██╗██║╚════██║██╔═══╝   ╚██╔╝  
╚███╔███╔╝██║██║ ╚████║███████║██║        ██║   
 ╚══╝╚══╝ ╚═╝╚═╝  ╚═══╝╚══════╝╚═╝        ╚═╝   
```

**Windows Remote Administration Tool**

![Python](https://img.shields.io/badge/Python-3.10%2B-blue?style=flat-square&logo=python&logoColor=white)
![Platform](https://img.shields.io/badge/Platform-Windows-blue?style=flat-square&logo=windows)
![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)
![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=flat-square)

</div>

---

## Overview

WinSpy is a Windows remote administration framework consisting of a lightweight Python client and a dark-themed GUI controller. Communication is handled over HTTP with Cloudflare tunnel support for portless, certificate-free connectivity.

> **For educational and authorized penetration testing purposes only.**

---

## Features

### 🖥️ HVNC — Hidden Virtual Desktop
- Full hidden desktop session using Windows `CreateDesktopW`
- Per-session explorer shell with taskbar and icons
- Live frame streaming over WebSocket
- Mouse, keyboard, and scroll input relay
- One-click launch of CMD, PowerShell, Notepad, Paint, Chrome, and custom executables

### 📸 Capture
- **Screenshot** — all monitors, uploaded instantly
- **Screen recording** — configurable duration, all monitors
- **Webcam** — single-shot and live stream modes
- **Microphone** — audio recording with configurable duration
- **Live stream** — real-time WebSocket screen feed

### ⌨️ Keylogger
- Low-level `WH_KEYBOARD_LL` hook
- Window title tagging per keystroke
- Start / dump / stop commands

### 🗂️ File System
- Full file browser with tree navigation
- Upload and download files bidirectionally
- Create, rename, delete files and folders
- Directory listing via terminal

### ⚙️ System
- System info (OS, CPU, RAM, network, user, privileges)
- Process manager — list and kill by PID
- Clipboard read and write
- Browser history (Chrome, Edge, Brave)
- Installed tools enumeration

### 🔒 Control
- Screen lock with low-level keyboard and mouse hooks
- Remote shell with full command output
- Self-update — push new client binary from controller
- Single-instance lock via UDP port binding

### 🌐 C2 Transport
- HTTP polling with Cloudflare tunnel integration
- Automatic tunnel startup from the controller
- SSL verification bypass for self-signed / tunnel certs
- Configurable reconnect interval

---

## Architecture

```
┌─────────────────────────┐        HTTP / WebSocket        ┌──────────────────────┐
│   gui_controller.pyw    │ ◄─────────────────────────────► │  winspy_client.py    │
│                         │                                 │                      │
│  ┌─────────────────┐    │    Cloudflare Tunnel (wss://)   │  ┌────────────────┐  │
│  │  Quick Actions  │    │ ◄─────────────────────────────► │  │  HVNC Module   │  │
│  │  Terminal       │    │                                 │  │  Keylogger     │  │
│  │  HVNC Viewer    │    │                                 │  │  Capture       │  │
│  │  File Browser   │    │                                 │  │  HTTP Manager  │  │
│  │  Proc Manager   │    │                                 │  └────────────────┘  │
│  │  Telegram       │    │                                 └──────────────────────┘
│  │  Builder        │    │
│  └─────────────────┘    │
└─────────────────────────┘
```

---

## Controller Tabs

| Tab | Description |
|-----|-------------|
| **Quick Actions** | One-click screenshot, record, webcam, keylogger, HVNC, file browser, process manager |
| **Terminal** | Interactive remote shell with command history |
| **Telegram** | Configure bot token and chat ID; send files and notifications |
| **Downloads** | View and save all files uploaded from the client |
| **Builder** | Generate and optionally compile a configured client |

---

## Builder

The builder patches a copy of `winspy_client.py` with your configuration and optionally compiles it to a standalone EXE via PyInstaller.

**Configurable options:**

| Option | Description |
|--------|-------------|
| C2 Host / Port | Controller address the client beacons to |
| Reconnect interval | Seconds between reconnect attempts |
| Mutex name | Prevents multiple client instances |
| Install path | Where the client copies itself on the target |
| Persistence | `Task Scheduler`, `Run Key`, `Both`, or `None` |
| Anti-Debug | Detects debugger presence and exits |
| Anti-VM | Detects sandbox / VM environment and exits |
| Watchdog | Spawns a monitor process that respawns the client if killed |
| UAC bypass | Attempts privilege escalation on startup |
| Stealth mode | Suppresses all console output |
| PyInstaller | `--onefile`, `--noconsole`, `--upx`, custom icon |

---

## Requirements

### Controller
```
Python 3.10+
customtkinter
requests
Pillow
opencv-python
cloudflared  (on PATH, for tunnel support)
```

### Client (auto-installed on first run)
```
Python 3.10+
requests
websocket-client
Pillow
pywin32
pyinstaller  (optional, for builder)
```

---

## Setup

**1. Clone the repo**
```bash
git clone https://github.com/youruser/winspy.git
cd winspy
```

**2. Install controller dependencies**
```bash
pip install customtkinter requests pillow opencv-python
```

**3. Configure Cloudflare tunnel** *(optional but recommended)*

Place your tunnel token and hostname in the controller config. The controller starts and stops cloudflared automatically per session.

**4. Launch the controller**
```bash
pythonw gui_controller.pyw
```

**5. Build a client**

Open the **Builder** tab, configure your C2 settings, select evasion options, and click **Build**. Deploy the output to the target.

---

## HVNC Usage

1. Connect a client
2. Click **Quick Actions → HVNC → Start**
3. Wait for the viewer window to show a live frame
4. Click **Explorer** in the HVNC toolbar to initialize the hidden desktop shell
5. Use CMD, PowerShell, Chrome, or **Run…** to launch applications on the hidden desktop

The hidden desktop is completely invisible to the target user. All activity occurs in an isolated `WinSta0\WinSpy_HVNC` desktop session.

---

## Project Structure

```
winspy/
├── gui_controller.pyw   # Controller GUI (run with pythonw)
├── winspy_client.py     # Client agent
└── README.md
```

---

## Disclaimer

This tool is provided for **authorized security research and educational purposes only**. Using WinSpy against systems you do not own or have explicit written permission to test is illegal. The authors accept no liability for misuse.
