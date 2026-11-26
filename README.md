# Windows WSL 2, Ubuntu & Linux Environment Setup Guide

A beginner-friendly, step-by-step guide to install, configure, verify, troubleshoot, back up, restore, and completely remove **Windows Subsystem for Linux (WSL 2)** and Ubuntu on Windows 10 or Windows 11.

> **Recommended approach:** Use WSL 2 with Ubuntu for most Linux development tasks on Windows. Use a traditional virtual machine only when you specifically need a separate virtualized computer.

---

## 1. Repository purpose

This repository documents a repeatable Windows-to-Linux setup, including:

- Windows prerequisites and virtualization checks
- WSL 2 installation
- Ubuntu installation and first-time configuration
- Linux package updates and essential tools
- Windows/Linux file-system access
- WSL verification and common commands
- Backup, restore, and migration
- Ubuntu-only removal
- Full WSL feature removal and optional cleanup
- Troubleshooting and recovery

The instructions are intended for personal learning, development, DevOps, cloud engineering, scripting, and container-related labs.

## 2. Recommended GitHub repository structure

Create a GitHub repository named:

`windows-wsl2-ubuntu-setup-guide`

Use the following folder and file structure:

```text
windows-wsl2-ubuntu-setup-guide/
│
├── README.md
│
├── installation/
│   ├── 01-windows-prerequisites.md
│   ├── 02-enable-wsl-and-install-ubuntu.md
│   └── 03-first-time-ubuntu-setup.md
│
├── configuration/
│   ├── 01-update-and-upgrade-ubuntu.md
│   ├── 02-essential-linux-tools.md
│   └── 03-windows-linux-file-access.md
│
├── verification/
│   └── 01-verify-wsl-and-ubuntu.md
│
├── backup-and-restore/
│   └── 01-export-import-wsl-distribution.md
│
├── troubleshooting/
│   └── 01-common-wsl-issues.md
│
├── uninstall-and-rollback/
│   ├── 01-uninstall-ubuntu.md
│   ├── 02-disable-wsl-features.md
│   └── 03-cleanup-and-disk-space.md
│
└── LICENSE
```

**How to use this structure:** Keep this top-level `README.md` as the landing page. Put detailed procedures in the corresponding subfolders. The file names are numbered so readers can follow the recommended order.

---

## 3. Before you begin

### 3.1 Supported Windows versions

WSL is supported on supported Windows 10 and Windows 11 releases. Some commands and features vary by Windows version. Install current Windows updates before starting.

### 3.2 Check your Windows version

1. Press `Windows + R`.
2. Type `winver`.
3. Press Enter.
4. Review the Windows version and OS build.

You can also run:

```powershell
winver
```

### 3.3 Check hardware virtualization

1. Press `Ctrl + Shift + Esc` to open Task Manager.
2. Select **Performance**.
3. Select **CPU**.
4. Find **Virtualization**.

It should show **Enabled**.

If it shows Disabled, enable Intel VT-x or AMD-V/SVM in your computer's UEFI/BIOS settings. The setting name and location depend on the manufacturer. Do not change unrelated firmware settings.

### 3.4 Open an elevated terminal

For installation and Windows feature commands:

1. Open Start.
2. Search for **Windows PowerShell** or **Terminal**.
3. Right-click it.
4. Select **Run as administrator**.

Use a normal, non-administrator terminal for everyday Linux commands unless a step explicitly requires elevation.

---

## 4. Installation

### Step 1 — Check whether WSL is already installed

In PowerShell, run:

```powershell
wsl --status
wsl --list --verbose
```

If WSL is not installed, continue to Step 2. If a distribution is already installed, do not install it again without checking its current state.

### Step 2 — Install WSL and Ubuntu

In an elevated PowerShell or Windows Terminal:

```powershell
wsl --install -d Ubuntu
```

This installs WSL and the Ubuntu distribution. Windows may ask you to restart. Restart when prompted.

If you want to see available distributions:

```powershell
wsl --list --online
```

To install a specific available distribution, replace `Ubuntu` with its exact listed name:

```powershell
wsl --install -d <DistributionName>
```

Do not type the angle brackets; replace the placeholder with the actual distribution name.

### Step 3 — Complete Ubuntu first launch

1. Open **Ubuntu** from the Start menu. It may also launch automatically after installation.
2. Wait while the Linux file system is initialized.
3. Enter a Linux username when prompted.
4. Enter a password.
5. Confirm the password.

The Linux username and password are separate from your Windows sign-in. When typing the Linux password, the terminal normally displays no characters or asterisks. This is expected.

