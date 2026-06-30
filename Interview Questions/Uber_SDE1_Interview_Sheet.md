# Uber SDE-1 (Software Engineer / E3 / L3) — Master Interview Question Sheet

> Compiled from real interview experiences on **LeetCode Discuss, GeeksforGeeks, Reddit (r/cscareerquestions, r/leetcode, r/developersIndia), Medium, interviewexperiences.in, Blind**, and dedicated company question banks (**CodeJeet, Crackr, HackMNC, takeuhigh/AlgoScience**).
> Coverage window: mostly **2024 → 2026** cycles (India + US).
> Last updated: 2026-06-30.
>
> **How to use:** Start at the top of each frequency tier in the DSA bank, master LLD (this is the round people fail most), and prepare 3–4 STAR stories. The companion spreadsheet is `Uber_SDE1_Questions.csv`.

---

## 1. Interview Process (SDE-1)

Uber's SDE-1 loop varies by team/geo, but the consistent shape across recent reports:

| Stage | Round | Platform | Notes |
|------|-------|----------|-------|
| 1 | **Online Assessment (OA)** | HackerRank / CodeSignal | 3 DSA questions, ~75 min (sometimes 2–4 Q / 45–120 min). Easy → Hard. All test cases must pass; no partial credit on some. |
| 2 | **Phone Screen / Elimination ("BPS") round** | CodeSignal / HackerRank | Newly common since 2025. 1 DSA problem + sometimes light HLD discussion. Gateway/disqualification round. |
| 3 | **Coding Round 1 — DSA** | HackerRank / CoderPad | 1–2 medium/hard problems. Heavy on graphs, DP, trees. Detailed time/space analysis expected. |
| 4 | **Coding Round 2 — LLD / Machine Coding** | Shared editor (runnable code) | The hardest & most-failed round. OOP design, design patterns, sometimes concurrency. |
| 5 | **Hiring Manager / Techno-HR** | Video | Behavioral + light system design + project deep-dive. |

**Difficulty mix (SDE-1 / E3-E4):** ~10–25% Easy, ~60–75% Medium, ~10–17% Hard.
**Most-tested topics (by share):** Arrays/Strings (~32%), Graphs (~24%), DP (~18%), Trees (~14%), Hash Tables (~8%), Sliding Window/Stacks/Heaps (~4%).
**Languages:** Any major language. Java/C++ favored for LLD (clean OOP), Python popular for DSA.

> **Key signal from candidates:** Uber values *getting code to a running state fast*, clean readable code, edge-case handling, and clear communication of trade-offs over a clever-but-broken optimal.

---

## 2. REAL Questions From Recent SDE-1 Experiences (verbatim/paraphrased)

These are the actual, specific questions reported by candidates — the highest-signal section.

### 2.1 Online Assessment (OA) — real reported problems

| # | Problem | Topic | Source / Cycle |
|---|---------|-------|----------------|
| 1 | **K-th Largest Subarray Bitwise OR** — given `nums` and `k`, find the k-th largest subarray bitwise-OR value. (Trick: from any index, OR values are non-decreasing → ≤31 unique values; store `val:cnt` in a dict.) | Bit manip + Hashing | interviewexperiences.in, SDE1 OA 24 Feb 2026 |
| 2 | **K-th Next Greatest Element Index** — for every index, return index of the k-th next greatest element. e.g. `[3,4,2,6,5], k=2` → ... | Monotonic stack / sorting | interviewexperiences.in, SDE1 OA 24 Feb 2026 |
| 3 | **MST with Mixed Edge Weights** — N nodes, complete undirected graph, some edges weight 1, rest weight 0. Return MST weight. (Need k-1 edges to connect k components.) | MST / Union-Find | interviewexperiences.in, SDE1 OA 24 Feb 2026 |
| 4 | **Base conversion** — convert a long numeric string from base A to base B. (Easy, 100 pts) | Math/String | Medium (Research Nest) |
| 5 | **Double Knapsack with time dimension** — knapsack with an extra "time" dimension. (Hard, 300 pts) | DP | Medium (Research Nest) |
| 6 | **Count subsequences with GCD = 1** — count unique subsequences whose GCD is 1. (Hard, 300 pts) | DP + number theory | Medium (Research Nest) |
| 7 | OA third problem lifted from **Codeforces Round 2091 / Problem F** (~1800 rated). | CF-style | Medium (Quote, 2025) |
| 8 | 3 DSA problems, rated ~1000 / 1100 / 1600 (Codeforces scale). | Mixed | Medium (Quote, 2025) |

