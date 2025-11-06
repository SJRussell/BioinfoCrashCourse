# Module 0 — Setup & Understanding Your Environment

**Time:** 45–60 min
**Goal:** Understand what a terminal is, how commands work, and set up your computing environment for bioinformatics work.
**Exit ticket (email me):** A screenshot of your terminal showing the output of `pwd`, `whoami`, and `date` commands, plus one sentence confirming you submitted your Alliance account request.

---

## What You'll Learn

By the end of this module, you will understand:

- What a terminal, shell, and command line are
- The difference between your operating system and the Unix command line
- How commands are structured
- How to get your environment set up for bioinformatics work

---

## Understanding the Terminal

### What is a Terminal?

A **terminal** (also called a **terminal emulator** or **console**) is a program that allows you to interact with your computer using text commands instead of clicking on icons and menus. Think of it as having a conversation with your computer through typing.

The terminal does two main things:

1. **Accepts input**: It lets you type commands to tell the computer what to do
2. **Displays output**: It shows you the results of those commands

Before graphical user interfaces (windows, icons, mice), the terminal was the *only* way to use a computer. Today, it's still the most powerful and efficient way to work with data, especially in bioinformatics.

### What is a Shell?

When you open a terminal, a program called a **shell** runs inside it. The shell is the interpreter that:

- Reads the commands you type
- Figures out what you want to do
- Executes those commands
- Returns the results to you

The most common shell is called **bash** (Bourne Again SHell), though macOS now defaults to **zsh** (Z Shell). For this course, both work identically for our purposes.

Think of it this way:
```
┌─────────────────────────────────┐
│         Terminal Window         │  ← The program you see
│  ┌───────────────────────────┐  │
│  │        Shell (bash)       │  │  ← The interpreter running inside
│  │  Reads and executes your  │  │
│  │        commands           │  │
│  └───────────────────────────┘  │
└─────────────────────────────────┘
```

### Why Unix/Linux for Bioinformatics?

Most bioinformatics tools are built for **Unix-like** operating systems (Linux, macOS). These systems provide:

- Powerful text processing tools (perfect for genomic data)
- Ability to chain commands together efficiently
- Remote access to high-performance computing clusters
- Free and open-source software ecosystem

**Important for Windows users:** Windows uses a different system, so we'll install a Linux environment inside Windows called WSL (Windows Subsystem for Linux). This gives you a real Linux environment to work in.

---

## Understanding Commands

### What is a Command?

A **command** is an instruction you type that tells the computer to perform an action. Commands follow a general structure:

```
command [options] [arguments]
```

Let's break this down:

- **command**: The program or action you want to run (e.g., `ls` to list files)
- **options**: Modifiers that change how the command behaves (usually start with `-` or `--`)
- **arguments**: The things you want the command to act on (e.g., file names, directories)

**Example:**
```bash
ls -l /home
```

- `ls` = command (list directory contents)
- `-l` = option (use long format to show details)
- `/home` = argument (the directory to list)

### Getting Help with Commands

Almost every command has built-in documentation. There are two main ways to access help:

**Method 1: The `--help` option**

Most commands understand `--help` (or sometimes `-h`):

```bash
ls --help
```

This displays a quick summary of what the command does and what options it accepts.

**Method 2: Manual pages (man pages)**

For more detailed information, use the `man` command (short for "manual"):

```bash
man ls
```

This opens a detailed manual. Navigate it with:
- **Space bar**: Move forward one page
- **b**: Move backward one page
- **q**: Quit and return to the command prompt
- **/word**: Search for "word"
- **n**: Go to next search result

!!! tip "Always Check Help First"
    When you encounter a new command, run `command --help` or `man command` to understand what it does. This habit will save you time and prevent mistakes.

---

## Setting Up Your Environment

Now let's get your computer ready for bioinformatics work. Follow the instructions for your operating system.

