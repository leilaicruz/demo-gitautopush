# This repo is to demo the gitautopush tool to Carpentry instructors 

## Commands from Unix terminal to be transferred to a file

### Prerequisites
- Make a public github repository to send your notes to : 
    - make one live in github (or have done one) and invite the participants as collaborators
    - make the repo with a readme file 
- Make a file text in the location of your local github repository to store the commands   
    - Participants
        - clone the repo and create a new branch : git checkout -b NAME_OF_YOUR_BRANCH
    - the participants can clone or pull my changes, so they have the file text in their local repository
- Install gitautopush in your computer : `pip install gitautopush`

### Instructions

#### Firs step to check the history is stored in real time
1. Open one terminal 
2. Open a second terminal where the history is redirected and put: `tail -n 0 -f ~/.bash_history`
3. type commands in the first terminal and check if they appear in the second terminal in real time

#### Second step to store the history in a file text in your local github repository and push it to the remote repository in real time

1. Open one terminal (the main where you are going to type the commands of the lesson)
2. Open a second terminal where the history is redirected and put:
`tail -n 0 -f ~/.bash_history | tee -a "path to the file text you created in the local github repository"` 

3. Establish the proper ssh keys to connect to your remote github repository

4. Open a third terminal in the path of the repository to send the commands and
    - Run gitautopush : `gitautopush .`



## Commands from a jupyter notebook to be set to a github repository 

### Prerequisites
- Make a public github repository to send your notes to
- Install gitautopush in your computer : `pip install gitautopush`


### Instructions
- Open a terminal in the path of your local github repository where is where you want to open your local jupyter notebook 
- Establish the proper ssh keys to connect to your remote github repository (and make sure you have the most updated remote version of your repository in your local repository)
- Run gitautopush : `gitautopush .`

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
