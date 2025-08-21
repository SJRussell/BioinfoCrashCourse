# Module 2 — Pipes, Redirects & a TSV Search Lab

**Time:** 75 min  
**Goal:** Chain commands and do real data wrangling on a large tab-separated file.  
**Exit ticket (email me):** your three one-liners + their answers.

!!! info "Key ideas"
    - Glue tools with | (pipes)
    - Save/append with > and >>; handle errors with 2>
    - Pick fields with cut/awk; search with grep -E
    - Sort, count uniques, format columns

## 1) Create a working folder

```bash
mkdir -p ~/de-onramp/lesson2 && cd ~/de-onramp/lesson2
```

## 2) Generate a big TSV locally (10k rows)

We'll synthesize "student grades" so everyone has the same file.

```bash
# If python3 is missing on Ubuntu:
# sudo apt update && sudo apt install -y python3

python3 - <<'PY'
import random, string
random.seed(42)
first = ["Ava","Ben","Cam","Diya","Eli","Finn","Gia","Hao","Ira","Jae","Kai","Luz","Mia","Nia","Oli","Paz","Raj","Sol","Tao","Uma","Vic","Wes","Xiu","Yas","Zoe"]
last  = ["Arden","Bose","Cho","Diaz","Evan","Faro","Gupta","Hale","Ivanov","Jin","Kim","Lopez","Mori","Ng","Omar","Patel","Quinn","Reed","Singh","Trent","Ueda","Voss","Wu","Xu","Young","Zimmer"]
asg   = [f"A{i:02d}" for i in range(1,21)]
print("student_id\tfirst\tlast\tassignment\tscore\tmax\tsection")
for _ in range(10000):
    sid = "S" + "".join(random.choice(string.digits) for _ in range(6))
    f = random.choice(first); l = random.choice(last); a = random.choice(asg)
    mx = random.choice([10,20,50,100]); sc = random.randint(0,mx); sec = random.choice(["A","B","C"])
    print(f"{sid}\t{f}\t{l}\t{a}\t{sc}\t{mx}\t{sec}")
PY

# Save to a file:
python3 - <<'PY' > grades.tsv
import random, string
random.seed(42)
first = ["Ava","Ben","Cam","Diya","Eli","Finn","Gia","Hao","Ira","Jae","Kai","Luz","Mia","Nia","Oli","Paz","Raj","Sol","Tao","Uma","Vic","Wes","Xiu","Yas","Zoe"]
last  = ["Arden","Bose","Cho","Diaz","Evan","Faro","Gupta","Hale","Ivanov","Jin","Kim","Lopez","Mori","Ng","Omar","Patel","Quinn","Reed","Singh","Trent","Ueda","Voss","Wu","Xu","Young","Zimmer"]
asg   = [f"A{i:02d}" for i in range(1,21)]
print("student_id\tfirst\tlast\tassignment\tscore\tmax\tsection")
for _ in range(10000):
    sid = "S" + "".join(random.choice(string.digits) for _ in range(6))
    f = random.choice(first); l = random.choice(last); a = random.choice(asg)
    mx = random.choice([10,20,50,100]); sc = random.randint(0,mx); sec = random.choice(["A","B","C"])
    print(f"{sid}\t{f}\t{l}\t{a}\t{sc}\t{mx}\t{sec}")
PY
```

Quick checks:

```bash
head -n 5 grades.tsv | column -t -s $'\t'
wc -l grades.tsv
```

!!! tip "Tabs vs spaces"
    Many tools default to spaces. For a TSV, use `-s $'\t'` with column and `cut -f`.

## 3) Pipes & redirects in 5 minutes

```bash
grep -E '^S[0-9]{6}' grades.tsv | head -n 3
cut -f1,4-7 grades.tsv | head -n 3
cut -f7 grades.tsv | sort | uniq -c | sort -nr
grep -E $'\tA07\t' grades.tsv | wc -l         # how many rows with assignment A07
grep -E $'\tA07\t' grades.tsv > a07.tsv       # save
grep -E 'notfound' grades.tsv 2> errors.log   # stderr redirect example
```

## 4) One-liner challenges (type first, then refine)

### Top 10 students by total percent in section B
**Hint:** group by student_id, sum score/max where section=="B", then sort desc.  
**Tools:** awk -F '\t', associative arrays, sort -k2,2nr | head -10, column -t.

### Median score for assignment A07 across all sections
**Hint:** filter rows where assignment=="A07", print score, sort numerically, pick the middle.  
**Tools:** awk, sort -n, maybe numfmt (optional).

### How many unique students scored 100% on any assignment?
**Hint:** score == max → gather student_id → unique count.  
**Tools:** awk, sort -u | wc -l.

**Deliverables:** paste each one-liner and its numeric/text answer.

## 5) Bonus: pretty output

Pipe to column:

```bash
... | column -t -s $'\t'
```

## 6) Exit Ticket (email)

**Subject:** DE M2 Exit Ticket – <Your Name>  
**Paste:**

- Your three one-liners (exact commands).
- The three answers (numbers / top 10 table).