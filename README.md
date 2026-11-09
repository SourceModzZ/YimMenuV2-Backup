# YimMenuV2 – Backup

Experimental mod menu for **Grand Theft Auto V: Enhanced** (PC).

This repository is a backup / fork of [YimMenuV2](https://github.com/YimMenu/YimMenuV2) and contains the full source code to build `YimMenuV2.dll`.

> **Disclaimer:** Use at your own risk. This project is intended for educational and development purposes. Follow Rockstar Games / Take-Two terms of service and applicable law.

---

## Table of Contents

- [Features](#features)
- [Prerequisites](#prerequisites)
- [Forking the Repository](#forking-the-repository)
- [Cloning the Repository](#cloning-the-repository)
- [Building](#building)
  - [Option A – Visual Studio (recommended on Windows)](#option-a--visual-studio-recommended-on-windows)
  - [Option B – Command Line (CMake + Ninja)](#option-b--command-line-cmake--ninja)
  - [Option C – Cross-Compile (Linux → Windows DLL)](#option-c--cross-compile-linux--windows-dll)
- [Finding the Output](#finding-the-output)
- [Usage](#usage)
- [Opening the Menu](#opening-the-menu)
- [Common Issues](#common-issues)
- [Project Structure](#project-structure)
- [Contributing](#contributing)
- [License](#license)

---

## Features

- Modern C++23 menu framework (optimized for speed and extensibility)
- ImGui-based overlay
- Lua scripting (LuaJIT)
- Hooking via MinHook
- Native Windows builds (MSVC / Clang) and cross-compilation with Zig

---

## Prerequisites

### Required (Windows – native builds)

| Tool | Purpose | Download |
|------|---------|----------|
| **Git** | Clone / fork the repository | [git-scm.com](https://git-scm.com/downloads) |
| **CMake** ≥ 3.20 | Generate the build system | [cmake.org](https://cmake.org/download/) |
| **Visual Studio 2022** | C++ compiler, Windows SDK, build tools | [visualstudio.microsoft.com](https://visualstudio.microsoft.com/) |
| **Ninja** *(recommended)* | Fast build generator | via VS Installer or [ninja-build.org](https://ninja-build.org/) |

#### Visual Studio – required workloads / components

When installing **Visual Studio 2022** (Community is fine), enable:

1. Workload **Desktop development with C++**
2. **MSVC v143** (or newer) – x64/x86 build tools
3. **Windows 10/11 SDK**
4. Optional but recommended:
   - **C++ CMake tools for Windows**
   - **C++ Clang tools for Windows** (Clang-CL)
   - **Ninja**

### Optional

| Tool | Purpose |
|------|---------|
| **Clang** | Recommended compiler (fewer warnings, matches CI) |
| **Zig** | Cross-compilation from Linux to Windows |
| **GitHub account** | Forking, issues, pull requests |

### System requirements

- **OS:** Windows 10 / 11 (x64) for native builds and usage
- **Game:** Grand Theft Auto V **Enhanced**
- **Architecture:** **x64** only

---

## Forking the Repository

Create your own copy on GitHub:

1. Open this repository:  
   [https://github.com/SourceModzZ/YimMenuV2-Backup](https://github.com/SourceModzZ/YimMenuV2-Backup)
2. Click **Fork** in the top-right corner
3. Choose your GitHub account as the destination
4. Optionally adjust the repository name and description → **Create fork**

You can then work in **your** fork without affecting the original.

---

## Cloning the Repository

### Clone your fork

```bash
git clone https://github.com/<YOUR-USERNAME>/YimMenuV2-Backup.git
cd YimMenuV2-Backup
```

### Clone this backup directly

```bash
git clone https://github.com/SourceModzZ/YimMenuV2-Backup.git
cd YimMenuV2-Backup
```

### Add the upstream (original) remote *(optional)*

If you want to pull updates from the original project:

```bash
git remote add upstream https://github.com/YimMenu/YimMenuV2.git
git fetch upstream
```

---

## Building

The project produces a **DLL**: `YimMenuV2.dll`.

CMake fetches dependencies (ImGui, MinHook, nlohmann/json, AsyncLogger, LuaJIT) automatically during configuration.

### Option A – Visual Studio (recommended on Windows)

1. Open **Visual Studio 2022**
2. Go to **File → Open → CMake…** and select `CMakeLists.txt` in the project folder  
   *(or open the folder directly – VS detects CMake automatically)*
3. Select a configuration at the top, for example:
   - `x64-Release-MSVC`
   - `x64-Release-Clang` *(recommended)*
4. Build via **Build → Build All** (or `Ctrl+Shift+B`)
5. The output will be at:

```text
out\build\x64-Release-Clang\YimMenuV2.dll
```

*(Exact path depends on the selected configuration.)*

Preconfigured CMake settings are defined in `CMakeSettings.json`.

---

### Option B – Command Line (CMake + Ninja)

#### 1. Open a developer environment

Start **x64 Native Tools Command Prompt for VS 2022**  
*(Start menu → search for “x64 Native Tools”)*

Or from a regular PowerShell:

```powershell
# Adjust the path if needed
& "C:\Program Files\Microsoft Visual Studio\2022\Community\Common7\Tools\Launch-VsDevShell.ps1" -Arch amd64
```

#### 2. Configure the project (Clang – matches CI)

```powershell
cd "C:\path\to\YimMenuV2-Backup"

cmake -DCMAKE_C_COMPILER=clang `
      -DCMAKE_CXX_COMPILER=clang++ `
      -DCMAKE_BUILD_TYPE=RelWithDebInfo `
      -S . -B build -G Ninja
```

#### Alternative: MSVC without Clang

```powershell
cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo -S . -B build -G Ninja
```

#### 3. Build

```powershell
cmake --build ./build --config RelWithDebInfo --target YimMenuV2
```

#### 4. Verify the output

```powershell
Test-Path .\build\YimMenuV2.dll
```

On success, the DLL is at `build\YimMenuV2.dll` (and optionally `YimMenuV2.pdb` for debugging).

---

### Option C – Cross-Compile (Linux → Windows DLL)

On Ubuntu / similar distros with **Zig**:

```bash
sudo apt update
sudo apt install -y cmake ninja-build git

# Install Zig (see https://ziglang.org/download/)
# then:

git clone https://github.com/SourceModzZ/YimMenuV2-Backup.git
cd YimMenuV2-Backup

cmake -DUSE_CROSSCOMPILER=Zig \
      -DCMAKE_BUILD_TYPE=RelWithDebInfo \
      -S . -B build -G Ninja

cmake --build ./build --config RelWithDebInfo --target YimMenuV2
```

Typical Linux output path: `build/libYimMenuV2.dll`

---

## Finding the Output

| Build method | Typical path |
|--------------|--------------|
| Visual Studio (`x64-Release-Clang`) | `out/build/x64-Release-Clang/YimMenuV2.dll` |
| Visual Studio (`x64-Release-MSVC`) | `out/build/x64-Release-MSVC/YimMenuV2.dll` |
| CMake CLI (Windows) | `build/YimMenuV2.dll` |
| Cross-compile (Linux/Zig) | `build/libYimMenuV2.dll` |

The `build/`, `out/`, and `.vs/` directories are listed in `.gitignore` and are **not** committed.

---

## Usage

1. **Optional, recommended:** Download [FSL](https://www.unknowncheats.me/forum/grand-theft-auto-v/616977-fsl-local-gtao-saves.html) and place `WINMM.dll` in your GTA V directory (local saves / account safety).
2. Get an injector, e.g. [Xenos](https://www.unknowncheats.me/forum/general-programming-and-reversing/124013-xenos-injector-v2-3-2-a.html).
3. In the **Rockstar Launcher**, open *Grand Theft Auto V Enhanced* → settings and **disable BattlEye**.  
   On Steam / Epic, you may also need the launch argument: `-nobattleye`
4. Launch GTA V Enhanced.
5. At the main menu, inject `YimMenuV2.dll`.

---

## Opening the Menu

| Key | Action |
|-----|--------|
| `INSERT` | Open / close the menu |
| `Ctrl` + `\` | Alternative hotkey |

---

## Common Issues

### Desynced from public sessions every five minutes

There is currently **no** public BattlEye bypass. Legitimate hosts may remove you due to heartbeat failure.

### Progress disappeared after removing FSL

FSL stores progress locally. Without FSL, that progress will not appear. You can use YimMenuV2 without FSL, but it is not recommended.

### Game no longer starts after removing FSL

Known issue: delete the folder `Documents/GTAV Enhanced/Profiles`.

### CMake cannot find a compiler

- Install Visual Studio with the C++ workload
- Work inside an **x64 Native Tools** / VsDevShell environment
- Verify: `cmake --version`, `cl`, or `clang --version`

### Build fails because of C++23

Make sure you are using Visual Studio **2022** (current toolset). Older MSVC versions only partially support C++23.

---

## Project Structure

```text
YimMenuV2-Backup/
├── cmake/                 # CMake modules & dependencies
├── docs/                  # Lua API & documentation
├── src/                   # Source code
│   ├── core/              # Core framework
│   └── game/              # Game-specific features
├── .github/               # CI/CD (GitHub Actions)
├── CMakeLists.txt         # Main build file
├── CMakeSettings.json     # Visual Studio CMake configurations
├── CONTRIBUTING.md        # Contributor guidelines
└── LICENSE                # GPL-2.0
```

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

Quick notes:

- Implement new features as `Command` / `LoopedCommand` where possible
- Prefer Clang and fix compiler warnings
- Use `static_cast` / `reinterpret_cast` instead of C-style casts
- Per upstream, PRs containing AI-generated code will **not** be accepted

---

## License

This project is licensed under the **GNU General Public License v2.0** — see [LICENSE](LICENSE).

---

## Credits

- Original project: [YimMenu/YimMenuV2](https://github.com/YimMenu/YimMenuV2)  
- Backup repository: [SourceModzZ/YimMenuV2-Backup](https://github.com/SourceModzZ/YimMenuV2-Backup)

Happy forking and building.
