# Module 6 — Regular Expressions for Bioinformatics

**Time:** 90–120 min
**Goal:** Master regular expressions (regex) for powerful pattern matching in genomic data, sample manifests, and sequence motifs.
**Exit ticket (email me):** Your solutions to the variant annotation challenge with all regex patterns used.

---

## What You'll Learn

By the end of this module, you will understand:

- What regular expressions are and how they differ from wildcards
- Literal characters and metacharacters
- Anchors (^, $) for matching positions
- Character classes ([...]) and predefined classes (\d, \w, \s)
- Quantifiers (*, +, ?, {n,m}) for repetition
- Groups and capturing with (...)
- Alternation with |
- Practical bioinformatics applications: validating IDs, finding motifs, parsing file formats

---

## Regular Expressions vs Wildcards

In Module 4, we learned **wildcards (globs)** for matching file names. Now we'll learn **regular expressions** for matching text **inside** files.

### Key Differences

| Feature | Wildcards (Globs) | Regular Expressions (Regex) |
|---------|-------------------|----------------------------|
| **Used for** | Matching **file names** | Matching **text content** |
| **Commands** | `ls`, `cp`, `mv`, `rm` | `grep`, `sed`, `awk`, `perl` |
| **`*` means** | Zero or more characters | Repeat previous character |
| **`.` means** | Literal dot | Any single character |
| **Complexity** | Simple patterns | Very powerful patterns |

**Example contrast:**

```bash
# Wildcard (file names):
ls sample*.fastq        # Match files: sample001.fastq, sample_ABC.fastq

# Regex (text content):
grep 'sample.*fastq' file.txt   # Match text containing "sample" then anything then "fastq"
```

### When to Use Each

- **Wildcards**: When working with files and directories
- **Regex**: When searching or processing text content

---

## Your First Regular Expression

Let's start simple:

```bash
cd ~/bioinfo-course
mkdir -p module06
cd module06

# Create a sample manifest
cat > samples.txt << 'EOF'
SMP-001 tumor patient_A
SMP-002 normal patient_A
SMP-003 tumor patient_B
BACKUP-SMP-001 tumor patient_A
SMP-004X normal patient_B
SMP-999 tumor patient_C
EOF

# Search for lines containing "SMP"
grep 'SMP' samples.txt
```

Output:
```
SMP-001 tumor patient_A
SMP-002 normal patient_A
SMP-003 tumor patient_B
BACKUP-SMP-001 tumor patient_A
SMP-004X normal patient_B
SMP-999 tumor patient_C
```

All lines contain "SMP" somewhere, so they all match. Let's get more specific.

---

## Literal Characters

Most characters in a regex match themselves exactly. These are called **literal characters**.

```bash
# Match exactly "tumor"
grep 'tumor' samples.txt
```

Output:
```
SMP-001 tumor patient_A
SMP-003 tumor patient_B
BACKUP-SMP-001 tumor patient_A
SMP-999 tumor patient_C
```

**Multiple words:**
```bash
# Match "patient_B"
grep 'patient_B' samples.txt
```

Output:
```
SMP-003 tumor patient_B
SMP-004X normal patient_B
```

Simple! But some characters are special...

---

## Metacharacters: Special Characters with Superpowers

**Metacharacters** have special meanings in regex instead of matching themselves literally.

### The Metacharacters

```
. ^ $ * + ? { } [ ] \ | ( )
```

We'll learn each one. For now, know that if you want to match them literally, you need to **escape** them with a backslash `\`.

**Example: Match a literal dot**
```bash
# Create a file list
echo "file.txt" > files.txt
echo "fileAtxt" >> files.txt
echo "file-txt" >> files.txt

# Wrong: . matches ANY character
grep 'file.txt' files.txt
```

Output (matches all!):
```
file.txt
fileAtxt
file-txt
```

**Correct: Escape the dot**
```bash
grep 'file\.txt' files.txt
```

Output (only the exact match):
```
file.txt
```

---

## The Dot (.) — Match Any Single Character

The **dot** (`.`) matches **any single character** except newline.

```bash
cat > sequences.txt << 'EOF'
ATGC
ATCC
ATNC
AT-C
AT7C
EOF

# Match "AT" + any character + "C"
grep 'AT.C' sequences.txt
```

Output (all match because . matches any character):
```
ATGC
ATCC
ATNC
AT-C
AT7C
```

