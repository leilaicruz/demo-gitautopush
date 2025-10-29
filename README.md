# This repo is to demo the gitautopush tool to Carpentry instructors 


## 🧰 Bash History Troubleshooting Guide

### 🔹 1. Make Bash Write History in Real Time

By default, Bash only writes history when the shell exits.
Fix this by adding the following to your **`~/.bashrc`**:

```bash
# Append rather than overwrite
shopt -s histappend

# After each command, write new history and read any new entries
export PROMPT_COMMAND='history -a; history -n'
```

✅ **Effect:** Commands appear in `~/.bash_history` immediately, visible with `tail -f`.

---

### 🔹 2. Verify History Configuration

Check these in your shell:

```bash
set -o | grep history      # should show: history on
echo "$HISTFILE"           # usually /home/you/.bash_history
test -w "$HISTFILE" && echo writable || echo NOT_WRITABLE
```

If `HISTFILE` is unset:

```bash
export HISTFILE="$HOME/.bash_history"
```

---

### 🔹 3. Enable History Append Mode

Avoid losing commands from other sessions:

```bash
shopt -s histappend
```

Without it, each shell overwrites the file on exit.

---

### 🔹 4. Check for Filters that Skip Commands

Bash may skip commands matching ignore rules:

```bash
echo "$HISTCONTROL"  # e.g., ignoredups:ignorespace
echo "$HISTIGNORE"   # e.g., ls:pwd:exit
```

* Commands starting with a **space** are ignored (`ignorespace`).
* Repeated identical commands may be skipped (`ignoredups`).

Disable temporarily for testing:

```bash
unset HISTCONTROL HISTIGNORE
```

---

### 🔹 5. Ensure the Right Shell Is Running

```bash
echo "$0"
```

Should print something like `bash`.
If you see `zsh`, `sh`, or something else — the settings above won’t apply.

---

### 🔹 6. Check Login vs Interactive Shell Config

* **Interactive shells:** read `~/.bashrc`
* **Login shells:** read `~/.bash_profile` or `~/.profile`

Ensure `~/.bash_profile` sources `~/.bashrc`:

```bash
# in ~/.bash_profile
if [ -f ~/.bashrc ]; then . ~/.bashrc; fi
```

---

### 🔹 7. Verify Multiple Sessions

If you have multiple terminals open:

* Each should call `history -a` to append new commands.
* `history -n` reloads new ones added by others.

That’s why `PROMPT_COMMAND='history -a; history -n'` is so important.

---

### 🔹 8. If Using `sudo` or Another User

`sudo -s` or `sudo su -` uses that user’s history file (e.g., `/root/.bash_history`).
Check which file you’re actually writing to:

```bash
whoami
echo "$HISTFILE"
```

---

### 🔹 9. Using `tail` Properly

Run this in a separate terminal:

```bash
tail -n 0 -F ~/.bash_history
```

* `-F` follows the file even if it gets re-created.
* You won’t get your `$` prompt back — use **Ctrl+C** to stop.
* Or run it in background:

  ```bash
  tail -n 0 -F ~/.bash_history &
  ```

---

### 🔹 10. Optional — Log Every Command Separately

If you want a permanent command log with timestamps:

```bash
export COMMAND_LOG="$HOME/.command_log"
shopt -s histappend
export PROMPT_COMMAND='history -a; history -n'
trap 'printf "%(%F %T)T | %s\n" -1 "$BASH_COMMAND" >> "$COMMAND_LOG"' DEBUG
```

Now watch it live with:

```bash
tail -f ~/.command_log
```

---

### 🔹 11. Check Environment Quirks

* **NFS or networked home**: history writes may be delayed; use `tail -F`.
* **SELinux/AppArmor**: check file context (`ls -lZ ~/.bash_history`).
* **Permissions**: ensure you own the file (`chown $USER ~/.bash_history`).

---

### ✅ Minimal Working Configuration (put in `~/.bashrc`)

```bash
# Realtime history logging
shopt -s histappend
export PROMPT_COMMAND='history -a; history -n'

# History behavior tuning
export HISTCONTROL=ignoredups:ignorespace
export HISTSIZE=100000
export HISTFILESIZE=200000
export HISTFILE="$HOME/.bash_history"
```

---

Would you like me to format this as a **markdown cheatsheet** (with collapsible sections or syntax highlighting) that you could keep in a README or share with colleagues?
