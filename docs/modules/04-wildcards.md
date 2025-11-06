# Module 4 — Wildcards & Pattern Matching

**Time:** 60–75 min
**Goal:** Master wildcards to efficiently work with multiple files at once—a critical skill for processing genomic datasets.
**Exit ticket (email me):** Your solutions to the batch file processing challenge with all commands used.

---

## What You'll Learn

By the end of this module, you will understand:

- What wildcards (glob patterns) are and why they're essential
- The `*` wildcard for matching any characters
- The `?` wildcard for matching single characters
- Character classes `[...]` for matching specific character sets
- Brace expansion `{...}` for generating patterns
- How to combine wildcards for complex patterns
- Safety considerations when using wildcards with destructive commands
- Real bioinformatics use cases

---

## What Are Wildcards?

**Wildcards** (also called **glob patterns** or **globbing**) are special characters that let you match multiple files based on patterns in their names.

Instead of typing:
```bash
ls sample001.fastq sample002.fastq sample003.fastq sample004.fastq
```

You can type:
```bash
ls sample*.fastq
```

The `*` wildcard matches "anything," so this matches ALL files starting with "sample" and ending with ".fastq".

### Why Wildcards Matter in Bioinformatics

In bioinformatics, you'll often have:
- **Hundreds or thousands of files** with systematic names
- **Paired-end reads**: `sample001_R1.fastq` and `sample001_R2.fastq`
- **Multiple samples**: `sample001.bam`, `sample002.bam`, etc.
- **Different file types**: `.fastq`, `.bam`, `.vcf`, `.bed`

Wildcards let you:
- Process all files matching a pattern with one command
- Select subsets of files (e.g., only R1 reads)
- Avoid tedious typing and errors

### Wildcards vs Regular Expressions

**Important distinction:**
- **Wildcards (globs)**: Used for **matching file names** (with `ls`, `cp`, `mv`, `rm`)
- **Regular expressions (regex)**: Used for **matching text inside files** (with `grep`, `sed`, `awk`)

We'll cover regular expressions in Module 7. This module focuses on wildcards for file names.

---

## The `*` Wildcard: Match Anything

The **asterisk** (`*`) matches **zero or more characters** of any kind.

### Basic Usage

**Match all files:**
```bash
ls *
```

**Match all `.txt` files:**
```bash
ls *.txt
```

**Match files starting with "data":**
```bash
ls data*
```

**Match files containing "sample":**
```bash
ls *sample*
```

### Setup: Practice Files

Let's create a realistic dataset:

```bash
cd ~/bioinfo-course
mkdir -p module04/fastq_data
cd module04/fastq_data

# Create sample FASTQ files (paired-end sequencing)
touch sample001_R1.fastq sample001_R2.fastq
touch sample002_R1.fastq sample002_R2.fastq
touch sample003_R1.fastq sample003_R2.fastq
touch control001_R1.fastq control001_R2.fastq
touch control002_R1.fastq control002_R2.fastq

# Create some BAM files (aligned reads)
touch sample001.bam sample002.bam sample003.bam
touch control001.bam control002.bam

# Create index files
touch sample001.bam.bai sample002.bam.bai sample003.bam.bai

# Create a README
touch README.txt
```

### Practice with `*`

Try these commands and observe the results:

**1. List all files:**
```bash
ls *
```

**2. List all FASTQ files:**
```bash
ls *.fastq
```

**3. List all BAM files (but not BAI index files):**
```bash
ls *.bam
```

Wait, this also shows `.bam.bai` files! We'll fix this later.

**4. List all R1 (forward) reads:**
```bash
ls *_R1.fastq
```

**5. List all R2 (reverse) reads:**
```bash
ls *_R2.fastq
```

**6. List all sample files (not controls):**
```bash
ls sample*
```

**7. List only sample FASTQ files:**
```bash
ls sample*.fastq
```

**8. List only control BAM files:**
```bash
ls control*.bam
```

### Using Wildcards with Commands

Wildcards work with ANY command, not just `ls`:

**Copy all FASTQ files to a backup directory:**
```bash
mkdir backup
cp *.fastq backup/
ls backup/
```

**Move all BAM files to a new directory:**
```bash
mkdir aligned
mv *.bam aligned/
```

Wait! This also moved the `.bam.bai` files. Let's fix that:

```bash
mv aligned/*.bam .     # Move them back
```

**Count lines in all R1 files:**
```bash
wc -l *_R1.fastq
```

**Delete all index files:**
```bash
rm *.bai
# Better with safety:
ls *.bai           # Check first!
rm -i *.bai        # Then delete with confirmation
```