### Step 4 — Confirm the distribution is using WSL 2

In PowerShell:

```powershell
wsl --list --verbose
```

Example output:

```text
  NAME      STATE           VERSION
* Ubuntu    Running         2
```

If Ubuntu shows version `1`, convert it to WSL 2:

```powershell
wsl --set-version Ubuntu 2
```

Set WSL 2 as the default for future distributions:

```powershell
wsl --set-default-version 2
```

---

## 5. First-time Ubuntu configuration

Open the Ubuntu terminal and run the following commands.

### Step 1 — Refresh package information

```bash
sudo apt update
```

### Step 2 — Install available package updates

```bash
sudo apt upgrade -y
```

### Step 3 — Install common development and administration tools

```bash
sudo apt install -y \
  curl \
  wget \
  git \
  vim \
  nano \
  unzip \
  zip \
  tree \
  jq \
  ca-certificates \
  gnupg \
  lsb-release \
  build-essential \
  software-properties-common
```

### Step 4 — Verify the tools

```bash
git --version
curl --version
python3 --version
```

Python 3 may already be installed. If it is not:

```bash
sudo apt install -y python3 python3-pip python3-venv
```

### Step 5 — Configure Git identity (optional)

Use your own Git name and email:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
```

Check the configuration:

```bash
git config --global --list
```

Do not put passwords, access tokens, private keys, or other secrets in Git configuration or in this repository.

---

## 6. Useful Windows and Linux file-system access

### Access Windows files from Ubuntu

Windows drives are normally mounted under `/mnt`.

Examples:

```bash
cd /mnt/c
ls
cd /mnt/c/Users
```

For a Windows project folder:

```bash
cd /mnt/c/Users/<WindowsUser>/Documents
```

Replace `<WindowsUser>` with your Windows account folder name.

### Open the current Linux directory in Windows File Explorer

From Ubuntu:

```bash
explorer.exe .
```

### Open the Linux home directory from Windows

In File Explorer's address bar, enter:

```text
\\wsl$\Ubuntu\home\<LinuxUser>
```

Replace `<LinuxUser>` with your Ubuntu username. Recent WSL versions may also expose distributions through the `\\wsl.localhost` path.

### File-system performance guidance

- For Linux command-line development, keep projects in the Linux file system, such as `/home/<LinuxUser>/projects`.
- For Windows applications that need to work directly with files, use the Windows file system, such as `C:\Users\<WindowsUser>\Projects`.
- Avoid repeatedly moving large project trees between Windows and Linux file systems.
- Do not manually edit or move WSL's internal virtual disk files while the distribution is running.

---

## 7. Verification checklist

Run these commands in PowerShell:

```powershell
wsl --status
wsl --version
wsl --list --verbose
```

Run these commands in Ubuntu:

```bash
uname -a
cat /etc/os-release
df -h
free -h
whoami
pwd
```

Expected checks:

- Ubuntu appears in `wsl --list --verbose`.
- Ubuntu's VERSION column shows `2`.
- `whoami` displays the Linux username you created.
- `cat /etc/os-release` identifies Ubuntu.
- `df -h` and `free -h` return system resource information.

Some older WSL installations may not support every option shown above. If `wsl --version` is unavailable, update WSL using:

```powershell
wsl --update
```

---

## 8. Essential WSL commands

Run these in PowerShell:

| Purpose | Command |
|---|---|
| List installed distributions | `wsl --list --verbose` |
| List available distributions | `wsl --list --online` |
| Launch default distribution | `wsl` |
| Launch Ubuntu | `wsl -d Ubuntu` |
| Shut down all WSL instances | `wsl --shutdown` |
| Stop one distribution | `wsl --terminate Ubuntu` |
| Update WSL | `wsl --update` |
| Check WSL status | `wsl --status` |
| Set WSL 2 as default | `wsl --set-default-version 2` |
| Set Ubuntu as default | `wsl --set-default Ubuntu` |

To see the full list of supported options:

```powershell
wsl --help
```

---

## 9. Backup and restore

> **Important:** Export your distribution before unregistering it or making major changes. An export is a backup file; store it on a drive with enough free space.

### Export Ubuntu

In PowerShell:

```powershell
wsl --shutdown
New-Item -ItemType Directory -Force C:\WSL-Backup
wsl --export Ubuntu C:\WSL-Backup\Ubuntu-backup.tar
```

Confirm that the TAR file exists and has a plausible size:

```powershell
Get-Item C:\WSL-Backup\Ubuntu-backup.tar
```

Copy the backup to another safe location if it contains important work.

### Restore Ubuntu as a new distribution

Create a destination directory:

```powershell
New-Item -ItemType Directory -Force C:\WSL\Ubuntu-Restored
```

Import the backup:

```powershell
wsl --import Ubuntu-Restored C:\WSL\Ubuntu-Restored C:\WSL-Backup\Ubuntu-backup.tar --version 2
```

Verify:

```powershell
wsl --list --verbose
wsl -d Ubuntu-Restored
```

An imported distribution may start as `root`. To create or select a normal user, use the distribution's supported configuration method. Do not assume the Microsoft Store launcher's `ubuntu config --default-user` command applies to every imported distribution.

---

## 10. Uninstall and rollback

Choose the level of removal you actually need. **Removing Ubuntu is not the same as disabling WSL.**

### Option A — Uninstall Ubuntu only

Use this if you want to remove the Ubuntu distribution but may use WSL again later.

1. Back up important Linux files.
2. Open PowerShell.
3. Check the exact distribution name:

```powershell
wsl --list --verbose
```

4. Stop WSL:

```powershell
wsl --shutdown
```

5. Unregister Ubuntu:

```powershell
wsl --unregister Ubuntu
```

**Warning:** `wsl --unregister Ubuntu` permanently deletes that distribution's Linux file system and data. Verify the distribution name and backup before running it.

6. If Ubuntu was installed as a Microsoft Store app, open **Settings → Apps → Apps & features** (wording varies by Windows version), find Ubuntu, and uninstall the app if it remains listed.

7. Verify:

```powershell
wsl --list --verbose
```

### Option B — Disable WSL Windows features

Use this only if you want to stop using WSL itself.

Before proceeding:

- Back up and unregister distributions you no longer need.
- Understand that other software, including some container or virtualization tools, may rely on Virtual Machine Platform.
- Do not disable Virtual Machine Platform if another application requires it.

Open PowerShell as Administrator and run:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux -NoRestart
```

