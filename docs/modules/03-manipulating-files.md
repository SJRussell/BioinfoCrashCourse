# Module 3 — Manipulating Files (Safely!)

**Time:** 60–75 min
**Goal:** Learn to copy, move, rename, and delete files and directories while understanding critical safety practices.
**Exit ticket (email me):** Your completed file organization exercise showing before and after directory listings.

---

## What You'll Learn

By the end of this module, you will understand:

- How to copy files and directories with `cp`
- How to move and rename files with `mv`
- How to delete files and directories with `rm` (safely!)
- Why Unix doesn't have an "undo" for deletions
- Safety practices every bioinformatician should follow
- File permissions basics

---

## ⚠️ Critical Safety Warning

**UNIX COMMANDS ARE PERMANENT. THERE IS NO RECYCLE BIN.**

When you delete a file with `rm`, it's **gone forever**. You cannot undo it. You cannot recover it (easily). This is not like deleting a file on Windows or macOS where it goes to the Recycle Bin.

**In this module, we'll teach you:**
1. How to use "interactive mode" to confirm deletions
2. How to test commands before running them
3. Best practices to protect yourself
4. How to make backups

**Golden rule:** When you're learning, ALWAYS use the `-i` flag with potentially destructive commands.

---

## Copying Files: The `cp` Command

The `cp` command copies files or directories.

```bash
cp [options] source destination
```

`cp` stands for "**c**o**p**y."

### Basic File Copying

**Copy a file to a new name:**
```bash
cp file.txt file_copy.txt
```

This creates `file_copy.txt` as an exact copy of `file.txt`. The original remains unchanged.

**Copy a file to a different directory:**
```bash
cp file.txt ~/backup/
```

This copies `file.txt` into the `~/backup/` directory with the same filename.

**Copy and rename in one command:**
```bash
cp file.txt ~/backup/file_backup_2024.txt
```

### Practice: Basic Copying

Let's practice:

```bash
cd ~/bioinfo-course
mkdir -p module03/practice
cd module03/practice

# Create a test file
echo "This is important data" > experiment.txt

# Make a backup copy
cp experiment.txt experiment_backup.txt

# Verify both exist
ls -lh

# Check they have the same content
cat experiment.txt
cat experiment_backup.txt
```

### Copying Multiple Files

**Copy multiple files to a directory:**
```bash
cp file1.txt file2.txt file3.txt destination_directory/
```

The destination must be a directory when copying multiple files.

**Example:**
```bash
mkdir ~/bioinfo-course/module03/practice/archive
cp experiment.txt experiment_backup.txt archive/
ls -lh archive/
```

### Useful `cp` Options

| Option | What It Does | Example |
|--------|-------------|---------|
| `-i` | **Interactive**: ask before overwriting | `cp -i file.txt backup.txt` |
| `-r` or `-R` | **Recursive**: copy directories and their contents | `cp -r dir1/ dir2/` |
| `-v` | **Verbose**: show what's being copied | `cp -v file.txt backup/` |
| `-u` | **Update**: copy only if source is newer | `cp -u *.txt backup/` |
| `-p` | **Preserve**: keep original timestamps and permissions | `cp -p file.txt backup/` |

### The `-i` Flag: Interactive Mode (Use This!)

The `-i` flag makes `cp` ask before overwriting existing files:

```bash
echo "Version 1" > data.txt
echo "Version 2" > data_new.txt

# Try to overwrite without -i (dangerous!)
cp data_new.txt data.txt      # Overwrites silently!

# Better: use -i
cp -i data_new.txt data.txt
```

Output:
```
cp: overwrite 'data.txt'?
```

Type `y` to confirm, `n` to cancel.

!!! tip "Make cp Interactive by Default"
    Many people create an "alias" so `cp` always uses `-i`:
    ```bash
    alias cp='cp -i'
    ```
    Add this to your `~/.bashrc` file to make it permanent. We'll learn about aliases in a later module.

### Copying Directories: The `-r` Flag

