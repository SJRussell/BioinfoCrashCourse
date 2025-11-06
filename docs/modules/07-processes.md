# Module 7 — Process Management & Job Control

**Time:** 60–75 min
**Goal:** Learn to monitor running programs, manage background jobs, stop stuck processes, and rescue a frozen terminal.
**Exit ticket (email me):** A screenshot showing you successfully identified and killed a runaway process, plus answers to the reflection questions.

---

## What You'll Learn

By the end of this module, you will understand:

- What a process is and how programs run
- The difference between foreground and background jobs
- How to suspend and resume processes
- How to view running processes with `ps`, `top`, and `htop`
- How to stop processes with `kill` and `pkill`
- Different signals (SIGTERM vs SIGKILL)
- How to rescue a hung terminal
- Job control for long-running bioinformatics analyses

---

## What is a Process?

A **process** is a running instance of a program. When you execute a command, the operating system creates a process for it.

**Examples:**
- When you run `ls`, a process is created, lists files, then exits
- When you run a long alignment with `bwa mem`, that's a process
- Your terminal (bash shell) itself is a process
- VS Code is a process

### Process Properties

Every process has:

1. **Process ID (PID)**: A unique number identifying the process
2. **Parent Process ID (PPID)**: The PID of the process that started it
3. **Owner**: The user who started it
4. **State**: Running, sleeping, stopped, zombie, etc.
5. **Resource usage**: CPU, memory, runtime

### The Process Tree

Processes form a tree structure—each process (except init/systemd) has a parent:

```
init (PID 1)
├─ sshd
│  └─ bash (your login shell)
│     ├─ python analysis.py
│     └─ grep 'pattern' file.txt
├─ cron
└─ other system processes
```

When you run a command in your shell, your shell (bash) becomes the parent of that command's process.

---

## Foreground vs Background Processes

### Foreground Processes

A **foreground** process is one that:
- Has control of your terminal
- You can't type other commands while it's running
- Receives keyboard input (like Ctrl+C)

**Example:**
```bash
sleep 10
```

This runs in the foreground. Your terminal is "blocked" for 10 seconds—you can't type anything else.

### Background Processes

A **background** process is one that:
- Runs without blocking your terminal
- Lets you continue typing other commands
- Doesn't receive keyboard input directly

**Example:**
```bash
sleep 10 &
```

The `&` at the end runs the command in the background. You immediately get your prompt back and can run other commands.

### Practice: Foreground vs Background

```bash
cd ~/bioinfo-course
mkdir -p module07
cd module07

# Foreground (blocks terminal)
sleep 5
# Wait 5 seconds... now your prompt is back

# Background (returns immediately)
sleep 5 &
# Prompt returns immediately!
# You can type other commands while it runs
```

When you run a background job, you'll see output like:
```
[1] 12345
```

- `[1]` is the **job number**
- `12345` is the **process ID (PID)**

---

## Job Control: Suspend, Resume, and Background

### Suspending a Foreground Process: Ctrl+Z

**Ctrl+Z** suspends (pauses) a foreground process.

**Practice:**
```bash
# Start a long-running command
sleep 100
```

While it's running, press **Ctrl+Z**.

You'll see:
```
^Z
[1]+  Stopped                 sleep 100
```

The process is now **suspended** (paused, not running, not terminated).

### Listing Jobs: `jobs`

The `jobs` command shows processes started from your current shell:

```bash
jobs
```

Output:
```
[1]+  Stopped                 sleep 100
```

- `[1]` is the job number
- `+` indicates this is the most recent job
- `Stopped` means it's suspended
- `sleep 100` is the command

### Resuming in Foreground: `fg`

**`fg`** brings a background or suspended job to the foreground:

```bash
fg %1
```

Or just:
```bash
fg
```

(Without a number, `fg` resumes the most recent job.)

The process continues running in the foreground.

### Resuming in Background: `bg`

**`bg`** resumes a suspended job in the background:

```bash
# First, suspend a job with Ctrl+Z
sleep 100
# Press Ctrl+Z

# Now resume it in the background
bg %1
```

Or just:
```bash
bg
```

The process continues running in the background.

### Complete Workflow Example

