# Module 8 — Text Editing & File Compression

**Time:** 75–90 min
**Goal:** Master basic text editing with nano, work efficiently with compressed files, and verify file integrity with checksums.
**Exit ticket (email me):** Your completed configuration file, checksum verification output, and analysis script.

---

## What You'll Learn

By the end of this module, you will understand:

- How to edit text files with nano
- Essential nano keyboard shortcuts
- How to safely edit configuration and script files
- Why bioinformatics files are compressed
- How to compress and decompress files with gzip
- How to work with compressed files without decompressing them
- How to verify file integrity with checksums (md5sum, sha256sum)
- Best practices for handling large genomic datasets

---

## Part 1: Text Editing with Nano

### Why Learn a Terminal Text Editor?

In bioinformatics, you'll frequently need to:
- Edit configuration files on remote servers
- Fix script errors
- Create analysis parameter files
- Add comments to data files

You need a text editor that works in the terminal (no GUI required).

**Popular terminal editors:**
- **nano**: Beginner-friendly, simple
- **vim/vi**: Powerful but steep learning curve
- **emacs**: Very powerful but complex

**We'll learn nano** because it's:
- Pre-installed on most systems
- Easy to use immediately
- Has helpful hints displayed at the bottom
- Perfect for quick edits

---

### Starting Nano

```bash
nano filename.txt
```

If the file doesn't exist, nano creates it. If it exists, nano opens it for editing.

**Practice:**
```bash
cd ~/bioinfo-course
mkdir -p module08
cd module08

nano my_notes.txt
```

You'll see the nano editor interface:

```
  GNU nano 5.4                my_notes.txt

[Blank editing area]


















^G Get Help  ^O Write Out  ^W Where Is   ^K Cut Text   ^J Justify
^X Exit      ^R Read File  ^\ Replace    ^U Paste Text ^T To Spell
```

The `^` symbol means **Ctrl**. So `^X` means **Ctrl+X**.

---

### Basic Nano Operations

### Typing Text

Just start typing! Nano works like a simple text editor.

```
This is my first note.
I can type multiple lines.
It's very straightforward!
```

### Moving Around

| Key | Action |
|-----|--------|
| **Arrow keys** | Move cursor |
| **Ctrl+A** | Jump to beginning of line |
| **Ctrl+E** | Jump to end of line |
| **Ctrl+Y** | Page up |
| **Ctrl+V** | Page down |
| **Ctrl+_** (underscore) | Go to line number |

### Saving (Writing Out)

1. Press **Ctrl+O** (Write Out)
2. Nano asks: `File Name to Write: my_notes.txt`
3. Press **Enter** to confirm

You'll see: `[ Wrote 3 lines ]`

### Exiting

Press **Ctrl+X**

If you have unsaved changes, nano asks:
```
Save modified buffer?
 Y Yes
 N No           ^C Cancel
```

- **Y**: Save and exit
- **N**: Exit without saving
- **Ctrl+C**: Cancel and return to editing

### Practice: Your First Edit

```bash
nano practice.txt
```

1. Type several lines of text
2. Press **Ctrl+O** to save
3. Press **Enter** to confirm filename
4. Press **Ctrl+X** to exit

Verify your file:
```bash
cat practice.txt
```

---

### Essential Nano Shortcuts

### Cutting, Copying, and Pasting

| Shortcut | Action | How It Works |
|----------|--------|--------------|
| **Ctrl+K** | Cut line | Cuts current line to clipboard |
| **Alt+6** | Copy line | Copies current line without deleting |
| **Ctrl+U** | Paste | Pastes clipboard content |

**Practice:**
```bash
nano cut_paste_practice.txt
```

Type:
```
Line 1
Line 2
Line 3
Line 4
```

1. Move cursor to Line 2
2. Press **Ctrl+K** (cuts the line)
3. Move cursor to end of file
4. Press **Ctrl+U** (pastes it)

Result:
```
Line 1
Line 3
Line 4
Line 2
```

**To cut multiple lines:**
1. Press **Ctrl+K** multiple times
2. All cut lines are in the clipboard
3. Press **Ctrl+U** to paste them all

### Searching

| Shortcut | Action |
|----------|--------|
| **Ctrl+W** | Search ("Where Is") |
| **Alt+W** | Find next occurrence |
| **Ctrl+\\** | Search and replace |

**Practice:**
```bash
nano search_practice.txt
```

Type:
```
The quick brown fox
jumps over the lazy dog.
The dog is sleeping.
The fox is running.
```

Save it (**Ctrl+O**, **Enter**).

Now:
1. Press **Ctrl+W**
2. Type `dog`
3. Press **Enter**