### Multiple `*` in One Pattern

You can use multiple asterisks:

```bash
# Match any file with "sample" anywhere in the name
ls *sample*

# Match files like: sample001.whatever.txt
ls sample*.*txt
```

---

## The `?` Wildcard: Match a Single Character

The **question mark** (`?`) matches **exactly one character**.

### When to Use `?`

Use `?` when you know:
- The position of the character
- That there's exactly one character there
- But you don't know (or don't care) what it is

### Examples

**Create numbered files:**
```bash
cd ~/bioinfo-course/module04
mkdir numbered
cd numbered

touch file1.txt file2.txt file3.txt file4.txt file5.txt
touch file10.txt file11.txt file12.txt
touch fileA.txt fileB.txt fileZ.txt
```

**Match single-digit numbered files (file1.txt through file9.txt):**
```bash
ls file?.txt
```

Output:
```
file1.txt  file2.txt  file3.txt  file4.txt  file5.txt
fileA.txt  fileB.txt  fileZ.txt
```

Notice: It matches `file?.txt` where `?` is ANY single character (digit or letter), but NOT `file10.txt` (two characters).

**Match only files with digit 1-5:**

We can't do this with `?` alone—we need character classes (next section).

**Match exactly 3 characters between "file" and ".txt":**
```bash
touch file001.txt file002.txt file003.txt
ls file???.txt
```

This matches `file001.txt`, `file002.txt`, etc., but not `file1.txt`.

### Bioinformatics Example

```bash
# Match samples with single-digit IDs
ls sample00?.fastq

# This matches:
# sample001.fastq
# sample002.fastq
# ...
# sample009.fastq

# But NOT:
# sample010.fastq (three digits after sample00)
```

---

## Character Classes `[...]`: Match Specific Characters

**Character classes** let you specify a set of characters to match **exactly one of**.

### Basic Syntax

```bash
[abc]     # Matches a, b, or c
[0-9]     # Matches any digit 0-9
[a-z]     # Matches any lowercase letter
[A-Z]     # Matches any uppercase letter
[a-zA-Z]  # Matches any letter
[!abc]    # Matches anything EXCEPT a, b, or c (negation)
```

### Examples

**Setup:**
```bash
cd ~/bioinfo-course/module04
mkdir classes
cd classes

touch sample1.txt sample2.txt sample3.txt
touch sampleA.txt sampleB.txt sampleC.txt
touch sampleX.txt sampleY.txt sampleZ.txt
touch sample10.txt sample11.txt
```

**Match samples 1, 2, or 3:**
```bash
ls sample[123].txt
```

Output:
```
sample1.txt  sample2.txt  sample3.txt
```

**Match samples with digits:**
```bash
ls sample[0-9].txt
```

Output:
```
sample1.txt  sample2.txt  sample3.txt
```

This does NOT match `sample10.txt` because `[0-9]` matches only ONE digit.

**Match samples with lowercase letters:**
```bash
ls sample[a-z].txt
```

**Match samples with uppercase letters:**
```bash
ls sample[A-Z].txt
```

**Match samples with any letter:**
```bash
ls sample[a-zA-Z].txt
```

**Match specific letters:**
```bash
ls sample[ABC].txt
```

### Ranges

You can specify ranges:

```bash
[0-9]      # Digits 0 through 9
[a-z]      # Lowercase letters a through z
[A-Z]      # Uppercase letters A through Z
[a-f]      # Letters a through f
[0-5]      # Digits 0 through 5
```

**Example: Match samples 1-5:**
```bash
ls sample[1-5].txt
```

### Negation: `[!...]`

Use `!` at the start to match anything EXCEPT those characters:

```bash
[!abc]     # Anything except a, b, or c
[!0-9]     # Anything except digits
[!a-z]     # Anything except lowercase letters
```

**Example: Match samples that DON'T have digits:**
```bash
ls sample[!0-9].txt
```

This matches:
```
sampleA.txt  sampleB.txt  sampleC.txt
sampleX.txt  sampleY.txt  sampleZ.txt
```

### Combining Classes with Other Wildcards

**Match three-digit sample IDs:**
```bash
touch sample001.txt sample002.txt sample010.txt sample100.txt
ls sample[0-9][0-9][0-9].txt
```

**Match sample001 through sample099 (but not sample100+):**
```bash
ls sample0[0-9][0-9].txt
```

**Match R1 or R2 reads:**
```bash
cd ~/bioinfo-course/module04/fastq_data
ls *_R[12].fastq
```

