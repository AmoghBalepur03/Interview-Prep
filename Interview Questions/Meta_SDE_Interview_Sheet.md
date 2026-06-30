# Meta / Facebook SWE (E3 New-Grad & E4 Mid-Level) — Master Interview Question Sheet

> Compiled from real interview experiences on **LeetCode Discuss, GeeksforGeeks, Reddit (r/leetcode, r/cscareerquestions), Blind, Medium, interviewing.io, IGotAnOffer, Hello Interview**, and dedicated question banks (**CodeJeet, magicsheet, Crackr, InterviewSolver, Verve**).
> Coverage window: mostly **2024 → 2026** cycles (India + US).
> Last updated: 2026-06-30.
>
> **How to use:** Meta is the most "studiable" FAANG — it **reuses pre-approved, frequency-ranked questions**, so the **top ~75 Meta-tagged problems give the highest ROI**. You must solve **2 problems in ~35 min** per round in **CoderPad with no execution**. Drill speed, narrate, and prepare 5–6 STAR stories (behavioral is an **unrecoverable fail**). Companion spreadsheet: `Meta_SDE_Questions.csv`.

---

## 1. Interview Process (E3 / E4 SWE)

| Stage | Round | Format | Notes |
|------|-------|--------|-------|
| 0 | **Recruiter screen** | 30 min call | Background, impact, "why Meta", and **track choice: Product vs Infrastructure** (decides your design round). |
| 0.5 | **Online Assessment (OA)** | CodeSignal, 90 min, proctored (video+mic) | New since 2025. **One 4-stage problem** (e.g., in-memory key-value DB, or cloud file-storage), stages unlock sequentially (core → TTL → versioning/point-in-time → concurrency/delete). Most don't finish all 4. No AI/search. |
| 1 | **Technical Phone Screen** | 45 min, CoderPad | 2 problems in ~35 min (often 1 medium + 1 easy/medium). LC easy–medium, Meta-tagged. No code execution. |
| 2 | **Coding 1 (onsite)** | 45 min, CoderPad | 2 problems / 35 min. Edge-case heavy, "classic DS with a Meta twist". |
| 3 | **AI-Enabled Coding (onsite)** | 60 min, CoderPad + AI assistant | Replaces one coding round (Q4 2025+). Multi-part problem on an existing multi-file codebase. **Scored like a code review** — prompt, read the diff, catch hallucinations, verify. Pasting AI output blindly = fail. |
| 4 | **System Design** *(Infra)* **or Product Architecture** *(Product)* | 45 min, Excalidraw | E4+ (E3 is conversational, not real design). Determines **level**. |
| 5 | **Behavioral ("Jedi" / Getting to know you)** | 45 min | 5 signals. **Unrecoverable fail** — a No-Hire here can't be offset by strong coding/design. |
| 6 | **Team Match → Offer** | HM calls | Happens **before** the offer (since 2023, replaced "Bootcamp"). Can extend timeline. |

**Total timeline:** ~4–8 weeks (longer if team match is slow).
**Difficulty mix (tagged set):** ~24% Easy, ~61% Medium, ~15% Hard.
**Topic shares (Crackr/CodeJeet):** Array ~49%, String ~29%, Hash Table ~18%, DFS/Trees ~16%, Two Pointers ~15%, DP ~16% (tagged) — **but see DP note below**.

> **Critical Meta-specific facts (from interviewer + candidate reports):**
> - **Meta almost only asks from its recently-asked, frequency-ranked list.** Drilling the **top ~75 by frequency** beats random grinding. Hards are usually *easier variants* of the tagged hard.
> - **~35 min, two problems.** Pace: ~15–20 min each. The 2nd problem often tests **clean, bug-free, tested code** quickly.
> - **Bugs are sometimes tolerated** on the "juicy" first problem (empty/null/out-of-scope inputs) so you can code fast — Meta is the *only* FAANG that explicitly relaxes this. But an **incomplete algorithm usually = No Hire**.
> - **Dynamic Programming is effectively banned** in Meta coding rounds (per Meta interviewers). Don't over-index on DP for coding (it still appears in the *tagged* set as variants/rare).
> - **You can't run code.** Practice in plain CoderPad/text; trace with a concrete example out loud after coding.
> - **Decision mechanics:** binary **Hire/No-Hire + a confidence score** (only FAANG that does this → a low-confidence fail carries less weight). Design + behavioral interviewers most influence **hire AND level**; explicit **downleveling** (e.g., "No-Hire E5, Hire E4").

---

## 2. REAL Questions From Recent Experiences

### 2.1 Top "almost-guaranteed" coding questions (highest frequency, verified)