To copy a directory and all its contents, you **must** use the `-r` (recursive) flag:

```bash
cp -r source_directory/ destination_directory/
```

**Example:**
```bash
cd ~/bioinfo-course/module03/practice

# Create a directory with files
mkdir project1
echo "Data" > project1/data.txt
echo "Analysis" > project1/analysis.txt

# Copy the entire directory
cp -r project1/ project1_backup/

# Verify
ls -lh project1_backup/
```

Without `-r`, you'll get an error:
```bash
cp project1/ project2/
# Error: cp: -r not specified; omitting directory 'project1/'
```

!!! warning "Copying Directories in Bioinformatics"
    When copying analysis directories with many files, use:
    ```bash
    cp -r -v project/ project_backup/
    ```
    The `-v` (verbose) flag shows progress, which is useful for large directory trees.

---

## Moving and Renaming: The `mv` Command

The `mv` command does **two** things:
1. **Move** files/directories to a new location
2. **Rename** files/directories

```bash
mv [options] source destination
```

`mv` stands for "**m**o**v**e."

### Renaming Files

**Rename a file:**
```bash
mv old_name.txt new_name.txt
```

**Example:**
```bash
cd ~/bioinfo-course/module03/practice
touch sample_01.txt

# Rename it
mv sample_01.txt sample_001.txt

# Verify
ls -lh
```

The original file is gone—it's now named `sample_001.txt`.

### Moving Files

**Move a file to a different directory:**
```bash
mv file.txt ~/destination/
```

**Move and rename simultaneously:**
```bash
mv file.txt ~/destination/renamed_file.txt
```

**Example:**
```bash
mkdir results
echo "Analysis complete" > summary.txt

# Move it to results/
mv summary.txt results/

# Verify it's gone from current directory
ls -lh

# Verify it's in results/
ls -lh results/
```

### Moving Multiple Files

**Move multiple files to a directory:**
```bash
mv file1.txt file2.txt file3.txt destination_directory/
```

**Example:**
```bash
touch data1.txt data2.txt data3.txt
mkdir raw_data
mv data1.txt data2.txt data3.txt raw_data/
ls -lh raw_data/
```

### Moving Directories

Unlike `cp`, `mv` doesn't need a `-r` flag for directories:

```bash
mv old_directory_name/ new_directory_name/    # Rename
mv directory/ ~/different/location/            # Move
```

**Example:**
```bash
mkdir experiment_draft
mv experiment_draft/ experiment_final/
ls -lh
```

### Useful `mv` Options

| Option | What It Does | Example |
|--------|-------------|---------|
| `-i` | **Interactive**: ask before overwriting | `mv -i file.txt backup.txt` |
| `-v` | **Verbose**: show what's being moved | `mv -v *.txt archive/` |
| `-n` | **No clobber**: never overwrite existing files | `mv -n file.txt existing.txt` |
| `-u` | **Update**: move only if source is newer | `mv -u *.txt backup/` |

!!! danger "mv Overwrites by Default"
    Unlike `cp`, `mv` will **silently overwrite** files without warning unless you use `-i`:
    ```bash
    mv important.txt existing_file.txt    # Destroys existing_file.txt!
    ```

    **Always use `-i` when learning:**
    ```bash
    mv -i important.txt existing_file.txt
    ```

### Renaming Best Practices for Bioinformatics

**Bad naming:**
```bash
results.txt
final_results.txt
final_results_v2.txt
final_results_v2_FINAL.txt    # 😱
```

**Better naming:**
```bash
results_2024-01-15.txt
results_2024-01-16.txt
```

**Best naming (programmatic):**
```bash
sample001_results.txt
sample002_results.txt
sample003_results.txt
```

This makes it easy to process files in loops or with wildcards (next module!).

---

## Deleting Files: The `rm` Command

The `rm` command **permanently deletes** files.

```bash
rm [options] file(s)
```

`rm` stands for "**r**e**m**ove."