If you also want to disable the WSL 2 virtualization platform and have confirmed nothing else needs it:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform -NoRestart
```

Restart Windows when prompted or after completing the changes:

```powershell
Restart-Computer
```

Check feature states:

```powershell
Get-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
Get-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
```

A feature may remain enabled intentionally if another application uses it. Do not treat that alone as a failed WSL removal.

### Option C — Remove a leftover Linux item from File Explorer

In some Windows configurations, a Linux entry may remain visible in File Explorer after distributions have been removed or WSL has been disabled.

First confirm that no distributions remain:

```powershell
wsl --list --verbose
```

If the Linux navigation entry remains, prefer checking Windows/WSL state and restarting File Explorer or Windows first. Registry changes are an advanced, optional cosmetic step and are not required to uninstall WSL.

If you choose to edit the registry:

1. Create a restore point or export the specific registry key first.
2. Open Registry Editor (`regedit`).
3. Navigate only to the exact Linux namespace key documented for your Windows installation.
4. Change only the relevant namespace visibility value if it exists and you understand its effect.
5. Do not delete broad registry branches or take ownership of unrelated system keys.
6. Restart File Explorer or Windows and verify the result.

Registry paths can differ between Windows versions. Do not blindly copy a registry path from another computer or run an unverified `.reg` file.

### Option D — Recover disk space

1. Remove only distributions you have intentionally unregistered.
2. Empty the Recycle Bin if appropriate.
3. Open **Settings → System → Storage → Temporary files**.
4. Review the categories carefully before deleting anything.
5. Run Windows Storage Sense or Disk Cleanup if appropriate.
6. Restart Windows and check free space.

Check free space in PowerShell:

```powershell
Get-PSDrive -Name C
```

Do not manually delete files from Windows system folders or WSL's internal package/storage directories. Do not delete a WSL virtual disk while its distribution is registered or running.

---

## 11. Troubleshooting

### Issue 1 — `wsl` is not recognized

Possible causes: WSL is not installed, Windows is outdated, or the required feature is disabled.

Try:

```powershell
wsl --install
```

If the command is unavailable, install current Windows updates and follow Microsoft's official WSL installation guidance.

### Issue 2 — Virtualization is disabled

1. Confirm the status in Task Manager → Performance → CPU.
2. Restart the computer and enter UEFI/BIOS setup.
3. Enable Intel Virtualization Technology (VT-x) or AMD-V/SVM, as applicable.
4. Save changes and restart Windows.
5. Recheck Task Manager.

The exact firmware steps depend on the PC manufacturer.

### Issue 3 — Ubuntu installation is stuck or fails

Run in elevated PowerShell:

```powershell
wsl --update
wsl --shutdown
```

Restart Windows and retry. Check that the system has adequate free disk space and current Windows updates.

### Issue 4 — Ubuntu opens as root

Check the current user:

```bash
whoami
```

If it displays `root`, use the distribution's documented default-user configuration procedure. For a Store-installed Ubuntu distribution, the launcher command may be available, for example:

```powershell
ubuntu.exe config --default-user <LinuxUser>
```

The executable name varies by Ubuntu release. Check the actual installed launcher and its supported options before using this command. Imported distributions may require a different configuration method.

### Issue 5 — WSL networking or DNS is not working

1. Check whether Windows itself has internet access.
2. Restart WSL:

```powershell
wsl --shutdown
```

3. Reopen Ubuntu.
4. Test name resolution:

```bash
getent hosts ubuntu.com
```

Avoid copying random DNS or networking configuration files from the internet. WSL networking behavior varies by version and configuration.

### Issue 6 — A distribution is not listed

Run:

```powershell
wsl --list --all --verbose
```

If it is not listed, it may not be installed for the current Windows user. Do not run `wsl --unregister` against a name that is not listed.

### Issue 7 — Linux File Explorer entry remains

- Confirm distributions are removed.
- Run `wsl --shutdown`.
- Restart File Explorer or Windows.
- If it remains, treat registry visibility changes as an optional advanced cosmetic fix, not as a required WSL uninstall step.

---

## 12. Security and maintenance recommendations

- Install Ubuntu updates regularly with `sudo apt update` and `sudo apt upgrade`.
- Use a non-root Linux user for daily work.
- Use `sudo` only for administrative tasks.
- Never commit credentials, SSH private keys, access tokens, or secrets to GitHub.
- Back up important Linux project data outside the WSL distribution.
- Keep Windows, WSL, and Ubuntu updated.
- Review scripts before running them, especially scripts that change Windows features, registry settings, or storage.
- Do not run destructive commands unless you understand the target and have a verified backup.

---

## 13. Official documentation

Use official documentation as the final reference because Windows and WSL behavior can change:

- [Microsoft — Install WSL](https://learn.microsoft.com/windows/wsl/install)
- [Microsoft — Basic WSL commands](https://learn.microsoft.com/windows/wsl/basic-commands)
- [Microsoft — Set up a WSL development environment](https://learn.microsoft.com/windows/wsl/setup/environment)
- [Microsoft — WSL troubleshooting](https://learn.microsoft.com/windows/wsl/troubleshooting)
- [Ubuntu — Official documentation](https://help.ubuntu.com/)

---

## 14. End-to-end checklist

### Installation

- [ ] Windows version checked and updated
- [ ] Hardware virtualization enabled
- [ ] WSL installed
- [ ] Ubuntu installed
- [ ] Ubuntu first-run username and password created
- [ ] Ubuntu confirmed to use WSL 2

### Configuration and verification

- [ ] Ubuntu packages updated
- [ ] Essential Linux tools installed
- [ ] Git identity configured if needed
- [ ] Windows/Linux file access tested
- [ ] WSL and Ubuntu verification commands completed

### Backup and removal readiness

- [ ] Important Linux files backed up
- [ ] Export backup verified
- [ ] Correct removal option selected
- [ ] Distribution name verified before unregistering
- [ ] Windows features changed only when appropriate
- [ ] Disk cleanup performed safely

---

## 15. Suggested Git commit workflow

From the root of your local repository:

```powershell
git status
git add README.md installation configuration verification backup-and-restore troubleshooting uninstall-and-rollback
git commit -m "Add complete WSL 2 and Ubuntu setup documentation"
git branch -M main
git remote add origin https://github.com/<GitHubUsername>/windows-wsl2-ubuntu-setup-guide.git
git push -u origin main
```

Replace `<GitHubUsername>` with your GitHub username. If the remote already exists, do not add it again; check it with:

```powershell
git remote -v
```

To create the empty folders and placeholder Markdown files shown in the structure, create the files in GitHub's web interface or locally. Git does not track empty folders, so each folder needs at least one file.

---

## 16. License

Choose a license before inviting others to reuse or modify this documentation. For example, the MIT License is a common permissive option. Add the full license text in a file named `LICENSE` and select the appropriate copyright holder and year. Do not add a license unless you intend to grant those permissions.