This matches both `*_R1.fastq` AND `*_R2.fastq`!

---

## Brace Expansion `{...}`: Generate Patterns

**Brace expansion** generates multiple strings by expanding comma-separated patterns inside braces.

**Important:** Brace expansion happens BEFORE wildcards. The shell expands braces first, then applies wildcards.

### Basic Syntax

```bash
{a,b,c}           # Expands to: a b c
file{1,2,3}.txt   # Expands to: file1.txt file2.txt file3.txt
{a..z}            # Expands to: a b c d ... z
{1..10}           # Expands to: 1 2 3 4 5 6 7 8 9 10
{01..10}          # Expands to: 01 02 03 ... 10 (with leading zeros!)
```

### Examples

**Create multiple files at once:**
```bash
cd ~/bioinfo-course/module04
mkdir braces
cd braces

# Create files: data1.txt, data2.txt, data3.txt
touch data{1,2,3}.txt
ls
```

**Create a range of files:**
```bash
# Create files: sample01.txt through sample10.txt
touch sample{01..10}.txt
ls
```

**Create directories for multiple samples:**
```bash
mkdir sample{001..005}_analysis
ls
```

**Create paired-end file names:**
```bash
touch sample{001..003}_R{1,2}.fastq
ls
```

This expands to:
```
sample001_R1.fastq
sample001_R2.fastq
sample002_R1.fastq
sample002_R2.fastq
sample003_R1.fastq
sample003_R2.fastq
```

**Create a project structure:**
```bash
mkdir -p project/{data,scripts,results/{tables,figures}}
ls -R project/
```

### Using Braces with Commands

**Copy specific samples:**
```bash
cp sample{001,003,005}.txt analysis/
```

This expands to:
```bash
cp sample001.txt sample003.txt sample005.txt analysis/
```

**Move R1 and R2 files:**
```bash
mv sample001_R{1,2}.fastq raw_data/
```

### Combining Braces and Wildcards

```bash
# Match all R1 and R2 files for samples 1-3
ls sample00[1-3]_R{1,2}.fastq

# Equivalent to:
ls sample001_R1.fastq sample001_R2.fastq sample002_R1.fastq sample002_R2.fastq sample003_R1.fastq sample003_R2.fastq
```

---

## Combining Wildcards: Advanced Patterns

Now let's combine everything we've learned for powerful pattern matching.

### Real Bioinformatics Examples

**Setup realistic dataset:**
```bash
cd ~/bioinfo-course/module04
mkdir -p real_project/{fastq,bam,vcf}
cd real_project/fastq

# Paired-end FASTQ files for multiple samples
touch patient001_tumor_R1.fastq.gz patient001_tumor_R2.fastq.gz
touch patient001_normal_R1.fastq.gz patient001_normal_R2.fastq.gz
touch patient002_tumor_R1.fastq.gz patient002_tumor_R2.fastq.gz
touch patient002_normal_R1.fastq.gz patient002_normal_R2.fastq.gz
touch patient003_tumor_R1.fastq.gz patient003_tumor_R2.fastq.gz
touch patient003_normal_R1.fastq.gz patient003_normal_R2.fastq.gz
```

**Example 1: List only tumor samples**
```bash
ls *tumor*.fastq.gz
```

**Example 2: List only normal samples**
```bash
ls *normal*.fastq.gz
```

**Example 3: List only R1 reads**
```bash
ls *_R1.fastq.gz
```

**Example 4: List tumor R1 reads**
```bash
ls *tumor*R1.fastq.gz
```

**Example 5: List patient 1 and patient 3 (but not patient 2)**
```bash
ls patient00[13]*.fastq.gz
```

**Example 6: List all compressed FASTQ files**
```bash
ls *.fastq.gz
```

**Example 7: Count reads in all R1 files**
```bash
zcat *_R1.fastq.gz | wc -l
```