### ⚠️ DANGER: rm is Permanent

**Before we continue, understand this:**

1. `rm` does NOT move files to trash
2. There is NO undo
3. Deleted files are essentially unrecoverable (unless you have backups)
4. `rm` can delete entire directories in milliseconds
5. There is no confirmation by default

**Horror stories:**
```bash
rm -rf /    # This would try to delete your ENTIRE SYSTEM (don't run this!)
rm -rf *    # In the wrong directory, this deletes everything
```

**That's why we'll teach you to use `-i` (interactive mode) while learning.**

### Deleting Files Safely

**Delete a single file (interactive mode):**
```bash
rm -i unwanted.txt
```

Output:
```
rm: remove regular file 'unwanted.txt'?
```

Type `y` to confirm, `n` to cancel.

**Example:**
```bash
cd ~/bioinfo-course/module03/practice
echo "Temporary data" > temp.txt

# Delete safely
rm -i temp.txt
# Type 'y' and press Enter

# Verify it's gone
ls -lh
```

### Deleting Multiple Files

```bash
rm -i file1.txt file2.txt file3.txt
```

It will ask about each file:
```
rm: remove regular file 'file1.txt'? y
rm: remove regular file 'file2.txt'? y
rm: remove regular file 'file3.txt'? y
```

### Deleting Directories: The `-r` Flag

To delete a directory and everything inside it, use `-r` (recursive):

```bash
rm -r directory_name/
```

**With safety:**
```bash
rm -ri directory_name/
```

This asks about EVERY file in the directory (can be tedious but safe!).

**Example:**
```bash
mkdir test_dir
touch test_dir/file1.txt
touch test_dir/file2.txt

# Delete directory and contents (with confirmation)
rm -ri test_dir/
```

### Useful `rm` Options

| Option | What It Does | Danger Level |
|--------|-------------|-------------|
| `-i` | **Interactive**: ask before every deletion | ✅ Safe |
| `-I` | Ask once if deleting 3+ files or recursive | ⚠️ Moderate |
| `-v` | **Verbose**: show what's being deleted | ✅ Safe (informative) |
| `-r` or `-R` | **Recursive**: delete directories | ⚠️ Dangerous |
| `-f` | **Force**: never prompt, ignore non-existent | 🔥 VERY DANGEROUS |
| `-rf` | Recursive + Force | 💀 EXTREMELY DANGEROUS |

!!! danger "NEVER Use rm -rf Casually"
    The combination `rm -rf` is incredibly dangerous:
    ```bash
    rm -rf directory/    # Deletes EVERYTHING inside with NO confirmation
    ```

    Many system disasters start with `rm -rf` in the wrong directory.

    **While learning, NEVER use `-f`.**

### Alternative: `rmdir` for Empty Directories

If you want to delete a directory but want assurance it's empty, use `rmdir`:

```bash
rmdir directory_name/
```

`rmdir` ONLY works on empty directories. If it contains files, you get an error:
```
rmdir: failed to remove 'directory_name/': Directory not empty
```

This is a safety feature!

**Example:**
```bash
mkdir empty_dir
rmdir empty_dir    # Works!

mkdir full_dir
touch full_dir/file.txt
rmdir full_dir     # Error! Directory not empty
```

---

## Safety Practices for File Manipulation

### Rule 1: Look Before You Leap

Before running a destructive command, **simulate** it:

```bash
# Instead of:
rm *.txt

# First do:
ls *.txt    # See what would be deleted

# Then if it looks right:
rm -i *.txt
```

### Rule 2: Use Interactive Mode While Learning

Add `-i` to destructive commands:
```bash
cp -i source.txt dest.txt    # Ask before overwriting
mv -i old.txt new.txt        # Ask before overwriting
rm -i file.txt               # Ask before deleting
```

### Rule 3: Make Backups of Important Files

Before major changes:
```bash
# Back up a file
cp important_data.txt important_data.txt.backup

# Back up a directory
cp -r project/ project_backup_2024-01-15/
```

