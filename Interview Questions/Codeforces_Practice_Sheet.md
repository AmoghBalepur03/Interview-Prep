# Codeforces Practice Sheet for Top-Company Interviews (FAANG / Big Tech)

> Auto-built from the **official Codeforces API** (`problemset.problems`) so every **link and rating is accurate**.
> Within each topic + rating bucket, problems are ranked by **popularity (solved count)** — these are the most-practiced, classic problems, which makes them ideal warmups before interviews.
> Ratings span **800 → 2300+**. Companion spreadsheet (filterable by topic/rating): `Codeforces_Practice_Questions.csv`.

**Totals:** 583 practice entries across 366 unique problems, 18 topics, 6 difficulty buckets.

---

## How to use this for interview prep

- **Codeforces rating ≈ interview difficulty:** ~800-1200 = LeetCode Easy, ~1300-1700 = LeetCode Medium, ~1800-2200 = LeetCode Medium-Hard / Hard, 2300+ = beyond typical interviews (great for sharpening).
- **For most FAANG SDE/SDE-1 loops, the 1200-1800 range is the sweet spot.** Use 800-1100 to warm up a topic, 1900-2300+ to build speed and confidence.
- **Practice by pattern:** pick a topic, solve the 800-1000 first to lock the idea, then climb. Re-implement without looking once you've seen the trick.
- **Simulate pressure:** give yourself 20-30 min per problem; narrate your approach and complexity out loud (mirrors a real interview).

### Codeforces topic -> interview topic mapping

| Codeforces tag | Shows up in interviews as |
|---|---|
| implementation / brute force | Warmup, simulation, careful coding |
| math / number theory / combinatorics | GCD/primes/modular, counting, probability |
| greedy | Intervals, scheduling, exchange-argument problems |
| two pointers | Sliding window, pair/subarray problems |
| binary search | Search on answer, rotated arrays, monotonic predicates |
| strings / hashing | Palindromes, substrings, anagrams, pattern matching |
| dp | Knapsack, LIS/LCS, grid paths, interval DP |
| data structures | Stacks/queues/heaps, sets/maps, monotonic stack |
| graphs / dfs and similar | BFS/DFS, islands, topological sort, cycles |
| trees | Tree traversal, LCA, tree DP |
| dsu | Union-Find, connected components, Kruskal |
| shortest paths | Dijkstra/BFS, grid shortest path |
| bitmasks | Subset enumeration, bit tricks, bitmask DP |
| constructive algorithms | 'Design a valid output' / ad-hoc reasoning |

---

## 1. Implementation & Brute Force

_Codeforces tags: `implementation`, `brute force`_

### 800-1000

