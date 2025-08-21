# Module 7 — Local Env + Tiny QC

**Time:** 60–75 min  
**Goal:** Create a clean Conda/Mamba env and run seqtk, fastqc, and multiqc locally.

!!! tip "Type it, don't paste it"
    Muscle memory now = speed later. Please type once before copy/paste.

## 1) Install Miniforge/Mambaforge

=== "Windows (WSL2 Ubuntu)"

    ```bash
    cd ~
    wget https://github.com/conda-forge/miniforge/releases/latest/download/Mambaforge-Linux-x86_64.sh
    bash Mambaforge-Linux-x86_64.sh -b -p $HOME/mambaforge
    eval "$($HOME/mambaforge/bin/conda shell.bash hook)"
    conda config --set auto_activate_base false
    ```

=== "macOS (Apple Silicon)"

    ```bash
    cd ~
    curl -LO https://github.com/conda-forge/miniforge/releases/latest/download/Mambaforge-MacOSX-arm64.sh
    bash Mambaforge-MacOSX-arm64.sh -b -p $HOME/mambaforge
    eval "$($HOME/mambaforge/bin/conda shell.zsh hook)"  # or bash if you use bash
    conda config --set auto_activate_base false
    ```

=== "macOS (Intel)"

    ```bash
    cd ~
    curl -LO https://github.com/conda-forge/miniforge/releases/latest/download/Mambaforge-MacOSX-x86_64.sh
    bash Mambaforge-MacOSX-x86_64.sh -b -p $HOME/mambaforge
    eval "$($HOME/mambaforge/bin/conda shell.zsh hook)"
    conda config --set auto_activate_base false
    ```

## 2) Make the env

```bash
conda create -y -n rnaseq101 -c conda-forge -c bioconda fastqc multiqc seqtk
conda activate rnaseq101
```

## 3) Prepare a tiny FASTQ and downsample

```bash
mkdir -p ~/de-onramp/lesson4 && cd ~/de-onramp/lesson4
cat > toy.fq << 'EOF'
@r1
ACGTTGCA
+
IIIIHHHF
@r2
GGGTTTAA
+
FFFFFIII
@r3
ACGTACGT
+
IIIIIIII
EOF

# Downsample "reads" (works better on real data but fine for demo)
seqtk sample -s 1 toy.fq 1.0 > toy.sub.fq
```

## 4) Run FastQC + MultiQC

```bash
fastqc -o qc toy.fq toy.sub.fq
multiqc -o qc qc
```

Open `qc/multiqc_report.html` locally (VS Code file explorer → click to open in your default browser).

## Exit Ticket (email)

**Subject:** DE M7 Exit Ticket – <Your Name>  
**Paste:**

- Output of `conda list | grep -E 'fastqc|multiqc|seqtk'`
- Two bullets from your MultiQC report (e.g., per-base quality and GC content).