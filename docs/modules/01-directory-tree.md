# Module 1 — The Directory Tree & Navigation

**Time:** 60–75 min
**Goal:** Understand how files and folders are organized in Unix, master navigation between directories, and learn to list and inspect directory contents.
**Exit ticket (email me):** The outputs of your treasure hunt commands proving you found the hidden files.

---

## What You'll Learn

By the end of this module, you will understand:

- How Unix organizes files in a tree structure
- What the root directory is and how paths work
- The difference between absolute and relative paths
- Special directory shortcuts: `~`, `.`, `..`
- How to navigate the filesystem with `cd`
- How to list directory contents with `ls` and all its useful options
- How to find out where you are at any time

---

## The Directory Tree

### How Unix Organizes Files

In Unix-like systems (Linux, macOS), all files and folders are organized in a **tree structure**, starting from a single point called the **root directory**.

Think of it like a real tree, but upside down:

```
                        /  (root)
                        |
        +---------------+---------------+
        |               |               |
      home            usr              var
        |               |               |
    +---+---+       +---+---+       +---+---+
    |       |       |       |       |       |
  alice   bob    bin     lib     log     tmp
    |
+---+---+
|       |
docs   data
```

**Key concepts:**

- **Root directory (`/`)**: The very top of the tree. Everything in the system lives under this directory.
- **Directories**: Folders that can contain files and other directories (also called "subdirectories")
- **Path**: The route you take through the tree to reach a file or directory

### Important: Not Like Windows

If you're coming from Windows, this is different:

