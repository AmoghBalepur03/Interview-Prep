# Google SDE-1 / SWE I (L3, New Grad / Entry-Level) — Master Interview Question Sheet

> Compiled from real interview experiences on **LeetCode Discuss, GeeksforGeeks, Reddit (r/cscareerquestions, r/developersIndia), Blind, LinkedIn, interviewexperiences.in, Medium**, and dedicated question banks (**CodeJeet, magicsheet, Verve, Scoutify, faangcoder, PapersAdda**).
> Coverage window: mostly **2023 → 2026** cycles (India + US).
> Last updated: 2026-06-30.
>
> **How to use:** Google weights coding heavily at L3 (system design is usually skipped). Master the Tier S/A problems at **optimal** complexity, practice in a **plain Google Doc / CoderPad with no IDE**, narrate out loud, and prepare 4–5 STAR stories for the Googleyness round. Companion spreadsheet: `Google_SDE1_Questions.csv`.

---

## 1. Interview Process (L3 / New Grad SWE)

| Stage | Round | Format | Notes |
|------|-------|--------|-------|
| 0 | **Recruiter screen** | Call | Role fit, logistics, level (L3). Sometimes a **Google Hiring Assessment (GHA)** questionnaire for referral/HM paths. |
| 1 | **Phone Screen** | 45–60 min, Google Doc / CoderPad | 1 medium DSA (now often "medium + follow-up optimization"). Big-O reasoning. May add a 2nd phone screen if borderline (not a negative signal). |
| 2 | **Coding 1** | 45 min | Hash maps + arrays; follow-up optimization. Medium. |
| 3 | **Coding 2** | 45 min | Trees / graphs / DP; multiple follow-ups. Medium–Hard. |
| 4 | **Coding 3** (loop-dependent) | 45 min | Additional DSA; sometimes a *bounded* design (URL shortener / basic chat) at L3, not full system design. |
| 5 | **Googleyness & Leadership** | 45 min | Behavioral, STAR. Project deep-dive, conflict, ambiguity, "why Google". |
| 6 | **Hiring Committee (HC)** | Async | Independent committee reviews written scorecards — **your interviewers do NOT decide.** |
| 7 | **Team Match** | Call(s) | Recruiter pairs you to a team + HM chat. A cleared loop with no team match can stall. |

**The 4 scoring signals (every round):** **GCA** (General Cognitive Ability), **RRK** (Role-Related Knowledge / DSA depth), **Leadership**, **Googleyness** (intellectual humility, comfort with ambiguity, collaboration, low ego).
**Difficulty mix:** ~26% Easy, ~52% Medium, ~22% Hard (L3 leans Medium with 1–2 clean follow-ups; fewer chained-hard than L4+).
**Topic shares (candidate-reported):** Arrays/Strings ~32–35%, Trees/Graphs ~24–25%, DP ~15%, Heaps ~9%, Backtracking ~7%, Linked Lists ~5%, Math/Bit ~4%.
**Timeline:** ~4–10 weeks (longer than Amazon/Meta due to HC + team match).

> **Critical Google-specific rules (from candidate reports):**
> - You code in a **plain shared doc — no IDE, no autocomplete, no syntax highlighting, no run button.** Some interviewers disallow built-in library functions. **Practice in a blank Google Doc for 2 weeks before the loop.**
> - **Brute force is a starting point, not the answer.** State it, then reach the **optimal** with crisp complexity analysis, ideally with time to spare. Stopping at brute force ⇒ no-hire.
> - **Narrate constantly.** Silent correct code is penalized (GCA is scored on your process).
> - **Use hints gracefully.** Interviewers nudge deliberately; resisting reads as inflexible.
> - **2026 AI-Assisted Coding pilot (select US teams):** given an existing codebase + Gemini — find/fix bugs, optimize. Scored on AI fluency, output validation, debugging. Treat Gemini like a fast intern, not an oracle.

---

## 2. REAL Questions From Recent Experiences (verbatim/paraphrased)