| # | Problem (LeetCode) | Difficulty | Freq |
|---|--------------------|-----------|------|
| 1 | **Minimum Remove to Make Valid Parentheses** (LC 1249) | Medium | 100% |
| 2 | **Valid Word Abbreviation** (LC 408) | Easy | 95% |
| 3 | **Binary Tree Vertical Order Traversal** (LC 314) | Medium | 93% |
| 4 | **Valid Palindrome II** (LC 680) | Easy | 93% |
| 5 | **Lowest Common Ancestor of a Binary Tree III** (LC 1650, parent pointers) | Medium | 90% |
| 6 | **Merge Sorted Array** (LC 88) | Easy | 88% |
| 7 | **Kth Largest Element in an Array** (LC 215) | Medium | 87% |
| 8 | **Pow(x, n)** (LC 50) | Medium | 87% |
| 9 | **Binary Tree Right Side View** (LC 199) | Medium | 86% |
| 10 | **Simplify Path** (LC 71) | Medium | 86% |
| 11 | **Merge Intervals** (LC 56) | Medium | 85% |
| 12 | **Basic Calculator II** (LC 227) | Medium | 84% |
| 13 | **Two Sum** (LC 1) | Easy | 84% |
| 14 | **Lowest Common Ancestor of a Binary Tree** (LC 236) | Medium | 83% |
| 15 | **Find Peak Element** (LC 162) | Medium | 83% |
| 16 | **Nested List Weight Sum** (LC 339) | Medium | 82% |
| 17 | **Random Pick with Weight** (LC 528) | Medium | 81% |
| 18 | **Sum Root to Leaf Numbers** (LC 129) | Medium | 81% |
| 19 | **Diameter of Binary Tree** (LC 543) | Easy | 79% |
| 20 | **Next Permutation** (LC 31) | Medium | 78% |
| 21 | **Top K Frequent Elements** (LC 347) | Medium | 78% |
| 22 | **Merge k Sorted Lists** (LC 23) | Hard | 77% |
| 23 | **3Sum** (LC 15) | Medium | 77% |
| 24 | **Best Time to Buy and Sell Stock** (LC 121) | Easy | 76% |
| 25 | **Buildings With an Ocean View** (LC 1762) | Medium | 75% |
| 26 | **Custom Sort String** (LC 791) | Medium | 73% |
| 27 | **Find First and Last Position in Sorted Array** (LC 34) | Medium | 72% |
| 28 | **K Closest Points to Origin** (LC 973) | Medium | 71% |
| 29 | **Clone Graph** (LC 133) | Medium | 71% |
| 30 | **Subarray Sum Equals K** (LC 560) | Medium | 71% |

### 2.2 Other commonly-reported coding problems

Valid Number (LC 65) · Range Sum of BST (938) · Dot Product of Two Sparse Vectors (1570) · Valid Palindrome (125) · Minimum Window Substring (76) · Shortest Path in Binary Matrix (1091) · Copy List with Random Pointer (138) · **Making A Large Island (827)** *(reported as a phone-screen "hard" — "if you know it you know it")* · Sliding Window Median (480) · Subsets (78) · Letter Combinations of a Phone Number (17) · Accounts Merge (721) · LRU Cache (146) · Max Consecutive Ones III (1004) · Trapping Rain Water (42) · Kth Missing Positive Number (1539) · Squares of a Sorted Array (977) · Exclusive Time of Functions (636) · Diagonal Traverse (498) · Missing Ranges (163) · Group Anagrams (49) · Continuous Subarray Sum (523) · Binary Tree Maximum Path Sum (124) · Remove Invalid Parentheses (301) · Interval List Intersections (986) · Vertical Order Traversal of a Binary Tree (987) · Robot Room Cleaner (489) · a **recursive backtracking** question (reported as a surprise/hard).

### 2.3 System Design / Product Architecture — reported prompts

**Product Architecture (SWE-Product):** Design Instagram · Design Instagram Auction/Ads System · Design LeetCode · Design Top-K Songs Widget for Spotify · Design a Price-Drop Tracker (CamelCamelCamel) · Design Ticketmaster / Ticket Booking · Design News Feed.
**System Design (SWE-Infra):** Design Ad Click Aggregator · Design an Online Game Leaderboard · Design a Chat/Messenger (WhatsApp scale) · Design Notification System (push/email/SMS) · Design a distributed cache / rate limiter · Design a data pipeline.

> E4 surprises: design rounds go **deep** ("how quad trees & geohashing work", writing SQL), and can be **hands-off** ("interviewer only said 'what else?'") — be ready to drive. 4 competencies: Problem Navigation, Solution Design, Technical Excellence, Technical Communication.

### 2.4 Behavioral ("Jedi") — real reported questions

