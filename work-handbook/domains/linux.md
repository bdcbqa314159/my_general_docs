# Domain: Linux

Operational knowledge for working on Linux. Reference doc — grows with use.

---

## Filesystem essentials

- Everything is a file (devices, sockets, processes via `/proc`).
- Paths are case-sensitive. `Foo.txt` and `foo.txt` are different files.
- Permissions: `rwx` for user/group/other. `chmod 644 file`, `chmod +x script`.
- Symlinks: `ln -s target link`. `ls -l` shows them.
- Hidden files start with `.`. `ls -a` to see them.
- Spaces and special characters in paths: quote them or escape with `\`.

---

## Shell — bash basics

```bash
# Variables
NAME="value"          # no spaces around =
echo "$NAME"          # quote to preserve spaces

# Substitution
echo "Today is $(date)"
echo "Files: $(ls | wc -l)"

# Conditionals
if [[ -f "$file" ]]; then ...; fi   # file exists
if [[ -d "$dir" ]]; then ...; fi    # directory
if [[ -z "$var" ]]; then ...; fi    # empty
if [[ "$a" == "$b" ]]; then ...; fi # equal (always quote!)

# Loops
for f in *.csv; do echo "$f"; done
while read line; do echo "$line"; done < file.txt
```

- Always `set -euo pipefail` at the top of non-trivial scripts:
  exit on error, undefined variables, pipe failures.
- `shellcheck` your scripts. Catches bugs.

---

## Text processing essentials

```bash
grep "pattern" file               # find lines matching
grep -r "pattern" dir/            # recursive
grep -v "pattern" file            # invert match

sed 's/old/new/g' file            # substitute
sed -i 's/old/new/g' file         # in place (BSD sed differs)

awk '{print $2}' file             # second whitespace-delimited column
awk -F',' '{print $1}' file       # CSV first column
awk 'NR>1 {sum += $3} END {print sum}'  # sum column 3, skip header

cut -d',' -f1,3 file              # columns 1 and 3 (no awk overhead)
sort file | uniq -c | sort -rn    # frequency count
```

---

## Process management

```bash
ps aux | grep python              # what's running
top                               # live view
htop                              # nicer top (often not preinstalled)
kill PID                          # SIGTERM
kill -9 PID                       # SIGKILL (last resort)
nohup ./long-job.sh &             # run in background, survive logout
```

- `&` puts a process in the background.
- `jobs` lists background jobs of current shell.
- `disown` removes a job from the shell's tracking — survives shell exit.

---

## File and disk

```bash
df -h                             # disk free per mount
du -sh dir/                       # size of directory
du -sh dir/* | sort -h            # which subdirs are big
ls -lh                            # sizes in human-readable
find . -name "*.py" -mtime -7     # python files modified in last 7 days
```

---

## Networking quick

```bash
ss -tlnp                          # listening TCP ports (modern netstat)
curl -v https://example.com       # verbose HTTP
curl -sS -o file URL              # download silently, show errors
ping host                         # ICMP
dig domain                        # DNS query
ssh user@host                     # remote shell
scp file user@host:/path          # remote copy
rsync -avz src/ dest/             # smart sync
```

---

## SSH workflow

- `~/.ssh/config` for host shortcuts:
  ```
  Host myserver
      HostName 1.2.3.4
      User myuser
      IdentityFile ~/.ssh/id_ed25519
  ```
- `ssh-copy-id host` to install key on remote.
- `ssh -L 8080:localhost:80 host` for port forwarding (local → remote).
- `ssh -R 8080:localhost:80 host` for reverse tunnel.

---

## Environment

- `~/.bashrc` for bash config. `~/.profile` for login shell.
- `export VAR=value` to make a variable available to subprocesses.
- `env` shows current environment.
- `which command` shows what gets executed. `type command` says
  whether it's a builtin, alias, or file.

---

## Useful one-liners

```bash
# Find and replace in many files
grep -rl "old" . | xargs sed -i 's/old/new/g'

# Tail a log with grep
tail -f log.txt | grep --line-buffered "ERROR"

# Most-used commands
history | awk '{print $2}' | sort | uniq -c | sort -rn | head

# Watch a command refresh
watch -n 1 'ls -lh /tmp'
```

---

## Debugging system issues

- `journalctl -u service-name -f` for systemd service logs.
- `dmesg | tail` for kernel messages (often hardware/driver issues).
- `strace -p PID` to see syscalls a process is making.
- `/proc/<pid>/` for live process inspection (status, fd, maps, etc.).

---

<!-- expand from practice -->

Add Linux patterns, traps, and useful commands as you encounter them.