The highest-signal section — actual reported problems from L3/L4/SWE-II loops (L4 included for context; L3 draws from the same coding pool, minus system design).

### 2.1 Phone Screen — real reported problems

| # | Problem | Topic | Source / Cycle |
|---|---------|-------|----------------|
| 1 | Medium **two-pointer** problem + a medium follow-up ("if you can solve LC 11 Container With Most Water, you can solve this"). | Two pointers | LinkedIn, SWE-II 2024 |
| 2 | Sorting/dedup array problem; interviewer follow-up on **handling duplicates**. | Array/Sorting | GfG SDE-2 2025 |
| 3 | **Longest Substring Without Repeating Characters** (sliding window + last-seen hashmap). | Sliding window | InterviewChamp new-grad |
| 4 | DP problem (≈ *min steps to reach 1*) + **Longest Palindromic Substring** (brute force partially accepted). | DP / Strings | GfG SDE |

### 2.2 Coding Rounds — real reported problems

| # | Problem | Topic | Source / Cycle |
|---|---------|-------|----------------|
| 1 | **Separate Squares I** (LC 3453 — binary search on a horizontal line that splits the total area of given squares in half). | Binary search / Geometry | interviewexperiences.in L4 May 2025 |
| 2 | **Run-Length Encoding & Decoding.** FU1: given encoded list + index, return original value at that index. FU2: original list sorted — given encoded list + value, return index of first occurrence (binary search on RLE). | Design / Binary search | interviewexperiences.in L4 May 2025 |
| 3 | **Design a class for distinct client events in the last K seconds** (sliding-window counter / queue + set). | Design / Stream | interviewexperiences.in L4 May 2025 |
| 4 | **Determine player ranks in a chess contest from M games** (graph / topological-style ordering from pairwise results). | Graph / Topo | interviewexperiences.in L4 May 2025 |
| 5 | C++-specific: interviewer wrote **node merge function overloading / method signatures** and asked language-semantics questions. | Language depth (C++) | interviewexperiences.in L4 May 2025 |
| 6 | **Hashing problem with an alternative deque solution** (medium-hard for hashset, hard for deque) — "if you can solve LC 239 Sliding Window Maximum, you can solve this". Forgot duplicates, then fixed. | Hashing / Monotonic deque | LinkedIn SWE-II 2024 |
| 7 | **Directed graph + DSU** (hardest reported problem); closest public analog **LC 952 Largest Component Size by Common Factor**. | Graph / Union-Find | LinkedIn SWE-II 2024 |
| 8 | **Binary tree postorder** with a hard space optimization → **Morris traversal O(1) space**. | Tree traversal | LinkedIn SWE-II 2024 |
| 9 | **Lowest Common Ancestor in a Binary Tree** (LC 236); follow-up: optimize for a **BST**. | Tree / BST | GfG SDE-2 2025 |
| 10 | DP medium + **Graph hard ≈ Shortest Path with Alternating Colors (LC 1129)** (a bit trickier). | DP / BFS | GfG SDE |
| 11 | Pattern-searching on strings + a **heaps / priority-queue** problem. | Strings / Heap | GfG SDE |
| 12 | **Permutations** (backtracking, visited set; FU: handle duplicates → sort + skip). | Backtracking | InterviewChamp new-grad |
| 13 | **Binary Tree Level Order Traversal** (BFS, track level size; FU: zigzag / right-side view). | Tree BFS | InterviewChamp new-grad |
| 14 | **Merge Two Sorted Lists** (dummy head, iterative then recursive). | Linked list | InterviewChamp new-grad |

### 2.3 High-frequency Google-tagged "signature" problems (often reused/varied)

Longest Absolute File Path (388) · K Empty Slots (683) · Next Closest Time (681) · Unique Email Addresses (929) · Fruit Into Baskets (904) · License Key Formatting (482) · Range Sum Query 2D – Mutable (308) · Guess the Word (843) · Unique Word Abbreviation (288) · Robot Room Cleaner (489) · Evaluate Division (399) · Repeated String Match (686) · Number of Islands (200) · Merge k Sorted Lists (23) · Find Median from Data Stream (295) · Median of Two Sorted Arrays (4) · Alien Dictionary (269) · Number of Provinces (547) · Redundant Connection (684) · Set Matrix Zeroes (73) · Serialize & Deserialize Binary Tree (297) · Binary Tree Right Side View (199).