Cursor jumps to the first "dog".

Press **Alt+W** to find the next occurrence.

### Search and Replace

1. Press **Ctrl+\\** (backslash)
2. `Search:`  Type what to find (e.g., `dog`)
3. Press **Enter**
4. `Replace with:` Type replacement (e.g., `cat`)
5. Press **Enter**
6. Nano asks: `Replace this instance?`
   - **Y**: Replace this one
   - **N**: Skip this one
   - **A**: Replace all
   - **Ctrl+C**: Cancel

**Practice:** Replace all "dog" with "cat" in your file.

### Undoing and Redoing

| Shortcut | Action |
|----------|--------|
| **Alt+U** | Undo |
| **Alt+E** | Redo |

**Note:** On some systems, these might not work. Nano's undo is limited compared to GUI editors, so save frequently!

### Getting Help

Press **Ctrl+G** to see the full help system. Press **Ctrl+X** to exit help.

---

### Complete Nano Keyboard Shortcut Table

| Shortcut | Action |
|----------|--------|
| **Ctrl+O** | Write Out (save) |
| **Ctrl+X** | Exit |
| **Ctrl+G** | Get Help |
| **Ctrl+K** | Cut line |
| **Ctrl+U** | Paste (Uncut) |
| **Alt+6** | Copy line |
| **Ctrl+W** | Search (Where Is) |
| **Alt+W** | Find next |
| **Ctrl+\\** | Search and replace |
| **Ctrl+A** | Beginning of line |
| **Ctrl+E** | End of line |
| **Ctrl+Y** | Page up |
| **Ctrl+V** | Page down |
| **Ctrl+_** | Go to line number |
| **Alt+U** | Undo |
| **Alt+E** | Redo |
| **Ctrl+C** | Show cursor position / Cancel |
| **Ctrl+R** | Read (insert) another file |

---

### Real Bioinformatics Use Cases

### Use Case 1: Editing a Configuration File

```bash
nano analysis_config.txt
```

Type:
```
# Analysis Configuration
reference_genome=/data/refs/hg38.fa
input_fastq=/data/samples/patient001_R1.fastq.gz
output_dir=/results/patient001
threads=8
min_quality=20
```

Save and exit.

### Use Case 2: Creating a Sample Manifest

```bash
nano samples.tsv
```

Type:
```
SampleID	Condition	Batch	Replicate
S001	control	Batch1	Rep1
S002	control	Batch1	Rep2
S003	treated	Batch1	Rep1
S004	treated	Batch1	Rep2
```

(Use **Tab** key between columns for proper TSV format)

### Use Case 3: Quick Script Fix

You have a script with an error:
```bash
nano analysis.sh
```

Original:
```bash
#!/bin/bash
fastqc input.fastq -o results/
# Oops, typo below:
multiqc resutls/ -o report/
```

Fix:
1. Press **Ctrl+W**, search for `resutls`
2. Press **Ctrl+\\**, replace `resutls` with `results`
3. Save and exit

---

## Part 2: File Compression

### Why Compress Files in Bioinformatics?

Genomic data files are **huge**:
- A single human genome FASTQ: ~200 GB uncompressed
- Compressed with gzip: ~60 GB (70% reduction!)
- A single RNA-seq sample: 5-20 GB compressed

**Compression saves:**
- Disk space
- Transfer time
- Storage costs

**Common compressed formats:**
- `.gz` — gzip compression (most common)
- `.bz2` — bzip2 compression (better compression, slower)
- `.zip` — ZIP archive (common on Windows)
- `.tar.gz` or `.tgz` — Compressed archive (multiple files)

In bioinformatics, **gzip (`.gz`)** is the standard.

---

### Compressing Files with gzip

**`gzip`** compresses files and adds a `.gz` extension.

```bash
gzip filename
```

**Important:** This **deletes** the original file!

**Practice:**
```bash
cd ~/bioinfo-course/module08

# Create a test file
echo "This is a test file with some repeated text text text text" > test.txt

# Check size
ls -lh test.txt

# Compress it
gzip test.txt

# Check size and name
ls -lh test.txt.gz
```

The original `test.txt` is gone, replaced by `test.txt.gz`.

### Keep the Original: gzip -c

To keep the original, use `-c` (write to stdout) and redirect:

```bash
gzip -c test.txt > test.txt.gz
```

Now both `test.txt` and `test.txt.gz` exist.

### Decompress with gunzip

```bash
gunzip test.txt.gz
```

This deletes `test.txt.gz` and creates `test.txt`.

Or keep the compressed file:
```bash
gunzip -c test.txt.gz > test.txt
```