```bash
# Start a program
python long_analysis.py

# Oops, it's taking forever and blocking my terminal!
# Press Ctrl+Z to suspend it
^Z

# Check jobs
jobs
# Output: [1]+  Stopped   python long_analysis.py

# Resume in background so I can keep working
bg %1

# Verify it's running in background
jobs
# Output: [1]+  Running   python long_analysis.py &

# Continue with other work...
```

---

## Viewing Running Processes

### The `ps` Command

**`ps`** shows process status.

**Basic usage:**
```bash
ps
```

Output:
```
    PID TTY          TIME CMD
  12345 pts/0    00:00:00 bash
  12456 pts/0    00:00:00 ps
```

This shows only processes in your current terminal.

**Show all your processes:**
```bash
ps -u $USER
```

**Show all processes on the system:**
```bash
ps aux
```

Output:
```
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0 168548 13472 ?        Ss   Jan10   0:05 /sbin/init
alice      12345  0.0  0.1  21532  5012 pts/0    Ss   14:32   0:00 -bash
alice      12456  2.5 10.2 4512000 836472 pts/0  R+   15:01   1:23 python analysis.py
```

**Understanding the output:**

| Column | Meaning |
|--------|---------|
| `USER` | Who owns the process |
| `PID` | Process ID |
| `%CPU` | CPU usage percentage |
| `%MEM` | Memory usage percentage |
| `VSZ` | Virtual memory size (KB) |
| `RSS` | Physical memory (RAM) used (KB) |
| `TTY` | Terminal associated with process |
| `STAT` | Process state (R=running, S=sleeping, Z=zombie, T=stopped) |
| `START` | When the process started |
| `TIME` | Total CPU time used |
| `COMMAND` | The command |

**Useful `ps` variations:**
```bash
# Show process tree (parent-child relationships)
ps auxf

# Show all processes in a tree format (if pstree is installed)
pstree

# Show full command with arguments
ps aux | grep python
```

### The `top` Command

**`top`** shows a live, updating view of running processes, sorted by resource usage.

```bash
top
```

You'll see a dynamic display that updates every few seconds:

```
top - 15:30:42 up 5 days, 3:45, 2 users, load average: 1.05, 0.85, 0.65
Tasks: 245 total, 2 running, 243 sleeping, 0 stopped, 0 zombie
%Cpu(s): 15.2 us, 2.1 sy, 0.0 ni, 82.3 id, 0.3 wa, 0.0 hi, 0.1 si, 0.0 st
MiB Mem : 16384.0 total, 2048.5 free, 10240.2 used, 4095.3 buff/cache
MiB Swap: 8192.0 total, 8192.0 free, 0.0 used. 5120.8 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  12456 alice     20   0  4.4g    816m   12m R  98.3  10.2   5:23.45 python
   1234 alice     20   0  2.1g    412m   45m S   5.2   5.1   0:45.12 bwa
```

**Navigation in top:**

| Key | Action |
|-----|--------|
| **q** | Quit |
| **k** | Kill a process (prompts for PID) |
| **M** | Sort by memory usage |
| **P** | Sort by CPU usage (default) |
| **u** | Show only processes from a specific user |
| **h** | Help |
| **Space** | Refresh immediately |

**Understanding the header:**

- **load average**: System load over last 1, 5, and 15 minutes (< 1.0 per CPU core is good)
- **Tasks**: How many processes in each state
- **%Cpu(s)**: CPU usage breakdown (us=user, sy=system, id=idle, wa=waiting for I/O)
- **Mem**: Memory usage

**Tip:** Press **M** to sort by memory usage—useful for finding memory hogs!

### The `htop` Command (Better than top!)

**`htop`** is an improved, user-friendly version of `top` with colors, mouse support, and easier process management.

**Installation:**

=== "Linux/WSL"

    ```bash
    sudo apt update
    sudo apt install htop
    ```

=== "macOS"

    ```bash
    brew install htop
    ```

**Run it:**
```bash
htop
```

**Navigation in htop:**

