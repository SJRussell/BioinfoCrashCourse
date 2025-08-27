# Module 4 — Editing, Compression & Checksums

**Time:** 45–60 min  
**Goal:** Make small edits safely, work with .gz, and verify files.

!!! tip "Edit safely"
    When learning, edit a copy. Keep originals read-only or backed up.

## 1) nano in 5 minutes

Open a file:

```bash
cd ~/de-onramp/lesson1
echo -e "line1\nline2\nline3" > notes.txt
nano notes.txt
```

Cheat sheet inside nano:

- **Ctrl-O** write, **Enter** confirm; **Ctrl-X** exit.
- **Ctrl-W** search; **Alt-W** next match.
- **Ctrl-K** cut line; **Ctrl-U** paste.

## 2) Compress & preview .gz

=== "Linux/WSL"

    ```bash
    echo "some long text" > big.txt
    gzip big.txt           # creates big.txt.gz, removes original
    zless big.txt.gz       # view without decompressing
    zcat big.txt.gz | head
    gunzip big.txt.gz      # back to big.txt
    ```

    Optional (faster parallel gzip):

    ```bash
    sudo apt install -y pigz
    pigz big.txt
    ```

=== "macOS"

    ```bash
    echo "some long text" > big.txt
    gzip big.txt           # creates big.txt.gz, removes original
    zless big.txt.gz       # view without decompressing
    gzcat big.txt.gz | head  # gzcat instead of zcat on macOS
    gunzip big.txt.gz      # back to big.txt
    ```

    Optional (faster parallel gzip):

    ```bash
    brew install pigz
    pigz big.txt
    ```

## 3) Checksums = trust

=== "Linux/WSL"

    ```bash
    sha256sum big.txt > big.txt.sha256
    sha256sum -c big.txt.sha256   # should say: OK
    ```

=== "macOS"

    ```bash
    shasum -a 256 big.txt > big.txt.sha256
    shasum -a 256 -c big.txt.sha256   # should say: OK
    ```

Why this matters: when you download FASTQs, you can verify integrity against provided checksums before analysis.

## 4) Mini-lab

1. Make `sample.txt`, add two lines with nano.
2. Compress to `sample.txt.gz`.
3. Create and verify a sha256.
4. Preview the compressed file with `zless`.

## Exit Ticket (email)

**Subject:** DE M4 Exit Ticket – <Your Name>
**Paste:**

- Your checksum verification output (`sha256sum -c` or `shasum -a 256 -c`), and two bullets on what you learned about .gz and checksums.