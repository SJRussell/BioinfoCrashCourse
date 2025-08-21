# Module 1 — Navigation & Files

**Time:** ~60 min  
**Goal:** Move confidently; preview text with less/head/tail/wc; get comfortable with paths and globs.  
**Exit ticket (email me):** paste the 3 commands you used for the treasure hunt + their outputs.

!!! info "What you'll learn"
    - Paths: absolute vs relative; ~, ., ..
    - Commands: pwd, ls -lah, cd, mkdir -p, touch, cp, mv, rm -i
    - Viewers: less, head, tail, wc
    - Globbing and quoting

## 1) Warm-up workspace

```bash
cd ~/de-onramp/lesson1
pwd
```

## 2) Paths & moving around

```bash
pwd                      # print working directory
mkdir -p demo/a/b && cd demo/a/b
pwd
cd ..                    # up one
cd -                     # jump back
```

## 3) Listing & sizes

```bash
ls
ls -lah                  # long list, human-readable sizes, include hidden
```

## 4) Make, copy, move, remove (safely)

```bash
touch notes.txt
cp notes.txt notes_copy.txt
mv notes_copy.txt notes_renamed.txt
rm -i notes_renamed.txt  # -i = interactive (safer)
```

## 5) View text quickly

```bash
echo -e "line1\nline2\nline3" > tiny.txt
less tiny.txt            # space=next page, /pattern, n=next, q=quit
head -n 2 tiny.txt
tail -n 1 tiny.txt
wc -l tiny.txt
```

!!! tip "Tab completion & friends"
    - Tab completes filenames and commands.
    - Double-Tab shows all matches.
    - Ctrl-r searches your command history (type part of a command, hit Enter to run).

## 6) Treasure hunt (type it!)

Create some fake files:

```bash
mkdir -p sandbox/{data,logs,results}
touch sandbox/data/sample_{A,B,C}.fastq.gz
touch sandbox/logs/{run1.log,run2.log,notes.md}
touch sandbox/results/{out.txt,summary.tsv}
```

**Challenges:**

1. Find every `*.fastq*` under `sandbox/` and show their sizes (human-readable).

2. Count how many files are in `sandbox/logs`.

3. Print full paths of everything in `sandbox/results`.

*(Hints: find, globs like `**/*.fastq*`, du -h, wc -l, realpath.)*

## 7) Common gotchas

- Spaces in path? Quote it: `cd "My Folder"`.
- `rm` is forever—use `-i` while learning.
- If `less` looks weird, press `q` then reopen with `-S` to disable line wrapping.

## 8) Exit Ticket (email)

**Subject:** DE M1 Exit Ticket – <Your Name>  
**Paste:**

- The 3 commands you used for the treasure hunt.
- The outputs (copy as text).