# Module 8 — Pull One Single-Cell FASTQ (Public SRA/ENA)

**Time:** 60–90 min  
**Goal:** Find a study, choose one small run, download the FASTQ via HTTPS, and verify it.

!!! info "What you'll practice"
    - Navigating SRA (Study → Sample → Experiment → Run (SRR))
    - Using the Run Selector to find a small run
    - Grabbing direct ENA HTTPS links for FASTQ
    - Resumable downloads and checksums

## 1) Pick a study

Use a classic human single-cell embryo dataset such as **SRP011546** (GEO GSE36552).  
Open the Run Selector for the study. Sort by size/spots and pick a small SRR.

!!! tip
    If your choice is still large, that's okay—download just one file and downsample in the next module.

## 2) Get the direct FASTQ URL

From the SRR page, follow the ENA / EBI link. On the ENA run page, copy an HTTPS FASTQ link (R1 or single-end).

## 3) Download (resumable) and verify

```bash
mkdir -p ~/de-onramp/lesson5/data && cd ~/de-onramp/lesson5/data

# Replace with your URL:
wget -c "https://.../SRRxxxxxxx.fastq.gz" -O SRR.fastq.gz

# Optional: if ENA provides checksums, verify; otherwise compute your own
sha256sum SRR.fastq.gz > SRR.fastq.gz.sha256
sha256sum -c SRR.fastq.gz.sha256

# Sanity peek (don't expand the whole file!):
zcat SRR.fastq.gz | head -n 8
```

If the file is very large and `head` feels slow, use `pv` or `zcat | head` as above (fast).  
If you need to stop, Ctrl-C and rerun `wget -c` later to resume.

## 4) (Optional) Keep a tiny working copy

```bash
conda activate rnaseq101
seqtk sample -s 7 SRR.fastq.gz 10000 | gzip > SRR.10k.fastq.gz   # ~10k reads
```

## Exit Ticket (email)

**Subject:** DE M8 Exit Ticket – <Your Name>  
**Paste:**

- The study accession and the SRR you chose
- Your exact `wget` command (with URL redacted if you prefer)
- File size (`ls -lh`) and the single line from `sha256sum -c`