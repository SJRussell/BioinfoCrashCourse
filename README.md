# Digital Embryo — Command Line & RNA-seq On-Ramp

Short, hands-on modules to get new thesis students productive on the command line and ready to work with FASTQ files. Local-first (no cluster access required), designed to be completed in 8–10 hours.

**Site:** [Bioinformatics Tutorial](https://sjrussell.github.io/BioinfoCrashCourse/), built with MkDocs Material

**Audience:** motivated undergrads/grad students with little CLI experience

**Ethos:** Type it, don't paste it — build muscle memory

## Quick start (view the site locally)

### Prereqs

- Python 3.8+ (Mac, Linux, or Windows via WSL2)
- pip (or pipx)

### Install & serve

```bash
# from repo root
pip install mkdocs-material
mkdocs serve
```

Open the local URL MkDocs prints (usually http://127.0.0.1:8000). Changes in `docs/` hot-reload automatically.

### Deploy to GitHub Pages

```bash
mkdocs gh-deploy --clean
```

If you prefer pipx: `pipx install mkdocs-material`

## Windows users (WSL2)

1. Open PowerShell (Admin) → `wsl --install` → choose Ubuntu 22.04 LTS → reboot if prompted.
2. Launch Ubuntu from Start, create a Linux username/password.
3. In VS Code, run "WSL: Connect to WSL", then open this repo folder.
4. Use the integrated terminal (Ctrl+`) for all commands in the modules.

## Repo layout

```
.
├─ mkdocs.yml
└─ docs/
   ├─ index.md
   ├─ cheatsheet.md
   └─ modules/
      ├─ 00-setup.md
      ├─ 01-directory-tree.md
      ├─ 02-working-with-files.md
      ├─ 03-manipulating-files.md
      ├─ 04-wildcards.md
      ├─ 05-pipes-filters.md
      ├─ 06-regex.md
      ├─ 07-processes.md
      ├─ 08-editing-compression.md
      ├─ 02-pipes-tsv.md          # Module 9
      ├─ 05-rnaseq-primer.md      # Module 10
      ├─ 06-fastq-101.md          # Module 11
      ├─ 07-env-qc.md             # Module 12
      ├─ 08-pull-one-srr.md       # Module 13
      ├─ 09-view-run-view.md      # Module 14
      └─ 10-capstone.md            # Module 15
```

If you add helper scripts later, keep them under `docs/assets/scripts/` so they're visible but students still type first.

## Navigation (MkDocs)

`mkdocs.yml` already defines the left-hand nav. If you add or rename pages, update the `nav:` section accordingly. Example:

```yaml
nav:
  - Home: index.md
  - Cheat Sheet: cheatsheet.md
  - Unix Fundamentals:
      - 0 — Setup & Understanding Your Environment: modules/00-setup.md
      - 1 — The Directory Tree & Navigation: modules/01-directory-tree.md
      - 2 — Working with Files: modules/02-working-with-files.md
      - 3 — Manipulating Files (Safely!): modules/03-manipulating-files.md
      - 4 — Wildcards & Pattern Matching: modules/04-wildcards.md
      - 5 — Pipes, Redirects & Filters: modules/05-pipes-filters.md
      - 6 — Regular Expressions for Bioinformatics: modules/06-regex.md
      - 7 — Process Management & Job Control: modules/07-processes.md
      - 8 — Text Editing & File Compression: modules/08-editing-compression.md
  - Bioinformatics Applications:
      - 9 — Advanced TSV Data Wrangling: modules/02-pipes-tsv.md
      - 10 — RNA-seq Primer (Watch): modules/05-rnaseq-primer.md
      - 11 — FASTQ 101 (Hands-on): modules/06-fastq-101.md
      - 12 — Local Env + Tiny QC: modules/07-env-qc.md
      - 13 — Pull One Single-Cell FASTQ: modules/08-pull-one-srr.md
      - 14 — The View → Run → View Loop: modules/09-view-run-view.md
      - 15 — Capstone One-Command QC Script: modules/10-capstone.md
```

## What students will learn (modules)

- **0–8 — Unix fundamentals:** environment setup, navigation, file operations, wildcards, pipes, regex, process management, editing, compression, and checksums.
- **9 — Advanced TSV data wrangling:** `grep`, `cut`, `awk`, `sort`, and `uniq` on a generated 10,000-row dataset.
- **10 — RNA-seq Primer:** short videos; read structure; quality scores; "look before you loop."
- **11 — FASTQ 101:** four-line records, robust read counts, longest read, and quick GC%.
- **12 — Local Env + Tiny QC:** Miniforge environment; seqtk, FastQC, and MultiQC.
- **13 — Pull One SRR:** SRA Run Selector → ENA HTTPS link → resumable download and checksum.
- **14 — View → Run → View:** inspect reads, run FastQC/MultiQC, and write QC notes.
- **15 — Capstone:** a `run_qc.sh` script that optionally downsamples, runs QC, and writes a summary.

Each module ends with an **Exit Ticket** (students email results to the instructor).

## Teaching notes

- Keep students in their own terminals; discourage copy-pasting until they've typed once.
- Emphasize sanity checks: head/tail/less before loops; verify downloads with checksums.
- Windows quirks vanish if they stay inside WSL Ubuntu.

## Troubleshooting

- **MkDocs not found:** `pip install mkdocs-material` (or `pipx install mkdocs-material`).
- **WSL can't find repo path:** open the repo from within WSL (e.g., `~/projects/digital-embryo-onramp`) rather than `C:\...`.
- **FastQC/MultiQC missing:** ensure `conda activate rnaseq101` (Module 12).
- **Long downloads:** resume with `wget -c` on Linux/WSL or `curl -L -C -` on macOS, then verify the checksum.

## Contributing

- Keep modules short; prefer one clear task + one "exit ticket".
- Use admonitions (tips/warnings) and tabs for OS-specific instructions.
- PRs welcome for typos, clarity, or new small labs.
