# Module 12 — Local Env + Tiny QC

**Time:** 60–75 min  
**Goal:** Create a clean Conda/Mamba env and run seqtk, fastqc, and multiqc locally.

!!! tip "Type it, don't paste it"
    Muscle memory now = speed later. Please type once before copy/paste.

## 1) Install Miniforge

Bioinformatics often requires many specialized tools, and managing their dependencies can be tricky. **Conda** and **Mamba** create isolated environments that prevent conflicts and make analyses reproducible. **Miniforge** provides both commands and is the maintained conda-forge installer.

=== "Windows (WSL2 Ubuntu)"

    ```bash
    cd ~
    wget -O Miniforge3.sh https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh
    bash Miniforge3.sh -b -p "$HOME/miniforge3"
    source "$HOME/miniforge3/etc/profile.d/conda.sh"
    conda init bash
    conda config --set auto_activate_base false
    ```

=== "macOS (Apple Silicon)"

    ```bash
    cd ~
    curl -L https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-MacOSX-arm64.sh -o Miniforge3.sh
    file Miniforge3.sh
    bash Miniforge3.sh -b -p "$HOME/miniforge3"
    source "$HOME/miniforge3/etc/profile.d/conda.sh"
    conda init zsh
    conda config --set auto_activate_base false
    ```

=== "macOS (Intel)"

    ```bash
    cd ~
    curl -L https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-MacOSX-x86_64.sh -o Miniforge3.sh
    file Miniforge3.sh
    bash Miniforge3.sh -b -p "$HOME/miniforge3"
    source "$HOME/miniforge3/etc/profile.d/conda.sh"
    conda init zsh
    conda config --set auto_activate_base false
    ```

## 2) Make the env

```bash
conda create -y -n rnaseq101 -c conda-forge -c bioconda fastqc multiqc seqtk
conda activate rnaseq101
```

## 3) Prepare a tiny FASTQ and downsample

```bash
mkdir -p ~/bioinfo-course/module12 && cd ~/bioinfo-course/module12
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
mkdir -p qc
fastqc -o qc toy.fq toy.sub.fq
multiqc -o qc qc
```

Open `qc/multiqc_report.html` locally (VS Code file explorer → click to open in your default browser).

## Exit Ticket (email)

**Subject:** Bioinfo M12 Exit Ticket – <Your Name>
**Paste:**

- Output of `conda list | grep -E 'fastqc|multiqc|seqtk'`
- Two bullets from your MultiQC report (e.g., per-base quality and GC content).

**Next:** [Module 13 — Pull One Single-Cell FASTQ](08-pull-one-srr.md)