=== "Windows (WSL2)"

    ### Understanding WSL2

    **WSL** stands for **Windows Subsystem for Linux**. It allows you to run a real Linux environment directly on Windows without needing a virtual machine or dual-boot setup. WSL2 is the second version and provides better performance.

    ### Step 1: Install WSL2 and Ubuntu

    Open **PowerShell** as Administrator (right-click the Start menu → choose "Windows PowerShell (Admin)"), then type:

    ```powershell
    wsl --install
    ```

    This command installs WSL2 and the Ubuntu Linux distribution (a popular, beginner-friendly version of Linux).

    If prompted to choose a Linux distribution, select **Ubuntu 22.04 LTS** (LTS means "Long Term Support" — it's stable and well-supported).

    **You may need to reboot your computer.** If so, restart and continue.

    ### Step 2: Launch Ubuntu and Create Your User Account

    After installation (and reboot if necessary):

    1. Open the Start menu and search for "Ubuntu"
    2. Click on "Ubuntu" to launch it
    3. The first time, it will take a minute to set up
    4. Create a Linux username (can be different from your Windows username)
    5. Create a password (you won't see characters as you type—this is normal for security)

    !!! warning "Remember Your Password"
        You'll need this password when running commands that require administrator privileges (using `sudo`). Write it down somewhere safe!

    ### Step 3: Verify WSL2 Installation

    Back in PowerShell, check your WSL version:

    ```powershell
    wsl -l -v
    ```

    You should see Ubuntu listed with VERSION 2. If it shows version 1, update it:

    ```powershell
    wsl --set-default-version 2
    ```

    ### Step 4: Install Visual Studio Code

    VS Code is a powerful, free code editor that works beautifully with WSL.

    1. Download VS Code for Windows from [https://code.visualstudio.com/](https://code.visualstudio.com/)
    2. Install it (accept default options)
    3. Open VS Code
    4. Install these extensions (click the Extensions icon on the left sidebar, search for each):
        - **WSL** (by Microsoft) — Required for connecting to your Linux environment
        - **Remote - SSH** (by Microsoft) — Useful later for connecting to compute clusters

    ### Step 5: Connect VS Code to WSL

    1. In VS Code, press **Ctrl+Shift+P** to open the command palette
    2. Type "WSL: Connect to WSL" and press Enter
    3. A new VS Code window will open connected to your Ubuntu environment

    ### Step 6: Open Your Terminal in VS Code

    Press **Ctrl+`** (that's the backtick key, usually above Tab) to open the integrated terminal.

    You should see a prompt like:
    ```
    username@computername:~$
    ```

    This is your bash prompt! Let's verify everything works:

    ```bash
    echo $SHELL
    ```

    Should output: `/bin/bash`

    ```bash
    bash --version
    ```

    Should show the bash version (e.g., "GNU bash, version 5.1.16...")

    !!! success "You're Ready!"
        You now have a full Linux environment running on Windows!

=== "macOS"

    ### Understanding the macOS Terminal

    macOS is built on Unix (specifically, BSD Unix), so it already has a powerful terminal built in. You don't need to install anything extra—you already have everything you need!

    ### Step 1: Open the Terminal

    There are three ways to open the Terminal on macOS:

    1. **Spotlight Search**: Press **Cmd+Space**, type "Terminal", press Enter
    2. **Finder**: Go to Applications → Utilities → Terminal
    3. **Use iTerm2** (optional): Many developers prefer [iTerm2](https://iterm2.com/), a terminal replacement with extra features, but the built-in Terminal works great

    ### Step 2: Understand Your Shell

    macOS Catalina (10.15) and later use **zsh** (Z Shell) as the default instead of bash. Don't worry—zsh is very similar to bash, and all commands in this tutorial work the same in both.

    To check which shell you're using:

    ```bash
    echo $SHELL
    ```

    You'll see either `/bin/bash` or `/bin/zsh`.

    If you prefer bash, you can temporarily switch:

    ```bash
    bash
    ```

    Or make bash your default shell:

    ```bash
    chsh -s /bin/bash
    ```

    (You'll need to enter your password and restart the terminal)

    ### Step 3: Install Visual Studio Code (Recommended)

    While you can use the built-in Terminal, VS Code provides a nice integrated environment.

    1. Download VS Code for macOS from [https://code.visualstudio.com/](https://code.visualstudio.com/)
    2. Open the .dmg file and drag VS Code to your Applications folder
    3. Open VS Code
    4. Press **Cmd+`** (backtick) to open the integrated terminal

    ### Step 4: Verify Your Environment

    In your terminal (built-in or VS Code), run:

    ```bash
    uname -a
    ```

    This shows your system information. You should see "Darwin" (the name of the macOS kernel).

    Try a few basic commands:

    ```bash
    pwd
    ```

    This shows your current directory (probably `/Users/yourusername`).

    ```bash
    whoami
    ```

    This displays your username.

    !!! success "You're Ready!"
        Your Mac is ready for bioinformatics work right out of the box!

=== "Linux"

    ### You're Already Using Unix!

    If you're running Linux, you're already in a Unix environment—no setup needed! Linux users are typically comfortable with the terminal, but we'll still walk through everything assuming no prior knowledge.

    ### Step 1: Open Your Terminal

    Every Linux distribution has a terminal. Common ways to open it:

    - **Keyboard shortcut**: Usually **Ctrl+Alt+T**
    - **Application menu**: Search for "Terminal" or "Console"
    - **Desktop environment**:
        - GNOME: Activities → Terminal
        - KDE: Applications → System → Konsole
        - XFCE: Applications → System → Terminal

    ### Step 2: Check Your Shell

    Most Linux distributions use bash by default. Check with:

    ```bash
    echo $SHELL
    ```

    Should output: `/bin/bash`

    ### Step 3: Install Visual Studio Code (Optional)

    VS Code provides a nice integrated environment, but it's entirely optional. If you want it:

    **Ubuntu/Debian:**
    ```bash
    sudo apt update
    sudo apt install code
    ```

    **Fedora:**
    ```bash
    sudo dnf install code
    ```

    Or download from [https://code.visualstudio.com/](https://code.visualstudio.com/)

    ### Step 4: Verify Your Environment

    Run these commands:

    ```bash
    uname -a
    ```

    Shows your kernel version and system info.

    ```bash
    whoami
    ```

    Displays your username.

    ```bash
    pwd
    ```

    Shows your current directory.

    !!! success "You're Ready!"
        Your Linux system is ready for bioinformatics work!

---

## Essential Keyboard Shortcuts

Before we continue, learn these keyboard shortcuts. They will save you enormous amounts of time:

| Shortcut | What It Does |
|----------|-------------|
| **Tab** | Autocomplete commands and file names |
| **Tab Tab** | Show all possible completions |
| **↑** (Up arrow) | Recall previous command |
| **↓** (Down arrow) | Cycle forward through command history |
| **Ctrl+R** | Search command history (type to search, Enter to run) |
| **Ctrl+A** | Jump to beginning of line |
| **Ctrl+E** | Jump to end of line |
| **Ctrl+U** | Delete from cursor to beginning of line |
| **Ctrl+K** | Delete from cursor to end of line |
| **Ctrl+C** | Cancel the current command |
| **Ctrl+L** | Clear the screen |

### Try Tab Completion Now

Type this but **don't press Enter yet**:

```bash
mkdi
```

Now press **Tab**. The terminal should automatically complete it to `mkdir`.

This works for commands, file names, and directory names. If there are multiple possibilities, press **Tab twice** to see all options.

!!! tip "Type It, Don't Paste It"
    For this tutorial, **type commands by hand** first. Copy/paste only to check your work. Typing builds muscle memory, and in a few weeks, you'll be much faster than if you had copy/pasted everything. Your future self will thank you!

---

## Your First Commands

Let's try some actual commands to make sure everything is working.

### Where Am I? (`pwd`)

Type this command and press Enter:

```bash
pwd
```

**What it does:** `pwd` stands for "print working directory." It tells you which folder (directory) you're currently in.

You'll see output like:
```
/home/username          (Linux/WSL)
/Users/username         (macOS)
```

This is your **home directory**—your personal folder on the system.

### Who Am I? (`whoami`)

```bash
whoami
```

**What it does:** Displays your username on the system.

### What Time Is It? (`date`)

```bash
date
```

**What it does:** Shows the current date and time.

### Simple Output (`echo`)

```bash
echo "Hello, terminal!"
```

**What it does:** `echo` simply prints whatever you give it. It's like the terminal's way of talking back to you.

Try:
```bash
echo "I am ready to learn bioinformatics"
```

---

## Create Your Course Workspace

Let's create a organized folder structure for this course. Type this command:

```bash
mkdir -p ~/bioinfo-course
```

**Breaking this down:**

- `mkdir` = "make directory" (create a folder)
- `-p` = create parent directories if needed, and don't error if it already exists
- `~` = shorthand for your home directory
- `/bioinfo-course` = the folder name

Now move into that directory:

```bash
cd ~/bioinfo-course
```

- `cd` = "change directory" (move to a different folder)

Verify you're there:

```bash
pwd
```

You should see something like `/home/username/bioinfo-course`

Create some subdirectories for organization:

```bash
mkdir module01 module02 module03
```

This creates three folders at once.

List them:

```bash
ls
```

- `ls` = "list" (show contents of current directory)

You should see:
```
module01  module02  module03
```

---

## Request Your Alliance/CCDB Account

For later modules, you'll need access to the Digital Research Alliance of Canada's computing resources. **Start the registration process now** to avoid delays (it can take a few days to be approved).

### Steps to Register:

1. Go to [https://ccdb.alliancecan.ca](https://ccdb.alliancecan.ca)
2. Click "Register" (top right)
3. Fill out the form:
    - **Role:** Select "Sponsored Student" or "Group Member"
    - **Sponsor:** `def-srussell` (Dr. Russell's research group)
    - **Email:** Use your institutional email address
    - **Username:** Choose carefully—you'll use this for remote login
4. Submit the form

!!! info "Save Your Username"
    Write down the username you choose. You'll need it when we cover remote computing in later modules.

**You won't actually use this account yet**—we're doing all early modules on your local computer. But starting the registration now avoids waiting later.

---

## Exit Ticket

To complete this module, send me an email with:

**Subject:** Bioinfo M0 Exit Ticket – [Your Name]

**Content:**
1. A screenshot of your terminal showing the output of these three commands:
   ```bash
   pwd
   whoami
   date
   ```
2. One sentence confirming you submitted your Alliance account request (or when you plan to)

**How to take a screenshot:**
- **Windows:** Press **Windows+Shift+S**, select the area, paste into your email
- **macOS:** Press **Cmd+Shift+4**, drag to select, image saves to Desktop
- **Linux:** Use **Print Screen** or Screenshot tool from your app menu

---

## Summary

Congratulations! You now understand:

✓ What a terminal, shell, and command are
✓ How commands are structured (command + options + arguments)
✓ How to get help with `--help` and `man`
✓ Essential keyboard shortcuts like Tab completion
✓ Your first commands: `pwd`, `whoami`, `echo`, `date`
✓ How to create directories with `mkdir`
✓ How to navigate with `cd`
✓ How to list contents with `ls`

In the next module, we'll dive deeper into the directory tree structure and learn how to navigate your file system like a pro.

**Next:** [Module 1 — Terminal Basics & First Commands](01-terminal-basics.md)