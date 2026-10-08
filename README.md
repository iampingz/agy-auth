# Antigravity Profile Switcher (`agy-auth`)

A production-grade CLI tool to manage, switch, and secure multiple accounts and profiles for the Antigravity CLI (`agy`) on Linux and macOS.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Platform: Linux / macOS](https://img.shields.io/badge/Platform-Linux%20%7C%20macOS-blue.svg)]()
[![Python: 3.x](https://img.shields.io/badge/Python-3.x-green.svg)]()

---

## ✨ Features

- 🔄 **Seamless Profile Switching**: Switch between multiple Antigravity sessions instantly.
- 🎮 **Interactive Menu**: Run `agy-auth use` without arguments to choose profiles via a numeric selector.
- ⚡ **Streamlined Account Creation**: Run `agy-auth new <name>` to automatically backup current session, trigger fresh login, and save under a new profile in a single step.
- 🏷️ **Rename Profiles**: Easily rename profiles (`agy-auth rename old new`) without losing credentials.
- 🔒 **Security-First**: Enforces strict POSIX permissions (`0600` for token files, `0700` for directories).
- ⏳ **JWT Health Inspection**: Inspects ID token expiration dates (`[OK]` / `[EXP]`) directly from your terminal.
- 🛡️ **Safe Delete**: Prevents accidental deletion of the currently active profile.
- 📦 **Zero Dependencies**: Pure Python 3 standard library; no third-party packages required.

---

## 🚀 Quick Install (One-Liner)

Install or update `agy-auth` on any Linux/macOS machine with a single command:

```bash
mkdir -p ~/.local/bin && curl -fsSL https://raw.githubusercontent.com/iampingz/agy-auth/main/agy-auth -o ~/.local/bin/agy-auth && chmod +x ~/.local/bin/agy-auth
```

> **Note:** Ensure `~/.local/bin` is in your shell `$PATH`. If not, add this to your `~/.bashrc` or `~/.zshrc`:
> ```bash
> echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc && source ~/.bashrc
> ```

---

## 📖 Command Reference

| Command | Description |
|---|---|
| `agy-auth use` | **Interactive mode**: Displays all profiles with numbers to quickly switch. |
| `agy-auth use <name>` | Switches directly to the specified profile. |
| `agy-auth new <name>` | Backs up current session, prompts fresh Google authentication, and saves as `<name>`. |
| `agy-auth list` | Lists all saved profiles, active status, and token health (`[OK]` / `[EXP]`). |
| `agy-auth rename <old> <new>` | Renames an existing profile. |
| `agy-auth save <name>` | Saves current session under `<name>`. |
| `agy-auth current` | Displays the currently active email address and token expiration details. |
| `agy-auth export [name]` | Exports all profiles (or a specific profile) to a secure JSON file. |
| `agy-auth import <file>` | Imports profiles from an exported JSON or token file. |
| `agy-auth remove <name>` | Deletes a saved profile (prompts confirmation if currently active). |
| `agy-auth logout` | Clears the active session token. |

---

## 💡 Quick Start Guide

### 1. Save Your Current Session
If you are already logged in to an account:
```bash
agy-auth save personal
```

### 2. Add Additional Accounts
Add a second or third profile with `new` (auto-backups current token and launches login):
```bash
agy-auth new work
agy-auth new client-a
```

### 3. Switch Between Profiles
Switch anytime using direct name or interactive menu:
```bash
# Direct switch
agy-auth use work

# Interactive selector
agy-auth use
```

**Interactive selector example:**
```text
Select a profile to switch to:
  [1] client-a        (dev@client.com)
  [2] personal        (me@gmail.com)
* [3] work            (me@company.com) (ACTIVE)

Enter number or profile name (or 'q' to cancel): 2
✓ Switched successfully to profile 'personal'!
Active Account: me@gmail.com
```

### 4. Check Profiles & Token Health
```bash
agy-auth list
```
**Output example:**
```text
======================================================================
 Antigravity Profiles Directory (~/.gemini/antigravity-cli/profiles)
======================================================================
  [1] client-a        [OK] dev@client.com
* [2] personal        [OK] me@gmail.com         (ACTIVE)
  [3] work            [OK] me@company.com
======================================================================
Current active session: me@gmail.com
```

### 5. Rename a Profile
```bash
agy-auth rename client-a project-alpha
```

### 6. Export Profiles (Backup / Migration)
Export all profiles or a single profile into a portable JSON backup:
```bash
# Export all saved profiles
agy-auth export

# Export all profiles to a custom file
agy-auth export -o my-profiles.json

# Export a specific profile only
agy-auth export work -o work-profile.json
```

### 7. Import Profiles
Import profiles onto another machine or restore from backup:
```bash
# Import all profiles from backup file
agy-auth import my-profiles.json

# Import only a specific profile from a package
agy-auth import my-profiles.json --name work

# Import and rename the profile
agy-auth import work-profile.json --as-name office-work

# Import without overwrite confirmation prompts
agy-auth import my-profiles.json -f
```

---

## ❓ Token Expiration & Refreshing

Google OAuth tokens come in two parts:
1. **ID / Access Token**: Short-lived (valid for ~1 hour). When expired, `agy-auth list` displays `[EXP]`.
2. **Refresh Token**: Long-lived credential stored securely in your token file.

> **Note:** If a profile shows `[EXP]`, it is **not broken**. When you switch to it (`agy-auth use`) and execute your next `agy` command, the CLI will automatically refresh your credentials in the background.

---

## 🔒 Security & Privacy

- All profile tokens are stored locally under `~/.gemini/antigravity-cli/profiles/`.
- File permissions are enforced at `0600` (read/write by owner only).
- Profiles directory is locked at `0700`.
- No credentials are ever sent to external services other than official Google OAuth endpoints.

---

## 📄 License

MIT License. Feel free to use, modify, and distribute.