### Rule 4: Use Verbose Mode to See What's Happening

```bash
cp -v source.txt destination.txt
mv -v *.txt archive/
rm -v temp*.txt
```

The `-v` flag shows exactly what's being operated on.

### Rule 5: Be Careful with Wildcards

Wildcards (like `*`) can match more than you expect:

```bash
# Dangerous!
rm *.txt    # Deletes ALL .txt files in the current directory!

# Safer:
ls *.txt    # Check what matches first
rm -i *.txt # Then delete with confirmation
```

We'll cover wildcards in detail in the next module.

### Rule 6: Never Run rm -rf Unless You're Absolutely Sure

```bash
# 💀 DANGER ZONE
rm -rf /path/to/directory/
```

**Before running this:**
1. Triple-check the path
2. Use `pwd` to confirm where you are
3. Use `ls` to verify the contents
4. Consider using `rm -ri` instead (with confirmation)

---

## Understanding File Permissions (Basics)

When you run `ls -l`, you see permissions:

```
-rw-r--r--  1 alice users  1234 Jan 15 09:15 data.txt
```

The first 10 characters show permissions:

```
-rw-r--r--
│││││││││└ Others can read
││││││└└└─ Group can read
│││└└└──── Owner can read and write
└───────── File type (- = regular file, d = directory)
```

**Permission letters:**
- `r` = **read**: can view the file
- `w` = **write**: can modify or delete the file
- `x` = **execute**: can run as a program

**Why this matters for deletion:**

You can delete a file if you have **write permission on the directory** containing it, even if you don't have write permission on the file itself!

```bash
# Create a read-only file
echo "Protected" > readonly.txt
chmod 444 readonly.txt    # Make it read-only

# You can still delete it (if you have write permission on the directory)
rm -i readonly.txt
```

We'll cover permissions in much more detail in a later module.

---

## Practical Exercise: Organizing a Messy Project

Let's practice file manipulation by organizing a messy project directory.

### Setup

```bash
cd ~/bioinfo-course/module03
mkdir -p messy_project
cd messy_project

# Create a messy directory structure
touch data1.txt data2.txt data3.txt
touch analysis_v1.txt analysis_v2.txt analysis_final.txt
touch notes.txt
touch temp_file.txt backup.txt
mkdir old_stuff
touch old_stuff/obsolete.txt
```

### Your Tasks

**Task 1:** Create organized subdirectories:
- `raw_data/`
- `analysis/`
- `notes/`
- `trash/`

<details>
<summary>Solution</summary>

```bash
mkdir raw_data analysis notes trash
```
</details>

**Task 2:** Move all `data*.txt` files into `raw_data/`

<details>
<summary>Solution</summary>

```bash
mv data*.txt raw_data/
```
</details>

**Task 3:** Move all `analysis*.txt` files into `analysis/`

<details>
<summary>Solution</summary>

```bash
mv analysis*.txt analysis/
```
</details>

**Task 4:** Move `notes.txt` into the `notes/` directory

<details>
<summary>Solution</summary>

```bash
mv notes.txt notes/
```
</details>

**Task 5:** Move `temp_file.txt` and `backup.txt` into `trash/`

<details>
<summary>Solution</summary>

```bash
mv temp_file.txt backup.txt trash/
```
</details>