(We'll learn about `zcat` and pipes in later modules!)

### Pattern Matching Strategies

**Strategy 1: Start Specific, Get General**

Start with specific pattern, then broaden:
```bash
ls patient001_tumor_R1.fastq.gz           # Specific file
ls patient001_tumor_R?.fastq.gz           # Both R1 and R2
ls patient001_*_R?.fastq.gz               # All patient001 files
ls patient00*_*_R?.fastq.gz               # All patients
```

**Strategy 2: Test Before Acting**

ALWAYS use `ls` to test your pattern before using it with destructive commands:

```bash
# DON'T DO THIS:
rm *tumor*    # What if it matches more than you expect?

# DO THIS:
ls *tumor*    # Check what matches
# If it looks right:
rm -i *tumor* # Delete with confirmation
```

---

## Safety Considerations with Wildcards

### Danger 1: Accidental Matches

Wildcards can match more than you expect:

```bash
# You want to delete temp files:
rm temp*

# But if you have:
# temp1.txt
# temp2.txt
# temporary_important_data.txt  ← OOPS!

# All three get deleted!
```

**Solution:** Use `ls` to preview matches first.

### Danger 2: Hidden Files

`*` does NOT match hidden files (starting with `.`) by default:

```bash
ls *              # Does not show .hidden_file
ls .*             # Shows hidden files
ls * .*           # Shows both
```

**Be careful:**
```bash
rm -rf .*    # DANGEROUS! Might match .. (parent directory)!
```

**Safer:**
```bash
rm -rf .[!.]* # Matches hidden files but not . or ..
```

### Danger 3: Spaces in File Names

File names with spaces can cause problems:

```bash
# File name: "my data.txt"
rm my data.txt    # Tries to delete "my" and "data.txt" (two files!)

# Correct:
rm "my data.txt"  # Quote it!
rm my\ data.txt   # Or escape the space
```

**Best practice:** Avoid spaces in file names. Use underscores or dashes:
```bash
my_data.txt       # Good
my-data.txt       # Also good
my data.txt       # Avoid!
```

### Danger 4: Empty Matches

If a wildcard doesn't match anything, what happens?

```bash
ls *.xyz
```

If no `.xyz` files exist, you get:
```
ls: cannot access '*.xyz': No such file or directory
```

The literal string `*.xyz` is passed to `ls` when nothing matches.

**In scripts, check if files exist first (we'll learn this later!).**

---

## Practical Exercise: Batch File Organization

Time to put your skills to work!

### Setup

```bash
cd ~/bioinfo-course/module04
mkdir batch_challenge
cd batch_challenge

# Create a mess of files
touch exp1_rep1_ctrl.txt exp1_rep2_ctrl.txt exp1_rep3_ctrl.txt
touch exp1_rep1_treat.txt exp1_rep2_treat.txt exp1_rep3_treat.txt
touch exp2_rep1_ctrl.txt exp2_rep2_ctrl.txt exp2_rep3_ctrl.txt
touch exp2_rep1_treat.txt exp2_rep2_treat.txt exp2_rep3_treat.txt
touch exp3_rep1_ctrl.txt exp3_rep2_ctrl.txt exp3_rep3_ctrl.txt
touch exp3_rep1_treat.txt exp3_rep2_treat.txt exp3_rep3_treat.txt
touch README.txt analysis_notes.txt
```

### Challenges

For each challenge, **write the command** and verify it works with `ls` first!

**Challenge 1:** List all control files

<details>
<summary>Solution</summary>

```bash
ls *_ctrl.txt
```
</details>

**Challenge 2:** List all treatment files

<details>
<summary>Solution</summary>

```bash
ls *_treat.txt
```
</details>

**Challenge 3:** List all files from experiment 1

<details>
<summary>Solution</summary>

```bash
ls exp1*.txt
```
</details>

**Challenge 4:** List all replicate 1 files

<details>
<summary>Solution</summary>

```bash
ls *_rep1_*.txt
```
</details>

**Challenge 5:** List all files from experiments 1 and 3 (but not 2)

<details>
<summary>Solution</summary>

```bash
ls exp[13]*.txt
```
</details>

**Challenge 6:** List all experiment 2 treatment files

<details>
<summary>Solution</summary>

```bash
ls exp2*treat.txt
```
</details>

**Challenge 7:** Count how many control files exist

<details>
<summary>Solution</summary>

```bash
ls *_ctrl.txt | wc -l
```
</details>

**Challenge 8:** Create directories `exp1/`, `exp2/`, `exp3/` in one command

<details>
<summary>Solution</summary>

```bash
mkdir exp{1,2,3}
```
</details>

**Challenge 9:** Move all exp1 files into `exp1/` directory

<details>
<summary>Solution</summary>

```bash
mv exp1*.txt exp1/
```
</details>

**Challenge 10:** Move all exp2 and exp3 files into their respective directories

<details>
<summary>Solution</summary>

```bash
mv exp2*.txt exp2/
mv exp3*.txt exp3/
```
</details>

---

## The Exit Ticket Challenge: Real Sequencing Project

### Scenario

You've received sequencing data for a cancer study. You have:
- 5 patients (patient01 through patient05)
- Each patient has tumor and normal samples
- Paired-end sequencing (R1 and R2 files)
- Files are compressed (.fastq.gz)

### Setup

```bash
cd ~/bioinfo-course/module04
mkdir cancer_study
cd cancer_study

# Generate realistic file names
for i in {01..05}; do
  touch patient${i}_tumor_R1.fastq.gz
  touch patient${i}_tumor_R2.fastq.gz
  touch patient${i}_normal_R1.fastq.gz
  touch patient${i}_normal_R2.fastq.gz
done

# Add some analysis files
touch analysis_config.txt patient_manifest.csv README.md
```

### Your Tasks

Use wildcards to accomplish these tasks. Record each command.

**Task 1:** List ONLY the tumor samples (all files with "tumor" in the name)

**Task 2:** List ONLY the R1 files (forward reads)

**Task 3:** List files for patients 1, 3, and 5 only (hint: use character class)

**Task 4:** Count how many total FASTQ files exist

**Task 5:** Create directories: `tumor_samples/` and `normal_samples/`

**Task 6:** Copy (not move) all tumor FASTQ files to `tumor_samples/`

**Task 7:** Copy all normal FASTQ files to `normal_samples/`

**Task 8:** In the `tumor_samples/` directory, list only patient 2, 3, and 4 files

**Task 9:** Create a backup directory called `fastq_backup/` and copy all FASTQ files there

**Task 10:** List all files that are NOT FASTQ files (hint: negate the pattern or list specific extensions)

---

## Quick Reference

### Wildcards

| Pattern | Matches | Example | Matches Files |
|---------|---------|---------|---------------|
| `*` | Zero or more characters | `*.txt` | All .txt files |
| `?` | Exactly one character | `file?.txt` | file1.txt, fileA.txt |
| `[abc]` | One character: a, b, or c | `file[123].txt` | file1.txt, file2.txt, file3.txt |
| `[a-z]` | One character in range | `file[0-9].txt` | file0.txt ... file9.txt |
| `[!abc]` | One character NOT a, b, or c | `file[!0-9].txt` | fileA.txt, fileX.txt |

### Brace Expansion

| Pattern | Expands To | Use Case |
|---------|------------|----------|
| `{a,b,c}` | a b c | List specific items |
| `{1..5}` | 1 2 3 4 5 | Number range |
| `{01..05}` | 01 02 03 04 05 | With leading zeros |
| `{a..z}` | a b c ... z | Letter range |
| `file{1,2}.txt` | file1.txt file2.txt | Create/match specific files |

### Combining Patterns

| Pattern | What It Matches |
|---------|----------------|
| `*_R[12].fastq` | Any file ending with _R1.fastq or _R2.fastq |
| `sample00[1-5]*.bam` | sample001.bam, sample002.bam ... sample005.bam |
| `patient{01..10}_*.fastq.gz` | All FASTQ files for patients 01-10 |
| `*[!0-9].txt` | Files ending in .txt but NOT with a digit before .txt |

### Safety Tips

1. **Always test with `ls` first** before using wildcards with `rm`, `mv`, or `cp`
2. **Use `-i` flag** with destructive commands
3. **Quote file names** with spaces: `"my file.txt"`
4. **Avoid spaces** in file names when possible
5. **Check for unintended matches** — wildcards can be broader than you expect

---

## Exit Ticket

To complete this module, send me an email with:

**Subject:** Bioinfo M4 Exit Ticket – [Your Name]

**Content:**

1. Your commands for all 10 tasks in the cancer study challenge
2. The output of `ls -R` showing your final directory structure
3. A one-sentence explanation of the difference between `*` and `?`
4. One example from your work showing how you used `ls` to test a wildcard pattern before running a destructive command

---

## Summary

Congratulations! You now understand:

✓ What wildcards (glob patterns) are and why they're essential
✓ The `*` wildcard for matching zero or more characters
✓ The `?` wildcard for matching exactly one character
✓ Character classes `[...]` for matching specific characters or ranges
✓ Negation with `[!...]` to exclude characters
✓ Brace expansion `{...}` for generating multiple patterns
✓ How to combine wildcards for complex pattern matching
✓ Safety considerations: testing with `ls`, using `-i`, avoiding spaces
✓ Real bioinformatics use cases for batch file operations

**This skill is fundamental!** In bioinformatics, you'll constantly use wildcards to:
- Process multiple samples at once
- Select subsets of files (e.g., only tumor samples)
- Organize large datasets
- Write efficient analysis pipelines

In the next module, we'll learn about pipes and filters—how to chain commands together to transform and analyze data.

**Next:** [Module 5 — Pipes, Redirects & Filters](05-pipes-filters.md)
