# 🛠️ Troubleshooting Guide: Google Workspace CLI Installation

> **Applies to:** Windows | **Tool:** Google Workspace CLI (`gws`)

This guide covers common errors encountered when installing the Google Workspace CLI via NPM on Windows, particularly permission errors (`EPERM`) and PowerShell not found errors (`ENOENT`).

---

## 📋 Table of Contents

1. [Error Symptoms](#1-error-symptoms)
2. [Fix 1: Update the PATH for PowerShell](#2-fix-1-update-the-path-for-powershell)
3. [Fix 2: Manual Installation](#3-fix-2-manual-installation)
4. [Important Notes](#4-important-notes)

---

## 1. Error Symptoms

When running the installation command:

```bash
npm install -g @googleworkspace/cli
```

You may encounter one of the following errors:

| Error Code | Message | Root Cause |
|------------|---------|------------|
| `ENOENT` | `npm error Error fetching release: spawnSync powershell.exe ENOENT` | System cannot find `powershell.exe` in PATH |
| `EPERM` | `npm warn cleanup [Error: EPERM: operation not permitted, rmdir ...]` | Missing Administrator privileges when writing files |

---

## 2. Fix 1: Update the PATH for PowerShell

> ✅ **This is the recommended fix.** Use this when the installer script cannot locate `powershell.exe` on your system.

### Steps

**Step 1 — Open Environment Variables settings:**

1. Press the `Windows` key, type **`env`**
2. Select **"Edit the system environment variables"**
3. Click the **Environment Variables...** button in the bottom-right corner

**Step 2 — Edit the `Path` variable:**

1. In the **System variables** section (lower panel), find the **`Path`** entry → click **Edit**
2. Click **New** and paste the following path:
   ```
   C:\Windows\System32\WindowsPowerShell\v1.0\
   ```
3. Click **Move Up** to give this entry a higher priority

**Step 3 — Save and apply:**

- Click **OK** to dismiss all dialogs

**Step 4 — Restart Terminal and reinstall:**

1. Close all currently open CMD / PowerShell windows
2. Open a new **Command Prompt** or **PowerShell** window as **Run as Administrator**
3. Run the install command again:
   ```bash
   npm install -g @googleworkspace/cli
   ```

---

## 3. Fix 2: Manual Installation

> 💡 **Use this when:** Your NPM environment is restricted by corporate security policies, a firewall, or an unstable network connection.

### Steps

**Step 1 — Download the executable:**

1. Visit the official releases page:
   👉 [Google Workspace CLI Releases](https://github.com/googleworkspace/cli/releases)
2. Download the `.zip` file for Windows:
   ```
   google-workspace-cli-x86_64-pc-windows-msvc.zip
   ```

**Step 2 — Extract the archive:**

- Unzip the downloaded `.zip` file — you will get the executable **`gs.exe`**

**Step 3 — Make it globally accessible:**

Choose **one** of the following options:

- **Option A *(Quickest)*:** Copy `gs.exe` directly into:
  ```
  C:\Windows\System32\
  ```

- **Option B *(Recommended)*:**
  1. Create a dedicated folder, e.g., `C:\bin`
  2. Copy `gs.exe` into that folder
  3. Add `C:\bin` to the **`Path`** environment variable (see [Fix 1, Step 2](#step-2--edit-the-path-variable))

**Step 4 — Verify the installation:**

Open a new terminal and run:

```bash
gs --version
```

If a version number is displayed → installation successful! 🎉

---

## 4. ⚠️ Important Notes

| # | Issue | Resolution |
|---|-------|------------|
| 1 | **Admin privileges** | Always run CMD / PowerShell as **Run as Administrator** during installation |
| 2 | **Antivirus software** | Some antivirus programs may block `spawnSync` calls. If the error persists, **temporarily disable your antivirus** during installation, then re-enable it |
| 3 | **Terminal restart** | After modifying the PATH environment variable, you **must open a new terminal window** for the changes to take effect |

---

* This document used for GDG CLoud Da Nang - Build with AI 2026