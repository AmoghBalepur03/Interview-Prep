# Amazon SDE-1 (SDE I / New-Grad & Early-Career) — Master Interview Question Sheet

> Compiled from real interview experiences on **LeetCode Discuss, GeeksforGeeks, Reddit (r/leetcode, r/cscareerquestions), Blind, Medium**, official **amazon.jobs** OA prep, and dedicated question banks (**CodeJeet, magicsheet, Crackr, InterviewSolver, PapersAdda, krishnadey30 company-wise repo**).
> Coverage window: mostly **2024 → 2026** cycles (India + US).
> Last updated: 2026-06-30.
>
> **How to use:** Amazon is **~50% coding / ~50% Leadership Principles (LP)**. Almost *every* round mixes a coding problem with 2–3 LP behavioral questions, and there is a dedicated **Bar Raiser** (external interviewer with veto power). Drill the **top ~75 frequency problems** (Tier S + A) AND prepare **10–15 STAR stories** mapped to the 16 LPs with quantified metrics. Companion spreadsheet: `Amazon_SDE1_Questions.csv`.

---

## 1. Interview Process (SDE-1)

| Stage | Round | Format | Notes |
|------|-------|--------|-------|
| 0 | **Recruiter screen** | 30 min call | Role fit, timelines, "Why Amazon?". Keep intro ~2 min. |
| 1 | **Online Assessment (OA)** | ~90 min, HackerRank/CodeSignal, 7-day window | **2 DSA problems** (medium / medium-hard, auto-graded with partial scoring) + **Work Simulation** (email/scenario modules, e.g. debug a product page not loading) + **Workstyle Assessment** (LP questionnaire: most/least likely, Strongly Agree→Disagree). New-grads sometimes also get an **OA1 debugging** section. |
| 2 | **Phone Screen** *(sometimes)* | 45–60 min | 1 coding problem + 1–2 LP questions. Often skipped for campus hires. |
| 3 | **Onsite Loop** | **3–5 rounds**, 45–60 min each | Mix of: 1–2 **DSA/coding**, sometimes **LLD** (class design), occasionally a **bounded system-design / project deep-dive**, and a dedicated **behavioral/LP** round. **Every round** includes LP questions. |
| 4 | **Bar Raiser** | 45–60 min | Interviewer from **outside the hiring team**, specially trained. **Veto power** — a No-Hire here on LP grounds blocks the offer even if all else passed. Heavy STAR deep-dives. |
| 5 | **Debrief / Hiring Committee** | async | Interviewers align; decision is committee-based, not by any single interviewer. Amazon uses a confidence/leveling discussion. |

**Total timeline:** ~3–6 weeks (India loops sometimes longer between rounds).
**Difficulty mix (tagged set):** ~19–24% Easy, ~55–60% Medium, ~16–21% Hard. Mediums dominate.
**Topic shares (PapersAdda 2025 est.):** Arrays & Sliding Window ~76%, Trees ~70%, Strings & Hashing ~62%, Two Pointers ~56%, DP ~55%, Graphs (BFS/DFS) ~48%, Heaps ~38%, Stack/Monotonic ~39%, Backtracking ~30%.