### Useful gzip Options

| Option | What It Does | Example |
|--------|-------------|---------|
| `-c` | Write to stdout (keep original) | `gzip -c file > file.gz` |
| `-d` | Decompress (same as gunzip) | `gzip -d file.gz` |
| `-k` | Keep original file | `gzip -k file` |
| `-1` to `-9` | Compression level (1=fast/big, 9=slow/small) | `gzip -9 file` |
| `-l` | List compressed file info | `gzip -l file.gz` |
| `-r` | Recursively compress directory | `gzip -r directory/` |
| `-t` | Test integrity | `gzip -t file.gz` |

### Check Compression Ratio

```bash
gzip -l test.txt.gz
```

Output:
```
         compressed        uncompressed  ratio uncompressed_name
                 42                  61  50.8% test.txt
```

---

### Working with Compressed Files WITHOUT Decompressing

This is **crucial** for bioinformatics! You don't want to decompress a 60 GB FASTQ file just to look at it!

### View with zcat (Compressed cat)

```bash
zcat file.gz
```

**Example:**
```bash
cd ~/bioinfo-course/module08

# Create and compress a test file
cat > sequences.txt << 'EOF'
>Seq1
ATGCGCTAG
>Seq2
GCTAGCTAC
>Seq3
TACGATCGA
EOF

gzip sequences.txt

# View without decompressing
zcat sequences.txt.gz
```

Output:
```
>Seq1
ATGCGCTAG
>Seq2
GCTAGCTAC
>Seq3
TACGATCGA
```

The file stays compressed!

### View with zless (Compressed less)

```bash
zless file.gz
```

Same navigation as `less` (Space, b, q, /search, etc.).

**Perfect for previewing large compressed FASTQ files:**
```bash
zless sample001_R1.fastq.gz
```

### Search with zgrep (Compressed grep)

```bash
zgrep 'pattern' file.gz
```

**Example:**
```bash
# Count sequences in a compressed FASTA
zgrep -c '^>' sequences.txt.gz
```

Output: `3`

### Process with Pipes

```bash
# Count lines in compressed file
zcat file.gz | wc -l

# Extract sequences from compressed FASTA
zcat sequences.txt.gz | grep -v '^>'

# Get first 1000 reads from compressed FASTQ
zcat sample_R1.fastq.gz | head -n 4000 | less
```

### OS-Specific Note for macOS

=== "Linux/WSL"

    Use: `zcat`, `zless`, `zgrep`

=== "macOS"

    Use: `gzcat`, `zless`, `zgrep`

    Or create aliases:
    ```bash
    alias zcat='gzcat'
    ```

---

### Practical Example: FASTQ File Inspection

```bash
cd ~/bioinfo-course/module08

# Create a mock FASTQ file
cat > sample.fastq << 'EOF'
@Read1
ATGCGCTAG
+
IIIIIIIII
@Read2
GCTAGCTAC
+
IIIIIIIII
@Read3
TACGATCGA
+
IIIIIIIII
EOF

# Compress it
gzip sample.fastq

# Preview first read (4 lines)
zcat sample.fastq.gz | head -n 4

# Count total reads (number of lines / 4)
echo "$(( $(zcat sample.fastq.gz | wc -l) / 4 )) reads"

# Count reads with quality score 'I' (high quality)
zgrep -c 'IIIIIIIII' sample.fastq.gz

# Extract just the sequences (every 2nd line starting from line 2)
zcat sample.fastq.gz | awk 'NR % 4 == 2'
```

---

## Part 3: Checksums — Verify File Integrity

### What is a Checksum?

A **checksum** is a unique "fingerprint" of a file's contents. Even changing a single byte produces a completely different checksum.

**Uses:**
- Verify file wasn't corrupted during download/transfer
- Confirm file hasn't been modified
- Compare files quickly without reading entire contents

**Common algorithms:**
- **MD5**: 128-bit hash (fast, but cryptographically weak)
- **SHA-256**: 256-bit hash (secure, standard for verification)

---

### Generating Checksums

### MD5 Checksum

=== "Linux/WSL"

    ```bash
    md5sum filename
    ```

=== "macOS"

    ```bash
    md5 filename
    ```

**Example:**
```bash
cd ~/bioinfo-course/module08

echo "Important data" > data.txt

# Generate MD5 checksum (Linux/WSL)
md5sum data.txt
```

Output:
```
3c5d4c68d9fc9a1b8e8c8a5c3f7e5f9a  data.txt
```

The first part is the checksum (32 hexadecimal characters).

### SHA-256 Checksum (Recommended)

=== "Linux/WSL"

    ```bash
    sha256sum filename
    ```

