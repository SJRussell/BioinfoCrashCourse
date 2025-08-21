# Digital Embryo — Command-Line One-Pager

**Type it, don't paste it.** You're building muscle memory you'll use all year.

## Must-know keys

- **Tab** = autocomplete (double-Tab to list); **↑/↓** = history
- **Ctrl-r** = reverse search history; **Ctrl-a/e** = start/end of line
- **Ctrl-u/k** = delete to start/end; **Ctrl-c** = interrupt; **Ctrl-z** = suspend

## Files & navigation

```bash
pwd                        # where am I?
ls -lah                    # list (long, all, human sizes)
cd -                       # jump back
mkdir -p path/to/dir       # make nested dirs
cp src dst; mv old new     # copy / move
rm -i file                 # safer remove (interactive)
find . -name "*.fastq.gz"  # find files by name
du -sh ./*                 # folder sizes (human)
```

## Viewing text fast

```bash
less file                  # space=next page, /pattern, n=next, q=quit
head -n 20 file            # first 20 lines
tail -n 20 file            # last 20 lines
wc -l file                 # line count
column -t -s $'\t' file    # pretty-print TSV
```

## Pipes, redirects, subshells

```bash
cmd1 | cmd2                # pipe
cmd > out.txt              # overwrite
cmd >> out.txt             # append
cmd 2> err.txt             # stderr only
echo "size: $(du -sh . | cut -f1)"  # command substitution
tee out.txt                # write to file AND stdout
```

**Example: top 10 extensions in a tree**

```bash
find . -type f | sed 's/.*\.//' | sort | uniq -c | sort -nr | head -10
```

## grep / cut / awk mini-cookbook

```bash
grep -E $'\tA07\t' grades.tsv                 # rows with assignment A07 (TSV)
cut -f1,4-7 grades.tsv | head                 # pick columns
awk -F '\t' '$7=="B"{sum[$1]+=$5/$6} END{for(s in sum) printf "%s\t%.3f\n", s, sum[s]}' grades.tsv \
  | sort -k2,2nr | head -10 | column -t
# ^ top 10 students by total percent in section B
awk -F '\t' '$4=="A07"{print $5}' grades.tsv | sort -n | awk 'NR%2{a=$1;next} {print (a+$1)/2}' # median (even/odd hack)
awk -F '\t' '$5==$6{print $1}' grades.tsv | sort -u | wc -l    # students with 100% on any assignment
```

## Processes (spot, juggle, stop)

```bash
yes > /dev/null &          # start background noise
jobs; fg %1; bg %1         # manage jobs
ps aux | grep yes          # find PID(s)
kill <PID>                 # polite stop (SIGTERM)
kill -9 <PID>              # last resort
pkill yes                  # kill by name (careful)
top / htop / pstree -p     # watch processes
disown %1                  # detach job from shell
```

## Compression & checksums

```bash
gzip file                  # -> file.gz
zless file.gz              # view without extracting
zcat file.gz | head -n 8   # peek first 8 lines
sha256sum file.gz > file.gz.sha256
sha256sum -c file.gz.sha256
```

## Conda/Mamba (Miniforge)

```bash
# one-time install: see Module 7
conda create -y -n rnaseq101 -c conda-forge -c bioconda fastqc multiqc seqtk
conda activate rnaseq101
conda env export --from-history > rnaseq101.yml
```

## FASTQ quick math

```bash
# reads = lines / 4
awk 'END{print NR/4}' sample.fastq

# longest read length
awk 'NR%4==2{ if(length($0)>m) m=length($0) } END{ print m }' sample.fastq

# GC% of first N reads (N=1000)
N=1000
awk -v N="$N" 'NR%4==2{seq=$0; gsub(/[^GgCc]/,"",seq); gc+=length(seq); bp+=length($0); n++; if(n==N) exit}
               END{ if(bp) printf("%.2f\n",100*gc/bp); else print 0 }' sample.fastq
```

## WSL2 (Windows) quick notes

```bash
wsl --install                 # install WSL + Ubuntu
wsl -l -v                     # show distros/versions (ensure version 2)
```

Launch Ubuntu from Start, then open VS Code → "WSL: Connect to WSL".

From WSL, `explorer.exe .` opens File Explorer at the current Linux folder.

## Sanity checks before scripts

- **Look before you loop:** `head`, `tail`, `less`, `zless`.
- Confirm record structure (FASTQ 4-line).
- Verify checksums after big downloads.
- Start with tiny subsets (`seqtk sample`) and scale up.