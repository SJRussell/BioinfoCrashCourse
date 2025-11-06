# Module 2 — Working with Files

**Time:** 60–75 min
**Goal:** Learn to create files and directories, view file contents, understand different file types, and inspect files efficiently.
**Exit ticket (email me):** Your answers to the DNA sequence analysis challenge, including all commands used.

---

## What You'll Learn

By the end of this module, you will understand:

- How to create empty files and directories
- Different ways to view file contents
- When to use `cat`, `less`, `head`, and `tail`
- How to identify file types
- How to count lines, words, and characters in files
- Best practices for viewing large files (like FASTQ files!)

---

## Creating Files and Directories

### Creating Empty Files with `touch`

The `touch` command creates an empty file or updates the timestamp of an existing file:

```bash
touch filename.txt
```

**What it does:** If the file doesn't exist, `touch` creates it. If it already exists, `touch` updates its "last modified" time to now.

**Why is it called "touch"?** Think of it as "touching" a file to update when it was last accessed.

**Examples:**

```bash
cd ~/bioinfo-course
touch notes.txt              # Create a single file
touch file1.txt file2.txt    # Create multiple files at once
ls -lh                       # Verify they were created
```

You'll see the files listed with 0 bytes—they're empty.

### Creating Directories with `mkdir`

We learned `mkdir` briefly in Module 0, but let's explore it more:

```bash
mkdir directory_name
```

**Create nested directories (parent + child):**

```bash
mkdir -p projects/experiment1/data
```

The `-p` flag means:
- Create parent directories if they don't exist
- Don't error if the directory already exists

Without `-p`, you'd have to create each level separately:

```bash
mkdir projects
mkdir projects/experiment1
mkdir projects/experiment1/data    # Three separate commands!
```

**Create multiple directories at once:**

```bash
mkdir analysis results figures
```

**Create a complex directory structure:**

```bash
mkdir -p project/{raw_data,processed_data,scripts,results/{tables,plots}}
```

This creates:
```
project/
├── raw_data/
├── processed_data/
├── scripts/
└── results/
    ├── tables/
    └── plots/
```

The curly braces `{}` allow you to create multiple directories in one command. We'll learn more about this syntax in the wildcards module.

!!! tip "Organize Early"
    In bioinformatics, organization is critical. Set up a logical directory structure at the start of each project. Your future self will thank you!

---

## Viewing File Contents

There are several commands for viewing files, each suited to different situations. Let's learn when to use each one.

### Setup: Create Practice Files

First, let's create some files with content to practice on:

```bash
cd ~/bioinfo-course
mkdir -p module02
cd module02

# Create a small file
echo "This is line 1" > small.txt
echo "This is line 2" >> small.txt
echo "This is line 3" >> small.txt

# Create a medium-sized file with DNA sequence
cat > sequence.fasta << 'EOF'
>Sequence1 Hypothetical gene
ATGCGATCGATCGATCGATCGTAGCTAGCTAGCTACGATCGATCGATCG
CGATCGATCGATCGATCGATCGTAGCTAGCTAGCTACGATCGATCGATCG
ATCGATCGATCGATCGATCGTAGCTAGCTAGCTACGATCGATCGATCGAT
>Sequence2 Another gene
GCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAG
CTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGC
TAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCT
EOF

# Create a file with many lines
seq 1 100 > numbers.txt
```

**What did we just do?**

- `echo "text"` prints text
- `>` redirects output to a file (overwrites if it exists)
- `>>` appends output to a file
- `cat > file << 'EOF'` lets us type multiple lines until we type `EOF`
- `seq 1 100` generates numbers from 1 to 100

### Command 1: `cat` - Concatenate and Print Files

`cat` displays the entire contents of a file (or multiple files) to the screen.

```bash
cat small.txt
```

**Output:**
```
This is line 1
This is line 2
This is line 3
```

**View multiple files:**
```bash
cat small.txt sequence.fasta
```

**What "cat" stands for:** "concatenate" — it was originally designed to combine (concatenate) multiple files.

**When to use `cat`:**
- ✓ For small files (10-50 lines)
- ✓ When you want to pipe the contents to another command
- ✗ NOT for large files—it dumps everything to your screen at once!

!!! warning "Don't Cat Large Files!"
    Running `cat` on a huge file (like a FASTQ file with millions of lines) will flood your terminal. Use `less`, `head`, or `tail` instead.

### Command 2: `less` - View Files Page by Page

`less` is a **pager**—it lets you view files one page at a time, scroll up and down, and search for text.

```bash
less numbers.txt
```

**Navigation inside `less`:**