- [Watermelon](https://codeforces.com/problemset/problem/4/A) — **800** · solved 702k · also: math
- [Team](https://codeforces.com/problemset/problem/231/A) — **800** · solved 439k · also: greedy
- [Bit++](https://codeforces.com/problemset/problem/282/A) — **800** · solved 366k
- [Next Round](https://codeforces.com/problemset/problem/158/A) — **800** · solved 327k · also: *special
- [Beautiful Matrix](https://codeforces.com/problemset/problem/263/A) — **800** · solved 324k
- [Petya and Strings](https://codeforces.com/problemset/problem/112/A) — **800** · solved 292k · also: strings

### 1100-1300

- [Taxi](https://codeforces.com/problemset/problem/158/B) — **1100** · solved 105k · also: *special, greedy
- [Interesting drink](https://codeforces.com/problemset/problem/706/B) — **1100** · solved 87k · also: binary search, dp
- [Vanya and Lanterns](https://codeforces.com/problemset/problem/492/B) — **1200** · solved 92k · also: binary search, math, sortings
- [Registration System](https://codeforces.com/problemset/problem/4/C) — **1300** · solved 111k · also: data structures, hashing
- [IQ test](https://codeforces.com/problemset/problem/25/A) — **1300** · solved 105k
- [T-primes](https://codeforces.com/problemset/problem/230/B) — **1300** · solved 98k · also: binary search, math, number theory

### 1400-1600

- [Books](https://codeforces.com/problemset/problem/279/B) — **1400** · solved 75k · also: binary search, two pointers
- [Two Buttons](https://codeforces.com/problemset/problem/520/B) — **1400** · solved 64k · also: dfs and similar, graphs, greedy, math, shortest paths
- [Given Length and Sum of Digits...](https://codeforces.com/problemset/problem/489/C) — **1400** · solved 52k · also: dp, greedy
- [Two Substrings](https://codeforces.com/problemset/problem/550/A) — **1500** · solved 44k · also: dp, greedy, strings
- [Little Girl and Maximum Sum](https://codeforces.com/problemset/problem/276/C) — **1500** · solved 41k · also: data structures, greedy, sortings
- [Hamburgers](https://codeforces.com/problemset/problem/371/C) — **1600** · solved 36k · also: binary search

### 1700-1900

- [Number of Ways](https://codeforces.com/problemset/problem/466/C) — **1700** · solved 42k · also: binary search, data structures, dp, two pointers
- [Solve The Maze](https://codeforces.com/problemset/problem/1365/D) — **1700** · solved 21k · also: constructive algorithms, dfs and similar, dsu, graphs, greedy, shortest paths
- [Sleeping Schedule](https://codeforces.com/problemset/problem/1324/E) — **1700** · solved 17k · also: dp
- [George and Job](https://codeforces.com/problemset/problem/467/C) — **1700** · solved 17k · also: dp
- [Little Girl and Maximum XOR](https://codeforces.com/problemset/problem/276/D) — **1700** · solved 17k · also: bitmasks, dp, greedy, math
- [Multiplication Table](https://codeforces.com/problemset/problem/448/D) — **1800** · solved 17k · also: binary search

### 2000-2200

- [Odd-Even Subsequence](https://codeforces.com/problemset/problem/1370/D) — **2000** · solved 11k · also: binary search, dp, dsu, greedy
- [Yet Another Yet Another Task](https://codeforces.com/problemset/problem/1359/D) — **2000** · solved 11k · also: data structures, dp, two pointers
- [Three Integers](https://codeforces.com/problemset/problem/1311/D) — **2000** · solved 10k · also: math
- [Powerful array](https://codeforces.com/problemset/problem/86/D) — **2200** · solved 17k · also: data structures, math, two pointers
- [Compatible Numbers](https://codeforces.com/problemset/problem/165/E) — **2200** · solved 10k · also: bitmasks, dfs and similar, dp

### 2300+ (hardest)

- [Physical Education Lessons](https://codeforces.com/problemset/problem/915/E) — **2300** · solved 6k · also: data structures, sortings
- [Sliding Tree](https://codeforces.com/problemset/problem/2134/D) — **2300** · solved 4k · also: constructive algorithms, dfs and similar, greedy, trees
- [Grid Xor](https://codeforces.com/problemset/problem/1628/C) — **2300** · solved 4k · also: constructive algorithms, greedy, interactive, math
- [Machine Learning](https://codeforces.com/problemset/problem/940/F) — **2600** · solved 3k · also: data structures

---

## 2. Math

_Codeforces tags: `math`_

### 800-1000

- [Watermelon](https://codeforces.com/problemset/problem/4/A) — **800** · solved 702k · also: brute force
- [Domino piling](https://codeforces.com/problemset/problem/50/A) — **800** · solved 325k · also: greedy
- [Elephant](https://codeforces.com/problemset/problem/617/A) — **800** · solved 254k
- [Soldier and Bananas](https://codeforces.com/problemset/problem/546/A) — **800** · solved 240k · also: brute force, implementation
- [Theatre Square](https://codeforces.com/problemset/problem/1/A) — **1000** · solved 325k
- [Young Physicist](https://codeforces.com/problemset/problem/69/A) — **1000** · solved 205k · also: implementation

### 1100-1300

- [Fancy Fence](https://codeforces.com/problemset/problem/270/A) — **1100** · solved 51k · also: geometry, implementation
- [Vanya and Lanterns](https://codeforces.com/problemset/problem/492/B) — **1200** · solved 92k · also: binary search, implementation, sortings
- [K-th Not Divisible by n](https://codeforces.com/problemset/problem/1352/C) — **1200** · solved 71k · also: binary search
- [Same Differences](https://codeforces.com/problemset/problem/1520/D) — **1200** · solved 67k · also: data structures, hashing
- [T-primes](https://codeforces.com/problemset/problem/230/B) — **1300** · solved 98k · also: binary search, implementation, number theory
- [Product of Three Numbers](https://codeforces.com/problemset/problem/1294/C) — **1300** · solved 48k · also: greedy, number theory

### 1400-1600

- [Two Buttons](https://codeforces.com/problemset/problem/520/B) — **1400** · solved 64k · also: dfs and similar, graphs, greedy, implementation, shortest paths
- [I Hate 1111](https://codeforces.com/problemset/problem/1526/B) — **1400** · solved 37k · also: dp, number theory
- [Maximum Median](https://codeforces.com/problemset/problem/1201/C) — **1400** · solved 35k · also: binary search, greedy, sortings
- [Zero Remainder Array](https://codeforces.com/problemset/problem/1374/D) — **1400** · solved 32k · also: sortings, two pointers
- [Divisibility by Eight](https://codeforces.com/problemset/problem/550/C) — **1500** · solved 33k · also: brute force, dp
- [Good Subarrays](https://codeforces.com/problemset/problem/1398/C) — **1600** · solved 38k · also: data structures, dp

### 1700-1900

- [Little Girl and Maximum XOR](https://codeforces.com/problemset/problem/276/D) — **1700** · solved 17k · also: bitmasks, dp, greedy, implementation
- [Mixing Water](https://codeforces.com/problemset/problem/1359/C) — **1700** · solved 15k · also: binary search
- [Another Problem About Dividing Numbers](https://codeforces.com/problemset/problem/1538/D) — **1700** · solved 15k · also: constructive algorithms, number theory
- [Baby Ehab Partitions Again](https://codeforces.com/problemset/problem/1516/C) — **1700** · solved 14k · also: bitmasks, constructive algorithms, dp
- [AND, OR and square sum](https://codeforces.com/problemset/problem/1368/D) — **1700** · solved 14k · also: bitmasks, greedy
- [Carousel](https://codeforces.com/problemset/problem/1328/D) — **1800** · solved 15k · also: constructive algorithms, dp, graphs, greedy

### 2000-2200

- [The least round way](https://codeforces.com/problemset/problem/2/B) — **2000** · solved 13k · also: dp
- [Two Divisors](https://codeforces.com/problemset/problem/1366/D) — **2000** · solved 13k · also: constructive algorithms, number theory
- [Three Integers](https://codeforces.com/problemset/problem/1311/D) — **2000** · solved 10k · also: brute force
- [Ant colony](https://codeforces.com/problemset/problem/474/F) — **2100** · solved 12k · also: data structures, number theory
- [Powerful array](https://codeforces.com/problemset/problem/86/D) — **2200** · solved 17k · also: data structures, implementation, two pointers

### 2300+ (hardest)

- [The Child and Sequence](https://codeforces.com/problemset/problem/438/D) — **2300** · solved 12k · also: data structures
- [Count Pairs](https://codeforces.com/problemset/problem/1188/B) — **2300** · solved 5k · also: matrices, number theory, two pointers
- [Sasha and Array](https://codeforces.com/problemset/problem/718/C) — **2300** · solved 5k · also: data structures, matrices
- [The Sum of the k-th Powers](https://codeforces.com/problemset/problem/622/F) — **2600** · solved 5k

---

## 3. Greedy

_Codeforces tags: `greedy`_

### 800-1000

- [Team](https://codeforces.com/problemset/problem/231/A) — **800** · solved 439k · also: brute force
- [Domino piling](https://codeforces.com/problemset/problem/50/A) — **800** · solved 325k · also: math
- [Helpful Maths](https://codeforces.com/problemset/problem/339/A) — **800** · solved 281k · also: implementation, sortings, strings
- [Twins](https://codeforces.com/problemset/problem/160/A) — **900** · solved 146k · also: sortings
- [Gravity Flip](https://codeforces.com/problemset/problem/405/A) — **900** · solved 129k · also: implementation, sortings
- [Chat room](https://codeforces.com/problemset/problem/58/A) — **1000** · solved 158k · also: strings

### 1100-1300

- [Taxi](https://codeforces.com/problemset/problem/158/B) — **1100** · solved 105k · also: *special, implementation
- [Chewbaсca and Number](https://codeforces.com/problemset/problem/514/A) — **1200** · solved 73k · also: implementation
- [BerSU Ball](https://codeforces.com/problemset/problem/489/B) — **1200** · solved 58k · also: dfs and similar, dp, graph matchings, sortings, two pointers
- [Alternating Subsequence](https://codeforces.com/problemset/problem/1343/C) — **1200** · solved 45k · also: dp, two pointers
- [Assembly via Minimums](https://codeforces.com/problemset/problem/1857/C) — **1200** · solved 44k · also: sortings
- [Product of Three Numbers](https://codeforces.com/problemset/problem/1294/C) — **1300** · solved 48k · also: math, number theory

### 1400-1600

- [Two Buttons](https://codeforces.com/problemset/problem/520/B) — **1400** · solved 64k · also: dfs and similar, graphs, implementation, math, shortest paths
- [Given Length and Sum of Digits...](https://codeforces.com/problemset/problem/489/C) — **1400** · solved 52k · also: dp, implementation
- [Maximum Median](https://codeforces.com/problemset/problem/1201/C) — **1400** · solved 35k · also: binary search, math, sortings
- [Two Substrings](https://codeforces.com/problemset/problem/550/A) — **1500** · solved 44k · also: brute force, dp, implementation, strings
- [Little Girl and Maximum Sum](https://codeforces.com/problemset/problem/276/C) — **1500** · solved 41k · also: data structures, implementation, sortings
- [Woodcutters](https://codeforces.com/problemset/problem/545/C) — **1500** · solved 34k · also: dp

### 1700-1900

- [Solve The Maze](https://codeforces.com/problemset/problem/1365/D) — **1700** · solved 21k · also: constructive algorithms, dfs and similar, dsu, graphs, implementation, shortest paths
- [Little Girl and Maximum XOR](https://codeforces.com/problemset/problem/276/D) — **1700** · solved 17k · also: bitmasks, dp, implementation, math
- [Running Miles](https://codeforces.com/problemset/problem/1826/D) — **1700** · solved 16k · also: brute force, dp
- [Table Decorations](https://codeforces.com/problemset/problem/478/C) — **1800** · solved 21k
- [The Sports Festival](https://codeforces.com/problemset/problem/1509/C) — **1800** · solved 16k · also: dp
- [Longest Regular Bracket Sequence](https://codeforces.com/problemset/problem/5/C) — **1900** · solved 19k · also: constructive algorithms, data structures, dp, sortings, strings

### 2000-2200

- [Odd-Even Subsequence](https://codeforces.com/problemset/problem/1370/D) — **2000** · solved 11k · also: binary search, dp, dsu, implementation
- [Jzzhu and Cities](https://codeforces.com/problemset/problem/449/B) — **2000** · solved 11k · also: graphs, shortest paths
- [Orac and Medians](https://codeforces.com/problemset/problem/1349/B) — **2000** · solved 9k · also: constructive algorithms, math
- [Reachability from the Capital](https://codeforces.com/problemset/problem/999/E) — **2000** · solved 8k · also: dfs and similar, graphs
- [Ciel the Commander](https://codeforces.com/problemset/problem/321/C) — **2100** · solved 10k · also: constructive algorithms, dfs and similar, divide and conquer, trees

### 2300+ (hardest)

- [Team Building](https://codeforces.com/problemset/problem/1316/E) — **2300** · solved 6k · also: bitmasks, dp, sortings
- [Centroids](https://codeforces.com/problemset/problem/708/C) — **2300** · solved 4k · also: data structures, dfs and similar, dp, graphs, trees
- [Minimal Labels](https://codeforces.com/problemset/problem/825/E) — **2300** · solved 4k · also: data structures, dfs and similar, graphs
- [Buy Low Sell High](https://codeforces.com/problemset/problem/865/D) — **2400** · solved 8k · also: constructive algorithms, data structures

---

## 4. Sorting

_Codeforces tags: `sortings`_

### 800-1000

- [Helpful Maths](https://codeforces.com/problemset/problem/339/A) — **800** · solved 281k · also: greedy, implementation, strings
- [Amusing Joke](https://codeforces.com/problemset/problem/141/A) — **800** · solved 103k · also: implementation, strings
- [The New Year: Meeting Friends](https://codeforces.com/problemset/problem/723/A) — **800** · solved 103k · also: implementation, math
- [Twins](https://codeforces.com/problemset/problem/160/A) — **900** · solved 146k · also: greedy
- [Gravity Flip](https://codeforces.com/problemset/problem/405/A) — **900** · solved 129k · also: greedy, implementation
- [Dragons](https://codeforces.com/problemset/problem/230/A) — **1000** · solved 100k · also: greedy

### 1100-1300

- [Laptops](https://codeforces.com/problemset/problem/456/A) — **1100** · solved 58k
- [Vanya and Lanterns](https://codeforces.com/problemset/problem/492/B) — **1200** · solved 92k · also: binary search, implementation, math
- [BerSU Ball](https://codeforces.com/problemset/problem/489/B) — **1200** · solved 58k · also: dfs and similar, dp, graph matchings, greedy, two pointers
- [Kuriyama Mirai's Stones](https://codeforces.com/problemset/problem/433/B) — **1200** · solved 55k · also: dp, implementation
- [Sort the Array](https://codeforces.com/problemset/problem/451/B) — **1300** · solved 54k · also: implementation
- [Pashmak and Flowers](https://codeforces.com/problemset/problem/459/B) — **1300** · solved 48k · also: combinatorics, implementation

### 1400-1600

- [Pair of Topics](https://codeforces.com/problemset/problem/1324/D) — **1400** · solved 35k · also: binary search, data structures, two pointers
- [Maximum Median](https://codeforces.com/problemset/problem/1201/C) — **1400** · solved 35k · also: binary search, greedy, math
- [Exams](https://codeforces.com/problemset/problem/479/C) — **1400** · solved 33k · also: greedy
- [Zero Remainder Array](https://codeforces.com/problemset/problem/1374/D) — **1400** · solved 32k · also: math, two pointers
- [Little Girl and Maximum Sum](https://codeforces.com/problemset/problem/276/C) — **1500** · solved 41k · also: data structures, greedy, implementation
- [Kefa and Company](https://codeforces.com/problemset/problem/580/B) — **1500** · solved 33k · also: binary search, two pointers

### 1700-1900

- [Array Destruction](https://codeforces.com/problemset/problem/1474/C) — **1700** · solved 14k · also: brute force, constructive algorithms, data structures, greedy, implementation
- [Quiz Master](https://codeforces.com/problemset/problem/1777/C) — **1700** · solved 13k · also: binary search, math, number theory, two pointers
- [Pashmak and Parmida's problem](https://codeforces.com/problemset/problem/459/D) — **1800** · solved 13k · also: data structures, divide and conquer
- [Colored Rectangles](https://codeforces.com/problemset/problem/1398/D) — **1800** · solved 13k · also: dp, greedy
- [Chef Monocarp](https://codeforces.com/problemset/problem/1437/C) — **1800** · solved 12k · also: dp, flows, graph matchings, greedy, math
- [Longest Regular Bracket Sequence](https://codeforces.com/problemset/problem/5/C) — **1900** · solved 19k · also: constructive algorithms, data structures, dp, greedy, strings

### 2000-2200

- [Match Points](https://codeforces.com/problemset/problem/1156/C) — **2000** · solved 7k · also: binary search, greedy, ternary search, two pointers
- [Minimizing Difference](https://codeforces.com/problemset/problem/1244/E) — **2000** · solved 7k · also: binary search, constructive algorithms, greedy, ternary search, two pointers
- [GCD and MST](https://codeforces.com/problemset/problem/1513/D) — **2000** · solved 7k · also: constructive algorithms, dsu, graphs, greedy, number theory
- [Choosing flowers](https://codeforces.com/problemset/problem/1379/C) — **2000** · solved 6k · also: binary search, brute force, data structures, dfs and similar, dp, greedy, two pointers
- [Maximum Value](https://codeforces.com/problemset/problem/484/B) — **2100** · solved 8k · also: binary search, math, two pointers

### 2300+ (hardest)

- [A Simple Task](https://codeforces.com/problemset/problem/558/E) — **2300** · solved 8k · also: data structures, strings
- [Sonya and Problem Wihtout a Legend](https://codeforces.com/problemset/problem/713/C) — **2300** · solved 7k · also: dp
- [Physical Education Lessons](https://codeforces.com/problemset/problem/915/E) — **2300** · solved 6k · also: data structures, implementation
- [Team Building](https://codeforces.com/problemset/problem/1316/E) — **2300** · solved 6k · also: bitmasks, dp, greedy

---

## 5. Two Pointers & Sliding Window

_Codeforces tags: `two pointers`_

### 800-1000

- [Sereja and Dima](https://codeforces.com/problemset/problem/381/A) — **800** · solved 92k · also: greedy, implementation
- [Prepend and Append](https://codeforces.com/problemset/problem/1791/C) — **800** · solved 77k · also: implementation
- [Favorite Sequence](https://codeforces.com/problemset/problem/1462/A) — **800** · solved 50k · also: implementation
- [Ski Resort](https://codeforces.com/problemset/problem/1840/C) — **1000** · solved 50k · also: combinatorics, math
- [Black and White Stripe](https://codeforces.com/problemset/problem/1690/D) — **1000** · solved 49k · also: implementation
- [Traffic Light](https://codeforces.com/problemset/problem/1744/C) — **1000** · solved 46k · also: binary search, implementation

### 1100-1300

- [Eating Candies](https://codeforces.com/problemset/problem/1669/F) — **1100** · solved 41k · also: binary search, data structures, greedy
- [Maximum Sum](https://codeforces.com/problemset/problem/1832/B) — **1100** · solved 37k · also: brute force, sortings
- [Yarik and Array](https://codeforces.com/problemset/problem/1899/C) — **1100** · solved 36k · also: dp, greedy
- [BerSU Ball](https://codeforces.com/problemset/problem/489/B) — **1200** · solved 58k · also: dfs and similar, dp, graph matchings, greedy, sortings
- [Alternating Subsequence](https://codeforces.com/problemset/problem/1343/C) — **1200** · solved 45k · also: dp, greedy
- [Queries about less or equal elements](https://codeforces.com/problemset/problem/600/B) — **1300** · solved 40k · also: binary search, data structures, sortings

### 1400-1600

- [Books](https://codeforces.com/problemset/problem/279/B) — **1400** · solved 75k · also: binary search, brute force, implementation
- [Pair of Topics](https://codeforces.com/problemset/problem/1324/D) — **1400** · solved 35k · also: binary search, data structures, sortings
- [Zero Remainder Array](https://codeforces.com/problemset/problem/1374/D) — **1400** · solved 32k · also: math, sortings
- [2^Sort](https://codeforces.com/problemset/problem/1692/G) — **1400** · solved 27k · also: data structures, dp, sortings
- [Array Game](https://codeforces.com/problemset/problem/1904/C) — **1400** · solved 25k · also: binary search, brute force, data structures, sortings
- [Kefa and Company](https://codeforces.com/problemset/problem/580/B) — **1500** · solved 33k · also: binary search, sortings

### 1700-1900

- [Number of Ways](https://codeforces.com/problemset/problem/466/C) — **1700** · solved 42k · also: binary search, brute force, data structures, dp
- [Checkposts](https://codeforces.com/problemset/problem/427/C) — **1700** · solved 17k · also: dfs and similar, graphs
- [Prefix Flip (Hard Version)](https://codeforces.com/problemset/problem/1381/A2) — **1700** · solved 14k · also: constructive algorithms, data structures, implementation, strings
- [Constant Palindrome Sum](https://codeforces.com/problemset/problem/1343/D) — **1700** · solved 14k · also: brute force, data structures, greedy
- [Eugene and an array](https://codeforces.com/problemset/problem/1333/C) — **1700** · solved 14k · also: binary search, data structures, implementation
- [Modulo Sum](https://codeforces.com/problemset/problem/577/B) — **1900** · solved 15k · also: combinatorics, data structures, dp

### 2000-2200

- [Prefixes and Suffixes](https://codeforces.com/problemset/problem/432/D) — **2000** · solved 12k · also: dp, string suffix structures, strings
- [Yet Another Yet Another Task](https://codeforces.com/problemset/problem/1359/D) — **2000** · solved 11k · also: data structures, dp, implementation
- [Match Points](https://codeforces.com/problemset/problem/1156/C) — **2000** · solved 7k · also: binary search, greedy, sortings, ternary search
- [Maximum Value](https://codeforces.com/problemset/problem/484/B) — **2100** · solved 8k · also: binary search, math, sortings
- [Powerful array](https://codeforces.com/problemset/problem/86/D) — **2200** · solved 17k · also: data structures, implementation, math

### 2300+ (hardest)

- [Count Pairs](https://codeforces.com/problemset/problem/1188/B) — **2300** · solved 5k · also: math, matrices, number theory
- [Fixed Point Removal](https://codeforces.com/problemset/problem/1404/C) — **2300** · solved 3k · also: binary search, constructive algorithms, data structures, greedy
- [Mocha and Diana (Hard Version)](https://codeforces.com/problemset/problem/1559/D2) — **2500** · solved 3k · also: brute force, constructive algorithms, dfs and similar, dsu, graphs, greedy, trees
- [Three Occurrences](https://codeforces.com/problemset/problem/1418/G) — **2500** · solved 3k · also: data structures, divide and conquer, hashing

---

## 6. Binary Search

_Codeforces tags: `binary search`_

### 800-1000

- [New Year and Hurry](https://codeforces.com/problemset/problem/750/A) — **800** · solved 94k · also: brute force, implementation, math
- [Legs](https://codeforces.com/problemset/problem/1996/A) — **800** · solved 58k · also: math, ternary search
- [Can I Square?](https://codeforces.com/problemset/problem/1915/C) — **800** · solved 55k · also: implementation
- [Square Year](https://codeforces.com/problemset/problem/2114/A) — **800** · solved 44k · also: brute force, math
- [Traffic Light](https://codeforces.com/problemset/problem/1744/C) — **1000** · solved 46k · also: implementation, two pointers
- [K-divisible Sum](https://codeforces.com/problemset/problem/1476/A) — **1000** · solved 45k · also: constructive algorithms, greedy, math

### 1100-1300

- [Interesting drink](https://codeforces.com/problemset/problem/706/B) — **1100** · solved 87k · also: dp, implementation
- [Building an Aquarium](https://codeforces.com/problemset/problem/1873/E) — **1100** · solved 46k · also: sortings
- [Vanya and Lanterns](https://codeforces.com/problemset/problem/492/B) — **1200** · solved 92k · also: implementation, math, sortings
- [K-th Not Divisible by n](https://codeforces.com/problemset/problem/1352/C) — **1200** · solved 71k · also: math
- [Worms](https://codeforces.com/problemset/problem/474/B) — **1200** · solved 64k · also: implementation
- [T-primes](https://codeforces.com/problemset/problem/230/B) — **1300** · solved 98k · also: implementation, math, number theory

### 1400-1600

- [Books](https://codeforces.com/problemset/problem/279/B) — **1400** · solved 75k · also: brute force, implementation, two pointers
- [Pair of Topics](https://codeforces.com/problemset/problem/1324/D) — **1400** · solved 35k · also: data structures, sortings, two pointers
- [Maximum Median](https://codeforces.com/problemset/problem/1201/C) — **1400** · solved 35k · also: greedy, math, sortings
- [Karen and Coffee](https://codeforces.com/problemset/problem/816/B) — **1400** · solved 29k · also: data structures, implementation
- [Kefa and Company](https://codeforces.com/problemset/problem/580/B) — **1500** · solved 33k · also: sortings, two pointers
- [Hamburgers](https://codeforces.com/problemset/problem/371/C) — **1600** · solved 36k · also: brute force

### 1700-1900

- [Number of Ways](https://codeforces.com/problemset/problem/466/C) — **1700** · solved 42k · also: brute force, data structures, dp, two pointers
- [Password](https://codeforces.com/problemset/problem/126/B) — **1700** · solved 26k · also: dp, hashing, string suffix structures, strings
- [Mixing Water](https://codeforces.com/problemset/problem/1359/C) — **1700** · solved 15k · also: math
- [Eugene and an array](https://codeforces.com/problemset/problem/1333/C) — **1700** · solved 14k · also: data structures, implementation, two pointers
- [Searching Local Minimum](https://codeforces.com/problemset/problem/1479/A) — **1700** · solved 13k · also: interactive, ternary search
- [Multiplication Table](https://codeforces.com/problemset/problem/448/D) — **1800** · solved 17k · also: brute force

### 2000-2200

- [Odd-Even Subsequence](https://codeforces.com/problemset/problem/1370/D) — **2000** · solved 11k · also: dp, dsu, greedy, implementation
- [Max Median](https://codeforces.com/problemset/problem/1486/D) — **2100** · solved 10k · also: data structures, dp
- [Blood Cousins](https://codeforces.com/problemset/problem/208/E) — **2100** · solved 9k · also: data structures, dfs and similar, trees
- [A and B and Lecture Rooms](https://codeforces.com/problemset/problem/519/E) — **2100** · solved 9k · also: data structures, dfs and similar, dp, trees
- [Tree Requests](https://codeforces.com/problemset/problem/570/D) — **2200** · solved 9k · also: bitmasks, constructive algorithms, dfs and similar, graphs, trees

### 2300+ (hardest)

- [Qpwoeirut and Vertices](https://codeforces.com/problemset/problem/1706/E) — **2300** · solved 4k · also: data structures, dfs and similar, divide and conquer, dsu, greedy, trees
- [Complete The Graph](https://codeforces.com/problemset/problem/715/B) — **2300** · solved 3k · also: constructive algorithms, graphs, shortest paths
- [MEX Queries](https://codeforces.com/problemset/problem/817/F) — **2300** · solved 3k · also: data structures, trees
- [Blood Cousins Return](https://codeforces.com/problemset/problem/246/E) — **2400** · solved 5k · also: data structures, dfs and similar, dp, sortings

---

## 7. Strings

_Codeforces tags: `strings`_

### 800-1000

- [Way Too Long Words](https://codeforces.com/problemset/problem/71/A) — **800** · solved 513k
- [Petya and Strings](https://codeforces.com/problemset/problem/112/A) — **800** · solved 292k · also: implementation
- [Boy or Girl](https://codeforces.com/problemset/problem/236/A) — **800** · solved 286k · also: brute force, implementation
- [Helpful Maths](https://codeforces.com/problemset/problem/339/A) — **800** · solved 281k · also: greedy, implementation, sortings
- [Word Capitalization](https://codeforces.com/problemset/problem/281/A) — **800** · solved 276k · also: implementation
- [String Task](https://codeforces.com/problemset/problem/118/A) — **1000** · solved 235k · also: implementation

### 1100-1300

- [Erase First or Second Letter](https://codeforces.com/problemset/problem/1917/B) — **1100** · solved 38k · also: brute force, combinatorics, data structures, dp
- [Slavic's Exam](https://codeforces.com/problemset/problem/1999/D) — **1100** · solved 33k · also: greedy, implementation
- [Yet Another Palindrome Problem](https://codeforces.com/problemset/problem/1324/B) — **1100** · solved 31k · also: brute force
- [Binary Cut](https://codeforces.com/problemset/problem/1971/D) — **1100** · solved 31k · also: dp, greedy, implementation, sortings
- [Double Strings](https://codeforces.com/problemset/problem/1703/D) — **1100** · solved 29k · also: brute force, data structures
- [Remove Two Letters](https://codeforces.com/problemset/problem/1800/D) — **1200** · solved 30k · also: data structures, greedy, hashing

### 1400-1600

- [Subsequence Hate](https://codeforces.com/problemset/problem/1363/B) — **1400** · solved 26k · also: implementation
- [Two Substrings](https://codeforces.com/problemset/problem/550/A) — **1500** · solved 44k · also: brute force, dp, greedy, implementation
- [Vasya and String](https://codeforces.com/problemset/problem/676/C) — **1500** · solved 23k · also: binary search, dp, two pointers
- [Codeforces Subsequences](https://codeforces.com/problemset/problem/1368/B) — **1500** · solved 22k · also: brute force, constructive algorithms, greedy, math
- [K-Complete Word](https://codeforces.com/problemset/problem/1332/C) — **1500** · solved 21k · also: dfs and similar, dsu, greedy, implementation
- [They Are Everywhere](https://codeforces.com/problemset/problem/701/C) — **1500** · solved 20k · also: binary search, two pointers

### 1700-1900

- [Password](https://codeforces.com/problemset/problem/126/B) — **1700** · solved 26k · also: binary search, dp, hashing, string suffix structures
- [Prefix Flip (Hard Version)](https://codeforces.com/problemset/problem/1381/A2) — **1700** · solved 14k · also: constructive algorithms, data structures, implementation, two pointers
- [Spy-string](https://codeforces.com/problemset/problem/1360/F) — **1700** · solved 14k · also: bitmasks, brute force, constructive algorithms, dp, hashing
- [String Game](https://codeforces.com/problemset/problem/778/A) — **1700** · solved 13k · also: binary search, greedy
- [Good Substrings](https://codeforces.com/problemset/problem/271/D) — **1800** · solved 14k · also: data structures
- [Longest Regular Bracket Sequence](https://codeforces.com/problemset/problem/5/C) — **1900** · solved 19k · also: constructive algorithms, data structures, dp, greedy, sortings

### 2000-2200

- [Prefixes and Suffixes](https://codeforces.com/problemset/problem/432/D) — **2000** · solved 12k · also: dp, string suffix structures, two pointers
- [Compress Words](https://codeforces.com/problemset/problem/1200/E) — **2000** · solved 10k · also: brute force, hashing, implementation, string suffix structures
- [Watto and Mechanism](https://codeforces.com/problemset/problem/514/C) — **2000** · solved 8k · also: binary search, data structures, hashing, string suffix structures
- [Number of Subsequences](https://codeforces.com/problemset/problem/1426/F) — **2000** · solved 6k · also: combinatorics, dp
- [Erase and Extend (Hard Version)](https://codeforces.com/problemset/problem/1537/E2) — **2200** · solved 6k · also: binary search, data structures, greedy, hashing, string suffix structures, two pointers

### 2300+ (hardest)

- [A Simple Task](https://codeforces.com/problemset/problem/558/E) — **2300** · solved 8k · also: data structures, sortings
- [Anthem of Berland](https://codeforces.com/problemset/problem/808/G) — **2300** · solved 4k · also: dp
- [Cases](https://codeforces.com/problemset/problem/1995/D) — **2300** · solved 3k · also: bitmasks, brute force, dp
- [You Are Given Some Strings...](https://codeforces.com/problemset/problem/1202/E) — **2400** · solved 3k · also: brute force, string suffix structures

---

## 8. Number Theory

_Codeforces tags: `number theory`_

### 800-1000

- [Game with Integers](https://codeforces.com/problemset/problem/1899/A) — **800** · solved 98k · also: games, math
- [Again Twenty Five!](https://codeforces.com/problemset/problem/630/A) — **800** · solved 95k
- [Design Tutorial: Learn from Math](https://codeforces.com/problemset/problem/472/A) — **800** · solved 72k · also: math
- [Maximum GCD](https://codeforces.com/problemset/problem/1370/A) — **800** · solved 66k · also: greedy, implementation, math
- [Odd Divisor](https://codeforces.com/problemset/problem/1475/A) — **900** · solved 90k · also: math
- [Lucky Division](https://codeforces.com/problemset/problem/122/A) — **1000** · solved 152k · also: brute force

### 1100-1300

- [Coprime](https://codeforces.com/problemset/problem/1742/D) — **1100** · solved 35k · also: brute force, greedy
- [Pleasant Pairs](https://codeforces.com/problemset/problem/1541/B) — **1200** · solved 40k · also: brute force, implementation, math
- [T-primes](https://codeforces.com/problemset/problem/230/B) — **1300** · solved 98k · also: binary search, implementation, math
- [Product of Three Numbers](https://codeforces.com/problemset/problem/1294/C) — **1300** · solved 48k · also: greedy, math
- [Buying Shovels](https://codeforces.com/problemset/problem/1360/D) — **1300** · solved 44k · also: math
- [Omkar and Last Class of Math](https://codeforces.com/problemset/problem/1372/B) — **1300** · solved 37k · also: greedy, math

### 1400-1600

- [I Hate 1111](https://codeforces.com/problemset/problem/1526/B) — **1400** · solved 37k · also: dp, math
- [Orac and Models](https://codeforces.com/problemset/problem/1350/B) — **1400** · solved 30k · also: dp, math
- [Mashmokh and ACM](https://codeforces.com/problemset/problem/414/B) — **1400** · solved 30k · also: combinatorics, dp
- [Fadi and LCM](https://codeforces.com/problemset/problem/1285/C) — **1400** · solved 29k · also: brute force, math
- [Number Game](https://codeforces.com/problemset/problem/1370/C) — **1400** · solved 27k · also: games, math
- [Plus and Multiply](https://codeforces.com/problemset/problem/1542/B) — **1500** · solved 27k · also: constructive algorithms, math

### 1700-1900

- [Another Problem About Dividing Numbers](https://codeforces.com/problemset/problem/1538/D) — **1700** · solved 15k · also: constructive algorithms, math
- [Soldier and Number Game](https://codeforces.com/problemset/problem/546/D) — **1700** · solved 14k · also: constructive algorithms, dp, math
- [Ehab the Xorcist](https://codeforces.com/problemset/problem/1325/D) — **1700** · solved 14k · also: bitmasks, constructive algorithms, greedy
- [Deleting Divisors](https://codeforces.com/problemset/problem/1537/D) — **1700** · solved 14k · also: games, math
- [Quiz Master](https://codeforces.com/problemset/problem/1777/C) — **1700** · solved 13k · also: binary search, math, sortings, two pointers
- [Even Subarrays](https://codeforces.com/problemset/problem/1731/C) — **1700** · solved 12k · also: bitmasks, brute force, hashing, math

### 2000-2200

- [Two Divisors](https://codeforces.com/problemset/problem/1366/D) — **2000** · solved 13k · also: constructive algorithms, math
- [SUM and REPLACE](https://codeforces.com/problemset/problem/920/F) — **2000** · solved 9k · also: brute force, data structures, dsu
- [The Football Season](https://codeforces.com/problemset/problem/1244/C) — **2000** · solved 8k · also: brute force, math
- [Ant colony](https://codeforces.com/problemset/problem/474/F) — **2100** · solved 12k · also: data structures, math
- [Gerald and Giant Chess](https://codeforces.com/problemset/problem/559/C) — **2200** · solved 8k · also: combinatorics, dp, math

### 2300+ (hardest)

- [Count Pairs](https://codeforces.com/problemset/problem/1188/B) — **2300** · solved 5k · also: math, matrices, two pointers
- [Devu and Flowers](https://codeforces.com/problemset/problem/451/E) — **2300** · solved 5k · also: bitmasks, combinatorics
- [DZY Loves Fibonacci Numbers](https://codeforces.com/problemset/problem/446/C) — **2400** · solved 4k · also: data structures, math
- [Beautiful numbers](https://codeforces.com/problemset/problem/55/D) — **2500** · solved 5k · also: dp

---

## 9. Combinatorics

_Codeforces tags: `combinatorics`_

### 800-1000

- [Sum in Binary Tree](https://codeforces.com/problemset/problem/1843/C) — **800** · solved 40k · also: bitmasks, math, trees
- [Password](https://codeforces.com/problemset/problem/1743/A) — **800** · solved 30k · also: brute force, implementation, math
- [Longest Divisors Interval](https://codeforces.com/problemset/problem/1855/B) — **900** · solved 47k · also: brute force, greedy, math, number theory
- [Luntik and Subsequences](https://codeforces.com/problemset/problem/1582/B) — **900** · solved 35k · also: math
- [Having Been a Treasurer in the Past, I Help Goblins Deceive](https://codeforces.com/problemset/problem/2072/B) — **900** · solved 31k · also: constructive algorithms, strings
- [Ski Resort](https://codeforces.com/problemset/problem/1840/C) — **1000** · solved 50k · also: math, two pointers

### 1100-1300

- [Erase First or Second Letter](https://codeforces.com/problemset/problem/1917/B) — **1100** · solved 38k · also: brute force, data structures, dp, strings
- [AND 0, Sum Big](https://codeforces.com/problemset/problem/1514/B) — **1200** · solved 36k · also: bitmasks, math
- [Pashmak and Flowers](https://codeforces.com/problemset/problem/459/B) — **1300** · solved 48k · also: implementation, sortings
- [Random Teams](https://codeforces.com/problemset/problem/478/B) — **1300** · solved 42k · also: constructive algorithms, greedy, math
- [Dreamoon and WiFi](https://codeforces.com/problemset/problem/476/B) — **1300** · solved 38k · also: bitmasks, brute force, dp, math, probabilities
- [K-th Beautiful String](https://codeforces.com/problemset/problem/1328/B) — **1300** · solved 32k · also: binary search, brute force, implementation, math

### 1400-1600

- [Mashmokh and ACM](https://codeforces.com/problemset/problem/414/B) — **1400** · solved 30k · also: dp, number theory
- [Ball in Berland](https://codeforces.com/problemset/problem/1475/C) — **1400** · solved 28k · also: graphs, math
- [AND Sequences](https://codeforces.com/problemset/problem/1513/B) — **1400** · solved 21k · also: bitmasks, constructive algorithms, math
- [Weird Sum](https://codeforces.com/problemset/problem/1648/A) — **1400** · solved 19k · also: data structures, geometry, math, matrices, sortings
- [Fun](https://codeforces.com/problemset/problem/1996/D) — **1500** · solved 20k · also: binary search, brute force, math, number theory
- [Kuroni and Impossible Calculation](https://codeforces.com/problemset/problem/1305/C) — **1600** · solved 19k · also: brute force, math, number theory

### 1700-1900

- [Chat Screenshots](https://codeforces.com/problemset/problem/1931/F) — **1700** · solved 12k · also: dfs and similar, graphs
- [Close Tuples (hard version)](https://codeforces.com/problemset/problem/1462/E2) — **1700** · solved 12k · also: binary search, implementation, math, sortings, two pointers
- [Beautiful Numbers](https://codeforces.com/problemset/problem/300/C) — **1800** · solved 12k · also: brute force
- [Modulo Sum](https://codeforces.com/problemset/problem/577/B) — **1900** · solved 15k · also: data structures, dp, two pointers
- [Classy Numbers](https://codeforces.com/problemset/problem/1036/C) — **1900** · solved 14k · also: dp
- [Zigzags](https://codeforces.com/problemset/problem/1400/D) — **1900** · solved 11k · also: brute force, data structures, math, two pointers

### 2000-2200

- [Square Subsets](https://codeforces.com/problemset/problem/895/C) — **2000** · solved 8k · also: bitmasks, dp, math
- [Modular Stability](https://codeforces.com/problemset/problem/1359/E) — **2000** · solved 7k · also: math, number theory
- [Number of Simple Paths](https://codeforces.com/problemset/problem/1454/E) — **2000** · solved 7k · also: dfs and similar, graphs, trees
- [Number of Subsequences](https://codeforces.com/problemset/problem/1426/F) — **2000** · solved 6k · also: dp, strings
- [Gerald and Giant Chess](https://codeforces.com/problemset/problem/559/C) — **2200** · solved 8k · also: dp, math, number theory

### 2300+ (hardest)

- [Devu and Flowers](https://codeforces.com/problemset/problem/451/E) — **2300** · solved 5k · also: bitmasks, number theory
- [Anton and School - 2](https://codeforces.com/problemset/problem/785/D) — **2300** · solved 4k · also: dp, math, number theory
- [Segment Sum](https://codeforces.com/problemset/problem/1073/E) — **2300** · solved 4k · also: bitmasks, dp, math
- [Jzzhu and Numbers](https://codeforces.com/problemset/problem/449/D) — **2400** · solved 6k · also: bitmasks, dp

---

## 10. Bitmasks

_Codeforces tags: `bitmasks`_

### 800-1000

- [Odd One Out](https://codeforces.com/problemset/problem/1915/A) — **800** · solved 81k · also: implementation
- [Not Quite Latin Square](https://codeforces.com/problemset/problem/1915/B) — **800** · solved 51k · also: brute force, implementation
- [We Need the Zero](https://codeforces.com/problemset/problem/1805/A) — **800** · solved 44k · also: brute force
- [And Then There Were K](https://codeforces.com/problemset/problem/1527/A) — **800** · solved 42k
- [Mocha and Math](https://codeforces.com/problemset/problem/1559/A) — **900** · solved 47k · also: constructive algorithms, math
- [Raising Bacteria](https://codeforces.com/problemset/problem/579/A) — **1000** · solved 81k

### 1100-1300

- [Fedor and New Game](https://codeforces.com/problemset/problem/467/B) — **1100** · solved 43k · also: brute force, constructive algorithms, implementation
- [Rock and Lever](https://codeforces.com/problemset/problem/1420/B) — **1200** · solved 37k · also: math
- [Petr and a Combination Lock](https://codeforces.com/problemset/problem/1097/B) — **1200** · solved 37k · also: brute force, dp
- [AND 0, Sum Big](https://codeforces.com/problemset/problem/1514/B) — **1200** · solved 36k · also: combinatorics, math
- [Make Almost Equal With Mod](https://codeforces.com/problemset/problem/1909/B) — **1200** · solved 33k · also: constructive algorithms, math, number theory
- [Dreamoon and WiFi](https://codeforces.com/problemset/problem/476/B) — **1300** · solved 38k · also: brute force, combinatorics, dp, math, probabilities

### 1400-1600

- [Preparing Olympiad](https://codeforces.com/problemset/problem/550/B) — **1400** · solved 28k · also: brute force
- [Johnny and Another Rating Drop](https://codeforces.com/problemset/problem/1362/C) — **1400** · solved 24k · also: greedy, math
- [Fortune Telling](https://codeforces.com/problemset/problem/1634/B) — **1400** · solved 23k · also: math
- [AGAGA XOOORRR](https://codeforces.com/problemset/problem/1516/B) — **1500** · solved 26k · also: brute force, dp, greedy
- [a-Good String](https://codeforces.com/problemset/problem/1385/D) — **1500** · solved 22k · also: brute force, divide and conquer, dp, implementation
- [Data Structures Fan](https://codeforces.com/problemset/problem/1872/E) — **1500** · solved 22k · also: binary search, data structures, dp

### 1700-1900

- [Little Girl and Maximum XOR](https://codeforces.com/problemset/problem/276/D) — **1700** · solved 17k · also: dp, greedy, implementation, math
- [Baby Ehab Partitions Again](https://codeforces.com/problemset/problem/1516/C) — **1700** · solved 14k · also: constructive algorithms, dp, math
- [AND, OR and square sum](https://codeforces.com/problemset/problem/1368/D) — **1700** · solved 14k · also: greedy, math
- [Cobb](https://codeforces.com/problemset/problem/1554/B) — **1700** · solved 14k · also: brute force, greedy, math
- [Ehab the Xorcist](https://codeforces.com/problemset/problem/1325/D) — **1700** · solved 14k · also: constructive algorithms, greedy, number theory
- [Bits](https://codeforces.com/problemset/problem/484/A) — **1700** · solved 14k · also: constructive algorithms

### 2000-2200

- [XOR on Segment](https://codeforces.com/problemset/problem/242/E) — **2000** · solved 12k · also: data structures
- [Minimax Problem](https://codeforces.com/problemset/problem/1288/D) — **2000** · solved 8k · also: binary search, dp
- [Square Subsets](https://codeforces.com/problemset/problem/895/C) — **2000** · solved 8k · also: combinatorics, dp, math
- [Compatible Numbers](https://codeforces.com/problemset/problem/165/E) — **2200** · solved 10k · also: brute force, dfs and similar, dp
- [Tree Requests](https://codeforces.com/problemset/problem/570/D) — **2200** · solved 9k · also: binary search, constructive algorithms, dfs and similar, graphs, trees

### 2300+ (hardest)

- [Xor-MST](https://codeforces.com/problemset/problem/888/G) — **2300** · solved 6k · also: constructive algorithms, data structures
- [Team Building](https://codeforces.com/problemset/problem/1316/E) — **2300** · solved 6k · also: dp, greedy, sortings
- [Devu and Flowers](https://codeforces.com/problemset/problem/451/E) — **2300** · solved 5k · also: combinatorics, number theory
- [Jzzhu and Numbers](https://codeforces.com/problemset/problem/449/D) — **2400** · solved 6k · also: combinatorics, dp

---

## 11. Dynamic Programming

_Codeforces tags: `dp`_

### 800-1000

- [Hit the Lottery](https://codeforces.com/problemset/problem/996/A) — **800** · solved 121k · also: greedy
- [Fair Division](https://codeforces.com/problemset/problem/1472/B) — **800** · solved 61k · also: greedy, math
- [Maximum Increase](https://codeforces.com/problemset/problem/702/A) — **800** · solved 56k · also: greedy, implementation
- [Kefa and First Steps](https://codeforces.com/problemset/problem/580/A) — **900** · solved 107k · also: brute force, implementation
- [New Year's Number](https://codeforces.com/problemset/problem/1475/B) — **900** · solved 58k · also: brute force, math
- [Raspberries](https://codeforces.com/problemset/problem/1883/C) — **1000** · solved 53k · also: math

### 1100-1300

- [Interesting drink](https://codeforces.com/problemset/problem/706/B) — **1100** · solved 87k · also: binary search, implementation
- [Fence](https://codeforces.com/problemset/problem/363/B) — **1100** · solved 69k · also: brute force
- [Ilya and Queries](https://codeforces.com/problemset/problem/313/B) — **1100** · solved 56k · also: implementation
- [BerSU Ball](https://codeforces.com/problemset/problem/489/B) — **1200** · solved 58k · also: dfs and similar, graph matchings, greedy, sortings, two pointers
- [Kuriyama Mirai's Stones](https://codeforces.com/problemset/problem/433/B) — **1200** · solved 55k · also: implementation, sortings
- [Cut Ribbon](https://codeforces.com/problemset/problem/189/A) — **1300** · solved 82k · also: brute force

### 1400-1600

- [Given Length and Sum of Digits...](https://codeforces.com/problemset/problem/489/C) — **1400** · solved 52k · also: greedy, implementation
- [Vacations](https://codeforces.com/problemset/problem/698/A) — **1400** · solved 39k
- [I Hate 1111](https://codeforces.com/problemset/problem/1526/B) — **1400** · solved 37k · also: math, number theory
- [Boredom](https://codeforces.com/problemset/problem/455/A) — **1500** · solved 74k
- [Two Substrings](https://codeforces.com/problemset/problem/550/A) — **1500** · solved 44k · also: brute force, greedy, implementation, strings
- [Good Subarrays](https://codeforces.com/problemset/problem/1398/C) — **1600** · solved 38k · also: data structures, math

### 1700-1900

- [Number of Ways](https://codeforces.com/problemset/problem/466/C) — **1700** · solved 42k · also: binary search, brute force, data structures, two pointers
- [Flowers](https://codeforces.com/problemset/problem/474/D) — **1700** · solved 31k
- [Consecutive Subsequence](https://codeforces.com/problemset/problem/977/F) — **1700** · solved 26k
- [Password](https://codeforces.com/problemset/problem/126/B) — **1700** · solved 26k · also: binary search, hashing, string suffix structures, strings
- [Caesar's Legions](https://codeforces.com/problemset/problem/118/D) — **1700** · solved 21k
- [Distance in Tree](https://codeforces.com/problemset/problem/161/D) — **1800** · solved 20k · also: dfs and similar, trees

### 2000-2200

- [The least round way](https://codeforces.com/problemset/problem/2/B) — **2000** · solved 13k · also: math
- [Prefixes and Suffixes](https://codeforces.com/problemset/problem/432/D) — **2000** · solved 12k · also: string suffix structures, strings, two pointers
- [Clear the String](https://codeforces.com/problemset/problem/1132/F) — **2000** · solved 11k
- [Odd-Even Subsequence](https://codeforces.com/problemset/problem/1370/D) — **2000** · solved 11k · also: binary search, dsu, greedy, implementation
- [Tree Painting](https://codeforces.com/problemset/problem/1187/E) — **2100** · solved 12k · also: dfs and similar, trees

### 2300+ (hardest)

- [Sonya and Problem Wihtout a Legend](https://codeforces.com/problemset/problem/713/C) — **2300** · solved 7k · also: sortings
- [Jzzhu and Numbers](https://codeforces.com/problemset/problem/449/D) — **2400** · solved 6k · also: bitmasks, combinatorics
- [The Fair Nut and Rectangles](https://codeforces.com/problemset/problem/1083/E) — **2400** · solved 6k · also: data structures, geometry
- [Ciel and Gondolas](https://codeforces.com/problemset/problem/321/E) — **2600** · solved 6k · also: data structures, divide and conquer

---

## 12. Data Structures

_Codeforces tags: `data structures`_

### 800-1000

- [ICPC Balloons](https://codeforces.com/problemset/problem/1703/B) — **800** · solved 68k · also: implementation
- [Advantage](https://codeforces.com/problemset/problem/1760/C) — **800** · solved 44k · also: implementation, sortings
- [Word Game](https://codeforces.com/problemset/problem/1722/C) — **800** · solved 37k · also: implementation
- [Remove Prefix](https://codeforces.com/problemset/problem/1714/B) — **800** · solved 34k · also: greedy, implementation
- [Odd Queries](https://codeforces.com/problemset/problem/1807/D) — **900** · solved 59k · also: implementation
- [Three Indices](https://codeforces.com/problemset/problem/1380/A) — **900** · solved 42k · also: brute force

### 1100-1300

- [Sereja and Suffixes](https://codeforces.com/problemset/problem/368/B) — **1100** · solved 51k · also: dp
- [A and B and Compilation Errors](https://codeforces.com/problemset/problem/519/B) — **1100** · solved 44k · also: implementation, sortings
- [Eating Candies](https://codeforces.com/problemset/problem/1669/F) — **1100** · solved 41k · also: binary search, greedy, two pointers
- [Same Differences](https://codeforces.com/problemset/problem/1520/D) — **1200** · solved 67k · also: hashing, math
- [Registration System](https://codeforces.com/problemset/problem/4/C) — **1300** · solved 111k · also: hashing, implementation
- [Queries about less or equal elements](https://codeforces.com/problemset/problem/600/B) — **1300** · solved 40k · also: binary search, sortings, two pointers

### 1400-1600

- [Pair of Topics](https://codeforces.com/problemset/problem/1324/D) — **1400** · solved 35k · also: binary search, sortings, two pointers
- [Grouping Increases](https://codeforces.com/problemset/problem/1919/C) — **1400** · solved 30k · also: dp, greedy
- [Karen and Coffee](https://codeforces.com/problemset/problem/816/B) — **1400** · solved 29k · also: binary search, implementation
- [Little Girl and Maximum Sum](https://codeforces.com/problemset/problem/276/C) — **1500** · solved 41k · also: greedy, implementation, sortings
- [Good Subarrays](https://codeforces.com/problemset/problem/1398/C) — **1600** · solved 38k · also: dp, math
- [Potions (Hard Version)](https://codeforces.com/problemset/problem/1526/C2) — **1600** · solved 31k · also: greedy

### 1700-1900

- [Number of Ways](https://codeforces.com/problemset/problem/466/C) — **1700** · solved 42k · also: binary search, brute force, dp, two pointers
- [Xenia and Bit Operations](https://codeforces.com/problemset/problem/339/D) — **1700** · solved 33k · also: trees
- [Color the Fence](https://codeforces.com/problemset/problem/349/B) — **1700** · solved 15k · also: dp, greedy, implementation
- [Longest Regular Bracket Sequence](https://codeforces.com/problemset/problem/5/C) — **1900** · solved 19k · also: constructive algorithms, dp, greedy, sortings, strings
- [Enemy is weak](https://codeforces.com/problemset/problem/61/E) — **1900** · solved 16k · also: trees
- [Modulo Sum](https://codeforces.com/problemset/problem/577/B) — **1900** · solved 15k · also: combinatorics, dp, two pointers

### 2000-2200

- [Sereja and Brackets](https://codeforces.com/problemset/problem/380/C) — **2000** · solved 20k · also: schedules
- [XOR on Segment](https://codeforces.com/problemset/problem/242/E) — **2000** · solved 12k · also: bitmasks
- [Ant colony](https://codeforces.com/problemset/problem/474/F) — **2100** · solved 12k · also: math, number theory
- [Powerful array](https://codeforces.com/problemset/problem/86/D) — **2200** · solved 17k · also: implementation, math, two pointers
- [Circular RMQ](https://codeforces.com/problemset/problem/52/C) — **2200** · solved 12k

### 2300+ (hardest)

- [Lomsat gelral](https://codeforces.com/problemset/problem/600/E) — **2300** · solved 19k · also: dfs and similar, dsu, trees
- [The Child and Sequence](https://codeforces.com/problemset/problem/438/D) — **2300** · solved 12k · also: math
- [Xenia and Tree](https://codeforces.com/problemset/problem/342/E) — **2400** · solved 11k · also: divide and conquer, trees
- [Tree and Queries](https://codeforces.com/problemset/problem/375/D) — **2400** · solved 9k · also: dfs and similar, trees

---

## 13. Graphs

_Codeforces tags: `graphs`_

### 800-1000

- [Love Triangle](https://codeforces.com/problemset/problem/939/A) — **800** · solved 30k
- [Destroying Bridges](https://codeforces.com/problemset/problem/1944/A) — **800** · solved 29k · also: greedy, math
- [Gregor and the Pawn Game](https://codeforces.com/problemset/problem/1549/B) — **800** · solved 27k · also: dfs and similar, dp, flows, graph matchings, greedy, implementation
- [Party](https://codeforces.com/problemset/problem/115/A) — **900** · solved 44k · also: dfs and similar, trees
- [New Year Transportation](https://codeforces.com/problemset/problem/500/A) — **1000** · solved 64k · also: dfs and similar, implementation
- [Badge](https://codeforces.com/problemset/problem/1020/B) — **1000** · solved 26k · also: brute force, dfs and similar

### 1100-1300

- [Long Jumps](https://codeforces.com/problemset/problem/1472/C) — **1100** · solved 36k · also: dp
- [The Lakes](https://codeforces.com/problemset/problem/1829/E) — **1100** · solved 27k · also: dfs and similar, dsu, implementation
- [Rumor](https://codeforces.com/problemset/problem/893/C) — **1300** · solved 29k · also: dfs and similar, greedy
- [Polygon](https://codeforces.com/problemset/problem/1360/E) — **1300** · solved 28k · also: dp, implementation, shortest paths
- [Mahmoud and Ehab and the bipartiteness](https://codeforces.com/problemset/problem/862/B) — **1300** · solved 27k · also: dfs and similar, trees
- [White-Black Balanced Subtrees](https://codeforces.com/problemset/problem/1676/G) — **1300** · solved 25k · also: dfs and similar, dp, trees

### 1400-1600

- [Two Buttons](https://codeforces.com/problemset/problem/520/B) — **1400** · solved 64k · also: dfs and similar, greedy, implementation, math, shortest paths
- [Ball in Berland](https://codeforces.com/problemset/problem/1475/C) — **1400** · solved 28k · also: combinatorics, math
- [News Distribution](https://codeforces.com/problemset/problem/1167/C) — **1400** · solved 27k · also: dfs and similar, dsu
- [Kefa and Park](https://codeforces.com/problemset/problem/580/C) — **1500** · solved 57k · also: dfs and similar, trees
- [Mortal Kombat Tower](https://codeforces.com/problemset/problem/1418/C) — **1500** · solved 29k · also: dp, greedy, shortest paths
- [Cyclic Components](https://codeforces.com/problemset/problem/977/E) — **1500** · solved 26k · also: dfs and similar, dsu

### 1700-1900

- [Solve The Maze](https://codeforces.com/problemset/problem/1365/D) — **1700** · solved 21k · also: constructive algorithms, dfs and similar, dsu, greedy, implementation, shortest paths
- [Valid BFS?](https://codeforces.com/problemset/problem/1037/D) — **1700** · solved 20k · also: dfs and similar, shortest paths, trees
- [Greg and Graph](https://codeforces.com/problemset/problem/295/B) — **1700** · solved 18k · also: dp, shortest paths
- [Checkposts](https://codeforces.com/problemset/problem/427/C) — **1700** · solved 17k · also: dfs and similar, two pointers
- [Dijkstra?](https://codeforces.com/problemset/problem/20/C) — **1900** · solved 49k · also: shortest paths
- [Roads not only in Berland](https://codeforces.com/problemset/problem/25/D) — **1900** · solved 16k · also: dsu, trees

### 2000-2200

- [Jzzhu and Cities](https://codeforces.com/problemset/problem/449/B) — **2000** · solved 11k · also: greedy, shortest paths
- [Directing Edges](https://codeforces.com/problemset/problem/1385/E) — **2000** · solved 9k · also: constructive algorithms, dfs and similar
- [Bertown roads](https://codeforces.com/problemset/problem/118/E) — **2000** · solved 9k · also: dfs and similar
- [Minimum spanning tree for each edge](https://codeforces.com/problemset/problem/609/E) — **2100** · solved 11k · also: data structures, dfs and similar, dsu, trees
- [Tree Requests](https://codeforces.com/problemset/problem/570/D) — **2200** · solved 9k · also: binary search, bitmasks, constructive algorithms, dfs and similar, trees

### 2300+ (hardest)

- [Legacy](https://codeforces.com/problemset/problem/786/B) — **2300** · solved 8k · also: data structures, shortest paths
- [Centroids](https://codeforces.com/problemset/problem/708/C) — **2300** · solved 4k · also: data structures, dfs and similar, dp, greedy, trees
- [Minimal Labels](https://codeforces.com/problemset/problem/825/E) — **2300** · solved 4k · also: data structures, dfs and similar, greedy
- [Minimum Path](https://codeforces.com/problemset/problem/1473/E) — **2400** · solved 5k · also: shortest paths

---

## 14. DFS / BFS & Similar

_Codeforces tags: `dfs and similar`_

### 800-1000

- [Gregor and the Pawn Game](https://codeforces.com/problemset/problem/1549/B) — **800** · solved 27k · also: dp, flows, graph matchings, graphs, greedy, implementation
- [Computer Game](https://codeforces.com/problemset/problem/1598/A) — **800** · solved 26k · also: brute force, dp, implementation
- [Party](https://codeforces.com/problemset/problem/115/A) — **900** · solved 44k · also: graphs, trees
- [Make it Divisible by 25](https://codeforces.com/problemset/problem/1593/B) — **900** · solved 42k · also: dp, greedy, math
- [New Year Transportation](https://codeforces.com/problemset/problem/500/A) — **1000** · solved 64k · also: graphs, implementation
- [Gold Rush](https://codeforces.com/problemset/problem/1829/D) — **1000** · solved 35k · also: brute force, dp, implementation

### 1100-1300

- [Frog Jumps](https://codeforces.com/problemset/problem/1324/C) — **1100** · solved 31k · also: binary search, data structures, greedy, implementation
- [The Lakes](https://codeforces.com/problemset/problem/1829/E) — **1100** · solved 27k · also: dsu, graphs, implementation
- [BerSU Ball](https://codeforces.com/problemset/problem/489/B) — **1200** · solved 58k · also: dp, graph matchings, greedy, sortings, two pointers
- [DZY Loves Chessboard](https://codeforces.com/problemset/problem/445/A) — **1200** · solved 34k · also: implementation
- [Districts Connection](https://codeforces.com/problemset/problem/1433/D) — **1200** · solved 32k · also: constructive algorithms
- [Rumor](https://codeforces.com/problemset/problem/893/C) — **1300** · solved 29k · also: graphs, greedy

### 1400-1600

- [Two Buttons](https://codeforces.com/problemset/problem/520/B) — **1400** · solved 64k · also: graphs, greedy, implementation, math, shortest paths
- [News Distribution](https://codeforces.com/problemset/problem/1167/C) — **1400** · solved 27k · also: dsu, graphs
- [Kefa and Park](https://codeforces.com/problemset/problem/580/C) — **1500** · solved 57k · also: graphs, trees
- [Cyclic Components](https://codeforces.com/problemset/problem/977/E) — **1500** · solved 26k · also: dsu, graphs
- [Maze](https://codeforces.com/problemset/problem/377/A) — **1600** · solved 28k
- [Linova and Kingdom](https://codeforces.com/problemset/problem/1336/A) — **1600** · solved 25k · also: dp, greedy, sortings, trees

### 1700-1900

- [Solve The Maze](https://codeforces.com/problemset/problem/1365/D) — **1700** · solved 21k · also: constructive algorithms, dsu, graphs, greedy, implementation, shortest paths
- [Valid BFS?](https://codeforces.com/problemset/problem/1037/D) — **1700** · solved 20k · also: graphs, shortest paths, trees
- [Checkposts](https://codeforces.com/problemset/problem/427/C) — **1700** · solved 17k · also: graphs, two pointers
- [Graph Without Long Directed Paths](https://codeforces.com/problemset/problem/1144/F) — **1700** · solved 15k · also: graphs
- [Cover it!](https://codeforces.com/problemset/problem/1176/E) — **1700** · solved 14k · also: dsu, graphs, shortest paths, trees
- [Distance in Tree](https://codeforces.com/problemset/problem/161/D) — **1800** · solved 20k · also: dp, trees

### 2000-2200

- [Directing Edges](https://codeforces.com/problemset/problem/1385/E) — **2000** · solved 9k · also: constructive algorithms, graphs
- [Tree Painting](https://codeforces.com/problemset/problem/1187/E) — **2100** · solved 12k · also: dp, trees
- [Minimum spanning tree for each edge](https://codeforces.com/problemset/problem/609/E) — **2100** · solved 11k · also: data structures, dsu, graphs, trees
- [Ciel the Commander](https://codeforces.com/problemset/problem/321/C) — **2100** · solved 10k · also: constructive algorithms, divide and conquer, greedy, trees
- [Compatible Numbers](https://codeforces.com/problemset/problem/165/E) — **2200** · solved 10k · also: bitmasks, brute force, dp

### 2300+ (hardest)

- [Lomsat gelral](https://codeforces.com/problemset/problem/600/E) — **2300** · solved 19k · also: data structures, dsu, trees
- [Centroids](https://codeforces.com/problemset/problem/708/C) — **2300** · solved 4k · also: data structures, dp, graphs, greedy, trees
- [Tree and Queries](https://codeforces.com/problemset/problem/375/D) — **2400** · solved 9k · also: data structures, trees
- [Blood Cousins Return](https://codeforces.com/problemset/problem/246/E) — **2400** · solved 5k · also: binary search, data structures, dp, sortings

---

## 15. Trees

_Codeforces tags: `trees`_

### 800-1000

- [Sum in Binary Tree](https://codeforces.com/problemset/problem/1843/C) — **800** · solved 40k · also: bitmasks, combinatorics, math
- [Party](https://codeforces.com/problemset/problem/115/A) — **900** · solved 44k · also: dfs and similar, graphs
- [Bmail Computer Network](https://codeforces.com/problemset/problem/1057/A) — **900** · solved 8k · also: *special, dfs and similar

### 1100-1300

- [Yet Another Card Deck](https://codeforces.com/problemset/problem/1511/C) — **1100** · solved 34k · also: brute force, data structures, implementation
- [Apple Tree](https://codeforces.com/problemset/problem/1843/D) — **1200** · solved 22k · also: combinatorics, dfs and similar, dp, math
- [Christmas Spruce](https://codeforces.com/problemset/problem/913/B) — **1200** · solved 20k · also: implementation
- [Strong Vertices](https://codeforces.com/problemset/problem/1857/D) — **1300** · solved 30k · also: math, sortings
- [Mahmoud and Ehab and the bipartiteness](https://codeforces.com/problemset/problem/862/B) — **1300** · solved 27k · also: dfs and similar, graphs
- [White-Black Balanced Subtrees](https://codeforces.com/problemset/problem/1676/G) — **1300** · solved 25k · also: dfs and similar, dp, graphs

### 1400-1600

- [Copil Copac Draws Trees](https://codeforces.com/problemset/problem/1830/A) — **1400** · solved 21k · also: dfs and similar, dp, graphs
- [Kefa and Park](https://codeforces.com/problemset/problem/580/C) — **1500** · solved 57k · also: dfs and similar, graphs
- [Journey](https://codeforces.com/problemset/problem/839/C) — **1500** · solved 21k · also: dfs and similar, dp, graphs, probabilities
- [k-Tree](https://codeforces.com/problemset/problem/431/C) — **1600** · solved 30k · also: dp, implementation
- [Linova and Kingdom](https://codeforces.com/problemset/problem/1336/A) — **1600** · solved 25k · also: dfs and similar, dp, greedy, sortings
- [Game On Leaves](https://codeforces.com/problemset/problem/1363/C) — **1600** · solved 21k · also: games

### 1700-1900

- [Xenia and Bit Operations](https://codeforces.com/problemset/problem/339/D) — **1700** · solved 33k · also: data structures
- [Valid BFS?](https://codeforces.com/problemset/problem/1037/D) — **1700** · solved 20k · also: dfs and similar, graphs, shortest paths
- [Cover it!](https://codeforces.com/problemset/problem/1176/E) — **1700** · solved 14k · also: dfs and similar, dsu, graphs, shortest paths
- [Distance in Tree](https://codeforces.com/problemset/problem/161/D) — **1800** · solved 20k · also: dfs and similar, dp
- [Roads not only in Berland](https://codeforces.com/problemset/problem/25/D) — **1900** · solved 16k · also: dsu, graphs
- [Enemy is weak](https://codeforces.com/problemset/problem/61/E) — **1900** · solved 16k · also: data structures

### 2000-2200

- [Tree Painting](https://codeforces.com/problemset/problem/1187/E) — **2100** · solved 12k · also: dfs and similar, dp
- [Minimum spanning tree for each edge](https://codeforces.com/problemset/problem/609/E) — **2100** · solved 11k · also: data structures, dfs and similar, dsu, graphs
- [Ciel the Commander](https://codeforces.com/problemset/problem/321/C) — **2100** · solved 10k · also: constructive algorithms, dfs and similar, divide and conquer, greedy
- [Blood Cousins](https://codeforces.com/problemset/problem/208/E) — **2100** · solved 9k · also: binary search, data structures, dfs and similar
- [Tree Requests](https://codeforces.com/problemset/problem/570/D) — **2200** · solved 9k · also: binary search, bitmasks, constructive algorithms, dfs and similar, graphs

### 2300+ (hardest)

- [Lomsat gelral](https://codeforces.com/problemset/problem/600/E) — **2300** · solved 19k · also: data structures, dfs and similar, dsu
- [Dominant Indices](https://codeforces.com/problemset/problem/1009/F) — **2300** · solved 8k · also: data structures, dsu
- [Xenia and Tree](https://codeforces.com/problemset/problem/342/E) — **2400** · solved 11k · also: data structures, divide and conquer
- [Tree and Queries](https://codeforces.com/problemset/problem/375/D) — **2400** · solved 9k · also: data structures, dfs and similar

---

## 16. DSU (Union-Find)

_Codeforces tags: `dsu`_

### 800-1000

- [Equal or Not Equal](https://codeforces.com/problemset/problem/1620/A) — **800** · solved 23k · also: constructive algorithms, implementation
- [Books Exchange (easy version)](https://codeforces.com/problemset/problem/1249/B1) — **1000** · solved 22k · also: math

### 1100-1300

- [The Lakes](https://codeforces.com/problemset/problem/1829/E) — **1100** · solved 27k · also: dfs and similar, graphs, implementation
- [Sakurako's Hobby](https://codeforces.com/problemset/problem/2008/D) — **1100** · solved 24k · also: dp, graphs, math
- [Misha and Changing Handles](https://codeforces.com/problemset/problem/501/B) — **1100** · solved 20k · also: data structures, strings
- [Ice Skating](https://codeforces.com/problemset/problem/217/A) — **1200** · solved 25k · also: brute force, dfs and similar, graphs
- [Find the Different Ones!](https://codeforces.com/problemset/problem/1927/D) — **1300** · solved 35k · also: binary search, brute force, data structures, dp, greedy, two pointers
- [Books Exchange (hard version)](https://codeforces.com/problemset/problem/1249/B2) — **1300** · solved 19k · also: dfs and similar, math

### 1400-1600

- [News Distribution](https://codeforces.com/problemset/problem/1167/C) — **1400** · solved 27k · also: dfs and similar, graphs
- [Learning Languages](https://codeforces.com/problemset/problem/277/A) — **1400** · solved 22k · also: dfs and similar
- [Cyclic Components](https://codeforces.com/problemset/problem/977/E) — **1500** · solved 26k · also: dfs and similar, graphs
- [K-Complete Word](https://codeforces.com/problemset/problem/1332/C) — **1500** · solved 21k · also: dfs and similar, greedy, implementation, strings
- [Round Dance](https://codeforces.com/problemset/problem/1833/E) — **1600** · solved 19k · also: dfs and similar, graphs, shortest paths
- [Split Into Two Sets](https://codeforces.com/problemset/problem/1702/E) — **1600** · solved 18k · also: dfs and similar, graphs

### 1700-1900

- [Solve The Maze](https://codeforces.com/problemset/problem/1365/D) — **1700** · solved 21k · also: constructive algorithms, dfs and similar, graphs, greedy, implementation, shortest paths
- [Cover it!](https://codeforces.com/problemset/problem/1176/E) — **1700** · solved 14k · also: dfs and similar, graphs, shortest paths, trees
- [XOUR](https://codeforces.com/problemset/problem/1971/G) — **1700** · solved 12k · also: data structures, sortings
- [String Transformation 1](https://codeforces.com/problemset/problem/1383/A) — **1700** · solved 11k · also: graphs, greedy, sortings, strings, trees, two pointers
- [Roads not only in Berland](https://codeforces.com/problemset/problem/25/D) — **1900** · solved 16k · also: graphs, trees
- [Mike and Feet](https://codeforces.com/problemset/problem/547/B) — **1900** · solved 11k · also: binary search, data structures, dp

### 2000-2200

- [Odd-Even Subsequence](https://codeforces.com/problemset/problem/1370/D) — **2000** · solved 11k · also: binary search, dp, greedy, implementation
- [SUM and REPLACE](https://codeforces.com/problemset/problem/920/F) — **2000** · solved 9k · also: brute force, data structures, number theory
- [GCD and MST](https://codeforces.com/problemset/problem/1513/D) — **2000** · solved 7k · also: constructive algorithms, graphs, greedy, number theory, sortings
- [Minimum spanning tree for each edge](https://codeforces.com/problemset/problem/609/E) — **2100** · solved 11k · also: data structures, dfs and similar, graphs, trees
- [Connected Components?](https://codeforces.com/problemset/problem/920/E) — **2100** · solved 8k · also: data structures, dfs and similar, graphs

### 2300+ (hardest)

- [Lomsat gelral](https://codeforces.com/problemset/problem/600/E) — **2300** · solved 19k · also: data structures, dfs and similar, trees
- [Dominant Indices](https://codeforces.com/problemset/problem/1009/F) — **2300** · solved 8k · also: data structures, trees
- [Qpwoeirut and Vertices](https://codeforces.com/problemset/problem/1706/E) — **2300** · solved 4k · also: binary search, data structures, dfs and similar, divide and conquer, greedy, trees
- [XOR Tree](https://codeforces.com/problemset/problem/1709/E) — **2400** · solved 4k · also: bitmasks, data structures, dfs and similar, greedy, trees

---

## 17. Shortest Paths

_Codeforces tags: `shortest paths`_

### 800-1000

- [Queue at the School](https://codeforces.com/problemset/problem/266/B) — **800** · solved 169k · also: constructive algorithms, graph matchings, implementation
- [Shortest path of the king](https://codeforces.com/problemset/problem/3/A) — **1000** · solved 22k · also: greedy

### 1100-1300

- [2D Traveling](https://codeforces.com/problemset/problem/1869/B) — **1100** · solved 27k · also: geometry, math, sortings
- [Thermostat](https://codeforces.com/problemset/problem/1759/C) — **1100** · solved 20k · also: greedy, math
- [Polygon](https://codeforces.com/problemset/problem/1360/E) — **1300** · solved 28k · also: dp, graphs, implementation
- [Arrow Path](https://codeforces.com/problemset/problem/1948/C) — **1300** · solved 21k · also: brute force, constructive algorithms, dfs and similar, dp, graphs
- [Getting Zero](https://codeforces.com/problemset/problem/1661/B) — **1300** · solved 21k · also: bitmasks, brute force, dfs and similar, dp, graphs, greedy
- [Product Queries](https://codeforces.com/problemset/problem/2193/E) — **1300** · solved 16k · also: dp, math, number theory

### 1400-1600

- [Two Buttons](https://codeforces.com/problemset/problem/520/B) — **1400** · solved 64k · also: dfs and similar, graphs, greedy, implementation, math
- [Mortal Kombat Tower](https://codeforces.com/problemset/problem/1418/C) — **1500** · solved 29k · also: dp, graphs, greedy
- [Eastern Exhibition](https://codeforces.com/problemset/problem/1486/B) — **1500** · solved 21k · also: binary search, geometry, sortings
- [Lunar New Year and a Wander](https://codeforces.com/problemset/problem/1106/D) — **1500** · solved 20k · also: data structures, dfs and similar, graphs, greedy
- [Did We Get Everything Covered?](https://codeforces.com/problemset/problem/1924/A) — **1500** · solved 17k · also: constructive algorithms, dp, greedy, strings
- [Round Dance](https://codeforces.com/problemset/problem/1833/E) — **1600** · solved 19k · also: dfs and similar, dsu, graphs

### 1700-1900

- [Solve The Maze](https://codeforces.com/problemset/problem/1365/D) — **1700** · solved 21k · also: constructive algorithms, dfs and similar, dsu, graphs, greedy, implementation
- [Valid BFS?](https://codeforces.com/problemset/problem/1037/D) — **1700** · solved 20k · also: dfs and similar, graphs, trees
- [Greg and Graph](https://codeforces.com/problemset/problem/295/B) — **1700** · solved 18k · also: dp, graphs
- [Zero Path](https://codeforces.com/problemset/problem/1695/C) — **1700** · solved 14k · also: brute force, data structures, dp, graphs, greedy
- [Cover it!](https://codeforces.com/problemset/problem/1176/E) — **1700** · solved 14k · also: dfs and similar, dsu, graphs, trees
- [Dijkstra?](https://codeforces.com/problemset/problem/20/C) — **1900** · solved 49k · also: graphs

### 2000-2200

- [Jzzhu and Cities](https://codeforces.com/problemset/problem/449/B) — **2000** · solved 11k · also: graphs, greedy
- [Buy a Ticket](https://codeforces.com/problemset/problem/938/D) — **2000** · solved 7k · also: data structures, graphs
- [1-Trees and Queries](https://codeforces.com/problemset/problem/1304/E) — **2000** · solved 6k · also: data structures, dfs and similar, trees
- [Police Stations](https://codeforces.com/problemset/problem/796/D) — **2100** · solved 7k · also: constructive algorithms, dfs and similar, dp, graphs, trees
- [Weights Distributing](https://codeforces.com/problemset/problem/1343/E) — **2100** · solved 6k · also: brute force, graphs, greedy, sortings

### 2300+ (hardest)

- [Legacy](https://codeforces.com/problemset/problem/786/B) — **2300** · solved 8k · also: data structures, graphs
- [Complete The Graph](https://codeforces.com/problemset/problem/715/B) — **2300** · solved 3k · also: binary search, constructive algorithms, graphs
- [Minimum Path](https://codeforces.com/problemset/problem/1473/E) — **2400** · solved 5k · also: graphs
- [The Shortest Statement](https://codeforces.com/problemset/problem/1051/F) — **2400** · solved 4k · also: graphs, trees

---

## 18. Constructive Algorithms

_Codeforces tags: `constructive algorithms`_

### 800-1000

- [Queue at the School](https://codeforces.com/problemset/problem/266/B) — **800** · solved 169k · also: graph matchings, implementation, shortest paths
- [Anton and Letters](https://codeforces.com/problemset/problem/443/A) — **800** · solved 114k · also: implementation
- [Insomnia cure](https://codeforces.com/problemset/problem/148/A) — **800** · solved 114k · also: implementation, math
- [Buy a Shovel](https://codeforces.com/problemset/problem/732/A) — **800** · solved 90k · also: brute force, implementation, math
- [Cover in Water](https://codeforces.com/problemset/problem/1900/A) — **800** · solved 68k · also: greedy, implementation, strings
- [Balanced Array](https://codeforces.com/problemset/problem/1343/B) — **800** · solved 66k · also: math

### 1100-1300

- [Fedor and New Game](https://codeforces.com/problemset/problem/467/B) — **1100** · solved 43k · also: bitmasks, brute force, implementation
- [Similar Pairs](https://codeforces.com/problemset/problem/1360/C) — **1100** · solved 37k · also: graph matchings, greedy, sortings
- [Same Parity Summands](https://codeforces.com/problemset/problem/1352/B) — **1200** · solved 45k · also: math
- [Palindrome Game (easy version)](https://codeforces.com/problemset/problem/1527/B1) — **1200** · solved 36k · also: games
- [Dora and Search](https://codeforces.com/problemset/problem/1793/C) — **1200** · solved 35k · also: data structures, two pointers
- [Random Teams](https://codeforces.com/problemset/problem/478/B) — **1300** · solved 42k · also: combinatorics, greedy, math

### 1400-1600

- [Phoenix and Beauty](https://codeforces.com/problemset/problem/1348/B) — **1400** · solved 28k · also: data structures, greedy, sortings
- [Epic Transformation](https://codeforces.com/problemset/problem/1506/D) — **1400** · solved 26k · also: data structures, greedy
- [Rotation Matching](https://codeforces.com/problemset/problem/1365/C) — **1400** · solved 25k · also: data structures, greedy, implementation
- [Jumping Through Segments](https://codeforces.com/problemset/problem/1907/D) — **1400** · solved 25k · also: binary search
- [Plus and Multiply](https://codeforces.com/problemset/problem/1542/B) — **1500** · solved 27k · also: math, number theory
- [Special Permutation](https://codeforces.com/problemset/problem/1352/G) — **1600** · solved 25k

### 1700-1900

- [Solve The Maze](https://codeforces.com/problemset/problem/1365/D) — **1700** · solved 21k · also: dfs and similar, dsu, graphs, greedy, implementation, shortest paths
- [Another Problem About Dividing Numbers](https://codeforces.com/problemset/problem/1538/D) — **1700** · solved 15k · also: math, number theory
- [Prefix Flip (Hard Version)](https://codeforces.com/problemset/problem/1381/A2) — **1700** · solved 14k · also: data structures, implementation, strings, two pointers
- [Baby Ehab Partitions Again](https://codeforces.com/problemset/problem/1516/C) — **1700** · solved 14k · also: bitmasks, dp, math
- [Carousel](https://codeforces.com/problemset/problem/1328/D) — **1800** · solved 15k · also: dp, graphs, greedy, math
- [Longest Regular Bracket Sequence](https://codeforces.com/problemset/problem/5/C) — **1900** · solved 19k · also: data structures, dp, greedy, sortings, strings

### 2000-2200

- [Two Divisors](https://codeforces.com/problemset/problem/1366/D) — **2000** · solved 13k · also: math, number theory
- [Engineer Artem](https://codeforces.com/problemset/problem/1438/C) — **2000** · solved 12k · also: 2-sat, chinese remainder theorem, fft, flows
- [Directing Edges](https://codeforces.com/problemset/problem/1385/E) — **2000** · solved 9k · also: dfs and similar, graphs
- [Ciel the Commander](https://codeforces.com/problemset/problem/321/C) — **2100** · solved 10k · also: dfs and similar, divide and conquer, greedy, trees
- [Tree Requests](https://codeforces.com/problemset/problem/570/D) — **2200** · solved 9k · also: binary search, bitmasks, dfs and similar, graphs, trees

### 2300+ (hardest)

- [Xor-MST](https://codeforces.com/problemset/problem/888/G) — **2300** · solved 6k · also: bitmasks, data structures
- [Errich-Tac-Toe (Hard Version)](https://codeforces.com/problemset/problem/1450/C2) — **2300** · solved 4k · also: math
- [Sliding Tree](https://codeforces.com/problemset/problem/2134/D) — **2300** · solved 4k · also: dfs and similar, greedy, implementation, trees
- [Buy Low Sell High](https://codeforces.com/problemset/problem/865/D) — **2400** · solved 8k · also: data structures, greedy

---

## Source

All problems, ratings, tags, and solved counts pulled live from the Codeforces API: `https://codeforces.com/api/problemset.problems`.
Problem links use the standard format `https://codeforces.com/problemset/problem/{contestId}/{index}`.
