# Antigravity Profile Switcher (`agy-auth`)

A production-grade CLI tool to manage, switch, and secure multiple accounts and profiles for the Antigravity CLI (`agy`) on Linux, macOS, and Windows.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Platform: Linux | macOS | Windows](https://img.shields.io/badge/Platform-Linux%20%7C%20macOS%20%7C%20Windows-blue.svg)]()
[![Python: 3.x](https://img.shields.io/badge/Python-3.x-green.svg)]()

---

## ✨ Features

- 🔄 **Seamless Profile Switching**: Switch between multiple Antigravity sessions instantly.
- 💬 **Conversation Portability**: Export, import, and resume full conversation sessions across different machines without losing context or brain artifacts.
- 🎮 **Interactive Menu**: Run `agy-auth use` or `agy-auth conv export` without arguments to choose via an interactive selector.
- ⚡ **Streamlined Account Creation**: Run `agy-auth new <name>` to automatically backup current session, trigger fresh login, and save under a new profile in a single step.
- 🏷️ **Rename Profiles**: Easily rename profiles (`agy-auth rename old new`) without losing credentials.
- 🔒 **Security-First**: Enforces strict POSIX permissions (`0600` for token files, `0700` for directories).
- ⏳ **JWT Health Inspection**: Inspects ID token expiration dates (`[OK]` / `[EXP]`) directly from your terminal.
- 🛡️ **Safe Delete**: Prevents accidental deletion of the currently active profile.
- 📦 **Zero Dependencies**: Pure Python 3 standard library; no third-party packages required.

---

## 🚀 Installation & Setup

### Linux / macOS (One-Liner)
```bash
mkdir -p ~/.local/bin && curl -fsSL https://raw.githubusercontent.com/iampingz/agy-auth/main/agy-auth -o ~/.local/bin/agy-auth && chmod +x ~/.local/bin/agy-auth
```
> **Note:** Ensure `~/.local/bin` is in your shell `$PATH`. If not, add this to your `~/.bashrc` or `~/.zshrc`:
> ```bash
> echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc && source ~/.bashrc
> ```

### Windows (PowerShell)
1. Download `agy-auth` to a folder in your user directory (e.g. `C:\Users\<YourUser>\bin`):
```powershell
New-Item -ItemType Directory -Force -Path "$HOME\bin" | Out-Null
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/iampingz/agy-auth/main/agy-auth" -OutFile "$HOME\bin\agy-auth"
```
2. Create a batch wrapper or alias to run it directly:
```powershell
# Create agy-auth.cmd wrapper
Set-Content -Path "$HOME\bin\agy-auth.cmd" -Value "@python `"%~dp0\agy-auth`" %*"
```
3. Add `$HOME\bin` to your User PATH if not already present:
```powershell
[Environment]::SetEnvironmentVariable("PATH", $env:PATH + ";$HOME\bin", [EnvironmentVariableTarget]::User)
```
*(Or simply run `python path\to\agy-auth <command>` directly)*

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
| `agy-auth conv list` | Lists recent conversation histories with IDs, titles, and timestamps. |
| `agy-auth conv export [id]` | Exports a conversation (DB + Brain artifacts) into a portable `.agyconv` package. |
| `agy-auth conv import <file>` | Safely imports a conversation package into the target machine (merges without deleting existing chats). |
| `agy-auth conv resume [id]` | Resumes a conversation directly using `agy --conversation <id>`. |


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

### 8. Conversation Management (Cross-Machine Migration)

Antigravity saves chat sessions in SQLite databases (`~/.gemini/antigravity-cli/conversations/<id>.db`), summaries index (`conversation_summaries.db`), and brain artifacts/transcripts (`brain/<id>/`). 

`agy-auth conv` allows you to bundle, migrate, and resume conversations on any other machine (Linux, macOS, Windows) seamlessly:

#### A. List Recent Conversations
Inspect recent conversations along with titles, timestamps, and conversation IDs:
```bash
agy-auth conv list
# Or specify how many conversations to display
agy-auth conv list -n 30
```

#### B. Export Conversation Package (`.agyconv`)
Create an encrypted/compressed `.agyconv` package containing the conversation database, brain logs/artifacts, and summary metadata:
```bash
# Interactive mode (displays numbered picker to choose conversation):
agy-auth conv export

# Export the latest conversation session:
agy-auth conv export --last

# Export specific conversation ID to custom path:
agy-auth conv export <conversation-id> -o my-chat.agyconv
```

#### C. Import Conversation on Target Machine (Append & Merge)
Copy the `.agyconv` file to another computer (or Windows / Linux environment) and run:
```bash
agy-auth conv import my-chat.agyconv

# Force overwrite if conversation ID already exists on target machine
agy-auth conv import my-chat.agyconv -f
```
> **Safety Guarantee:** Importing **appends** into `conversation_summaries.db` without deleting or modifying your existing conversation history on the target machine. If the conversation ID already exists, it prompts for confirmation before overwriting.

#### D. Resume Conversation
Resume the conversation right where you left off:
```bash
# Using agy-auth shortcut:
agy-auth conv resume <conversation-id>

# Or resume the latest imported/active session:
agy-auth conv resume --last

# Or native agy CLI command:
agy --conversation <conversation-id>
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
