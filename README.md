# Digital Embryo — Command Line & RNA-seq On-Ramp

Short, hands-on modules to get new thesis students productive on the command line and ready to work with FASTQ files. Local-first (no cluster access required), designed to be completed in 8–10 hours.

**Site:** built with MkDocs Material

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
      ├─ 01-navigation-files.md
      ├─ 02-pipes-tsv.md
      ├─ 03-processes.md
      ├─ 04-editing-compression.md
      ├─ 05-rnaseq-primer.md
      ├─ 06-fastq-101.md
      ├─ 07-env-qc.md
      ├─ 08-pull-one-srr.md
      ├─ 09-view-run-view.md
      └─ 10-capstone.md
```

If you add helper scripts later, keep them under `docs/assets/scripts/` so they're visible but students still type first.

## Navigation (MkDocs)

`mkdocs.yml` already defines the left-hand nav. If you add or rename pages, update the `nav:` section accordingly. Example:

```yaml
nav:
  - Home: index.md
  - Cheat Sheet: cheatsheet.md
  - Modules:
      - 0 — Setup & Expectations: modules/00-setup.md
      - 1 — Navigation & Files: modules/01-navigation-files.md
      - 2 — Pipes, Redirects & TSV Lab: modules/02-pipes-tsv.md
      - 3 — Processes & Hung-Terminal Rescue: modules/03-processes.md
      - 4 — Editing, Compression & Checksums: modules/04-editing-compression.md
      - 5 — RNA-seq Primer (Watch): modules/05-rnaseq-primer.md
      - 6 — FASTQ 101 (Hands-on): modules/06-fastq-101.md
      - 7 — Local Env + Tiny QC: modules/07-env-qc.md
      - 8 — Pull One Single-Cell FASTQ: modules/08-pull-one-srr.md
      - 9 — The View → Run → View Loop: modules/09-view-run-view.md
      - 10 — Capstone: One-Command QC Script: modules/10-capstone.md
```

## What students will learn (modules)

- **0 — Setup & Expectations:** VS Code + terminal; WSL2 on Windows; request Alliance account (no cluster use yet).
- **1 — Navigation & Files:** pwd, ls, cd, mkdir, safe rm, less, head, tail, wc, globs.
- **2 — Pipes & TSV Lab:** |, >, 2>, grep, cut, awk, sort, uniq; search a generated 10k-row TSV.
- **3 — Processes:** Ctrl-C, Ctrl-Z, jobs, fg/bg, ps, top/htop, kill, pkill, pstree.
- **4 — Editing/Compression:** nano, gzip/zcat/zless, sha256sum.
- **5 — RNA-seq Primer (watch):** short videos; read structure; quality scores; "look before you loop".
- **6 — FASTQ 101:** 4-line records, robust read counts, longest read, quick GC%.
- **7 — Local Env + Tiny QC:** Miniforge env; seqtk, fastqc, multiqc; open HTML report locally.
- **8 — Pull One SRR:** SRA Run Selector → ENA HTTPS link → wget -c + checksum; optional downsample.
- **9 — View → Run → View:** sanity-check reads; run FastQC/MultiQC; write quick notes on quality/GC/adapters.
- **10 — Capstone:** a single `run_qc.sh` that downsamples (optional), runs FastQC/MultiQC, and writes a summary.

Each module ends with an **Exit Ticket** (students email results to the instructor).

## Teaching notes

- Keep students in their own terminals; discourage copy-pasting until they've typed once.
- Emphasize sanity checks: head/tail/less before loops; verify downloads with checksums.
- Windows quirks vanish if they stay inside WSL Ubuntu.

## Troubleshooting

- **MkDocs not found:** `pip install mkdocs-material` (or `pipx install mkdocs-material`).
- **WSL can't find repo path:** open the repo from within WSL (e.g., `~/projects/digital-embryo-onramp`) rather than `C:\...`.
- **FastQC/MultiQC missing:** ensure `conda activate rnaseq101` (Module 7).
- **Long downloads:** use `wget -c` to resume; verify with `sha256sum -c`.

## Contributing

- Keep modules short; prefer one clear task + one "exit ticket".
- Use admonitions (tips/warnings) and tabs for OS-specific instructions.
- PRs welcome for typos, clarity, or new small labs.