| Key | Action |
|-----|--------|
| **Space** or **f** | Move forward one page |
| **b** | Move backward one page |
| **↓** or **Enter** | Move forward one line |
| **↑** | Move backward one line |
| **g** | Go to the beginning of the file |
| **G** | Go to the end of the file |
| **/word** | Search forward for "word" |
| **?word** | Search backward for "word" |
| **n** | Go to next search result |
| **N** | Go to previous search result |
| **q** | Quit and return to the terminal |

**When to use `less`:**
- ✓ For large files
- ✓ When you want to scroll and search
- ✓ When you want to inspect a file without editing it
- ✓ **For FASTQ, FASTA, and SAM files** (common in bioinformatics)

**Practice:** Open `numbers.txt` with `less` and try these actions:
1. Press Space to page down
2. Type `/50` and press Enter to search for "50"
3. Press `n` to find the next occurrence
4. Press `G` to jump to the end
5. Press `g` to jump back to the beginning
6. Press `q` to quit

!!! tip "Why is it called 'less'?"
    There's an older program called `more` that only lets you page forward. `less` lets you go forward AND backward, so the joke is: "less is more than more!" 😄

### Command 3: `head` - View the Beginning of a File

`head` shows the first 10 lines of a file by default.

```bash
head numbers.txt
```

**Output:**
```
1
2
3
4
5
6
7
8
9
10
```

**Show a specific number of lines:**
```bash
head -n 20 numbers.txt    # First 20 lines
head -20 numbers.txt      # Same thing (shorthand)
```

**When to use `head`:**
- ✓ Quick peek at file contents
- ✓ Check if a file has the expected format
- ✓ **Preview FASTQ/FASTA files** (look at first few records)
- ✓ Check log files

**Bioinformatics example:**
```bash
# Check the first few reads of a FASTQ file
head -n 4 sample001_R1.fastq
```

### Command 4: `tail` - View the End of a File

`tail` shows the last 10 lines of a file by default.

```bash
tail numbers.txt
```

**Output:**
```
91
92
93
94
95
96
97
98
99
100
```

**Show a specific number of lines:**
```bash
tail -n 20 numbers.txt    # Last 20 lines
tail -20 numbers.txt      # Same thing (shorthand)
```

**Follow a file in real-time (useful for logs!):**
```bash
tail -f logfile.txt
```

The `-f` flag ("follow") keeps `tail` running and shows new lines as they're added to the file. Press **Ctrl+C** to stop.

**When to use `tail`:**
- ✓ Check the end of a file
- ✓ **Monitor log files** as a program runs (`tail -f`)
- ✓ See if a large file completed properly
- ✓ Check the last entries in a data file

**Bioinformatics example:**
```bash
# Monitor a long-running alignment log
tail -f alignment.log
```

### Command 5: `wc` - Word Count (and Line Count!)

`wc` counts lines, words, and characters in a file.

```bash
wc numbers.txt
```

**Output:**
```
100  100  290 numbers.txt
```

This means:
- **100** lines
- **100** words
- **290** characters (bytes)

**Count only lines:**
```bash
wc -l numbers.txt
```

**Output:**
```
100 numbers.txt
```

**Count only words:**
```bash
wc -w numbers.txt
```

**Count only characters:**
```bash
wc -c numbers.txt
```

**When to use `wc`:**
- ✓ **Count reads in a FASTQ file** (divide line count by 4)
- ✓ Count samples in a manifest file
- ✓ Verify file sizes
- ✓ Check if files are empty

**Bioinformatics example:**
```bash
# Count reads in a FASTQ file (each read = 4 lines)
wc -l sample001_R1.fastq
# Divide the output by 4 to get number of reads
```

!!! tip "Quick FASTQ Read Count"
    FASTQ files have 4 lines per read, so to count reads:
    ```bash
    echo $(( $(wc -l < file.fastq) / 4 ))
    ```

---

## Understanding File Types

In Unix, file extensions (like `.txt` or `.fasta`) are just **conventions**—they don't actually determine what a file is. The system looks at the file's contents, not its name.

### The `file` Command

The `file` command examines a file and tells you what type it is:

```bash
file filename
```

**Examples:**

```bash
cd ~/bioinfo-course/module02
file small.txt
file sequence.fasta
file numbers.txt
```

**Typical output:**
```
small.txt: ASCII text
sequence.fasta: ASCII text
numbers.txt: ASCII text
```

Let's try with different file types:

```bash
# Create a compressed file
gzip -c numbers.txt > numbers.txt.gz
file numbers.txt.gz
```

**Output:**
```
numbers.txt.gz: gzip compressed data
```

```bash
# Check a binary
file /bin/ls
```

**Output:**
```
/bin/ls: ELF 64-bit LSB executable, x86-64 ...
```

**Why this matters in bioinformatics:**

Sometimes files are mislabeled or don't have extensions. The `file` command helps you identify:
- Plain text vs compressed
- Binary files vs text files
- File format verification

