# Digital Embryo — Bioinformatics Command-Line Tutorial

Welcome to the **Digital Embryo** bioinformatics tutorial! This hands-on course will take you from command-line basics to analyzing real RNA-seq data.

## Learning Path

```mermaid
graph TD
    A[Module 0: Setup & Environment] --> B[Module 1: Navigation & Files]
    B --> C[Module 2: Pipes & Data Wrangling]
    C --> C2[Module 2b: Regex for Manifests & Motifs]
    C2 --> D[Module 3: Process Management]
    D --> E[Module 4: Editing & Compression]
    E --> F[Module 5: RNA-seq Primer]
    F --> G[Module 6: FASTQ Analysis]
    G --> H[Module 7: Conda & QC Tools]
    H --> I[Module 8: Download Real Data]
    I --> J[Module 9: View-Run-View Loop]
    J --> K[Module 10: Capstone Script]
    
    style A fill:#e1f5fe
    style K fill:#c8e6c9
    style F fill:#fff3e0
```

1. [Module 0: Setup & Expectations](modules/00-setup.md) — Install tools and confirm your environment.
2. [Module 1: Navigation & Files](modules/01-navigation-files.md) — Move around, inspect data, and manage directories.
3. [Module 2: Pipes, Redirects & a TSV Search Lab](modules/02-pipes-tsv.md) — Chain commands to wrangle tabular manifests.
4. [Module 2b: Regex Primer for Manifests & Motifs](modules/02b-regex-basics.md) — Learn anchors, classes, quantifiers, and groups to vet manifests and spot short DNA motifs.
5. [Module 3: Processes & Hung-Terminal Rescue](modules/03-processes.md) — Monitor and control what’s running.
6. [Module 4: Editing & Compression](modules/04-editing-compression.md) — Edit text safely and work with compressed data.
7. [Module 5: RNA-seq Primer](modules/05-rnaseq-primer.md) — Preview the RNA-seq workflow and vocabulary.
8. [Module 6: FASTQ Analysis](modules/06-fastq-101.md) — Inspect reads and compute quick QC stats.
9. [Module 7: Conda & QC Tools](modules/07-env-qc.md) — Build environments and run FastQC/MultiQC.
10. [Module 8: Download Real Data](modules/08-pull-one-srr.md) — Retrieve sequencing runs from public archives.
11. [Module 9: View-Run-View Loop](modules/09-view-run-view.md) — Iterate on alignments and reports.
12. [Module 10: Capstone Script](modules/10-capstone.md) — Automate the workflow end-to-end.

## Course Philosophy

!!! tip "Type it, don't paste it"
    This tutorial emphasizes **muscle memory**. Type commands first, then copy/paste to check your work. Your future self will thank you.

!!! info "Look before you loop"
    Always examine data with `head`, `tail`, `less`, or `zless` before writing scripts that process many files.

!!! note "Help first, experiment second"
    Always run `command --help` (or `man command`) the first time you encounter a new tool. Understanding the options saves time and prevents mistakes.

## What You'll Learn

- **Command-line fundamentals**: navigation, file operations, text processing
- **Data wrangling**: pipes, redirects, grep, awk, and cut
- **Process management**: background jobs, monitoring, and safe termination
- **Bioinformatics tools**: conda environments, FastQC, MultiQC, seqtk
- **Real data analysis**: downloading from SRA/ENA, quality control workflows
- **Scripting**: building robust, reusable analysis scripts

## Prerequisites

- A computer with WSL2 (Windows), Terminal (macOS), or Linux
- VS Code (recommended)
- Willingness to type commands and learn by doing

## Time Commitment

- **Total**: ~8-10 hours
- **Per module**: 30-90 minutes
- **Format**: Self-paced with email exit tickets

## Getting Started

1. Start with [Module 0: Setup & Expectations](modules/00-setup.md)
2. Keep the [Cheat Sheet](cheatsheet.md) handy for quick reference
3. Type commands first, copy/paste second
4. Submit exit tickets as you complete each module

Ready to begin? Let's build those command-line skills! 🧬