- What project are you most proud of, and why?
- Tell me about a time you had **conflict with a coworker** — how did you resolve it?
- Tell me about a project where **requirements were unclear / kept changing** — how did you adapt?
- Give an example of **tough/critical feedback** you received.
- A time you **disagreed with your manager** / a technical decision.
- A time you wanted to change something **outside your scope**.
- A **fast decision** you had to make and live with.
- A project that **took longer than expected** / overcame external obstacles.
- The **most challenging person/team** you worked with.
- A **mistake** you made and what you learned.

**5 scored signals:** Resolving Conflicts · Driving Results · Embracing Ambiguity · Growing Continuously · Communicating Effectively. Interviewers probe deep ("What *specifically* did you do?", "How did you measure impact?", "Who disagreed?") — pick multi-layered stories with **metrics**, focus on **individual** contribution (not "we"). E4 needs feature-ownership scope, not "I implemented what was assigned" (= E3 signal).

---

## 3. Master DSA Question Bank (frequency-ranked)

Frequency = share of recent Meta reports mentioning the problem (CodeJeet, ~2024–2026). **Drill Tier S + A first** — this covers the vast majority of what Meta actually asks. Full 217-problem list with acceptance rates is in **`Meta_SDE_Questions.csv`**.

### Tier S — Near-guaranteed (62%–100%)

Minimum Remove to Make Valid Parentheses · Valid Word Abbreviation · Binary Tree Vertical Order Traversal · Valid Palindrome II · LCA of a Binary Tree III · Merge Sorted Array · Kth Largest Element in an Array · Pow(x,n) · Binary Tree Right Side View · Simplify Path · Merge Intervals · Basic Calculator II · Two Sum · LCA of a Binary Tree · Find Peak Element · Nested List Weight Sum · Random Pick with Weight · Sum Root to Leaf Numbers · Diameter of Binary Tree · Next Permutation · Top K Frequent Elements · Merge k Sorted Lists · 3Sum · Best Time to Buy and Sell Stock · Buildings With an Ocean View · Custom Sort String · Find First and Last Position of Element in Sorted Array · K Closest Points to Origin · Clone Graph · Subarray Sum Equals K · Valid Parentheses · Valid Number · Range Sum of BST · Dot Product of Two Sparse Vectors · Valid Palindrome · Minimum Window Substring · Shortest Path in Binary Matrix · Copy List with Random Pointer · Longest Common Prefix · Add Two Numbers · Sliding Window Median · Making A Large Island · Subsets · Letter Combinations of a Phone Number · Remove Nth Node From End of List · Longest Substring Without Repeating Characters · Accounts Merge · LRU Cache · Max Consecutive Ones III.

### Tier A — Frequently asked (40%–62%)

Trapping Rain Water · String to Integer (atoi) · Kth Missing Positive Number · Squares of a Sorted Array · Longest Palindromic Substring · Remove Duplicates from Sorted Array · Median of Two Sorted Arrays · Palindrome Number · Search in Rotated Sorted Array · Roman to Integer · Diagonal Traverse · Missing Ranges · Moving Average from Data Stream · Exclusive Time of Functions · Merge Two Sorted Lists · Group Anagrams · Count and Say · Climbing Stairs · Maximum Subarray · Add Strings · Binary Search Tree Iterator · Minimum Add to Make Parentheses Valid · Robot Room Cleaner · Container With Most Water · Sort Colors · Set Matrix Zeroes · Add Binary · Multiply Strings · Reverse Integer · Word Search · Generate Parentheses · Plus One · Rotate Image · Remove Invalid Parentheses · Palindromic Substrings · Continuous Subarray Sum · Closest Binary Search Tree Value · Vertical Order Traversal of a Binary Tree · Group Shifted Strings · Course Schedule · Insert into a Sorted Circular Linked List · All Nodes Distance K in Binary Tree · Convert BST to Sorted Doubly Linked List · Spiral Matrix · Regular Expression Matching · Majority Element · Insert Interval · Expression Add Operators · Find the Index of the First Occurrence in a String · Sqrt(x) · Search a 2D Matrix · Binary Tree Zigzag Level Order Traversal · Divide Two Integers · Number of Islands · Largest Rectangle in Histogram · Binary Tree Level Order Traversal · Unique Paths · Remove Duplicates from Sorted Array II · Remove Element · Jump Game · Text Justification · Validate Binary Search Tree · Product of Two Run-Length Encoded Arrays · Contains Duplicate II · Swim in Rising Water · Maximum Swap · Cutting Ribbons · Word Break · Binary Tree Maximum Path Sum · Car Pooling · Strobogrammatic Number · Capacity To Ship Packages Within D Days · Word Ladder · Find Median from Data Stream · Valid Palindrome III · Kth Smallest Element in a Sorted Matrix · 3Sum Closest.