| Key | Action |
|-----|--------|
| **F10** or **q** | Quit |
| **F9** | Kill a process (select signal) |
| **F6** | Sort by column |
| **F4** | Filter by name |
| **F5** | Tree view (show parent-child relationships) |
| **Space** | Tag a process |
| **U** | Show only your processes |
| **/** | Search |
| **Arrows** | Navigate |

**Advantages over top:**
- Color-coded display
- Mouse support
- Easier to kill processes
- Tree view shows parent-child relationships
- Can scroll horizontally to see full command lines

---

## Stopping Processes: kill and pkill

### The `kill` Command

**`kill`** sends a signal to a process, typically to stop it.

**Basic usage:**
```bash
kill PID
```

**Example:**
```bash
# Start a background process
sleep 100 &
# Output: [1] 12456

# Kill it
kill 12456

# Verify it's gone
jobs
```

### Understanding Signals

When you "kill" a process, you're actually sending it a **signal**. Different signals do different things.

**Common signals:**

| Signal | Number | Name | What It Does |
|--------|--------|------|--------------|
| SIGTERM | 15 | Terminate | Polite "please exit" (default) |
| SIGKILL | 9 | Kill | Force quit immediately (can't be ignored) |
| SIGINT | 2 | Interrupt | Same as Ctrl+C |
| SIGSTOP | 19 | Stop | Pause (same as Ctrl+Z) |
| SIGCONT | 18 | Continue | Resume after STOP |
| SIGHUP | 1 | Hangup | Terminal closed (often used to reload config) |

### SIGTERM vs SIGKILL

**SIGTERM (kill or kill -15):**
- Polite request to terminate
- Process can clean up (close files, save state, etc.)
- Process can ignore it (if badly written)
- **Use this first!**

**SIGKILL (kill -9):**
- Immediate termination
- No cleanup possible
- Process cannot ignore it
- Files may be left corrupted
- **Last resort only!**

**Best practice workflow:**
```bash
# First, try polite termination
kill 12456

# Wait a few seconds
sleep 3

# If still running, check:
ps aux | grep 12456

# Still there? Now use force:
kill -9 12456
```

### Examples

**Send specific signals:**
```bash
kill -SIGTERM 12456    # Polite termination (default)
kill -15 12456         # Same thing (signal number)

kill -SIGKILL 12456    # Force kill
kill -9 12456          # Same thing (signal number)

kill -SIGSTOP 12456    # Pause the process
kill -SIGCONT 12456    # Resume it
```

### The `pkill` Command

**`pkill`** kills processes by name instead of PID.

**Example:**
```bash
# Kill all processes named "python"
pkill python
```

**Warning:** This kills ALL matching processes! Be careful!

**Safer: see what would be killed first:**
```bash
# Show what matches (doesn't kill)
pgrep -a python

# If it looks right, then kill
pkill python
```

**Common pkill options:**

| Option | What It Does | Example |
|--------|-------------|---------|
| `-u USER` | Kill only processes owned by USER | `pkill -u alice python` |
| `-f` | Match against full command line | `pkill -f "analysis.py"` |
| `-9` | Send SIGKILL (force) | `pkill -9 python` |
| `-TERM` | Send SIGTERM (default) | `pkill -TERM python` |

**Example: Kill a specific script:**
```bash
# Running: python analysis.py
# But also: python other_script.py

# Kill only the specific one
pkill -f "analysis.py"
```

---

## Practical Scenarios

### Scenario 1: Terminal Frozen by Runaway Process

**Problem:** You ran a command that's eating 100% CPU and your terminal is stuck.

**Solution:**

1. **Open a new terminal window/tab** (don't close the frozen one yet!)

2. **Find the offending process:**
   ```bash
   top
   # Look for high %CPU
   # Or:
   ps aux | grep your_username | sort -k3 -nr | head -n 5
   ```

3. **Note the PID of the CPU hog**

4. **Kill it (try polite first):**
   ```bash
   kill 12456
   ```

5. **If it doesn't die, force it:**
   ```bash
   kill -9 12456
   ```

6. **Go back to your original terminal—it should be responsive now**

### Scenario 2: You Accidentally Started a Long Job in Foreground

**Problem:** You ran `bwa mem huge_file.fastq` and it's blocking your terminal.

**Solution:**

1. **Press Ctrl+Z** to suspend it

2. **Resume it in the background:**
   ```bash
   bg
   ```

3. **Verify it's running:**
   ```bash
   jobs
   ```

4. **Redirect output to avoid terminal spam (optional but recommended next time!):**
   ```bash
   # Next time, start it properly:
   bwa mem huge_file.fastq > output.sam 2> errors.log &
   ```

### Scenario 3: Multiple Instances Running, Want to Kill One

**Problem:** You have multiple `python` processes. You want to kill only one specific script.

**Solution:**

1. **See all Python processes with full commands:**
   ```bash
   ps aux | grep python
   ```

   Output:
   ```
   alice  12456  98.3  5.0 ... python analysis.py
   alice  12789   0.1  2.0 ... python helper.py
   alice  13000   5.2  3.0 ... python test.py
   ```

2. **Kill the specific one by PID:**
   ```bash
   kill 12456
   ```

   Or use `pkill` with pattern matching:
   ```bash
   pkill -f "analysis.py"
   ```

### Scenario 4: Check If Your Job Is Still Running

**Problem:** You started a long analysis in the background yesterday. Is it still running?

**Solution:**

```bash
# Check all your processes
ps -u $USER

# Or search for a specific command
ps aux | grep "your_command_name"

# Or use pgrep
pgrep -a "command_name"
```

---

## Practice Exercise: Creating and Managing Processes

Let's practice with harmless processes!

### Setup

```bash
cd ~/bioinfo-course/module07
mkdir practice
cd practice
```

### Exercise 1: Background Jobs

```bash
# Start three background jobs
sleep 30 &
sleep 60 &
sleep 90 &

# List them
jobs

# Bring job 2 to foreground
fg %2

# Suspend it with Ctrl+Z
^Z

# Resume it in background
bg %2

# List jobs again
jobs
```

### Exercise 2: Creating a CPU Hog

Let's create a process that uses CPU so we can practice finding and killing it.

**Warning:** This will intentionally eat CPU! Don't worry, we'll kill it.

```bash
# This creates an infinite loop that does nothing
yes > /dev/null &
# Note the PID, e.g., [1] 12456
```

**Now find and kill it:**

```bash
# Method 1: Use jobs and kill
jobs
# See: [1]+  Running   yes > /dev/null &
# Note the PID (12456 in this example)

kill 12456

# Method 2: Use top
top
# Press P to sort by CPU
# You'll see "yes" at 100% CPU
# Press q to quit
# Kill it:
pkill yes

# Method 3: Use htop (if installed)
htop
# Find "yes" process
# Press F9, select SIGTERM, press Enter
```

### Exercise 3: Process Tree

```bash
# Install pstree if needed (shows parent-child relationships)
# Linux/WSL:
sudo apt install psmisc

# macOS:
brew install pstree

# View process tree for your user
pstree -p $USER

# Or in htop, press F5 for tree view
```

---

## The Exit Ticket Challenge: Rescue Mission

Time to put your skills to the test!

### Your Mission

Create a "runaway" process, find it, analyze it, and safely terminate it while documenting every step.

### Tasks

**Task 1:** Create a CPU-intensive background process

```bash
# This one uses CPU without output
cat /dev/zero | head -c 100M | md5sum &
```

Record the job number and PID.

**Task 2:** Use `ps` to find information about this process

```bash
ps aux | grep [first_few_characters_of_command]
```

Record:
- The PID
- The %CPU
- The %MEM
- The full COMMAND

**Task 3:** Use `top` or `htop` to monitor it in real-time

Take a screenshot showing the process near the top of the list.

**Task 4:** Attempt to kill it with SIGTERM

```bash
kill PID
```

Wait 3 seconds, then check if it's still running:
```bash
ps aux | grep PID
```

Record whether it died or is still running.

**Task 5:** If it's still running, force kill it with SIGKILL

```bash
kill -9 PID
```

Verify it's gone:
```bash
ps aux | grep PID
jobs
```

**Task 6:** Answer these reflection questions:

1. Why is it important to try SIGTERM before SIGKILL?
2. What's the difference between `kill` (with PID) and `pkill` (with name)?
3. In bioinformatics, when might you need to kill a process? Give two scenarios.
4. How can you prevent a long-running analysis from blocking your terminal?

---

## Best Practices for Long-Running Analyses

### 1. Always Use Background Jobs for Long Analyses

```bash
# BAD: Blocks terminal
bwa mem ref.fa sample.fastq > output.sam

# GOOD: Runs in background
bwa mem ref.fa sample.fastq > output.sam 2> bwa.log &
```

### 2. Redirect Output and Errors

```bash
# Redirect both stdout and stderr
command > output.txt 2> errors.log &

# Or combine them
command &> combined_log.txt &
```

### 3. Use `nohup` for Processes That Survive Logout

**`nohup`** (no hang up) makes a process immune to terminal closure:

```bash
nohup long_analysis.sh > output.log 2>&1 &
```

Now you can:
- Close your terminal
- Log out
- Lose SSH connection

And the process keeps running!

### 4. Use `screen` or `tmux` for Session Persistence

**`screen`** and **`tmux`** are terminal multiplexers that let you:
- Create persistent sessions that survive disconnections
- Have multiple terminal windows in one SSH session
- Detach and reattach to running sessions

**Basic screen usage:**
```bash
# Start a screen session
screen -S analysis

# Run your analysis
python long_analysis.py

# Detach: Press Ctrl+A, then D

# Reattach later
screen -r analysis
```

We'll cover these in more depth in later modules on cluster computing.

### 5. Monitor Resource Usage Before Running

```bash
# Check current load
top

# Check available memory
free -h

# Check disk space
df -h
```

Make sure you have enough resources before starting a huge job!

---

## Quick Reference

### Job Control

| Command | What It Does |
|---------|-------------|
| `command &` | Run in background |
| **Ctrl+Z** | Suspend foreground job |
| `jobs` | List jobs in this shell |
| `fg %N` | Bring job N to foreground |
| `bg %N` | Resume job N in background |
| **Ctrl+C** | Interrupt (kill) foreground job |

### Viewing Processes

| Command | What It Does |
|---------|-------------|
| `ps` | Show your processes |
| `ps aux` | Show all processes |
| `ps -u USER` | Show processes for USER |
| `top` | Live process monitor |
| `htop` | Better live monitor (if installed) |
| `pgrep name` | Find PID by process name |
| `pstree` | Show process tree |

### Killing Processes

| Command | What It Does |
|---------|-------------|
| `kill PID` | Send SIGTERM to process (polite) |
| `kill -9 PID` | Send SIGKILL to process (force) |
| `pkill name` | Kill processes by name |
| `pkill -9 name` | Force kill by name |
| `pkill -u USER name` | Kill USER's processes named "name" |
| `killall name` | Kill all processes named "name" |

### Useful Patterns

```bash
# Find CPU hog
ps aux | sort -k3 -nr | head -n 5

# Find memory hog
ps aux | sort -k4 -nr | head -n 5

# Find all Python processes
ps aux | grep python

# Kill all Python scripts (careful!)
pkill python

# Check if a specific command is running
pgrep -a "command_name"
```

---

## Exit Ticket

To complete this module, send me an email with:

**Subject:** Bioinfo M7 Exit Ticket – [Your Name]

**Content:**

1. Screenshot from Task 3 showing the process in top/htop
2. Recorded information from all 5 tasks (PID, CPU%, etc.)
3. Your answers to the 4 reflection questions
4. One paragraph (3-4 sentences) describing a time you might need these skills in real bioinformatics work

---

## Summary

Congratulations! You now understand:

✓ What processes are and how they work
✓ Foreground vs background execution
✓ Job control: suspend (Ctrl+Z), resume (fg/bg), list (jobs)
✓ Viewing processes with ps, top, and htop
✓ Killing processes with kill and pkill
✓ Different signals: SIGTERM (polite) vs SIGKILL (force)
✓ How to rescue a frozen terminal
✓ Best practices for long-running bioinformatics analyses
✓ Using nohup for processes that survive logout

**These skills are essential!** You'll use them constantly when:
- A script hangs and you need to kill it
- You accidentally start a huge job in foreground
- You need to check if an overnight alignment finished
- You want to run multiple analyses in parallel
- Your terminal freezes and you need to recover

Next module, we'll learn text editing with nano and how to work with compressed files.

**Next:** [Module 8 — Editing & Compression](08-editing-compression.md)
