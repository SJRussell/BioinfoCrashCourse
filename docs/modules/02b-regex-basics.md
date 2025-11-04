# Module 2b — Regex Primer for Manifests & Motifs

**Time:** 45 min  
**Goal:** Spot and extract patterns in manifest files, short DNA strings, and messy lab notes using extended regular expressions.

!!! info "Key ideas"
    - Anchor patterns with `^` (start) and `$` (end) to avoid false positives.
    - Use character classes (`[ACGT]`, `[0-9]`, `[^#]`) to describe allowed or forbidden characters.
    - Control repetition with quantifiers like `?`, `+`, `*`, `{n}`, and `{m,n}`.
    - Group patterns with parentheses to reuse chunks or capture sub-matches.

## 1) Anchors — trust, but verify your manifest IDs

Sample manifests often blend good and bad IDs. Anchors prevent partial matches.

Try it now:

```bash
# Keep only rows with sample IDs that start with "SMP-" followed by 3 digits
grep -E '^SMP-[0-9]{3}\t' manifest.tsv

# Preview everything that *fails* the anchored rule (handy for cleanup)
ripgrep --invert-match --multiline-dotall '^SMP-[0-9]{3}\t' manifest.tsv
```

**Why anchors?** Without `^`/`$`, `grep -E 'SMP-[0-9]{3}'` would also match stray notes like `backup_SMP-012.txt`.

## 2) Character classes — clean the lab notes

Field notes get noisy with hashtags, timestamps, and typos. Describe acceptable characters to filter or fix lines.

Try it now:

```bash
# Spot handwritten temperature notes like "Temp: 23C" (block emoji/comments)
ripgrep -n 'Temp: [0-9]{2}[CF]' lab-notes.txt

# Flag anything with unexpected punctuation after the sample ID
grep -nE '^SMP-[0-9]{3}[^\tA-Za-z0-9]' lab-notes.txt
```

**Swap classes quickly:** `[A-Za-z]` for letters, `[[:alpha:]]` for locale-aware letters, `[0-9]` or `\d` (via `grep -P`) for digits, and `[^...]` to negate.

## 3) Quantifiers — DNA motifs in bite-sized strings

Short reads or qPCR probes often contain motifs like "AATAA" or "TATA" boxes. Quantifiers keep the expressions compact.

Try it now:

```bash
# Match a simple poly-A tail: "AAA" repeated 4–6 times (12–18 As total)
grep -E 'A{12,18}' tiny-motifs.txt

# Find the minimal promoter motif TATA followed by 3 arbitrary bases and a G
grep -E 'TATA.{3}G' tiny-motifs.txt
```

**Remember:** `{n}` exact repetitions, `{m,}` lower-bound only, `{m,n}` range, `?` (0 or 1), `+` (1 or more), `*` (0 or more).

## 4) Grouping — capture, reuse, and report

Parentheses let you capture repeated chunks or apply quantifiers to multi-character patterns.

Try it now:

```bash
# Match paired sample IDs like "SMP-101_vs_SMP-205" and extract both sides
ripgrep -o --replace '$1 -> $2' '(SMP-[0-9]{3})_vs_(SMP-[0-9]{3})' comparisons.txt

# Detect repeated phrases in lab notes ("run run"), even with capitalization changes
ripgrep -in '\b([A-Za-z]+)\b\s+\1' lab-notes.txt
```

**Grouping tricks:** `(?:...)` for non-capturing groups (works in `ripgrep`), `(pattern){2}` repeats the whole group, and backreferences like `\1` reuse earlier groups.

## Exit Ticket (email)

**Subject:** DE M2b Exit Ticket – <Your Name>

**Send:**

1. One anchored regex you used to validate manifest IDs (share the command + match count).
2. One motif-search command (e.g., a quantifier-based pattern) and a short note on what it found.
3. One lab-note cleanup command that relied on character classes or grouping, plus a sentence on how you'd tidy the source file.
