# Codeforces Practice Sheet for Top-Company Interviews (800 → 2200+)

> Companion to the Uber / Google / Meta / Amazon interview sheets. This is a **broad practice bank** of real Codeforces problems across **all topics and all difficulties (rating 800 → 2900)**, with **direct links** and a **company column** showing which top companies most lean on each pattern.
> Data pulled live from the **official Codeforces API** (`problemset.problems`), so every link is valid and every rating/tag is accurate.
> Spreadsheet: **`Codeforces_Practice_Questions.csv`** — **697 problems**.

---

## What's in the CSV

Columns: `Topic | Problem | Rating | Difficulty Band | Tags | Link | Companies (pattern-relevant)`

- **Link format:** `https://codeforces.com/problemset/problem/{contestId}/{index}` (canonical Codeforces URL — opens directly in a browser).
- **Topic:** the primary interview-relevant pattern (a problem may carry several tags; the most useful one is used for grouping).
- **Companies:** which of **Google / Amazon / Meta / Microsoft / Uber / Apple** most emphasize that pattern in interviews. *Codeforces problems are not officially company-tagged — this is a pattern→company heuristic to help you target prep, not a claim that the exact problem was asked.*

### Coverage (697 problems)

**By difficulty band**

| Band | Rating | Count |
|------|--------|-------|
| Easy | 800–1100 | ~164 |
| Medium | 1200–1500 | ~191 |
| Hard | 1600–1900 | ~190 |
| Very Hard | 2000–2200+ | ~152 |

Every 100-point bucket from **800 to 2100** has ~40–50 problems; **2200+** has ~60 (ranging up to 2900).

**By topic** (≈ counts): Dynamic Programming 45 · Graphs 45 · Data Structures 45 · Two Pointers 45 · Strings 45 · Greedy 45 · Math 45 · Constructive 45 · Implementation 45 · Binary Search 44 · Number Theory 41 · Brute Force 40 · Trees 36 · Sorting 36 · Bitmasks 28 · DFS/BFS 24 · Combinatorics 18 · Union-Find (DSU) 11 · Shortest Paths 6 · Divide & Conquer 5 · Hashing 3.

---

## How Codeforces rating maps to interview difficulty

| CF Rating | Rough interview equivalent | Use it for |
|-----------|---------------------------|------------|
| **800–1000** | LeetCode Easy | Warm-ups, syntax speed, edge cases |
| **1100–1400** | LeetCode Easy–Medium | Core patterns, the bulk of phone screens |
| **1500–1700** | LeetCode Medium | The "interview sweet spot" — most onsite coding lands here |
| **1800–2000** | LeetCode Medium–Hard | Hard onsite problems, Google/▢ follow-ups |
| **2100–2200+** | LeetCode Hard+ | Stretch goal — pattern mastery, tie-breaker rounds |

> For most SDE-1 / new-grad loops, **living comfortably at 1300–1700 solves the vast majority of real interview problems.** Push past 1800 only after that's automatic.

---

## Suggested practice strategy

1. **Filter the CSV by Topic** and do problems in ascending Rating — this builds each pattern from easy to hard (the fastest way to "see" a pattern).
2. **Filter by Company** if you have a specific loop coming up (e.g., sort to `Google` rows for math/graphs/DP-heavy prep, `Meta` for strings/two-pointers, `Amazon` for arrays/greedy/trees).
3. **Time-box like a real interview:** 800–1300 in ≤15 min, 1400–1700 in ≤25–30 min, 1800+ in ≤40 min. If you blow the budget, read the editorial, then re-solve from scratch 2 days later.
4. **Pattern-first, not problem-first.** The point is to recognize the pattern instantly in an interview. After solving, write one line: "trigger → technique" (e.g., *"k-th smallest in range → binary search on answer"*).
5. **Translate to interview form.** Codeforces I/O is stdin/stdout; interviews want a clean function + complexity analysis + edge cases narrated aloud. Practice restating each solution as a method signature.

### A balanced 1-week sampler (≈ how to use it)

| Day | Focus | From CSV |
|-----|-------|----------|
| 1 | Arrays/Greedy/Two Pointers | 800→1500 rows in those topics |
| 2 | Strings + Hashing | Strings 800→1700 |
| 3 | Binary Search (incl. on answer) | Binary Search 1000→1900 |
| 4 | Trees + DFS/BFS | Trees + DFS/BFS 900→1800 |
| 5 | Graphs + Shortest Paths + DSU | 1100→2000 |
| 6 | Dynamic Programming | DP 1000→2000 (ladder) |
| 7 | Mixed mock | 5 random rows, 1500–1800, timed |

---

## Notes & caveats

- **403 when testing links via scripts?** That's just Codeforces' Cloudflare anti-bot blocking automated clients — every link opens normally in a browser.
- **Company column is heuristic** (pattern → company emphasis). Treat it as a *targeting aid*, not verified per-problem company tags. For verified per-company LeetCode questions, use the four company sheets (Uber / Google / Meta / Amazon) already on your Desktop.
- Want a tighter list? Filter the CSV to your target rating band + 2–3 weak topics and you'll have a focused ~80-problem grind set.

*Source: Codeforces public API (`https://codeforces.com/api/problemset.problems`), fetched 2026-06-30. 697 problems selected for topic + difficulty spread, ranked within each topic/bucket by community solve-count (i.e., the most canonical, well-known problems first).*