### 2.2 DSA Interview Rounds — real reported problems

| # | Problem | Topic | Source / Cycle |
|---|---------|-------|----------------|
| 1 | **Earliest Timestamp When All Users Become Connected** (a.k.a. *Earliest Moment When Everyone Become Friends*, LC 1101). Solve with Union-Find. | Union-Find / DSU | interviewexperiences.in Feb 2026; GfG off-campus |
| 2 | **…with cancellations** — connections can also be *removed*; find earliest time all are connected. | DSU + offline/rollback | interviewexperiences.in Feb 2026 |
| 3 | **Microservice Restart Cycles** — min number of cycles to start all services, or "impossible" (cycle detection / topological order). | Topo sort / Graph | interviewexperiences.in Feb 2026 |
| 4 | **Minimum Transitions Between Network Nodes** — like Word Ladder (LC 127), min transitions via BFS. | BFS | GfG off-campus |
| 5 | **Circular Rock-Paper-Scissors** — engineers in a circle pick R/P/S; min changes so no two neighbors tie (circular, first & last are neighbors). O(n) greedy / 3-state DP. | Greedy / DP | LeetCode Discuss (CTC 65L post) |
| 6 | **Game Theory + Multi-dimensional DP** — maximize player's value assuming optimal opponent (memoized minimax). | Game theory DP | LeetCode Discuss |
| 7 | **DP on Trees** (toughest round for one candidate). | Tree DP | LeetCode Discuss SDE Nov 2025 |
| 8 | **Find next closest palindrome strictly greater than N** (string). Edge cases: `999→1001`, even/odd length, already-palindrome `1221→1331`. | String / Math | DevBrainiac |
| 9 | **Rearrange positive & negative numbers alternately preserving relative order** (in-place, right-rotation, no extra array). | Array / Two-pointer | DevBrainiac |
| 10 | **First non-repeating character in a stream** — after each char, output first unique (Queue + HashMap). | Stream / Queue+Hash | DevBrainiac |
| 11 | Tree views: **left view, top view, bottom view**; code bottom & top view. | Tree BFS | Medium (Research Nest) |
| 12 | **Find median from a data stream** in real time. | Heaps | HackMNC reported |
| 13 | **Longest substring with K distinct characters.** | Sliding window | HackMNC reported |
| 14 | **Bus Routes** (LC 815) — most-reported Uber graph problem (BFS on implicit stop↔route graph). | BFS / Graph | takeuhigh (12× seen), Stackademic |
| 15 | **Quad Tree** construction (LC 427). | Divide & conquer | bugfree.ai, Stackademic |

### 2.3 LLD / Machine Coding — real reported problems

| # | Problem | What to focus on | Source |
|---|---------|------------------|--------|
| 1 | **Design an in-memory File System** (mimic Linux): `mkdir(dir)`, `pwd()`, `cd(path)`, `ls`. Use N-ary tree for hierarchy; handle `.`/`..`/absolute & relative paths; error handling. | N-ary tree, path parsing, errors | interviewexperiences.in Feb 2026; LC Discuss Nov 2025 |
| 2 | **Implement core functionalities of Google Sheets / Excel** — model the **dependency graph** between cells; recompute on update; detect cycles. | Dependency graph, observer pattern | Medium (Quote 2025) |
| 3 | **Design a Parking Lot System** — classes, interfaces, UML relationships; OOP follow-ups throughout. | SOLID, Strategy/Factory | GfG off-campus |
| 4 | **Design a Car Rental System** — Car/User/Booking/Inventory/Payment; Strategy for pricing (daily/weekend/luxury), Factory for vehicle types. | SOLID, Strategy, Factory | DevBrainiac |
| 5 | **Splitwise** (LLD/HLD edge) — minimize number of transactions among a group (graph edge optimization). | Graph, greedy | LC Discuss Nov 2025 |
| 6 | **Job Scheduler with concurrency/multithreading** — thread pool limit, dependencies, retries, status (more common at SDE-2 but appears). | Concurrency, locks, queues | PapersAdda / Roundz |
| 7 | Common LLD bank: **LRU Cache, Meeting Room Scheduler, Ride-Sharing / Booking service, caching mechanisms.** | Patterns + clean code | Multiple |