### 2.4 If a bounded "design" appears at L3

URL shortener (hashing, base62, DB schema, caching with Redis, read-heavy scaling) · basic chat · rate limiter · LRU cache. Interviewer guides actively — they're probing structured thinking, not deep distributed-systems depth.

### 2.5 Googleyness & Leadership — real reported questions

- Tell me about a time you dealt with **ambiguity** / a project that changed direction.
- Describe a **disagreement** with a colleague and how you handled it.
- Tell me about a **project you drove yourself** (expect a 30+ min deep-dive on your most complex project).
- Describe a time you **failed** and what you learned.
- How do you handle **feedback you disagree with**?
- How would you handle **conflicting priorities**? How do you collaborate with cross-functional teams?

> Google scores **behaviors during the round** (how you handle pushback, ask for help, integrate feedback), not just your stories. Show intellectual humility ("I don't know — here's how I'd find out") over confident BS.

---

## 3. Master DSA Question Bank (frequency-ranked)

Frequency = share of recent Google interview reports mentioning the problem (CodeJeet aggregation, ~2023–2026). Treat **45%+** as must-do, **30–45%** as high-value. Full ranked list (400+ problems) is in **`Google_SDE1_Questions.csv`**.

### Tier S — Asked most often (57%–100%)

| # | Problem | Difficulty | Freq |
|---|---------|-----------|------|
| 1 | Two Sum | Easy | 100% |
| 2 | Add Two Numbers | Medium | 77.7% |
| 3 | Trapping Rain Water | Hard | 73.9% |
| 4 | Median of Two Sorted Arrays | Hard | 73.9% |
| 5 | Longest Common Prefix | Easy | 73.3% |
| 6 | Palindrome Number | Easy | 72.7% |
| 7 | Merge Sorted Array | Easy | 72.7% |
| 8 | Longest Substring Without Repeating Characters | Medium | 71.9% |
| 9 | Best Time to Buy and Sell Stock | Easy | 71.8% |
| 10 | 3Sum | Medium | 71.8% |
| 11 | Merge Strings Alternately | Easy | 70.6% |
| 12 | Longest Palindromic Substring | Medium | 70.1% |
| 13 | Longest Consecutive Sequence | Medium | 69.1% |
| 14 | Container With Most Water | Medium | 67.5% |
| 15 | Valid Parentheses | Easy | 66.2% |
| 16 | Majority Element | Easy | 66.2% |
| 17 | Roman to Integer | Easy | 66.1% |
| 18 | Merge Intervals | Medium | 65.9% |
| 19 | Number of Islands | Medium | 65.6% |
| 20 | Maximum Subarray | Medium | 65.3% |
| 21 | Reverse Integer | Medium | 65.2% |
| 22 | Remove Duplicates from Sorted Array | Easy | 65.2% |
| 23 | Subarray Sum Equals K | Medium | 64.7% |
| 24 | Climbing Stairs | Easy | 64.4% |
| 25 | Merge Two Sorted Lists | Easy | 61.4% |
| 26 | Next Permutation | Medium | 61.4% |
| 27 | Generate Parentheses | Medium | 61.0% |
| 28 | Spiral Matrix | Medium | 60.1% |
| 29 | Meeting Rooms II | Medium | 59.5% |
| 30 | Reverse Linked List | Easy | 59.2% |
| 31 | Decode String | Medium | 58.9% |
| 32 | Koko Eating Bananas | Medium | 58.2% |
| 33 | Search in Rotated Sorted Array | Medium | 58.1% |
| 34 | Move Zeroes | Easy | 58.1% |
| 35 | LRU Cache | Medium | 57.6% |
| 36 | 4Sum | Medium | 57.3% |
| 37 | N-Queens | Hard | 57.2% |
| 38 | Largest Rectangle in Histogram | Hard | 57.2% |
| 39 | Jump Game | Medium | 57.0% |
| 40 | Letter Combinations of a Phone Number | Medium | 57.0% |
| 41 | Split Array Largest Sum | Hard | 56.9% |
| 42 | Top K Frequent Elements | Medium | 56.8% |
| 43 | Find Peak Element | Medium | 56.8% |
| 44 | Rotate Array | Medium | 56.2% |
| 45 | Valid Anagram | Easy | 56.1% |

