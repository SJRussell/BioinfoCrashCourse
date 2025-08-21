# Module 0 — Setup & Expectations (Local-Only)

**Time:** 30–45 min  
**Goal:** Be ready to type, save, and run commands locally. Windows users will use WSL2 + Ubuntu.  
**Exit ticket (email me):** a screenshot of your VS Code terminal and one sentence confirming you submitted your Alliance account request.

!!! tip "Type it, don't paste it"
    For this tutorial, type commands first. Copy/paste only to check your work afterward. Your brain will thank you in month 3.

## 1) Editor + terminal

=== "Windows (WSL2)"

    Open PowerShell as Administrator, then install WSL + Ubuntu:

    ```bash
    wsl --install
    ```

    If prompted, pick Ubuntu 22.04 LTS. Reboot if asked.

    After reboot, launch Ubuntu from the Start menu; create your Linux username/password.

    Verify WSL2:

    ```bash
    wsl -l -v
    ```

    If needed:

    ```bash
    wsl --set-default-version 2
    ```

    Install VS Code (Windows). In VS Code, install the extensions:

    - WSL (Microsoft)
    - Remote - SSH (Microsoft) (for later)

    Open VS Code → Ctrl+Shift+P → "WSL: Connect to WSL".

    In VS Code (WSL), open a terminal (Ctrl+`) and run:

    ```bash
    echo $SHELL
    bash --version
    ```

=== "macOS"

    Use the built-in Terminal (or iTerm2).

    Install VS Code (Mac).

    In VS Code, open the integrated terminal (Ctrl+` / Cmd+`).

    macOS defaults to zsh. That's fine—these lessons work the same. If you prefer bash:

    ```bash
    bash
    ```

=== "Linux"

    Use your distro's terminal and install VS Code if desired. You're good to go.

## 2) Keyboard habits (do these now)

- Tab to autocomplete; double-Tab to see suggestions.
- ↑/↓ to cycle history; Ctrl-r for reverse search; Ctrl-a/e to jump to start/end; Ctrl-u/k to cut line.

Try it:

```bash
mkdi<Tab>    # should complete to mkdir
```

## 3) Create your workspace

```bash
mkdir -p ~/de-onramp/{lesson1,lesson2,assets} && cd ~/de-onramp && pwd
ls -lah
```

## 4) Request your Alliance/CCDB account (do now)

Go to https://ccdb.computecanada.ca → Register.

- **Role:** Sponsored Student / Group Member.
- **Sponsor/group:** def-srussell (you'll be approved under Dr. Russell's group).
- Use your institutional email. Save your username.

You won't use the cluster yet. This just avoids delays later.

## 5) Exit Ticket (email)

**Subject:** DE M0 Exit Ticket – <Your Name>  
**Attach:** screenshot of VS Code terminal.  
**Message:** "I submitted my Alliance request on <date>."