**Example:**
```bash
# Is this file compressed or not?
file mystery_data
```

---

## Practical Skills for Bioinformatics Files

### Previewing FASTA Files

FASTA format is used for nucleotide or protein sequences:
```
>SequenceID Description
ATGCGATCGATCGAT...
CGATCGATCGATCGA...
>AnotherSequence Description
GCTAGCTAGCTAG...
```

**View the first sequence:**
```bash
head -n 10 sequence.fasta
```

**Count how many sequences are in the file:**
```bash
grep -c "^>" sequence.fasta
```

The `^>` pattern matches lines starting with `>` (sequence headers in FASTA format).

### Previewing FASTQ Files

FASTQ format is used for raw sequencing reads (4 lines per read):
```
@ReadID
ATGCGATCGATCGAT
+
IIIIIIIIIIIIIII
```

**View the first read:**
```bash
head -n 4 file.fastq
```

**Count total reads:**
```bash
echo $(( $(wc -l < file.fastq) / 4 ))
```

### Checking File Integrity

Before analyzing a large file, always check:

**1. Is it empty?**
```bash
ls -lh file.fastq
# or
wc -l file.fastq
```

**2. Does it have the expected format?**
```bash
head file.fastq
```

**3. Did it finish writing (not truncated)?**
```bash
tail file.fastq
```

**4. Is it compressed?**
```bash
file file.fastq.gz
```

---

## Practice Exercise: Exploring a Sample Dataset

Let's create a mock sequencing dataset and explore it:

```bash
cd ~/bioinfo-course/module02

# Create a fake FASTQ file (simplified format)
cat > sample001.fastq << 'EOF'
@Read1
ATGCGATCGATCGATCGTAGCTAGCTAGCTACGATCG
+
IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIII
@Read2
GCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAG
+
IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIII
@Read3
CGATCGATCGATCGATCGATCGATCGATCGATCGAT
+
IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIII
@Read4
TAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGC
+
IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIII
@Read5
ATCGATCGATCGATCGATCGATCGATCGATCGATCG
+
IIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIII
EOF

# Create a metadata file
cat > sample_info.txt << 'EOF'
SampleID: sample001
Date: 2024-01-15
Platform: Illumina
Reads: 5
Quality: Pass
EOF
```

### Now answer these questions:

**Question 1:** How many reads are in `sample001.fastq`?

<details>
<summary>Solution</summary>

```bash
wc -l sample001.fastq
# Output: 20 lines
# 20 / 4 = 5 reads

# Or more elegantly:
echo $(( $(wc -l < sample001.fastq) / 4 ))
```
</details>

**Question 2:** What are the IDs of the first and last reads?

<details>
<summary>Solution</summary>

```bash
# First read ID:
head -n 1 sample001.fastq

# Last read ID:
tail -n 4 sample001.fastq | head -n 1
```
</details>

**Question 3:** How many lines are in the metadata file?

<details>
<summary>Solution</summary>

```bash
wc -l sample_info.txt
# Output: 5 lines
```
</details>

**Question 4:** Display only the sequence lines (every 2nd line starting from line 2) of the FASTQ file.

<details>
<summary>Solution</summary>

```bash
# Using sed (we'll learn this later, but here's a preview):
sed -n '2~4p' sample001.fastq

# Or manually:
head -n 6 sample001.fastq | tail -n 4 | head -n 1    # Gets the 2nd line (Read1 seq)
```
</details>

---

## The DNA Sequence Analysis Challenge

Now for your exit ticket challenge! Create a larger DNA sequence file and analyze it.

### Setup

```bash
cd ~/bioinfo-course/module02

# Create a DNA sequence file
cat > dna_sequences.fasta << 'EOF'
>Gene1 Transcription factor
ATGAAACGCATTAGCACCACCATTACCACCACCATCACCATTACCACAGGTAACGGTGCGGGC
TGACGCGTACAGGAAACACAGAAAAAAGCCCGCACCTGACAGTGCGGGCTTTTTTTTTCGACC
GGCCGAACGTACAGGAAACACAGAAAAAAGCCCGCACCTGACAGTGCGGGCTTTTTTTTCGAC
>Gene2 DNA polymerase
GCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCT
AGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGC
TAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAGCTAG
>Gene3 Ribosomal protein
TTTTAAACCCGGGTTTAAACCCGGGTTTAAACCCGGGTTTAAACCCGGGTTTAAACCCGGGTTT
AAACCCGGGTTTAAACCCGGGTTTAAACCCGGGTTTAAACCCGGGTTTAAACCCGGGTTTAAAC
CCGGGTTTAAACCCGGGTTTAAACCCGGGTTTAAACCCGGGTTTAAACCCGGGTTTAAACCCGG
>Gene4 Helicase
ATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATC
GATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGAT
CGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGA
>Gene5 Kinase domain
AAAAGGGGCCCCTTTTAAAAGGGGCCCCTTTTAAAAGGGGCCCCTTTTAAAAGGGGCCCCTTTT
AAAAGGGGCCCCTTTTAAAAGGGGCCCCTTTTAAAAGGGGCCCCTTTTAAAAGGGGCCCCTTTT
AAAAGGGGCCCCTTTTAAAAGGGGCCCCTTTTAAAAGGGGCCCCTTTTAAAAGGGGCCCCTTTT
EOF
```