**Practical example:**
```bash
# Match sample IDs like SMP-001, SMP-002, etc.
# Pattern: SMP-00 followed by any single digit
grep 'SMP-00.' samples.txt
```

Output:
```
SMP-001 tumor patient_A
SMP-002 normal patient_A
SMP-003 tumor patient_B
```

But this also matches `SMP-004X` if we had `SMP-00X`. Let's be more specific with character classes...

---

## Character Classes [...] — Match Specific Characters

**Character classes** let you specify which characters are allowed in a position.

```bash
[abc]      # Match a, b, or c
[0-9]      # Match any digit
[A-Z]      # Match any uppercase letter
[a-z]      # Match any lowercase letter
[a-zA-Z]   # Match any letter
[^abc]     # Match anything EXCEPT a, b, or c (negation)
```

**Note:** Inside brackets, most metacharacters lose their special meaning (except `^`, `-`, `]`, and `\`).

### Examples

```bash
# Match SMP- followed by exactly 3 digits
grep 'SMP-[0-9][0-9][0-9]' samples.txt
```

Output:
```
SMP-001 tumor patient_A
SMP-002 normal patient_A
SMP-003 tumor patient_B
SMP-999 tumor patient_C
```

Doesn't match `SMP-004X` (has an X) or `BACKUP-SMP-001` (doesn't start with SMP-).

**Match samples ending with specific patients:**
```bash
# Match patient_A or patient_B
grep 'patient_[AB]' samples.txt
```

Output:
```
SMP-001 tumor patient_A
SMP-002 normal patient_A
SMP-003 tumor patient_B
SMP-004X normal patient_B
```

### Negated Character Classes

Use `^` at the start to match anything EXCEPT those characters:

```bash
cat > ids.txt << 'EOF'
ID-A01
ID-B02
ID-C03
ID-X99
EOF

# Match IDs that DON'T contain X, Y, or Z
grep 'ID-[^XYZ]' ids.txt
```

Output:
```
ID-A01
ID-B02
ID-C03
```

### Predefined Character Classes

Some character classes are so common they have shortcuts:

| Shortcut | Equivalent | Matches |
|----------|------------|---------|
| `\d` | `[0-9]` | Any digit |
| `\D` | `[^0-9]` | Any non-digit |
| `\w` | `[a-zA-Z0-9_]` | Word character (letter, digit, underscore) |
| `\W` | `[^a-zA-Z0-9_]` | Non-word character |
| `\s` | `[ \t\n\r]` | Whitespace (space, tab, newline) |
| `\S` | `[^ \t\n\r]` | Non-whitespace |

**Important:** With `grep`, use `-P` flag for these shortcuts (Perl-compatible regex):

```bash
# Match sample IDs with digits
grep -P 'SMP-\d{3}' samples.txt
```

Or use the extended regex flag `-E` with traditional classes:

```bash
grep -E 'SMP-[0-9]{3}' samples.txt
```

---

## Anchors — Match Positions, Not Characters

**Anchors** don't match characters—they match **positions** in the string.

### Start of Line: `^`

`^` matches the beginning of a line.

```bash
# Match lines that START with "SMP-"
grep '^SMP-' samples.txt
```

Output:
```
SMP-001 tumor patient_A
SMP-002 normal patient_A
SMP-003 tumor patient_B
SMP-004X normal patient_B
SMP-999 tumor patient_C
```

Doesn't match `BACKUP-SMP-001` (doesn't start with SMP-).

### End of Line: `$`

`$` matches the end of a line.

```bash
# Match lines that END with "patient_A"
grep 'patient_A$' samples.txt
```

Output:
```
SMP-001 tumor patient_A
SMP-002 normal patient_A
BACKUP-SMP-001 tumor patient_A
```

### Combining Anchors

```bash
# Match lines that are EXACTLY "tumor"
cat > types.txt << 'EOF'
tumor
normal
tumor_derived
EOF

grep '^tumor$' types.txt
```

Output:
```
tumor
```

Only the exact line, not `tumor_derived`.

### Practical Bioinformatics Example

**Validate sample IDs that must be exactly in the format SMP-###:**

```bash
# Must start with SMP-, have exactly 3 digits, and nothing after
grep -E '^SMP-[0-9]{3}$' samples.txt
```

This rejects:
- Lines not starting with SMP-
- Lines with more/fewer than 3 digits
- Lines with anything after the digits

---

## Quantifiers — Repeat Patterns

Quantifiers specify how many times the previous element should repeat.

### Basic Quantifiers

| Quantifier | Meaning | Example |
|------------|---------|---------|
| `*` | Zero or more times | `a*` matches "", "a", "aa", "aaa" |
| `+` | One or more times | `a+` matches "a", "aa", "aaa" (not "") |
| `?` | Zero or one time (optional) | `a?` matches "", "a" |
| `{n}` | Exactly n times | `a{3}` matches "aaa" |
| `{n,}` | At least n times | `a{2,}` matches "aa", "aaa", "aaaa" |
| `{n,m}` | Between n and m times | `a{2,4}` matches "aa", "aaa", "aaaa" |

**Important:** The quantifier applies to the character or group immediately before it!

### Examples with `*` (Zero or More)

```bash
cat > codes.txt << 'EOF'
SMP001
SMP-001
SMP--001
SMP---001
EOF

# Match SMP, then zero or more dashes, then 001
grep 'SMP-*001' codes.txt
```

Output (all match):
```
SMP001
SMP-001
SMP--001
SMP---001
```

### Examples with `+` (One or More)

```bash
# Match SMP, then ONE OR MORE dashes, then 001
grep 'SMP-\+001' codes.txt
```

Output (doesn't match SMP001 with no dashes):
```
SMP-001
SMP--001
SMP---001
```

**Note:** In basic grep, you need to escape `+` as `\+`. With `grep -E` (extended), you don't:

```bash
grep -E 'SMP-+001' codes.txt    # Same result
```

### Examples with `?` (Optional)

```bash
cat > colors.txt << 'EOF'
color
colour
EOF

# Match "colo" + optional "u" + "r"
grep -E 'colou?r' colors.txt
```

Output (both match):
```
color
colour
```

### Examples with `{n}` (Exact Count)

```bash
# Match exactly 3 digits
grep -E '[0-9]{3}' samples.txt
```

### Examples with `{n,m}` (Range)

```bash
cat > phone.txt << 'EOF'
555-1234
555-12345
555-123456
555-12
EOF

# Match phone numbers with 4 to 6 digits after the dash
grep -E '555-[0-9]{4,6}' phone.txt
```

Output:
```
555-1234
555-12345
555-123456
```

### DNA Motif Examples

```bash
cat > dna.txt << 'EOF'
ATGCCCCCGAT
ATGCGAT
ATGCCGAT
ATGCCCGAT
EOF

# Match poly-C: "ATGC" + 2 to 5 Cs + "GAT"
grep -E 'ATGC{2,5}GAT' dna.txt
```

Output:
```
ATGCCCCCGAT
ATGCCGAT
ATGCCCGAT
```

---

## Grouping with (...) — Treat Multiple Characters as One

Parentheses `()` group characters together so quantifiers apply to the whole group.

### Without Grouping

```bash
# This repeats only the "e"
grep -E 'AGe{3}' dna.txt    # Matches "AGeee"
```

### With Grouping

```bash
cat > repeats.txt << 'EOF'
AGAGAG
ATATAT
AGAG
ATAT
EOF

# Match "AG" repeated exactly 3 times
grep -E '(AG){3}' repeats.txt
```

Output:
```
AGAGAG
```

### Practical Example: Tandem Repeats

```bash
cat > microsatellite.txt << 'EOF'
ATCGATCGATCG
CAGCAGCAGCAG
TTATTATTA
GGCGGCGGC
EOF

# Match "CAG" repeated 2-4 times
grep -E '(CAG){2,4}' microsatellite.txt
```

Output:
```
CAGCAGCAGCAG
```

---

## Alternation with | — Match This OR That

The pipe `|` means "or" — match the pattern on the left OR the right.

```bash
cat > phenotypes.txt << 'EOF'
patient001 resistant healthy
patient002 sensitive sick
patient003 resistant sick
patient004 sensitive healthy
EOF

# Match lines with "resistant" OR "sensitive"
grep -E 'resistant|sensitive' phenotypes.txt
```

Output (all match):
```
patient001 resistant healthy
patient002 sensitive sick
patient003 resistant sick
patient004 sensitive healthy
```

### Combining with Groups

```bash
# Match tumor OR normal samples
grep -E '^SMP-[0-9]{3} (tumor|normal)' samples.txt
```

### Multiple Alternations

```bash
# Match lines mentioning patient_A OR patient_B OR patient_C
grep -E 'patient_(A|B|C)' samples.txt
```

---

## Escape Characters: \

To match metacharacters literally, escape them with `\`.

### Common Escapes

```bash
\.    # Literal dot
\*    # Literal asterisk
\+    # Literal plus
\?    # Literal question mark
\^    # Literal caret
\$    # Literal dollar sign
\[    # Literal left bracket
\]    # Literal right bracket
\(    # Literal left paren
\)    # Literal right paren
\\    # Literal backslash
```

### Example: Matching Version Numbers

```bash
cat > versions.txt << 'EOF'
version 1.0
version 2.5
version 10.12
EOF

# Match "version" + space + number + dot + number
grep -E 'version [0-9]+\.[0-9]+' versions.txt
```

Output:
```
version 1.0
version 2.5
version 10.12
```

---

## grep Flavors: Basic vs Extended vs Perl

`grep` has different regex flavors:

### 1. Basic Regular Expressions (BRE)

Default `grep` — requires escaping `+`, `?`, `{`, `}`, `|`, `(`, `)`

```bash
grep 'pattern\+' file.txt    # Need to escape +
```

### 2. Extended Regular Expressions (ERE)

Use `grep -E` or `egrep` — don't escape `+`, `?`, `{`, `}`, `|`, `(`, `)`

```bash
grep -E 'pattern+' file.txt    # No escape needed
```

**Recommendation:** Always use `grep -E` for clarity!

### 3. Perl-Compatible Regular Expressions (PCRE)

Use `grep -P` — most powerful, supports `\d`, `\w`, `\s`, lookaheads, and more

```bash
grep -P '\d{3}-\d{4}' file.txt
```

**Note:** `grep -P` isn't available on all systems (especially macOS). For portability, stick with `grep -E`.

---

## Practical Bioinformatics Applications

### 1. Validate Sample IDs

**Requirement:** Sample IDs must be exactly `SMP-` followed by 3 digits.

```bash
cat > manifest.txt << 'EOF'
SMP-001
SMP-042
SMP-999
BACKUP-SMP-001
SMP-1234
SMP-01
SMP-A01
EOF

# Valid sample IDs only
grep -E '^SMP-[0-9]{3}$' manifest.txt
```

Output:
```
SMP-001
SMP-042
SMP-999
```

### 2. Find DNA Motifs

**Find TATA box (TATAAA or TATAAAA):**

```bash
cat > promoter.txt << 'EOF'
GCGATATAAAAGGCT
ATGCGCTATAAGGCT
CGCTATAATTAGGCT
EOF

# Match "TATA" + 2 or 3 As
grep -E 'TATA{2,3}' promoter.txt
```

Output:
```
GCGATATAAAAGGCT
ATGCGCTATAAGGCT
```

**Find poly-A tails (10+ As):**

```bash
cat > sequences.txt << 'EOF'
ATGCGCTAAAAAAAAAAAAGTC
CGTATGCAAAAGTC
TGCAAAAAAAAAAAAAAAAAAATGC
EOF

grep -E 'A{10,}' sequences.txt
```

Output:
```
ATGCGCTAAAAAAAAAAAAGTC
TGCAAAAAAAAAAAAAAAAAAATGC
```

### 3. Extract Email Addresses

```bash
cat > contacts.txt << 'EOF'
Contact: john.doe@university.edu
Email jane_smith@lab.org for details
Reach out to admin@server.com
EOF

# Simple email pattern
grep -E -o '[a-zA-Z0-9._+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' contacts.txt
```

The `-o` flag shows only the matching part:
```
john.doe@university.edu
jane_smith@lab.org
admin@server.com
```

### 4. Parse FASTA Headers

```bash
cat > sequences.fasta << 'EOF'
>NM_001234.5 Homo sapiens gene [organism=human]
ATGCGCTAG
>XM_005678.2 Mus musculus protein [organism=mouse]
GCTAGCTAG
>NR_999999.1 Rattus norvegicus RNA [organism=rat]
TACGATCGA
EOF

# Extract accession numbers (pattern: 2 letters, underscore, 6 digits, dot, version)
grep -E -o '[A-Z]{2}_[0-9]{6}\.[0-9]+' sequences.fasta
```

Output:
```
NM_001234.5
XM_005678.2
NR_999999.1
```

### 5. Validate Genomic Coordinates

**Format: chr# or chrX/chrY, then position**

```bash
cat > coordinates.txt << 'EOF'
chr1:12345
chr10:999999
chrX:54321
chr2:100
chrZ:12345
chr:12345
EOF

# Match chr + digit(s) or X/Y, colon, then position
grep -E '^chr([0-9]+|[XY]):[0-9]+$' coordinates.txt
```

Output:
```
chr1:12345
chr10:999999
chrX:54321
chr2:100
```

---

## Practice Exercises

### Setup: Create Test Files

```bash
cd ~/bioinfo-course/module06
mkdir practice
cd practice

# Sample IDs with various formats
cat > sample_ids.txt << 'EOF'
SMP-001
SMP-042
SMP-999
CTRL-001
BACKUP_SMP-001
SMP-1234
SMP-01A
smp-001
SMP_001
EOF

# Gene names
cat > genes.txt << 'EOF'
BRCA1
BRCA2
TP53
tp53
MYC
MYCN
EGFR
ERBB2
KRAS
EOF

# DNA sequences with motifs
cat > motifs.txt << 'EOF'
ATGCGATATAAAAGGCTAG
CGCTATAAAGGCTTAG
GCGATATGGCTAGCT
ATGCTATAATTAGGCT
CGCGCGCGCGCGCGCG
ATGCCCCCCCCCGAT
EOF
```

### Exercises

**Exercise 1:** Find all valid sample IDs (format: SMP-### where ### is exactly 3 digits)

<details>
<summary>Solution</summary>

```bash
grep -E '^SMP-[0-9]{3}$' sample_ids.txt
```

Output:
```
SMP-001
SMP-042
SMP-999
```
</details>

**Exercise 2:** Find all BRCA genes (BRCA1 or BRCA2)

<details>
<summary>Solution</summary>

```bash
grep -E '^BRCA[12]$' genes.txt
```

Output:
```
BRCA1
BRCA2
```
</details>

**Exercise 3:** Find genes starting with "TP" or "MYC"

<details>
<summary>Solution</summary>

```bash
grep -E '^(TP|MYC)' genes.txt
```

Output:
```
TP53
MYC
MYCN
```
</details>

**Exercise 4:** Case-insensitive search for "tp53"

<details>
<summary>Solution</summary>

```bash
grep -i 'tp53' genes.txt
```

Output:
```
TP53
tp53
```
</details>

**Exercise 5:** Find sequences with a TATA box (TATAA or TATAAA)

<details>
<summary>Solution</summary>

```bash
grep -E 'TATA{2,3}' motifs.txt
```

Output:
```
ATGCGATATAAAAGGCTAG
CGCTATAAAGGCTTAG
ATGCTATAATTAGGCT
```
</details>

**Exercise 6:** Find sequences with poly-C runs (5+ Cs in a row)

<details>
<summary>Solution</summary>

```bash
grep -E 'C{5,}' motifs.txt
```

Output:
```
ATGCCCCCCCCCGAT
```
</details>

**Exercise 7:** Find sequences with CG dinucleotide repeats (3+ times)

<details>
<summary>Solution</summary>

```bash
grep -E '(CG){3,}' motifs.txt
```

Output:
```
CGCGCGCGCGCGCGCG
```
</details>

---

## The Exit Ticket Challenge: Variant Annotation File

### Scenario

You've received a variant annotation file and need to extract specific information using regex.

### Setup

```bash
cd ~/bioinfo-course/module06
cat > variants_annotated.txt << 'EOF'
chr1:12345:A>G|GENE1|missense|pathogenic|rs123456
chr2:67890:C>T|GENE2|synonymous|benign|rs234567
chr3:11111:G>A|GENE3|nonsense|pathogenic|rs345678
chr4:22222:T>C|GENE4|missense|uncertain|rs456789
chr5:33333:A>T|GENE5|missense|benign|rs567890
chrX:44444:C>G|GENE6|frameshift|pathogenic|rs678901
chr10:55555:G>C|GENE7|missense|pathogenic|rs789012
chr22:66666:T>A|GENE8|synonymous|benign|rs890123
chrY:77777:A>C|GENE9|missense|uncertain|rs901234
chr1:88888:C>T|GENE10|nonsense|pathogenic|rs012345
EOF
```

### Format Explanation

Each line: `chromosome:position:variant|gene|consequence|significance|rsID`

### Your Tasks

Use regex to answer these questions. Record each command!

**Task 1:** Extract all variants on chromosome 1

**Task 2:** Extract all pathogenic variants (lines containing "pathogenic")

**Task 3:** Extract all rsIDs (pattern: rs followed by 6 digits)

**Task 4:** Find nonsense or frameshift variants

**Task 5:** Extract variants on sex chromosomes (chrX or chrY)

**Task 6:** Find variants in genes starting with "GENE" and a single digit (GENE1-GENE9, not GENE10)

**Task 7:** Extract chromosome and position for all missense variants

**Task 8:** Count how many pathogenic variants exist

**Task 9:** Find variants at positions greater than 50000 (regex can't do numeric comparison directly, but you can match position starting with 5-9 or having 6+ digits)

**Task 10:** Create a file `pathogenic_variants.txt` with only pathogenic variants, sorted by chromosome

---

## Quick Reference

### Basic Syntax

| Pattern | Meaning | Example |
|---------|---------|---------|
| `abc` | Literal characters | Matches "abc" |
| `.` | Any single character | `a.c` matches "abc", "a1c", "a c" |
| `\` | Escape special character | `\.` matches literal "." |

### Anchors

| Pattern | Meaning | Example |
|---------|---------|---------|
| `^` | Start of line | `^SMP` matches lines starting with "SMP" |
| `$` | End of line | `txt$` matches lines ending with "txt" |

### Character Classes

| Pattern | Meaning | Example |
|---------|---------|---------|
| `[abc]` | Match a, b, or c | `[ACGT]` matches any nucleotide |
| `[a-z]` | Match range | `[0-9]` matches any digit |
| `[^abc]` | Match anything except a, b, or c | `[^ACGT]` matches non-ACGT |
| `\d` | Digit (with -P) | `\d{3}` matches 3 digits |
| `\w` | Word character (with -P) | `\w+` matches word |
| `\s` | Whitespace (with -P) | `\s+` matches spaces/tabs |

### Quantifiers

| Pattern | Meaning | Example |
|---------|---------|---------|
| `*` | Zero or more | `A*` matches "", "A", "AA" |
| `+` | One or more | `A+` matches "A", "AA", not "" |
| `?` | Zero or one (optional) | `colou?r` matches "color" and "colour" |
| `{n}` | Exactly n times | `[0-9]{3}` matches 3 digits |
| `{n,}` | At least n times | `A{2,}` matches "AA", "AAA", etc. |
| `{n,m}` | Between n and m times | `A{2,4}` matches "AA", "AAA", "AAAA" |

### Groups and Alternation

| Pattern | Meaning | Example |
|---------|---------|---------|
| `(...)` | Group | `(AG){3}` matches "AGAGAG" |
| `\|` | Alternation (OR) | `cat\|dog` matches "cat" or "dog" |

### grep Options

| Option | What It Does |
|--------|-------------|
| `-E` | Extended regex (recommended) |
| `-P` | Perl-compatible regex |
| `-i` | Case-insensitive |
| `-v` | Invert match (show non-matching lines) |
| `-o` | Show only matching part |
| `-c` | Count matching lines |
| `-n` | Show line numbers |

---

## Exit Ticket

To complete this module, send me an email with:

**Subject:** Bioinfo M6 Exit Ticket – [Your Name]

**Content:**

1. Your commands for all 10 tasks in the variant annotation challenge
2. The output for tasks 3, 8, and 10
3. One paragraph (4-5 sentences) explaining:
   - The difference between wildcards and regex
   - One example where anchors (^ or $) were essential
   - One example where quantifiers made your pattern more flexible

---

## Summary

Congratulations! You now understand:

✓ Regular expressions vs wildcards—when to use each
✓ Literal characters and metacharacters
✓ The dot (.) for matching any character
✓ Character classes [...] and ranges
✓ Anchors (^, $) for matching positions
✓ Quantifiers (*, +, ?, {n,m}) for repetition
✓ Grouping (...) for complex patterns
✓ Alternation (|) for OR logic
✓ Escaping special characters with \
✓ grep flavors: basic, extended (-E), and Perl (-P)
✓ Practical bioinformatics applications: ID validation, motif finding, parsing file formats

**Regular expressions are a superpower!** They let you:
- Validate sample IDs and file formats
- Find sequence motifs
- Parse complex bioinformatics file formats
- Extract specific information from large datasets
- Quality-control manifest files

You'll use regex constantly in bioinformatics. With practice, complex patterns become second nature!

In the next module, we'll learn about process management—how to monitor, control, and rescue stuck programs.

**Next:** [Module 7 — Process Management & Job Control](07-processes.md)