**LLD expectations:** clarify requirements first → define modular classes (SRP) → apply SOLID + a pattern or two (Strategy, Factory, Observer) → write *runnable* code → handle the requirement the interviewer adds mid-round without a rewrite.

### 2.4 Hiring Manager / Behavioral — real reported questions

- Tell me about a time you disagreed with a teammate or manager. (Looking for: listen first, use data, preserve trust.)
- How do you prioritize tasks when deadlines are tight? (Think like an owner: customer/revenue impact, blockers, communicate trade-offs early.)
- Describe a performance bottleneck you fixed. (How you *found* it + collaborated matters more than the fix.)
- If your feature fails in production during peak hours, what do you do first? (Answer: **stabilize first** — severity, rollback, feature flags, inform stakeholders — *then* root-cause.)
- Deep dive into your current/most-recent project: architecture, scaling challenges, what you'd redesign.
- Questions about Uber's work culture / why Uber (candidates who asked good reverse-questions, e.g. about H3 vs S2 geo-indexing, stood out).

---

## 3. Master DSA Question Bank (frequency-ranked)

Frequency = share of recent Uber interview reports that mention the problem (CodeJeet/Crackr aggregation). Treat **100% → 50%** as must-do.

### Tier S — Asked most often (62.5%–100% frequency)