> **Critical Amazon-specific facts:**
> - **~50% of the loop is Leadership Principles.** Strong LeetCode alone is *not* enough — many candidates are rejected on LP/Bar Raiser despite solving everything.
> - **Bar Raiser has veto.** Treat their pushback ("Why that approach? Trade-offs? What would you change?") as a *test*, not an attack — don't get defensive.
> - **STAR + "I" over "we" + metrics.** "I led a project" is useless; "I escalated a blocked dependency (Dive Deep), cutting launch delay by 3 weeks" is evidence. Interviewers ask "what was the impact?" if you don't lead with a number.
> - **Don't reuse the same story** for different LPs across rounds — interviewers compare notes in debrief.
> - **Coding:** explain approach + edge cases **before** writing; clean, bug-free, syntactically correct code (OA expects real code, not pseudo). Aim to solve **2 problems in ~1 hour**.
> - **System design** is usually **not a full round for SDE-1** (it's SDE-2+), but expect **project deep-dives** and bounded design/LLD discussions.
> - **GenAI question (2026):** some HM rounds now ask "How do you use LLMs effectively? Do you verify the output?" — have a thoughtful answer.

---

## 2. The 16 Leadership Principles (and SDE-1 priority)

All 16 are fair game, but **SDE-1 loops most heavily probe** the ones in **bold**:

1. **Customer Obsession** · 2. **Ownership** · 3. **Invent and Simplify** · 4. Are Right, A Lot · 5. **Learn and Be Curious** · 6. Hire and Develop the Best · 7. **Insist on the Highest Standards** · 8. Think Big · 9. **Bias for Action** · 10. Frugality · 11. **Earn Trust** · 12. **Dive Deep** · 13. **Have Backbone; Disagree and Commit** · 14. **Deliver Results** · 15. Strive to be Earth's Best Employer · 16. Success and Scale Bring Broad Responsibility.

> For SDE-1, prioritize stories showing **execution + growth**: Deliver Results, Bias for Action, Learn and Be Curious, Dive Deep, Ownership, Customer Obsession.

---

## 3. REAL Questions From Recent Experiences (2024–2026)

### 3.1 Coding/DSA problems actually reported in loops

| Problem (LeetCode) | Difficulty | Where reported |
|--------------------|-----------|----------------|
| **Clone Graph** (LC 133) | Medium | India SDE-1 R2 (dry-run before coding) |
| **Next Palindromic / Next Palindrome Number** | Medium | India SDE-1 R2 |
| **Detonate the Maximum Bombs** (LC 2101) + directed-graph variant | Medium/Hard | SDE-1 onsite R1 |
| **Aggressive Cows** (binary-search-on-answer) | Medium | SDE-1 onsite R2 |
| **Next Greater Element** (LC 496/503) | Easy/Med | HM round |
| **String + Prefix Sum** problem | Medium | SDE-1 R1 |
| **Graph + Dynamic Programming** problem | Medium–Hard | SDE-1 R2 |
| **Hash Map / "maps"** problem | Medium | SDE-1 R2 |
| **Sliding Window** problem | Medium | SDE-1 R4 |
| LLD: clean class structures w/ TC/SC | LLD | SDE-1 LLD round |

### 3.2 Top "high-probability" coding questions (by frequency)

| # | Problem (LeetCode) | Difficulty | Freq |
|---|--------------------|-----------|------|
| 1 | **Two Sum** (LC 1) | Easy | 100% |
| 2 | **LRU Cache** (LC 146) | Medium | 89% |
| 3 | **Trapping Rain Water** (LC 42) | Hard | 87% |
| 4 | **Number of Islands** (LC 200) | Medium | 86% |
| 5 | **Longest Substring Without Repeating Characters** (LC 3) | Medium | 84% |
| 6 | **Best Time to Buy and Sell Stock** (LC 121) | Easy | 83% |
| 7 | **Group Anagrams** (LC 49) | Medium | 80% |
| 8 | **Add Two Numbers** (LC 2) | Medium | 79% |
| 9 | **Reorganize String** (LC 767) | Medium | 79% |
| 10 | **3Sum** (LC 15) | Medium | 79% |
| 11 | **Longest Palindromic Substring** (LC 5) | Medium | 79% |
| 12 | **Merge Intervals** (LC 56) | Medium | 77% |
| 13 | **Container With Most Water** (LC 11) | Medium | 77% |
| 14 | **Median of Two Sorted Arrays** (LC 4) | Hard | 76% |
| 15 | **Koko Eating Bananas** (LC 875) | Medium | 75% |
| 16 | **Valid Parentheses** (LC 20) | Easy | 75% |
| 17 | **Course Schedule** (LC 207) | Medium | 75% |
| 18 | **Merge k Sorted Lists** (LC 23) | Hard | 74% |
| 19 | **Rotting Oranges** (LC 994) | Medium | 74% |
| 20 | **Subarray Sum Equals K** (LC 560) | Medium | 73% |
| 21 | **Search in Rotated Sorted Array** (LC 33) | Medium | 71% |
| 22 | **Top K Frequent Elements** (LC 347) | Medium | 71% |
| 23 | **Maximum Subarray** (LC 53) | Medium | 71% |
| 24 | **Copy List with Random Pointer** (LC 138) | Medium | 70% |
| 25 | **Word Ladder** (LC 127) | Hard | 69% |

### 3.3 LP / Behavioral questions actually asked

- "Tell me about a time you **handled tasks with a strict deadline**." *(asked by multiple interviewers)*
- "A scenario where you had to **deep dive**" (extensive follow-ups).
- "A time you **missed a commitment / deadline**" (deep probing).
- "A time you had to **convince someone** of your approach."
- "Tell me about a **conflict with a teammate / manager**. How did you resolve it?"
- "A time you **disagreed with your manager**. What happened?"
- "Tell me about a **significant technical challenge** you faced."
- "Describe a situation where you had to **solve a problem with limited / incomplete information**."
- "A complex problem you solved — how did you **identify the root cause**?"
- "Something you did that was **outside your scope / job description**."
- "A **mistake** you made and how you fixed it / what you learned."
- **GenAI (2026):** "How do you make the best use of LLMs? Do you verify/validate the results? How?"

> Bar Raiser pushback to expect on every story: *"Why did you choose that approach? What were the trade-offs? What would you do differently with more time? What was the measurable impact?"*

---

## 4. Master DSA Question Bank (frequency-ranked)

Frequency = share of recent Amazon reports mentioning the problem (CodeJeet, ~2024–2026). **Drill Tier S + A first.** Full ranked list (with acceptance rates + topics) is in **`Amazon_SDE1_Questions.csv`**.

### Tier S — Must-do / near-guaranteed (60%–100%)

Two Sum · LRU Cache · Trapping Rain Water · Number of Islands · Longest Substring Without Repeating Characters · Best Time to Buy and Sell Stock · Group Anagrams · Add Two Numbers · Reorganize String · 3Sum · Longest Palindromic Substring · Merge Intervals · Container With Most Water · Median of Two Sorted Arrays · Koko Eating Bananas · Valid Parentheses · Course Schedule · Merge k Sorted Lists · Longest Common Prefix · Rotting Oranges · Subarray Sum Equals K · Merge Sorted Array · Search in Rotated Sorted Array · Top K Frequent Elements · Maximum Subarray · Longest Consecutive Sequence · Copy List with Random Pointer · LCA of a Binary Tree · Generate Parentheses · Palindrome Number · Word Ladder · Majority Element · Product of Array Except Self · Merge Two Sorted Lists · Jump Game · Climbing Stairs · House Robber · Letter Combinations of a Phone Number · Course Schedule II · Word Search · Sliding Window Maximum · Spiral Matrix · Valid Anagram · Rotate Image · Next Permutation · Analyze User Website Visit Pattern · Coin Change · Task Scheduler · Kth Largest Element in an Array · Remove Duplicates from Sorted Array · Meeting Rooms II · Find Median from Data Stream · Roman to Integer · Subsets · Merge Strings Alternately · Unique Paths · Insert Delete GetRandom O(1) · Find Peak Element · Pascal's Triangle · Word Break · First Missing Positive · Asteroid Collision · Move Zeroes · Jump Game II · Reverse Integer · Sort Colors · Largest Rectangle in Histogram · Rotate Array · N-Queens · Find First and Last Position of Element in Sorted Array · Longest Repeating Character Replacement · Fruit Into Baskets · Min Stack · Best Time to Buy and Sell Stock II.

### Tier A — Frequently asked (45%–60%)

Minimum Window Substring · Binary Tree Maximum Path Sum · Valid Sudoku · 4Sum · Identify the Largest Outlier in an Array · Single Number · Max Consecutive Ones III · Serialize and Deserialize Binary Tree · Palindrome Linked List · Reverse Nodes in k-Group · Capacity To Ship Packages Within D Days · Sqrt(x) · Daily Temperatures · Search a 2D Matrix · Single Element in a Sorted Array · Binary Tree Right Side View · Reverse Linked List · Binary Tree Zigzag Level Order Traversal · Zigzag Conversion · LFU Cache · Regular Expression Matching · All Nodes Distance K in Binary Tree · Set Matrix Zeroes · Search Insert Position · Contains Duplicate · Remove Element · Find the Duplicate Number · Binary Tree Level Order Traversal · Next Greater Element II · First Unique Character in a String · Missing Number · Word Search II · Pow(x,n) · Candy · Permutations · String Compression · Gas Station · Combination Sum · Two Sum II · Find the Index of the First Occurrence in a String · Concatenated Words · Maximum Product Subarray · String to Integer (atoi) · Integer to Roman · Flood Fill · Reorder List · Longest Increasing Subsequence · Next Greater Element I · Validate Binary Search Tree · Basic Calculator · Integer to English Words · Remove Nth Node From End of List · Valid Palindrome · Middle of the Linked List · Diameter of Binary Tree · Odd Even Linked List · Linked List Cycle · Reverse Words in a String · Word Break II · Edit Distance · Plus One · Partition Equal Subset Sum · Maximum Profit in Job Scheduling · 3Sum Closest · Search a 2D Matrix II · Sum of Subarray Minimums · Intersection of Two Arrays · Split Array Largest Sum · Number of Provinces · Basic Calculator II · Happy Number · Evaluate Division · Palindrome Partitioning · Balanced Binary Tree · Largest Number · K Closest Points to Origin.

### Tier B — Regular (40%–45%)

Contains Duplicate II · Plates Between Candles · Max Consecutive Ones · Longest Valid Parentheses · Rotate List · Sudoku Solver · Decode String · Minimum Size Subarray Sum · Time Based Key-Value Store · Evaluate Reverse Polish Notation · Isomorphic Strings · Search Suggestions System · Peak Index in a Mountain Array · Symmetric Tree · Add Binary · Remove Duplicates from Sorted Array II · Intersection of Two Linked Lists · Rotate String · Count Primes · Subarrays with K Different Integers · Maximum Depth of Binary Tree · Find Minimum in Rotated Sorted Array · Same Tree · Construct Binary Tree from Preorder and Inorder · Find All Anagrams in a String · Vertical Order Traversal of a Binary Tree · Maximum Width of Binary Tree · LCA of a BST · Word Ladder II · Random Pick with Weight · Permutation in String · Reverse Linked List II · Surrounded Regions · Find K Closest Elements · LCA of a Binary Tree III · Minimum Health to Beat Game · Reverse String · Pacific Atlantic Water Flow · Minimum Path Sum · Rearrange Array Elements by Sign · Simplify Path · Populating Next Right Pointers in Each Node · Linked List Cycle II · Top K Frequent Words · Sort List · Subsets II · Kth Smallest Element in a BST · Target Sum · Reverse Pairs · Min Cost Climbing Stairs · Maximal Rectangle · Partition Labels · Online Stock Span.

### Tier C — Appears occasionally (34%–40%)

Squares of a Sorted Array · Binary Search · Cheapest Flights Within K Stops · Sort Characters By Frequency · Divide Two Integers · Design Parking System · House Robber III · Length of Last Word · Invert Binary Tree · Boats to Save People · Majority Element II · Minimum Number of Days to Make m Bouquets · Valid Palindrome II · Remove Duplicates from Sorted List · Kth Largest Element in a Stream · Non-overlapping Intervals · Critical Connections in a Network · Remove K Digits · Frequency of the Most Frequent Element · Set Mismatch · Contiguous Array · Snakes and Ladders · Swap Nodes in Pairs · Is Subsequence · Trapping Rain Water II · Open the Lock · Count and Say · Maximum Width Ramp · Furthest Building You Can Reach · Sort an Array · Longest Palindromic Subsequence · Smallest Range Covering Elements from K Lists · Binary Tree Inorder Traversal · Kth Missing Positive Number · Design HashMap · Can Place Flowers · Accounts Merge · House Robber II · Exclusive Time of Functions · Making A Large Island · Palindromic Substrings · Design Twitter · Decode Ways · Maximum Units on a Truck · Unique Paths II · Combination Sum II · Triangle · 01 Matrix · Implement Trie (Prefix Tree) · Russian Doll Envelopes · Maximum Points You Can Obtain from Cards · Design Tic-Tac-Toe · Max Area of Island · Delete Node in a BST · Clone Graph · Longest Common Subsequence · Shortest Path in Binary Matrix · Convert Sorted Array to BST · Design In-Memory File System · Number of Distinct Islands · Alien Dictionary · Design Add and Search Words Data Structure · Flatten Binary Tree to Linked List · Maximal Square · Subarray Product Less Than K · Increasing Triplet Subsequence · Diagonal Traverse · Insert Interval · Is Graph Bipartite? · Number of Connected Components in an Undirected Graph · Burst Balloons.

> Note: Amazon's tagged set includes some **SQL/Database** problems (Combine Two Tables, Second Highest Salary, Rising Temperature, etc.) and a few **Amazon-internal/OA-style** problems (Analyze User Website Visit Pattern, Search Suggestions System, Identify the Largest Outlier, Minimum Health to Beat Game). These show up mainly in the **OA**.

---

## 5. Prep Plan (4–8 weeks)

- **Wk 1–2:** Tier S coding (top 50). Patterns: arrays/sliding window, two pointers, hashing, binary search, stack. Solve each ≤25 min; explain approach + edge cases before coding.
- **Wk 3:** Trees/BST, graphs (BFS/DFS, islands, course schedule, clone graph), heaps, intervals. Tier A.
- **Wk 4:** DP (coin change, house robber, word break, LIS, edit distance), backtracking. Begin **OA simulations** (HackerRank-style, timed, 2 problems / 90 min) + Work-Simulation practice.
- **Wk 5–6:** **Behavioral** — write **10–15 STAR stories**, each mapped to 2–3 LPs, with **metrics**. Rehearse deep follow-ups ("why / trade-offs / what'd you change / impact?"). Don't reuse stories.
- **Wk 7:** LLD basics (clean class design, SOLID, TC/SC) + project deep-dive prep (architecture, your specific contribution, alternatives considered).
- **Wk 8:** Full mock loops — timeboxed coding + LP in each round. Prepare a thoughtful **"how I use LLMs"** answer and questions for the interviewer.

---

## 6. Sources

- LeetCode Discuss SDE-1 experiences (2025–2026: "Offer Received", "Verdict: selected", Amazon India SDE-1, Bangalore offers).
- GeeksforGeeks ("SDE-1 Amazon Experience | Verdict – Selected"), Medium (Shiwangi Kumari SDE-1).
- Official **amazon.jobs** SDE OA prep page (Coding + System Design + Work Styles).
- Process guides: PapersAdda (Amazon Interview Process 2026 + Bar Raiser), codinginterview.com, sirjohnnymai, InterviewEra, ManyOffer, Interview Sidekick.
- Question banks: CodeJeet (frequency-ranked), magicsheet (1898), Crackr, InterviewSolver (100), PapersAdda top-25, krishnadey30 company-wise repo (6-month list).

*Note: SDE-1 = entry/early-career. System design is generally **not** a standalone SDE-1 round (becomes central at SDE-2+), but project deep-dives, LLD, and bounded design discussions appear. The OA and LP/Bar Raiser components are the biggest differentiators vs. other FAANG loops — budget real prep time for behavioral, not just LeetCode.*
