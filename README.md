# Week 2 assignment — talk to an API and change something in it

**Due:** before Session 3.
**Time:** about 90 minutes. If you are past two hours, stop and message me — being
stuck for two hours means the brief is unclear, and that is my problem, not yours.

Everything here uses only what we did in Week 2: `requests`, `json`, `csv`,
`try`/`except`, loops, functions. Nothing new, nothing clever.

The API is **https://api.studiotypo.xyz** — the same coffee API from Session 2.
Open it in a browser and you will see the live board. Keep it open in a tab while
you work; you will watch your own requests land on it.

Reads need nothing. **Writes need this header:**

```
X-API-Key: dataeko-2026
```

---

## Your seat

Every one of you owns one seat. **You only ever write to your own seat.**
Find yours:

| Seat | Name |
|---|---|
| `seat-01` | Abhishek Singh Lagwal |
| `seat-02` | Aditya Narayan Gour |
| `seat-03` | Chaitna Sree Anisetty |
| `seat-04` | Deekshitha Karri |
| `seat-05` | Himanshu Singh |
| `seat-06` | Prasanna Lakshmi Chettu |
| `seat-07` | R.sidhu |
| `seat-08` | Roop vardhan |
| `seat-09` | Shivang Gupta |
| `seat-10` | SUMIT VERMA |
| `seat-11` | Yuvanandhan Raja |

Everywhere below that says `seat-07`, put your own seat instead.

---

## Part 1 — read the order book

`GET https://api.studiotypo.xyz/book` is a fixed record of 100 coffee orders.
Nobody can change it, including me. It will read the same on Sunday night as it
does now.

Write **`book_report.py`** that:

1. Fetches every order from `/book`.
2. Writes them to **`orders.csv`** with exactly these columns, in this order:
   `id,drink,size,sugar,name,status`
3. Prints three numbers:
   - how many orders are in the book
   - how many have `status` of `ready`
   - which drink was ordered most, and how many times

Put those three numbers in **`ANSWERS.md`** as well, so I can read them without
running your code.

> **Read the whole response before you trust it.** The JSON has more in it than
> the list of orders. One of the other fields will tell you whether you actually
> received everything you asked for. If your first number is not a round 100,
> that field is where the answer is.

---

## Part 2 — work a shift

Now you write. Everything in this part happens on **your seat only**.

Write **`shift.py`** that does the following, in order.

**Step 0 — clear your seat.**
`GET` the orders for your seat and `DELETE` each one you find.
Your script has to be safe to run twice. Run it, run it again, and the result
should be identical both times. This step is what makes that true.

**Step 1 — a customer orders, and you make it.**

```
POST   drink "latte", size "medium", sugar 2, name "Anita", seat "seat-07"
PATCH  it to  "brewing"
PATCH  it to  "ready"
```

**Step 2 — a customer changes their mind completely.**

```
POST   drink "espresso", size "small", sugar 3, name "Bharat", seat "seat-07"
```

Then they come back. They no longer want that order at all. They now want
**a large mocha, under the name Chandni, and it has not been started yet.**
It is the same order slot — same id — but nothing about the drink is what it
was. Make the order match what they now want.

**Step 3 — a customer walks out.**

```
POST   drink "americano", size "large", sugar 0, name "Dev", seat "seat-07"
```

Then they leave before you make it. Remove the order.

**Step 4 — write down where you ended up.**
`GET` the orders for your seat and write them to **`seat.csv`**, same columns as
before, plus `seat` at the front.

When the script finishes, your seat should hold **exactly two orders**. If it
holds three, or four, or six, re-read Step 0.

---

## Handling failure

Two things in your code, both from Session 2:

- **`timeout=10` on every single request.** No exceptions.
- At least one `try` / `except requests.exceptions.RequestException` around a
  request, which prints something useful and does not crash the script.

You do not need to retry. You do need to not hang forever.

---

## What to hand in

Fork this repository, make a branch, and open a pull request that adds one
folder — **`submissions/<your-github-username>/`** — containing:

```
submissions/your-username/
  book_report.py
  shift.py
  orders.csv
  seat.csv
  ANSWERS.md
```

`ANSWERS.md` needs the three numbers from Part 1, your seat, and one or two
sentences on anything that surprised you. That last part is not marked. I read
it anyway.

**Do not commit your `venv/` folder.** If `git status` shows hundreds of files,
that is what happened. There is a `.gitignore` in this repo already.

### The Git bit, step by step

```bash
# 1. Fork this repo on GitHub (the Fork button, top right)

# 2. Clone YOUR fork, not mine
git clone https://github.com/<your-username>/dataeko-week2-assignment.git
cd dataeko-week2-assignment

# 3. Branch
git checkout -b week2-<your-username>

# 4. Do the work in submissions/<your-username>/

# 5. Commit and push
git add submissions/<your-username>
git commit -m "Week 2 submission: <your name>"
git push origin week2-<your-username>

# 6. Open a pull request from your branch to this repo
```

One folder each means nobody's work collides with anybody else's.

---

## How it is marked

Out of 100. There is no second tier and no bonus round.

| | |
|---|---|
| **Part 1 correct** — three numbers right, `orders.csv` has 100 rows and the right columns | **35** |
| **Part 2 correct** — your seat ends in exactly the right state | **35** |
| **Failure handling** — `timeout=` everywhere, one real `except` that does something | **10** |
| **Git** — forked, branched, one clean folder, sensible commit message, no `venv/` | **20** |

Part 2 is marked by reading your seat straight off the API, so it is worth being
sure your script really finished rather than nearly finished.

---

## If you get stuck

- **401** — you left the key header off a write. Reads do not need it; writes do.
- **404** — right URL shape, but that id is not there. Did you already delete it?
- **415** — you forgot `Content-Type: application/json`, or you sent form data.
- **422** — your JSON parsed fine and the *values* are wrong. Read the `problems`
  list in the response; it names every field that is unhappy.
- **405** — right URL, wrong verb for it. `/book` is read-only on purpose.

Every error the API returns has a `hint` field written for exactly this moment.
Print the whole response body before you ask anyone anything.