| # | Problem (LeetCode) | Difficulty | Topics | Freq |
|---|--------------------|-----------|--------|------|
| 1 | Bus Routes | Hard | Graph, BFS, Hash | 100% |
| 2 | Number of Islands | Medium | DFS/BFS, Matrix, Union-Find | 87.5% |
| 3 | Kth Smallest Element in a BST | Medium | Tree, DFS, BST | 87.5% |
| 4 | Alien Dictionary | Hard | Graph, Topo sort | 87.5% |
| 5 | Number of Islands II | Hard | Union-Find | 87.5% |
| 6 | Find the Closest Palindrome | Hard | Math, String | 87.5% |
| 7 | Construct Quad Tree | Medium | Divide & Conquer, Tree | 87.5% |
| 8 | Squares of a Sorted Array | Easy | Two Pointers | 87.5% |
| 9 | Longest Continuous Subarray With Abs Diff ≤ Limit | Medium | Sliding Window, Deque | 87.5% |
| 10 | Text Justification | Hard | String, Simulation | 75% |
| 11 | Word Search | Medium | Backtracking, Matrix | 75% |
| 12 | Best Time to Buy and Sell Stock | Easy | DP | 75% |
| 13 | LRU Cache | Medium | Hash + Linked List, Design | 75% |
| 14 | Word Search II | Hard | Trie, Backtracking | 75% |
| 15 | Product of Array Except Self | Medium | Prefix product | 75% |
| 16 | Design Hit Counter | Medium | Design, Queue | 75% |
| 17 | Insert Delete GetRandom O(1) | Medium | Hash + Array, Design | 75% |
| 18 | Evaluate Division | Medium | Graph, DFS/Union-Find | 75% |
| 19 | My Calendar I | Medium | Intervals, TreeMap | 75% |
| 20 | Random Pick with Weight | Medium | Prefix sum + Binary search | 75% |
| 21 | First Unique Number | Medium | Hash + Queue, Design | 75% |
| 22 | Two Sum | Easy | Hash | 62.5% |
| 23 | Letter Combinations of a Phone Number | Medium | Backtracking | 62.5% |
| 24 | Merge k Sorted Lists | Hard | Heap, Divide & Conquer | 62.5% |
| 25 | Search in Rotated Sorted Array | Medium | Binary Search | 62.5% |
| 26 | Valid Sudoku | Medium | Matrix, Hash | 62.5% |
| 27 | Sudoku Solver | Hard | Backtracking | 62.5% |
| 28 | Group Anagrams | Medium | Hash, String | 62.5% |
| 29 | Spiral Matrix | Medium | Matrix, Simulation | 62.5% |
| 30 | Minimum Window Substring | Hard | Sliding Window | 62.5% |
| 31 | Decode Ways | Medium | DP | 62.5% |
| 32 | Maximum Depth of Binary Tree | Easy | Tree, DFS | 62.5% |
| 33 | Word Break | Medium | DP | 62.5% |
| 34 | Course Schedule | Medium | Topo sort, Graph | 62.5% |
| 35 | Course Schedule II | Medium | Topo sort, Graph | 62.5% |
| 36 | Basic Calculator | Hard | Stack, String | 62.5% |
| 37 | Valid Anagram | Easy | Hash, Sorting | 62.5% |
| 38 | Meeting Rooms II | Medium | Intervals, Heap | 62.5% |
| 39 | Serialize and Deserialize Binary Tree | Hard | Tree, DFS/BFS | 62.5% |
| 40 | House Robber III | Medium | Tree DP | 62.5% |
| 41 | Top K Frequent Elements | Medium | Heap, Hash | 62.5% |
| 42 | Shuffle an Array | Medium | Design, Math | 62.5% |
| 43 | All O`one Data Structure | Hard | Hash + DLL, Design | 62.5% |
| 44 | The Maze | Medium | BFS/DFS | 62.5% |
| 45 | Design In-Memory File System | Hard | Trie/N-ary tree, Design | 62.5% |
| 46 | Exclusive Time of Functions | Medium | Stack | 62.5% |
| 47 | Top K Frequent Words | Medium | Heap, Hash | 62.5% |
| 48 | Cherry Pickup | Hard | DP, Matrix | 62.5% |
| 49 | Making A Large Island | Hard | Union-Find/DFS | 62.5% |
| 50 | Shortest Bridge | Medium | BFS/DFS | 62.5% |
| 51 | Time Based Key-Value Store | Medium | Hash + Binary search, Design | 62.5% |
| 52 | Leftmost Column with at Least a One | Medium | Binary search, Matrix | 62.5% |
| 53 | Merge Strings Alternately | Easy | Two Pointers | 62.5% |
| 54 | Min Number of Operations to Make Array Continuous | Hard | Sliding Window, Binary search | 62.5% |
| 55 | Escape the Spreading Fire | Hard | Multi-source BFS | 62.5% |
| 56 | Count Subarrays With Fixed Bounds | Hard | Sliding Window | 62.5% |
| 57 | Split Message Based on Limit | Hard | String, Enumeration | 62.5% |
| 58 | The Earliest Moment When Everyone Become Friends | Medium | Union-Find | 37.5% (real-exp ↑) |
| 59 | Find Median from Data Stream | Hard | Two Heaps, Design | 50% |

### Tier A — Frequently asked (50% frequency)

| # | Problem | Difficulty | Topics |
|---|---------|-----------|--------|
| 1 | String to Integer (atoi) | Medium | String |
| 2 | Regular Expression Matching | Hard | DP, Recursion |
| 3 | Generate Parentheses | Medium | Backtracking |
| 4 | Combination Sum | Medium | Backtracking |
| 5 | Rotate Image | Medium | Matrix |
| 6 | Subsets | Medium | Backtracking |
| 7 | Largest Rectangle in Histogram | Hard | Monotonic Stack |
| 8 | Clone Graph | Medium | DFS/BFS, Hash |
| 9 | Copy List with Random Pointer | Medium | Hash, Linked List |
| 10 | Word Break II | Hard | DP, Backtracking |
| 11 | Min Stack | Medium | Stack, Design |
| 12 | One Edit Distance | Medium | String |
| 13 | Dungeon Game | Hard | DP, Matrix |
| 14 | Reverse Words in a String II | Medium | String |
| 15 | House Robber | Medium | DP |
| 16 | Binary Tree Right Side View | Medium | Tree BFS |
| 17 | Implement Trie (Prefix Tree) | Medium | Trie, Design |
| 18 | Group Shifted Strings | Medium | Hash, String |
| 19 | Factor Combinations | Medium | Backtracking |
| 20 | Trips and Users | Hard | SQL/Database |
| 21 | Walls and Gates | Medium | Multi-source BFS |
| 22 | Word Pattern / Word Pattern II | Easy/Medium | Hash / Backtracking |
| 23 | Coin Change | Medium | DP |
| 24 | Find K Pairs with Smallest Sums | Medium | Heap |
| 25 | Delete Node in a BST | Medium | BST |
| 26 | Optimal Account Balancing | Hard | Backtracking (Splitwise core) |
| 27 | Longest Palindromic Subsequence | Medium | DP |
| 28 | Encode and Decode TinyURL | Medium | Hash, Design |
| 29 | Replace Words | Medium | Trie |
| 30 | Employee Importance | Medium | DFS/BFS |
| 31 | Falling Squares | Hard | Segment tree / Ordered set |
| 32 | Flood Fill | Easy | DFS/BFS |
| 33 | Asteroid Collision | Medium | Stack |
| 34 | Open the Lock | Medium | BFS |
| 35 | Koko Eating Bananas | Medium | Binary search on answer |
| 36 | Rotting Oranges | Medium | Multi-source BFS |
| 37 | Min Cost to Connect All Points | Medium | MST, Union-Find |
| 38 | Design Parking System | Easy | Design |
| 39 | Design File System | Medium | Trie/Hash, Design |
| 40 | Design Memory Allocator | Medium | Design |
| 41 | Design Browser History | Medium | Stack/DLL, Design |
| 42 | Word Ladder II | Hard | BFS + Backtracking |
| 43 | Number of Provinces | Medium | Union-Find |
| 44 | Course Schedule IV | Medium | Graph reachability |
| 45 | Remove Max Number of Edges to Keep Graph Fully Traversable | Hard | Union-Find |
| 46 | Find the City With the Smallest Number of Neighbors at Threshold | Medium | Floyd-Warshall |
| 47 | Burst Balloons | Hard | Interval DP |
| 48 | Min Cost to Make at Least One Valid Path in a Grid | Hard | 0-1 BFS |
| 49 | Meeting Rooms III | Hard | Heap, Simulation |
| 50 | Maximum Profit in Job Scheduling | Hard | DP + Binary search |

### Tier B — Regularly seen (37.5% frequency)

Add Two Numbers · Longest Substring Without Repeating Characters · Container With Most Water · Valid Parentheses · Find First and Last Position in Sorted Array · Trapping Rain Water · Permutations · Maximum Subarray · Merge Intervals · Insert Interval · Minimum Path Sum · Binary Tree Maximum Path Sum · Word Ladder · Longest Consecutive Sequence · Candy · Find Minimum in Rotated Sorted Array · Find Peak Element · Shortest Palindrome · Kth Largest Element in an Array · The Skyline Problem · Sliding Window Maximum · Move Zeroes · Longest Increasing Path in a Matrix · Patching Array · Reconstruct Itinerary · Split Array Largest Sum · Pacific Atlantic Water Flow · Longest Repeating Character Replacement · LFU Cache · Task Scheduler · Candy Crush · Sliding Puzzle · Is Graph Bipartite? · Cheapest Flights Within K Stops · Number of Matching Subsequences · Exam Room · Verifying an Alien Dictionary · Minimum Cost For Tickets · Interval List Intersections · Subarrays with K Different Integers · Design A Leaderboard · Find All People With Secret · Accounts Merge · Detonate the Maximum Bombs · Maximum Points You Can Obtain from Cards · Simplify Path · Shortest Path in Binary Matrix · Find Critical and Pseudo-Critical Edges in MST.

### Tier C — Appears occasionally (25% frequency)

Median of Two Sorted Arrays · Longest Palindromic Substring · 3Sum · Longest Valid Parentheses · Climbing Stairs · Validate Binary Search Tree · Majority Element · Search a 2D Matrix II · Longest Increasing Subsequence · Russian Doll Envelopes · Design Twitter · String Compression · Can I Win (game theory) · Predict the Winner (game theory) · Stone Game II (game theory) · Next Greater Element II · Freedom Trail · Contiguous Array · Subarray Sum Equals K · Find K Closest Elements · Max Area of Island · Swim in Rising Water · Custom Sort String · Find Eventual Safe States · Possible Bipartition · Capacity To Ship Packages Within D Days · Analyze User Website Visit Pattern · Min Number of Taps to Open to Water a Garden · Furthest Building You Can Reach · Car Pooling · Magnetic Force Between Two Balls · IPO · Construct Binary Tree from Preorder and Inorder · Strange Printer · Meeting Rooms · Design Search Autocomplete System · Meeting Scheduler · Maximum Frequency Stack · Find the Celebrity · Design a Text Editor · Surrounded Regions · BST Iterator · 24 Game · Shortest Path in a Grid with Obstacles Elimination · Valid Palindrome II · Design Snake Game · Min Cost to Merge Stones · Cherry Pickup II · Robot Room Cleaner · Reverse Nodes in k-Group.

> Full ranked list (381 problems with exact frequency %) is in **`Uber_SDE1_Questions.csv`**.

---

## 4. LLD / Machine-Coding Prep Checklist

Practice writing **runnable** code (60-min timer) for:
- In-Memory File System (mkdir/cd/ls/pwd) — N-ary tree
- Spreadsheet / Excel with cell dependency graph + cycle detection
- Parking Lot (Strategy for pricing, Factory for vehicle/spot types)
- Car Rental / Ride-booking service
- Splitwise (Optimal Account Balancing)
- LRU & LFU Cache
- Meeting Room Scheduler
- Rate Limiter / Producer-Consumer queue (thread-safe)
- Logging framework / Notification dispatcher (Observer)

**Rubric Uber grades on:** requirement clarification → SRP class design → SOLID + 1–2 patterns → compiling/running code → graceful edge-case + error handling → absorbing a mid-round requirement change without rewriting.

---

## 5. System Design / HLD (light for SDE-1, heavier for E4)

Prompts reported (E3 light, E4 50/50, E5+ always): Design Booking.com/Airbnb · Splitwise · Real-time stock-price notification system · Facebook Messenger · Driver-location ingestion + time-window queries · Top-K popularity. Expect **evolving requirements mid-round**. Cover: API design, SQL vs NoSQL, partitioning/sharding, Kafka for event streams, caching, idempotency/dedup, retries with backoff, replication/fault-tolerance, monitoring.

---

## 6. Behavioral / Hiring-Manager — STAR Story Bank to Prepare

Prepare 3–4 concrete, metric-backed stories covering:
1. **Conflict / disagreement** with teammate or manager (listened, used data, kept trust).
2. **Ownership** — took responsibility beyond your assigned task.
3. **Production incident** — stabilized first (rollback/flags/comms), then root-caused.
4. **Performance bottleneck** you diagnosed and fixed (e.g., "added missing index, p99 4.2s → 180ms").
5. **Prioritization under tight deadlines** — customer/revenue impact + communicating trade-offs.
6. **Failure you recovered from.**

> Avoid vague answers. Specifics with numbers win. Authenticity beats over-polished scripts.

---

## 7. Prep Plan (8 weeks)

- **Wk 1–2:** Arrays/Strings/Hashing/Two-pointer/Sliding window (30–40 problems, ~20 min/medium).
- **Wk 3:** Graphs (BFS/DFS, Union-Find, topo, Dijkstra) + Trees/BST. → Uber's heaviest area.
- **Wk 4:** DP (1D/2D, interval, game theory, knapsack) + Heaps/Stacks.
- **Wk 5:** Tier S + Tier A Uber-tagged list above.
- **Wk 6:** LLD — write 6+ designs from scratch with a timer.
- **Wk 7:** Mock interviews (45-min timed), edge cases & communication.
- **Wk 8:** Behavioral STAR doc + revisit weak problems + light HLD.

---

## 8. Sources

- LeetCode Discuss — "Uber | SDE-1 | Interview Exp | CTC 65L"; "Interview Experience | SDE2 | Uber | Nov 2025".
- GeeksforGeeks — "Uber Interview Experience | Software Engineer-1 (Off-Campus)".
- Medium — "Uber SDE 1 (2025) Interview Experience" (Quote); "Uber SDE 1 Interview Experience & Prep Strategy" (The Research Nest).
- interviewexperiences.in — "Uber SDE I – Feb 2026"; "Uber | Sde1 | OA | 24 Feb 2026".
- DevBrainiac — "Uber SDE 2 Interview Experience (5 Rounds, Selected)".
- PapersAdda / Roundz Substack — "Uber SDE-2 (L4) India 2025".
- bugfree.ai — "Uber L4 (SDE-2) Interview Experience: OA to Offer".
- Stackademic — "Uber SDE2/L4 Backend Interview Questions (2026)".
- Question banks: CodeJeet (381), Crackr (102), HackMNC (300+), takeuhigh/AlgoScience, magicsheet (457).
- Reddit r/cscareerquestions, r/leetcode, r/developersIndia; Blind (aggregated via above).

*Note: SDE-2/L4 experiences are included for context — their LLD/HLD/behavioral patterns mirror what SDE-1 candidates increasingly face since Uber added screening + LLD rounds in 2025.*