### Tier B — Regular (30%–40%)

Best Time to Buy and Sell Stock II · Combination Sum · Decode Ways · Two Sum II · Populating Next Right Pointers in Each Node · Rotate Array · LCA of a BST · Longest Consecutive Sequence · 4Sum · Valid Sudoku · Subsets II · Maximal Rectangle · Word Break II · Permutations · Zigzag Conversion · Reverse Nodes in k-Group · Design Tic-Tac-Toe · Alien Dictionary · Shortest Distance from All Buildings · Meeting Rooms II · Product of Array Except Self · Move Zeroes · Find K Closest Elements · First Missing Positive · Wildcard Matching · Combination Sum II · Unique Paths II · Minimum Depth of Binary Tree · Binary Tree Inorder Traversal · Jump Game II · N-Queens · Search Insert Position · Construct Binary Tree from Preorder and Inorder · Rotate List · Search in Rotated Sorted Array II · Reverse Linked List · Shortest Path in a Hidden Grid · Interval List Intersections · Range Sum Query - Immutable · Frequency of the Most Frequent Element · Random Pick Index · Kth Smallest Element in a BST · Decode String · Koko Eating Bananas · Remove All Adjacent Duplicates in String II · Best Time to Buy and Sell Stock III · Toeplitz Matrix · Edit Distance · Maximum Depth of Binary Tree · Reverse Linked List II · Length of Last Word · Sudoku Solver · Symmetric Tree.

### Tier C — Appears occasionally (15%–30%)

Minimum Size Subarray Sum · Course Schedule II · Max Points on a Line · Reverse Words in a String · Pascal's Triangle · Restore IP Addresses · Longest Valid Parentheses · Convert Sorted List to BST · Swap Nodes in Pairs · Same Tree · Integer to Roman · Path Sum · House Robber · Palindrome Partitioning · Find Minimum in Rotated Sorted Array · Single Number · Contains Duplicate · Combinations · Convert Sorted Array to BST · Balanced Binary Tree · Minimum Path Sum · Path Sum II · Permutations II · Substring with Concatenation of All Words · Unique Binary Search Trees II · Flatten Binary Tree to Linked List · Word Search II · Spiral Matrix II · Evaluate Reverse Polish Notation · Binary Tree Level Order Traversal II.

> Note: many "DP-looking" entries above appear as *variants*; remember **pure DP is rarely asked live at Meta** — prioritize arrays, strings, trees, graphs, intervals, two-pointers, heaps, BFS/DFS.

---

## 4. Prep Plan (4–6 weeks, Meta-optimized)

- **Wk 1:** Top 30 frequency questions (Tier S). Solve each in **CoderPad, no execution**, ≤20 min, narrate + trace.
- **Wk 2:** Tier S remainder + Tier A. Drill **2-problems-in-35-min** back-to-back on one timer.
- **Wk 3:** Patterns — trees/BST, graphs (BFS/DFS, clone, islands), intervals, two-pointers, heaps, stack/parentheses, binary search. Re-solve top questions a 2nd time.
- **Wk 4:** **AI-enabled round** practice — add features to a multi-file repo with Cursor/AI; practice the **code-review mindset** (read every diff, find bugs, reject/modify). Run 1 session/week.
- **Wk 5:** Design (pick track) — 3–4 full 45-min mocks (Instagram, News Feed, Messenger, Ad Click Aggregator, LeetCode, Ticketmaster). Scope out loud first.
- **Wk 6:** Behavioral — 5–6 STAR stories mapped to the 5 signals, with metrics; rehearse deep follow-ups. Mock onsites.

---

## 5. Sources

- Hello Interview — "Meta E4 Software Engineer Interview Guide" (real questions, OA stages, behavioral signals).
- interviewing.io — "Senior Engineer's Guide to Meta Interviews" (interviewer anecdotes: DP banned, bugs-tolerated, decision mechanics).
- IGotAnOffer — "Meta Software Engineer Interview".
- GeeksforGeeks, LeetCode Discuss, Reddit (r/leetcode, r/cscareerquestions), Blind ("how many Meta-tagged to be ready").
- Guides: CrackingWalnuts (E4), SpaceComplexity, Prepfully, techinterview.org, jobsbyculture, ResumeAdapter, DesignGurus, DG Learning, jobjourney.
- Question banks: CodeJeet (217 w/ frequency), magicsheet (1347), Crackr (107), InterviewSolver (100), Verve (top 30).

*Note: E3 (new-grad) uses the same coding pool as E4; E3 has no real design round (conversational only) and behavioral is at "contribution" scope vs E4 "feature ownership". E5+ adds a second design round and heavier behavioral. The AI-enabled coding round (Q4 2025+) now applies to all onsite candidates.*
