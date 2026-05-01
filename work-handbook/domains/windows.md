# Domain: Windows

Operational knowledge for working on Windows. Reference doc — grows with use.

---

## PowerShell over cmd

PowerShell is the modern shell. `cmd` is legacy — use only for old scripts
and a handful of system tools.

PowerShell pipes objects, not text. This is fundamentally different from
bash and changes the idioms.

```powershell
Get-Process | Where-Object { $_.CPU -gt 100 }
Get-ChildItem *.csv | Select-Object Name, Length
Get-Content file.txt | Select-String "pattern"
```

- Verbs are `Verb-Noun` (`Get-`, `Set-`, `Remove-`, `New-`, `Test-`).
- `$_` is the current pipeline object.
- `.` operator on objects: `$proc.Name`, `$proc.Id`.

---

## PowerShell essentials

```powershell
# Variables
$name = "value"
$num = 42

# Conditionals
if (Test-Path $file) { ... }
if ($a -eq $b) { ... }            # -eq, -ne, -lt, -gt, -like, -match

# Loops
foreach ($f in Get-ChildItem) { ... }
1..10 | ForEach-Object { ... }

# Functions
function Get-Greeting {
    param([string]$Name)
    "Hello, $Name"
}

# Error handling
try { ... } catch { ... } finally { ... }
$ErrorActionPreference = 'Stop'   # turn warnings into errors
```

---

## Filesystem

- Paths use `\` natively but `/` works in most contexts.
- Drive letters: `C:`, `D:`. UNC paths: `\\server\share`.
- Case-insensitive filesystem (NTFS technically supports case-sensitive,
  rarely enabled).
- Permissions are ACL-based, more complex than POSIX. `Get-Acl`, `Set-Acl`.
- Hidden / system attributes: `Get-ChildItem -Force` shows them.

```powershell
Test-Path $path
New-Item -ItemType Directory -Path $path
Remove-Item $path -Recurse -Force
Copy-Item src dest
Move-Item src dest
```

---

## Process management

```powershell
Get-Process                              # all processes
Get-Process python                        # by name
Stop-Process -Id $pid -Force             # kill
Start-Process -FilePath app.exe          # launch
Get-Service | Where-Object Status -EQ 'Running'
```

---

## Useful PowerShell modules

- `PSReadLine` — better command-line editing (preinstalled on Win10+).
- `posh-git` — git status in prompt.
- `oh-my-posh` — prompt customisation.
- `PowerShellGet` for `Install-Module`.

---

## WSL — Windows Subsystem for Linux

If you have it available, WSL gives you a real Linux environment alongside
Windows. Most Unix-style work is easier inside WSL.

```powershell
wsl --list --verbose
wsl                              # enter default distro
wsl -d Ubuntu                    # specific distro
```

- WSL2 has full kernel, near-native performance.
- Filesystem: `\\wsl$\Ubuntu\home\user` from Windows;
  `/mnt/c/...` from Linux to access Windows files.
- Cross-filesystem I/O is slow. Keep working files on the side that's
  doing the work.

---

## Path issues to watch for

- Backslash vs forward slash. Most things accept both; some legacy tools
  insist on `\`.
- Spaces in paths (`Program Files`) — quote, or use short names (`PROGRA~1`).
- Long paths (>260 chars) are still a thing in some APIs. Enable long path
  support if it bites you (registry + manifest).
- Line endings: Windows is CRLF, Unix is LF. Git auto-converts unless
  configured otherwise (`core.autocrlf`).

---

## Git on Windows

- Use Git for Windows. Includes a bash shell (Git Bash) that's serviceable.
- Configure line endings explicitly:
  ```
  git config --global core.autocrlf input    # for cross-platform repos
  ```
- SSH keys live in `%USERPROFILE%\.ssh\`. Same format as Unix.

---

## Numerical / dev work specifics

- Visual Studio (full IDE) vs VS Code (editor). Both have their place.
- Windows-native C++ compilation: MSVC via Visual Studio Build Tools.
- Python on Windows: prefer `py` launcher (`py -3.11 ...`) over plain `python`.
- Conda works well on Windows. Miniforge / Mambaforge are clean choices.
- C# / .NET is first-class on Windows: `dotnet` CLI everywhere.

---

## Common annoyances and fixes

- **PowerShell execution policy** blocks scripts. Set per-user:
  `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`.
- **Antivirus scanning** of dev directories slows builds. Add exclusions
  for project trees.
- **OneDrive syncing** project folders silently. Move dev work outside
  OneDrive.
- **Locale and decimal separator** — Windows respects regional settings.
  `1,5` vs `1.5` issues in CSV reads. Pin format explicitly.

---

<!-- expand from practice -->

Add Windows patterns, traps, and useful commands as you encounter them.