=== "macOS"

    ```bash
    shasum -a 256 filename
    ```

**Example:**
```bash
sha256sum data.txt
```

Output:
```
a7e2c4f5d8b9e1c3a6f4d2b8e5c9f1a3d7b2e8c4f6a1d9b5c2e8f4a7d3b6e1c5  data.txt
```

(64 hex characters)

---

### Saving and Verifying Checksums

### Save Checksum to File

```bash
sha256sum data.txt > data.txt.sha256
cat data.txt.sha256
```

Output:
```
a7e2c4f5d8b9e1c3a6f4d2b8e5c9f1a3d7b2e8c4f6a1d9b5c2e8f4a7d3b6e1c5  data.txt
```

### Verify Checksum Later

=== "Linux/WSL"

    ```bash
    sha256sum -c data.txt.sha256
    ```

=== "macOS"

    ```bash
    shasum -a 256 -c data.txt.sha256
    ```

Output:
```
data.txt: OK
```

This confirms the file hasn't changed.

### Test: Modify the File

```bash
# Change the file slightly
echo "Modified data" > data.txt

# Verify again
sha256sum -c data.txt.sha256
```

Output:
```
data.txt: FAILED
sha256sum: WARNING: 1 computed checksum did NOT match
```

The checksum detected the change!

---

### Real Bioinformatics Use Case: Download Verification

When downloading genomic data, you should **always** verify checksums.

**Example workflow:**

```bash
# Download FASTQ file (simulated)
curl -O ftp://data.server.edu/sample001_R1.fastq.gz

# Download checksum file
curl -O ftp://data.server.edu/sample001_R1.fastq.gz.md5

# Verify
md5sum -c sample001_R1.fastq.gz.md5
```

If it says "OK", your download is complete and uncorrupted!

### Generate Checksums for Multiple Files

```bash
# Generate checksums for all FASTQ files
sha256sum *.fastq.gz > all_samples.sha256

# Verify all at once later
sha256sum -c all_samples.sha256
```

Output:
```
sample001_R1.fastq.gz: OK
sample001_R2.fastq.gz: OK
sample002_R1.fastq.gz: OK
sample002_R2.fastq.gz: OK
```

---

## Practice Exercises

### Exercise 1: Create and Edit a Script

```bash
cd ~/bioinfo-course/module08
nano qc_script.sh
```

Type:
```bash
#!/bin/bash
# Quality control script for RNA-seq samples

SAMPLE_ID="sample001"
INPUT_DIR="/data/raw_fastq"
OUTPUT_DIR="/results/qc"

echo "Starting QC for $SAMPLE_ID"
mkdir -p $OUTPUT_DIR

# Placeholder for FastQC
echo "Running FastQC..."
# fastqc ${INPUT_DIR}/${SAMPLE_ID}_R1.fastq.gz -o $OUTPUT_DIR

echo "QC complete!"
```

Save (**Ctrl+O**, **Enter**), then exit (**Ctrl+X**).

Verify:
```bash
cat qc_script.sh
```

### Exercise 2: Compress and Verify

```bash
# Create a data file
cat > experiment_results.txt << 'EOF'
Sample,ReadCount,MappingRate
S001,25000000,95.2
S002,26000000,94.8
S003,24500000,93.1
EOF

# Generate checksum BEFORE compression
sha256sum experiment_results.txt > experiment_results.txt.sha256

# Compress
gzip -k experiment_results.txt

# Verify original (should work)
sha256sum -c experiment_results.txt.sha256

# Try to view compressed without decompressing
zcat experiment_results.txt.gz | head
```

### Exercise 3: FASTQ Simulation

```bash
# Create a larger mock FASTQ
cat > reads.fastq << 'EOF'
@Read1
ATGCGCTAGCTAGCTAGCTAGCTAGCT
+
IIIIIIIIIIIIIIIIIIIIIIIIIII
@Read2
GCTAGCTACGATCGATCGATCGATCG
+
IIIIIIIIIIIIIIIIIIIIIIIIIII
@Read3
TACGATCGAGCTAGCTAGCTAGCTAG
+
IIIIIIIIIIIIIIIIIIIIIIIIIII
@Read4
ATCGATCGATCGATCGATCGATCGAT
+
IIIIIIIIIIIIIIIIIIIIIIIIIII
@Read5
GCTAGCTAGCTAGCTAGCTAGCTAGC
+
IIIIIIIIIIIIIIIIIIIIIIIIIII
EOF

# Compress
gzip reads.fastq

# Count reads
echo "$(( $(zcat reads.fastq.gz | wc -l) / 4 )) reads total"

# Extract just sequence IDs
zcat reads.fastq.gz | grep '^@' | cut -c 2-

# Extract just sequences
zcat reads.fastq.gz | awk 'NR % 4 == 2'

# Find reads containing "GCTAGC" motif
zgrep 'GCTAGC' reads.fastq.gz
```