### Tier A — Frequently asked (44%–56%)

House Robber · Kth Largest Element in an Array · Group Anagrams · Rotate Image · Sort Colors · Pow(x,n) · Sliding Window Maximum · Longest Repeating Character Replacement · Product of Array Except Self · Russian Doll Envelopes · Longest Increasing Subsequence · Regular Expression Matching · Set Matrix Zeroes · Find Median from Data Stream · Find First and Last Position in Sorted Array · Maximal Square · Course Schedule · Subsets · Basic Calculator · Rotting Oranges · Maximal Rectangle · Min Stack · Permutations · Unique Paths · Merge k Sorted Lists · Maximum Product Subarray · Coin Change · 3Sum Closest · Binary Tree Maximum Path Sum · First Missing Positive · Max Consecutive Ones III · Two Sum II · Reverse Words in a String · Fruit Into Baskets · Insert Delete GetRandom O(1) · Sudoku Solver · Best Time to Buy and Sell Stock II · Jump Game II · The Earliest Moment When Everyone Become Friends · Valid Sudoku · Candy · Reverse Nodes in k-Group · Partition Equal Subset Sum · Daily Temperatures · Combination Sum · Guess the Word · Edit Distance · Separate Squares I · Remove K Digits · Insert Interval · Text Justification · Sum of Subarray Minimums · Word Break · Palindrome Partitioning · Minimum Area Rectangle · Next Greater Element II · Find the Duplicate Number · Diameter of Binary Tree · Subsets II · Reverse Pairs · Capacity To Ship Packages Within D Days · Wildcard Matching · Random Pick with Weight.

### Tier B — Common (37%–44%)

Surrounded Regions · Largest Number · Divide Two Integers · Range Module · String to Integer (atoi) · String Compression · Target Sum · Word Search · Minimum Window Substring · Search a 2D Matrix II · Permutation in String · Pacific Atlantic Water Flow · Squares of a Sorted Array · Integer to Roman · Binary Tree Level Order Traversal · First Unique Character in a String · Search a 2D Matrix · Invert Binary Tree · Meeting Rooms III · Number of Provinces · Frequency of the Most Frequent Element · Word Ladder · Lowest Common Ancestor of a Binary Tree · Majority Element II · Burst Balloons · Delete Nodes And Return Forest · Kth Smallest Element in a BST · Minimum Size Subarray Sum · Maximum Width Ramp · Sort List · Swim in Rising Water · Largest Divisible Subset · Task Scheduler · Longest Valid Parentheses · Clone Graph · 24 Game · Gas Station · Find K Closest Elements · Perfect Squares · Binary Tree Zigzag Level Order Traversal · Copy List with Random Pointer · Longest Common Subsequence · Trapping Rain Water II · Longest Increasing Path in a Matrix · Asteroid Collision · 01 Matrix · Serialize and Deserialize Binary Tree · Shortest Palindrome · Coin Change II · Reorganize String · Group Shifted Strings · Shortest Path in a Grid with Obstacles Elimination · Evaluate Division · LFU Cache · Expression Add Operators · Implement Trie · House Robber II · Find All Duplicates in an Array · Online Stock Span · Number of Islands II · Path With Minimum Effort · Alien Dictionary · Word Search II · Cheapest Flights Within K Stops · Course Schedule II · Contiguous Array · Network Delay Time · Redundant Connection.

### Tier C — Regular (30%–37%)

