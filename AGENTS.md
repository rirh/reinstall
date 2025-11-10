# Repository Guidelines

## Project Structure & Module Organization
Core logic lives in `reinstall.sh` plus OS profiles such as `cloud-init.yaml`, `ubuntu.yaml`, `debian.cfg`, and `redhat.cfg`. Windows automation sits in `reinstall.bat`, the `windows-*.bat/.xml` templates, and `wmic.ps1`. Shared helpers (`trans.sh`, `frpc.*`, `logviewer.*`) stay at the repository root. Colocate new assets with the OS they serve and keep the `<os>-<purpose>.sh` naming for easy discovery.

## Build, Test, and Development Commands
- `bash reinstall.sh ubuntu 24.04 --hold 1 --ssh-port 2222` dry-runs the installer to confirm networking/SSH.  
- `bash trans.sh alpine --mirror http://mirrors.kernel.org` launches the Alpine rescue environment for recovery.  
- `./get-frpc-url.sh > frpc-url.txt` prints the frp URL consumed by `--frpc-toml`.  
- `.\reinstall.bat windows --image-name "Windows Server 2022 Datacenter" --lang en-us` tests the Windows bootstrapper.  
Attach `/reinstall.log` or `%TEMP%\reinstall.log%` to reports.

## Coding Style & Naming Conventions
Shell scripts declare the right shebang, enable `set -eE`, indent four spaces inside functions, keep functions `snake_case`, and reserve uppercase for exported variables (`SCRIPT_VERSION`). Stick to POSIX-friendly constructs so busybox/dash targets keep working, and lint with `shellcheck`, noting any suppressions. Batch files keep ANSI-safe `mode con cp select=437`, use `rem` comments, wrap mutations in `setlocal EnableDelayedExpansion`, and stay on relative paths. YAML/CFG files use two-space indents plus short comments explaining unusual kernel, storage, or network tweaks.

## Testing Guidelines
There is no CI, so every change needs manual smoke tests. Run Linux commands with `--hold 1` to verify the initrd and networking, then repeat with `--hold 2` to inspect `/os` or `/target` before rebooting. Validate Windows edits inside Hyper-V or QEMU by invoking `reinstall.bat` with the relevant switches (`--allow-ping`, `--rdp-port`, `--add-driver`).

## Commit & Pull Request Guidelines
Follow the `<scope>: <imperative summary>` format (`windows: 添加 win8 链接`, `core: 改成使用 /dev/urandom ...`). Keep scopes short (`core`, `windows`, `netboot`, `docs`) and use the body for rationale plus rollback steps. PRs should link issues when applicable, describe the scenario reproduced, list the commands run, and attach `/reinstall.log` snippets or `logviewer.html` screenshots.

## Security & Configuration Tips
Do not commit passwords, SSH keys, ISO tokens, or customer data; route secrets through `--password`, `--ssh-key`, `--img`, or local files outside version control. Add mirrors only when they host official images, prefer HTTPS, and update both `confhome` and `confhome_cn`. Keep `frpc` samples generic, redact domains in shared logs, and preserve the `grep -iv password` filtering when expanding logging.