- **Windows** has multiple drive letters: `C:\`, `D:\`, etc.
- **Unix/Linux/macOS** has ONE root (`/`), and everything hangs off it

Even USB drives and external hard drives appear as directories under the root, not as separate drive letters.

---

## Understanding Paths

### What is a Path?

A **path** is the address of a file or directory—it tells the computer exactly where something is in the directory tree.

There are two types of paths:

### 1. Absolute Paths

An **absolute path** starts from the root directory (`/`) and specifies the complete route to a file or directory.

**Examples of absolute paths:**
```
/home/alice/documents/report.txt
/usr/bin/python3
/var/log/system.log
```

Think of an absolute path like a complete street address: "123 Main Street, Springfield, IL, USA"—it tells you exactly where something is, no matter where you're currently located.

**The key identifier:** Absolute paths **always start with `/`** (the root directory).

### 2. Relative Paths

A **relative path** starts from your **current location** (your current working directory) and gives directions from there.

**Examples of relative paths:**
```
documents/report.txt          (from your home directory)
../bob/projects               (go up one level, then into bob/projects)
./data/sequences.fasta        (in the current directory's data folder)
```

Think of a relative path like giving directions: "Go two blocks north, then turn left"—the directions only make sense based on where you're starting from.

**The key identifier:** Relative paths **never start with `/`**.

### Special Directory Symbols

Unix provides shortcuts for common locations:

| Symbol | Meaning | Example |
|--------|---------|---------|
| `~` | Your home directory | `~/documents` = `/home/alice/documents` |
| `.` | The current directory | `./script.sh` = run script in current directory |
| `..` | The parent directory (one level up) | `../` = go up one level |
| `/` | The root directory | `/home` = the home directory at root level |

**Visual example of `.` and `..`:**

```
/home/alice/projects/bioinformatics
                     ^
                     You are here

.   = /home/alice/projects/bioinformatics  (current location)
..  = /home/alice/projects                  (one level up)
../.. = /home/alice                         (two levels up)
```

---

## Where Am I? The `pwd` Command

When working in the terminal, you're always "inside" a directory—this is called your **current working directory** or **working directory**.

To find out where you are, use the `pwd` command:

```bash
pwd
```

`pwd` stands for "**p**rint **w**orking **d**irectory."

**Example output:**
```
/home/alice/bioinfo-course
```

This tells you that you're currently in the `bioinfo-course` directory, which is inside `alice`, which is inside `home`, which is at the root (`/`).

!!! tip "Lost? Use pwd"
    If you ever feel lost or confused about where you are in the filesystem, just type `pwd`. It's like a GPS for the terminal.

---

## Moving Around: The `cd` Command

To change your location (move to a different directory), use the `cd` command:

```bash
cd [directory]
```

`cd` stands for "**c**hange **d**irectory."

### Basic Examples

**Go to your home directory:**
```bash
cd ~
# or just:
cd
```

Running `cd` with no arguments always takes you home.

**Go to a specific directory (absolute path):**
```bash
cd /usr/local/bin
```

**Go to a subdirectory (relative path):**
```bash
cd documents
```

This moves into the `documents` folder inside your current directory.

**Go up one level:**
```bash
cd ..
```

**Go up two levels:**
```bash
cd ../..
```

**Go to the previous directory:**
```bash
cd -
```

This is like an "undo" for `cd`—it takes you back to wherever you just were.

### Practice Navigation

Let's practice moving around. Type these commands one at a time:

```bash
cd ~                    # Go home
pwd                     # Check where you are
cd /                    # Go to the root directory
pwd                     # Now you're at /
ls                      # List what's in root (we'll learn ls next!)
cd ~                    # Go back home
cd bioinfo-course       # Go into the course folder you made in Module 0
pwd                     # Confirm you're there
cd ..                   # Go up one level
pwd                     # Back in your home directory
cd -                    # Jump back to bioinfo-course
pwd                     # Confirm
```

!!! warning "Case Sensitivity"
    Unix is **case-sensitive**. `Documents` and `documents` are different directories. If you get "No such file or directory," check your capitalization.

---

## Listing Contents: The `ls` Command

The `ls` command shows you what's inside a directory.

```bash
ls [options] [directory]
```

`ls` stands for "**l**i**s**t."

### Basic Usage

**List the current directory:**
```bash
ls
```

**List a specific directory:**
```bash
ls /home
ls ~/bioinfo-course
```

**List with more detail:**
```bash
ls -l
```

The `-l` option means "long format"—it shows permissions, owner, size, and modification date.

### Understanding `ls -l` Output

When you run `ls -l`, you'll see output like this:

```
drwxr-xr-x  2 alice users  4096 Jan 15 14:32 documents
-rw-r--r--  1 alice users  1234 Jan 15 09:15 notes.txt
-rwxr-xr-x  1 alice users  8192 Jan 14 16:45 script.sh
```

Let's decode this:

```
drwxr-xr-x  2 alice users  4096 Jan 15 14:32 documents
│││││││││  │   │     │      │     │          └─ File/directory name
││││││││││ │   │     │      │     └─ Modification date and time
│││││││││  │   │     │      └─ Size in bytes
││││││││││ │   │     └─ Group
│││││││││  │   └─ Owner
││││││││││ └─ Number of links
│└┴┴┴┴┴┴┴─ Permissions
└─ Type (d=directory, -=regular file, l=link)
```

**First character tells you the type:**
- `d` = directory (folder)
- `-` = regular file
- `l` = symbolic link (like a shortcut)

**The next 9 characters are permissions** (we'll cover these in detail later):
- `rwx` = read, write, execute permissions for the owner
- `r-x` = read and execute for the group
- `r-x` = read and execute for everyone else

### Useful `ls` Options

| Option | What It Does | Example |
|--------|-------------|---------|
| `-l` | Long format (detailed) | `ls -l` |
| `-a` | Show **all** files, including hidden ones (names starting with `.`) | `ls -a` |
| `-h` | Human-readable sizes (KB, MB, GB instead of bytes) | `ls -lh` |
| `-t` | Sort by modification time (newest first) | `ls -lt` |
| `-r` | Reverse the sort order | `ls -lr` |
| `-S` | Sort by file size (largest first) | `ls -lS` |
| `-R` | Recursive (show subdirectories too) | `ls -R` |

### Combining Options

You can combine multiple options with a single dash:

```bash
ls -lah
```

This means: long format (`-l`) + show all files (`-a`) + human-readable sizes (`-h`)

**Example output:**
```bash
$ ls -lah ~/bioinfo-course
total 24K
drwxr-xr-x  5 alice users 4.0K Jan 15 14:32 .
drwxr-xr-x 28 alice users 4.0K Jan 15 14:30 ..
drwxr-xr-x  2 alice users 4.0K Jan 15 14:32 module01
drwxr-xr-x  2 alice users 4.0K Jan 15 14:32 module02
drwxr-xr-x  2 alice users 4.0K Jan 15 14:32 module03
-rw-r--r--  1 alice users  127 Jan 15 14:35 README.txt
```

Notice:
- `.` represents the current directory itself
- `..` represents the parent directory
- Sizes are shown as `4.0K` instead of `4096` bytes (thanks to `-h`)

### Hidden Files

In Unix, files and directories that start with a `.` are **hidden** by default. These are usually configuration files.

**Examples of hidden files/directories:**
- `.bashrc` — bash configuration
- `.ssh/` — SSH keys and settings
- `.config/` — application configurations

To see hidden files, use `-a`:

```bash
ls -a
ls -la      # Long format + show all
```

### Practice with `ls`

Try these commands in your home directory:

```bash
cd ~
ls                    # Basic list
ls -l                 # Long format
ls -la                # Long format including hidden files
ls -lh                # Long format with human-readable sizes
ls -lt                # Long format sorted by time (newest first)
ls -lS                # Long format sorted by size (largest first)
ls -lrS               # Long format sorted by size (smallest first - reversed)
```

---

## Practical Exercise: Exploring Your System

Let's explore some standard Unix directories to understand the filesystem structure.

### Exercise 1: Explore the Root Directory

```bash
cd /                  # Go to the root
ls                    # See what's there
```

You'll see directories like:

- **`/home`** — Where user home directories live (on Linux)
- **`/Users`** — User home directories (on macOS)
- **`/usr`** — Unix System Resources (programs, libraries)
- **`/bin`** — Essential command binaries (programs)
- **`/etc`** — System configuration files
- **`/var`** — Variable data (logs, temporary files)
- **`/tmp`** — Temporary files (cleared on reboot)

### Exercise 2: Count Subdirectories

How many items are in the root directory?

```bash
cd /
ls | wc -l
```

The `|` (pipe) sends the output of `ls` to `wc -l` (word count, lines mode), which counts the lines.

### Exercise 3: Find Your Home

```bash
cd ~
pwd
```

Compare:

```bash
echo $HOME
```

`$HOME` is an **environment variable** that stores your home directory path. The `~` symbol is actually a shortcut that expands to `$HOME`.

---

## The Treasure Hunt Challenge

Now let's put your skills to the test! This exercise will require you to navigate directories, list contents, and use various `ls` options.

### Setup: Create a Treasure Hunt Directory Structure

First, let's create a practice filesystem. Type or paste this entire block:

```bash
cd ~/bioinfo-course
mkdir -p treasure_hunt/{level1/{level2a,level2b,level2c},archive,secrets}
touch treasure_hunt/level1/clue1.txt
touch treasure_hunt/level1/level2a/clue2.txt
touch treasure_hunt/level1/level2b/.hidden_treasure.txt
touch treasure_hunt/secrets/.secret_key.txt
echo "Congratulations! You found the treasure!" > treasure_hunt/level1/level2b/.hidden_treasure.txt
echo "The secret key is: BIOINFO2024" > treasure_hunt/secrets/.secret_key.txt
echo "Look deeper in level2b..." > treasure_hunt/level1/clue1.txt
echo "The treasure is hidden nearby..." > treasure_hunt/level1/level2a/clue2.txt
```

### Your Mission

Complete these challenges and record your commands and outputs:

**Challenge 1:** Starting from `~/bioinfo-course`, navigate to the `treasure_hunt` directory and list all visible files and folders. What do you see?

<details>
<summary>Hint</summary>

```bash
cd ~/bioinfo-course/treasure_hunt
ls
```
</details>

**Challenge 2:** Use `ls` with the appropriate option to find ALL files in the `treasure_hunt` directory, including hidden ones. What hidden file(s) do you see?

<details>
<summary>Hint</summary>

```bash
ls -a
```
</details>

**Challenge 3:** Navigate to `treasure_hunt/level1` and read the contents of `clue1.txt`. Where does it tell you to look?

<details>
<summary>Hint</summary>

```bash
cd level1
cat clue1.txt
```
</details>

**Challenge 4:** Following the clue, navigate to `level2b` and find the hidden treasure file. Display its contents. What does it say?

<details>
<summary>Hint</summary>

```bash
cd level2b
ls -a
cat .hidden_treasure.txt
```
</details>

**Challenge 5:** Using a single absolute path, navigate directly from wherever you are to the `secrets` directory and reveal the hidden secret key file. What is the secret key?

<details>
<summary>Hint</summary>

```bash
cd ~/bioinfo-course/treasure_hunt/secrets
ls -a
cat .secret_key.txt
```
</details>

**Challenge 6:** From the `secrets` directory, use a relative path with `..` to navigate to the `archive` directory. Then use `pwd` to confirm your location.

<details>
<summary>Hint</summary>

```bash
cd ../archive
pwd
```
</details>

---

## Understanding File Paths in Bioinformatics

Why does all this matter for bioinformatics?

When you're working with genomic data, you'll often have:

- **Data files** in one directory: `/data/fastq/sample001.fastq.gz`
- **Reference genomes** in another: `/refs/human/GRCh38.fa`
- **Analysis scripts** in your home directory: `~/scripts/run_alignment.sh`
- **Results** in yet another place: `~/projects/cancer_study/results/`

You need to be comfortable:
- Specifying where input files are (absolute or relative paths)
- Telling programs where to write output
- Organizing your projects logically
- Navigating quickly between directories

**Example bioinformatics command using paths:**
```bash
# Running an aligner with absolute paths
bwa mem /refs/human/GRCh38.fa /data/fastq/sample001_R1.fastq.gz /data/fastq/sample001_R2.fastq.gz > ~/results/sample001.sam
```

Understanding paths is fundamental to running bioinformatics pipelines!

---

## Common Mistakes and How to Avoid Them

### Mistake 1: Forgetting Where You Are

**Problem:** Running commands without knowing your current directory.

**Solution:** Use `pwd` frequently, especially when you're starting out.

### Mistake 2: Using the Wrong Path Type

**Problem:** Using a relative path when you should use absolute, or vice versa.

**Solution:**
- If you need something to work regardless of where you are, use **absolute paths** (`/home/alice/data`)
- If you're working within a project, **relative paths** are fine (`./data`)

### Mistake 3: Spaces in Paths

**Problem:** Paths with spaces cause errors.

```bash
cd my documents     # ERROR! Tries to cd to "my" then run "documents"
```

**Solution:** Quote paths with spaces or escape them:

```bash
cd "my documents"   # Correct
cd my\ documents    # Also correct (backslash escapes the space)
```

**Better solution:** Avoid spaces in directory and file names! Use underscores or dashes instead:

```bash
mkdir my_documents  # Better
mkdir my-documents  # Also good
```

### Mistake 4: Case Sensitivity

**Problem:** Typing `Documents` when the folder is actually `documents`.

**Solution:** Use tab completion! Start typing and press Tab—the terminal will complete it with the correct capitalization.

---

## Quick Reference

### Navigation Commands

| Command | What It Does |
|---------|-------------|
| `pwd` | Print working directory (where am I?) |
| `cd [dir]` | Change to directory |
| `cd ~` or `cd` | Go to home directory |
| `cd /` | Go to root directory |
| `cd ..` | Go up one level |
| `cd -` | Go to previous directory |
| `cd ../..` | Go up two levels |

### Listing Commands

| Command | What It Does |
|---------|-------------|
| `ls` | List directory contents |
| `ls -l` | Long format (detailed) |
| `ls -a` | Show all files (including hidden) |
| `ls -lh` | Long format with human-readable sizes |
| `ls -lt` | Sort by modification time |
| `ls -lS` | Sort by size |
| `ls -R` | Recursive (show subdirectories) |

### Path Shortcuts

| Symbol | Meaning |
|--------|---------|
| `~` | Home directory |
| `.` | Current directory |
| `..` | Parent directory (one level up) |
| `/` | Root directory |
| `-` | Previous directory (with `cd`) |

---

## Exit Ticket

To complete this module, send me an email with:

**Subject:** Bioinfo M1 Exit Ticket – [Your Name]

**Content:**

1. The command you used for Challenge 4 and the message you found in the hidden treasure file
2. The command you used for Challenge 5 and the secret key you discovered
3. The command you used for Challenge 6 and the output of `pwd` showing you reached the archive directory
4. One sentence describing the difference between absolute and relative paths in your own words

---

## Summary

Congratulations! You now understand:

✓ How Unix organizes files in a tree structure starting from the root (`/`)
✓ The difference between absolute and relative paths
✓ Special directory shortcuts: `~`, `.`, `..`
✓ How to check your location with `pwd`
✓ How to navigate with `cd`
✓ How to list directory contents with `ls` and its many options
✓ How to find hidden files with `ls -a`
✓ Why understanding paths is critical for bioinformatics workflows

In the next module, we'll learn how to work with files—creating, viewing, and understanding their contents.

**Next:** [Module 2 — Working with Files](02-working-with-files.md)