Construct Binary Tree from Preorder and Inorder · Validate Binary Search Tree · H-Index · Accounts Merge · Find K Pairs with Smallest Sums · Design Hit Counter · Maximum Swap · Diagonal Traverse · Substring with Concatenation of All Words · Triangle · Vertical Order Traversal of a Binary Tree · Best Time to Buy and Sell Stock with Cooldown · Combination Sum IV · Find Eventual Safe States · Remove Duplicate Letters · Integer to English Words · Detect Squares · Bus Routes · Car Fleet · Subarrays with K Different Integers · Combination Sum II · Exclusive Time of Functions · Count of Smaller Numbers After Self · Shortest Path in Binary Matrix · Number of Closed Islands · The Skyline Problem · Find the Safest Path in a Grid · Binary Tree Right Side View · Sequence Reconstruction · Maximum Points You Can Obtain from Cards · Maximum XOR of Two Numbers in an Array · Max Area of Island · Fraction to Recurring Decimal · Sum of Distances in Tree · Find All Anagrams in a String · Flatten Binary Tree to Linked List · Reconstruct Itinerary · Minimum Cost to Make at Least One Valid Path in a Grid · Detonate the Maximum Bombs · Encode and Decode Strings · Top K Frequent Words · Shortest Path Visiting All Nodes · Palindromic Substrings · Find Duplicate Subtrees · Longest Continuous Subarray With Abs Diff ≤ Limit · Recover Binary Search Tree · Making A Large Island · Word Break II · Graph Valid Tree · Predict the Winner · Keys and Rooms · Minimum Falling Path Sum · House Robber III · Delete Node in a BST · Decode Ways · Increasing Triplet Subsequence · Regions Cut By Slashes · Binary Tree Vertical Order Traversal · Longest Absolute File Path · Sliding Window Median · Non-overlapping Intervals · Binary Search Tree Iterator · Best Time to Buy and Sell Stock III · K Closest Points to Origin · Robot Room Cleaner · My Calendar I · Design a Text Editor · Word Ladder II · All Nodes Distance K in Binary Tree.

> The CSV contains every problem above plus exact LeetCode acceptance rates and frequency %.

---

## 4. Prep Plan (8–10 weeks)

- **Wk 1–2:** Foundations → **optimal**. Arrays, two-pointer, sliding window, hashing, prefix sum, Kadane. Narrate + state complexity. Solve in a blank Google Doc.
- **Wk 3–5:** Graphs (BFS/DFS, multi-source BFS, topological sort, Union-Find, Dijkstra) + Trees/BST (traversals, LCA, serialize/deserialize, Morris).
- **Wk 6–7:** DP (1D/2D, intervals, string DP, knapsack) + Intervals + Backtracking + Heaps.
- **Wk 8:** Tier S + Tier A Google-tagged list.
- **Wk 9–10:** Mock interviews **with hints**, behavioral STAR doc, one bounded design (URL shortener).

**Daily:** 1–2 problems, no IDE, time-boxed; rehearse Big-O verbally.

---

## 5. Sources

- LeetCode Discuss — "Google | L4 | SWE 3 | Bangalore"; FAANG frequency threads.
- GeeksforGeeks — "Google Interview Experience for Software Engineer (SDE-2) 2025"; ".../for SDE"; ".../for SWE".
- interviewexperiences.in — "Google | L4 | India | May 2025 [Offer]".
- LinkedIn — Harshit Sharma, "My 2024 Google interview experience (SWE-II)".
- Medium / Reddit r/cscareerquestions & r/developersIndia / Blind (aggregated).
- Guides: InterviewChamp.AI, Simplify, SpaceComplexity, gitGood.dev, PapersAdda (loop + rubric), Prepfully, DG Learning, FastApply, Exponent, Scoutify.
- Question banks: CodeJeet (2305 problems w/ frequency), magicsheet (2001), Verve (top 30), faangcoder (top-75 Google focus).

*Note: L4/SWE-II experiences are included for context. At L3 the coding pool is the same; system design is generally dropped (appears as a single bounded design at most), and Googleyness/Leadership is scored in every round.*