---

## The Exit Ticket Challenge: Complete Workflow

### Scenario

You're setting up a bioinformatics analysis. Create configuration files, compress data, and verify integrity.

### Tasks

**Task 1:** Create an analysis configuration file

```bash
nano analysis_config.txt
```

Include:
- Reference genome path
- Input FASTQ paths (sample001_R1.fastq.gz, sample001_R2.fastq.gz)
- Output directory
- Number of threads (8)
- Minimum quality score (20)

**Task 2:** Create a sample manifest in TSV format

```bash
nano sample_manifest.tsv
```

Include 5 samples with columns:
- SampleID
- Condition (control or treated)
- Batch
- ReadCount
- QC_Status (pass/fail)

**Task 3:** Create a mock FASTQ file with 10 reads

```bash
nano reads.fastq
```

Use proper FASTQ format (4 lines per read).

**Task 4:** Compress the FASTQ file, keeping the original

**Task 5:** Generate SHA-256 checksums for:
- The uncompressed FASTQ
- The compressed FASTQ
- The manifest file

Save all checksums to `checksums.sha256`

**Task 6:** Verify all checksums

**Task 7:** Without decompressing, count the number of reads in the compressed FASTQ

**Task 8:** Create a simple analysis script (`run_analysis.sh`) that:
- Prints "Analysis started"
- Prints the sample ID and paths from your config file (just echo statements)
- Prints "Analysis complete"

Make the script executable and test it:
```bash
chmod +x run_analysis.sh
./run_analysis.sh
```

---

## Quick Reference

### Nano Shortcuts

| Shortcut | Action |
|----------|--------|
| **Ctrl+O** | Save (Write Out) |
| **Ctrl+X** | Exit |
| **Ctrl+K** | Cut line |
| **Ctrl+U** | Paste (Uncut) |
| **Ctrl+W** | Search |
| **Ctrl+\\** | Replace |
| **Ctrl+A** | Start of line |
| **Ctrl+E** | End of line |
| **Alt+U** | Undo |
| **Ctrl+G** | Help |

### Compression

| Command | What It Does |
|---------|-------------|
| `gzip file` | Compress (deletes original) |
| `gzip -k file` | Compress (keeps original) |
| `gzip -c file > file.gz` | Compress to stdout |
| `gunzip file.gz` | Decompress (deletes .gz) |
| `zcat file.gz` | View compressed file |
| `zless file.gz` | Page through compressed file |
| `zgrep pattern file.gz` | Search compressed file |
| `gzip -l file.gz` | Show compression info |

### Checksums

| Command | What It Does |
|---------|-------------|
| `md5sum file` | Generate MD5 checksum (Linux) |
| `sha256sum file` | Generate SHA-256 checksum (Linux) |
| `shasum -a 256 file` | Generate SHA-256 checksum (macOS) |
| `sha256sum -c file.sha256` | Verify checksum (Linux) |
| `shasum -a 256 -c file.sha256` | Verify checksum (macOS) |

---

## Exit Ticket

To complete this module, send me an email with:

**Subject:** Bioinfo M8 Exit Ticket – [Your Name]

**Content:**

1. Contents of your `analysis_config.txt`
2. Contents of your `sample_manifest.tsv`
3. Output of checksum verification (`sha256sum -c checksums.sha256`)
4. The command you used to count reads in the compressed FASTQ
5. Contents of your `run_analysis.sh` script
6. One paragraph (3-4 sentences) explaining why compression and checksums are important in bioinformatics

---

## Summary

Congratulations! You now understand:

✓ How to edit text files with nano
✓ Essential nano keyboard shortcuts for efficient editing
✓ How to safely edit configuration files and scripts
✓ Why compression is critical for genomic data
✓ How to compress and decompress files with gzip
✓ How to work with compressed files without decompressing (zcat, zless, zgrep)
✓ How to verify file integrity with checksums (md5sum, sha256sum)
✓ Best practices for handling large genomic datasets

**These skills are foundational!** You'll use them constantly when:
- Editing configuration files for bioinformatics tools
- Working with compressed FASTQ/BAM files
- Verifying downloaded datasets
- Creating and modifying analysis scripts
- Managing disk space efficiently

In the next module, we'll dive into advanced text processing with awk—a powerful tool for manipulating tabular data.

**Next:** [Module 9 — Advanced Text Processing with awk](09-awk.md)