### Your Tasks

Answer these questions using the commands you've learned. **Record both the commands you use and the answers.**

**Task 1:** How many genes (sequences) are in `dna_sequences.fasta`?

**Task 2:** How many total lines are in the file?

**Task 3:** Display only the first gene's header and sequence (first 3 lines).

**Task 4:** Display only the last gene's information (last 4 lines).

**Task 5:** What is the description/name of the third gene? (Hint: you need to get line 7 somehow)

**Task 6:** Without manually counting, what's the length of the first sequence line in Gene1? (Hint: use `head` to get just that line, then pipe to `wc -c` to count characters)

---

## Best Practices for File Viewing

### The "Look Before You Loop" Principle

In bioinformatics, you'll often write scripts that process many files. **Always inspect files manually before writing automation!**

**Good workflow:**
1. `ls -lh` — Check file sizes
2. `file` — Verify file type
3. `head` — Preview the beginning
4. `tail` — Check the end
5. Write your script

**Why?** You'll catch:
- Corrupted files
- Unexpected formats
- Empty files
- Wrong file types

### Quick Inspection Checklist for Any Data File

```bash
# 1. File size and type
ls -lh myfile.txt
file myfile.txt

# 2. First few lines
head myfile.txt

# 3. Last few lines
tail myfile.txt

# 4. Line count
wc -l myfile.txt

# 5. If it's small enough, view it all
less myfile.txt
```

---

## Common Mistakes to Avoid

### Mistake 1: Using `cat` on Huge Files

```bash
cat huge_file.fastq    # DON'T! This will flood your screen
```

**Instead:**
```bash
less huge_file.fastq   # Much better!
head huge_file.fastq   # Or just peek at the start
```

### Mistake 2: Forgetting That FASTQ Has 4 Lines Per Read

```bash
# WRONG: This shows 10 lines = 2.5 reads (broken!)
head huge_file.fastq

# RIGHT: Show 12 lines = 3 complete reads
head -n 12 huge_file.fastq
```

### Mistake 3: Not Checking File Types

```bash
# Trying to read a compressed file as text
cat file.fastq.gz      # Outputs binary garbage!

# Check first:
file file.fastq.gz     # Shows it's gzip compressed
zcat file.fastq.gz | head   # Decompress and view
```

---

## Quick Reference

### Creating Files and Directories

| Command | What It Does |
|---------|-------------|
| `touch file.txt` | Create empty file or update timestamp |
| `mkdir dirname` | Create directory |
| `mkdir -p path/to/dir` | Create directory and parents if needed |

### Viewing File Contents

| Command | When to Use It | Navigation |
|---------|---------------|------------|
| `cat file` | Small files (< 50 lines) | N/A (prints all) |
| `less file` | Large files, need to scroll/search | Space/b, /search, q |
| `head file` | First 10 lines | `-n` to specify number |
| `tail file` | Last 10 lines | `-n` to specify, `-f` to follow |
| `wc file` | Count lines/words/characters | `-l`, `-w`, `-c` flags |

### File Information

| Command | What It Does |
|---------|-------------|
| `file filename` | Identify file type |
| `ls -lh file` | Show file size and details |
| `wc -l file` | Count lines |

---

## Exit Ticket

To complete this module, send me an email with:

**Subject:** Bioinfo M2 Exit Ticket – [Your Name]

**Content:**

1. Your answers to all 6 tasks from the DNA Sequence Analysis Challenge
2. For each task, include:
   - The command you used
   - The answer/output
3. One sentence explaining when you would use `head` vs `less` vs `cat`

---

## Summary

Congratulations! You now understand:

✓ How to create files with `touch` and directories with `mkdir`
✓ When to use `cat`, `less`, `head`, and `tail` for viewing files
✓ How to navigate inside `less` (paging, searching, quitting)
✓ How to count lines, words, and characters with `wc`
✓ How to identify file types with `file`
✓ Best practices for previewing FASTA and FASTQ files
✓ The "look before you loop" principle
✓ Common mistakes and how to avoid them

In the next module, we'll learn how to manipulate files—copying, moving, renaming, and (carefully!) deleting them.

**Next:** [Module 3 — Manipulating Files](03-manipulating-files.md)