**Task 6:** Rename `analysis_final.txt` to `analysis_2024-01-15.txt` (use today's date)

<details>
<summary>Solution</summary>

```bash
cd analysis/
mv analysis_final.txt analysis_2024-01-15.txt
cd ..
```
</details>

**Task 7:** Delete the `old_stuff/` directory and its contents (using `-ri` for safety)

<details>
<summary>Solution</summary>

```bash
rm -ri old_stuff/
```
</details>

**Task 8:** List your final directory structure

<details>
<summary>Solution</summary>

```bash
ls -lR
```
</details>

---

## The Exit Ticket Challenge: Real-World Bioinformatics Organization

Now for a realistic bioinformatics scenario!

### Scenario

You've just downloaded data from a sequencing facility, and it's a mess. Your task: organize it into a clean project structure.

### Setup

```bash
cd ~/bioinfo-course/module03
mkdir -p exit_challenge
cd exit_challenge

# Create messy files (like from a sequencing center!)
touch sample1_R1.fastq sample1_R2.fastq
touch sample2_R1.fastq sample2_R2.fastq
touch sample3_R1.fastq sample3_R2.fastq
touch sample_info.txt
touch README.txt
touch pipeline_v1.sh pipeline_v2.sh pipeline_v3.sh
touch results_temp.txt output_old.txt
touch .DS_Store    # Mac junk file
```

### Your Mission

Create this directory structure and organize the files:

```
exit_challenge/
├── 00_raw_data/
│   └── fastq/
├── 01_scripts/
├── 02_results/
└── docs/
```

**Specific tasks:**

1. Create the directory structure above
2. Move all `.fastq` files into `00_raw_data/fastq/`
3. Move the most recent pipeline script (`pipeline_v3.sh`) into `01_scripts/` and rename it to `pipeline.sh`
4. Move `sample_info.txt` and `README.txt` into `docs/`
5. Delete old pipeline versions (`pipeline_v1.sh`, `pipeline_v2.sh`)
6. Delete temporary/junk files (`results_temp.txt`, `output_old.txt`, `.DS_Store`)
7. Create a backup of your `docs/` directory called `docs_backup/`

**Verification:**

After completing your tasks, run:
```bash
ls -lR
```

This shows your entire directory structure.

---

## Exit Ticket

To complete this module, send me an email with:

**Subject:** Bioinfo M3 Exit Ticket – [Your Name]

**Content:**

1. A "before" directory listing (the output of `ls -lh` in the messy `exit_challenge/` directory before organizing)
2. The commands you used to complete all 7 tasks (in order)
3. An "after" directory listing (the output of `ls -lR` showing your organized structure)
4. One sentence explaining why you should use `-i` with `rm`

---

## Quick Reference

### Copying Files

| Command | What It Does |
|---------|-------------|
| `cp file1 file2` | Copy file1 to file2 |
| `cp file dir/` | Copy file into directory |
| `cp -r dir1/ dir2/` | Copy directory recursively |
| `cp -i file1 file2` | Interactive (ask before overwrite) |
| `cp -v file1 file2` | Verbose (show what's copied) |

### Moving/Renaming Files

| Command | What It Does |
|---------|-------------|
| `mv old new` | Rename file/directory |
| `mv file dir/` | Move file into directory |
| `mv -i file1 file2` | Interactive (ask before overwrite) |
| `mv -v *.txt dir/` | Verbose (show what's moved) |

### Deleting Files

| Command | What It Does | Safety |
|---------|-------------|--------|
| `rm -i file` | Delete file (ask for confirmation) | ✅ Safe |
| `rm -ri dir/` | Delete directory recursively (ask) | ✅ Safe |
| `rm -v file` | Delete with verbose output | ⚠️ Moderate |
| `rmdir dir/` | Delete empty directory only | ✅ Safe |
| `rm -rf dir/` | Force delete directory | 🔥 Dangerous |

---

## Summary

Congratulations! You now understand:

✓ How to copy files with `cp` and directories with `cp -r`
✓ How to move and rename files with `mv`
✓ How to delete files safely with `rm -i` and `rm -ri`
✓ Why `rm` is permanent and dangerous without precautions
✓ The difference between `rm` and `rmdir`
✓ Safety practices: interactive mode, backups, looking before leaping
✓ Basic file permissions and why they matter
✓ How to organize bioinformatics projects logically

**Remember:** When in doubt, use `-i` (interactive mode) and make backups!

In the next module, we'll learn about wildcards and pattern matching—powerful tools for working with multiple files at once.

**Next:** [Module 4 — Wildcards & Pattern Matching](04-wildcards.md)
