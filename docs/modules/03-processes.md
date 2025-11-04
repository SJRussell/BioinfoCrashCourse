# Module 3 — Processes & Hung-Terminal Rescue

**Time:** 45–60 min  
**Goal:** Spot stuck jobs, stop them safely, and keep working.

!!! tip "Type it, don't paste it"
    For this module too: type each command once before you copy/paste.

!!! note "Regex refresher required"
    Make sure you've completed [Module 2b — Regex Primer for Manifests & Motifs](02b-regex-basics.md); the pattern-matching skills carry straight into spotting process names and log noise.

## 1) Foreground vs background

Try these to feel the difference:

```bash
# Foreground job (Ctrl-C to stop)
sleep 10

# Background job (&)
sleep 300 &
jobs      # list shell jobs

# Bring a job to foreground (replace %1 with your job number)
fg %1

# Suspend a foreground job then background it
yes > /dev/null
# Press Ctrl-Z to suspend
bg %1
jobs
```

## 2) See what's running

Install a couple of helpers:

=== "Linux/WSL"

    ```bash
    sudo apt update && sudo apt install -y htop psmisc
    ```

=== "macOS"

    ```bash
    brew install htop psmisc
    ```

Now inspect:

```bash
ps aux | head -n 5
ps aux | grep yes | grep -v grep
top           # q to quit
htop          # arrows to navigate, F10 to quit
pstree -p     # processes as a tree with PIDs
```

## 3) Stop stuff (nicely, then firmly)

```bash
# Ask a process to terminate (SIGTERM)
kill <PID>

# If it ignores you, last resort (SIGKILL)
kill -9 <PID>

# Kill by name (careful; can match multiple)
pkill yes
```

!!! warning "Use kill -9 sparingly"
    `-9` skips cleanup. Try plain `kill` first to allow programs to close files gracefully.

## 4) Practice: make a "runaway" and fix it

```bash
# Start a noisy CPU-waster in the background
yes > /dev/null &
# Find its PID, then terminate it:
ps aux | grep yes | grep -v grep
kill <PID>        # try without -9 first
```

Bonus: start two background jobs and use `jobs`, `fg`, and `bg` to juggle them.

## 5) Detach from the shell (advanced preview)

```bash
# Disown a background job so it survives if the shell exits
sleep 600 &
disown %1
```

## Exit Ticket (email)

**Subject:** DE M3 Exit Ticket – <Your Name>  
**Paste:**

- The commands you used to locate/kill your runaway, including the PID.
- One sentence about when you'd use `kill` vs `kill -9`.