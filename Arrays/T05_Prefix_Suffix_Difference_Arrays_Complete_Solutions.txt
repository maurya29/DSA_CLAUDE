# T05 — Prefix/Suffix & Difference Arrays: Complete Solutions

**Locked curriculum:** 32 LeetCode questions, 13 algorithm families (X1–X13).
**Language:** Java 21 (LeetCode-compatible).
**Curriculum source:** `MASTER_DSA_CURRICULUM.md` — C5, T05 LOCKED question registry.
**Scope:** Full reference workbook; producing this file does not mark unpracticed questions as completed.

> Note on the class-name requirement: LeetCode 303 mandates a `NumArray` constructor and `sumRange` method; its ready-to-submit code therefore uses `class NumArray`. All other solutions use `class Solution` as requested.

## Table of Contents

- [X1 — Running prefix states / split balance](#x1--running-prefix-states--split-balance) — [LC 1480](#leetcode-1480), [LC 1732](#leetcode-1732), [LC 1413](#leetcode-1413), [LC 724](#leetcode-724), [LC 2574](#leetcode-2574)
- [X2 — Immutable range queries plus boundary preprocessing](#x2--immutable-range-queries-plus-boundary-preprocessing) — [LC 303](#leetcode-303), [LC 2559](#leetcode-2559), [LC 2055](#leetcode-2055)
- [X3 — Prefix/suffix exclusion product](#x3--prefixsuffix-exclusion-product) — [LC 238](#leetcode-238)
- [X4 — Prefix/suffix weighted distance contributions](#x4--prefixsuffix-weighted-distance-contributions) — [LC 1769](#leetcode-1769), [LC 1685](#leetcode-1685), [LC 2602](#leetcode-2602)
- [X5 — Prefix-state frequency for subarray counting](#x5--prefix-state-frequency-for-subarray-counting) — [LC 1524](#leetcode-1524), [LC 560](#leetcode-560), [LC 974](#leetcode-974), [LC 2845](#leetcode-2845)
- [X6 — Earliest/latest prefix-state index for lengths](#x6--earliestlatest-prefix-state-index-for-lengths) — [LC 525](#leetcode-525), [LC 523](#leetcode-523), [LC 1590](#leetcode-1590), [LC 1124](#leetcode-1124)
- [X7 — Offline range additions with difference arrays](#x7--offline-range-additions-with-difference-arrays) — [LC 1109](#leetcode-1109), [LC 1094](#leetcode-1094), [LC 3355](#leetcode-3355), [LC 2381](#leetcode-2381)
- [X8 — Forced local range updates via difference sweep](#x8--forced-local-range-updates-via-difference-sweep) — [LC 2772](#leetcode-2772)
- [X9 — Contribution counting over all contiguous spans](#x9--contribution-counting-over-all-contiguous-spans) — [LC 1588](#leetcode-1588)
- [X10 — Prefix/suffix cut evaluation](#x10--prefixsuffix-cut-evaluation) — [LC 2256](#leetcode-2256), [LC 2483](#leetcode-2483), [LC 2270](#leetcode-2270)
- [X11 — Feasible multiple-prefix partition bounds](#x11--feasible-multiple-prefix-partition-bounds) — [LC 1712](#leetcode-1712)
- [X12 — Endpoint-conditioned optimal prefix state](#x12--endpoint-conditioned-optimal-prefix-state) — [LC 3026](#leetcode-3026)
- [X13 — Two-pass monotone streak lengths](#x13--two-pass-monotone-streak-lengths) — [LC 2100](#leetcode-2100)

---

## X1: Running prefix states / split balance

### When to Apply

The answer depends on values before the current index, a running maximum/minimum of cumulative sums, or the balance between left and right sides. Maintain the sum of processed elements; where needed, use total − left − current for the right side.

### Basic Java Code Snippet

```java
long total = 0;
for (int x : a) total += x;
long left = 0;
for (int i = 0; i < a.length; i++) {
    long right = total - left - a[i];
    // Inspect the prefix or compare left and right.
    left += a[i];
}
```

### Core Logic

The `left` invariant is the sum strictly before index `i`. The complement formula excludes the current element; a pure running prefix needs only `left += a[i]`.

### Questions Covered

LeetCode 1480, LeetCode 1732, LeetCode 1413, LeetCode 724, LeetCode 2574.

---

### LeetCode 1480

**Question:**

Given an integer array `nums`, produce an array `runningSum` of the same length. At each position i, the output must equal the sum of all elements from index 0 through i, inclusive. Preserve the original order.

**Examples:**

- `nums=[1,2,3,4]` → `[1,3,6,10]` (successive cumulative totals).
- `nums=[1,1,1,1,1]` → `[1,2,3,4,5]`.
- `nums=[3,1,2,10,1]` → `[3,4,6,16,17]`.

**Constraints:**

`1 ≤ nums.length ≤ 1000`; `−10^6 ≤ nums[i] ≤ 10^6`.

**Why This Subpattern Applies:**

Each position asks for the sum of the prefix ending there; one running state covers all positions.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int[] runningSum(int[] nums) {
        int running = 0;
        for (int i = 0; i < nums.length; i++) {
            running += nums[i];
            nums[i] = running;
        }
        return nums;
    }
}
```

**Key Logic:**

After processing index i, `running` equals the original sum from 0 through i. In-place assignment is safe because later iterations only need unseen elements.

**Time Complexity:**

O(n): each element is visited once.

**Space Complexity:**

O(1) auxiliary; the input is also the output array.

---

### LeetCode 1732

**Question:**

A cyclist begins at altitude 0 and follows consecutive road segments. Array `gain` contains the altitude change for each segment; changes can be positive, zero, or negative. Return the highest altitude reached, including the starting point before any segment.

**Examples:**

- `gain=[-5,1,5,0,-7]` → `1` (altitudes 0,−5,−4,1,1,−6).
- `gain=[-4,-3,-2,-1,4,3,2]` → `0` (starting altitude is highest).
- `gain=[2,-1,3]` → `4`.

**Constraints:**

`1 ≤ gain.length ≤ 100`; `−100 ≤ gain[i] ≤ 100`.

**Why This Subpattern Applies:**

The altitude after each segment is its running prefix sum, so a running maximum gives the answer.

**Connection to Previous Solution:**

Retain `running += gain[i]` from 1480; instead of writing every prefix, initialize `best=0` and update `best=Math.max(best,running)`.

**Java Solution:**

```java
class Solution {
    public int largestAltitude(int[] gain) {
        int altitude = 0, highest = 0;
        for (int change : gain) {
            altitude += change;
            highest = Math.max(highest, altitude);
        }
        return highest;
    }
}
```

**Key Logic:**

`altitude` represents the current prefix; `highest` includes the empty prefix (starting altitude 0). Negative-only rides correctly return 0.

**Time Complexity:**

O(n): one scan.

**Space Complexity:**

O(1) auxiliary.

---

### LeetCode 1413

**Question:**

Given an array `nums`, choose the smallest **positive integer** `startValue` such that, after beginning at `startValue` and adding each array element in order, the running total is always at least 1. Return that minimum starting value.

**Examples:**

- `nums=[-3,2,-3,4,2]` → `5` (minimum prefix −4).
- `nums=[1,2]` → `1` (already positive throughout).
- `nums=[1,-2,-3]` → `5`.

**Constraints:**

`1 ≤ nums.length ≤ 100`; `−100 ≤ nums[i] ≤ 100`.

**Why This Subpattern Applies:**

The binding requirement occurs at the smallest prefix sum; offsetting this minimum to 1 satisfies every step.

**Connection to Previous Solution:**

Reuse 1732’s cumulative scan; replace `highest=max(highest,prefix)` with `minPrefix=min(minPrefix,prefix)` (seed 0), then return `1-minPrefix`.

**Java Solution:**

```java
class Solution {
    public int minStartValue(int[] nums) {
        int prefix = 0, minPrefix = 0;
        for (int x : nums) {
            prefix += x;
            minPrefix = Math.min(minPrefix, prefix);
        }
        return 1 - minPrefix;
    }
}
```

**Key Logic:**

All totals equal `startValue + prefix`. Requiring their minimum to be ≥1 yields `startValue≥1−minPrefix`. Initialize minimum with zero to enforce positivity even when all prefixes are nonnegative.

**Time Complexity:**

O(n): one scan.

**Space Complexity:**

O(1) auxiliary.

---

### LeetCode 724

**Question:**

Return the **leftmost index** of an integer array `nums` where the sum of all elements strictly to its left equals the sum of all elements strictly to its right. Neither side includes the index itself; an empty side contributes zero. Return −1 when no such pivot exists.

**Examples:**

- `nums=[1,7,3,6,5,6]` → `3` (left/right sums both 11).
- `nums=[1,2,3]` → `-1`.
- `nums=[2,1,-1]` → `0` (both sides sum to 0).

**Constraints:**

`1 ≤ nums.length ≤ 10^4`; `−1000 ≤ nums[i] ≤ 1000`.

**Why This Subpattern Applies:**

For each candidate, the right sum is total minus the current value minus the already accumulated left sum.

**Connection to Previous Solution:**

Replace 1413’s min-prefix extremum with a preliminary total pass and test `left == total-left-nums[i]`; advance `left` only after testing.

**Java Solution:**

```java
class Solution {
    public int pivotIndex(int[] nums) {
        long total = 0;
        for (int x : nums) total += x;
        long left = 0;
        for (int i = 0; i < nums.length; i++) {
            if (left == total - left - nums[i]) return i;
            left += nums[i];
        }
        return -1;
    }
}
```

**Key Logic:**

At candidate index i, `left` excludes `nums[i]`, while `total-left-nums[i]` excludes it from the right. Scan left to right to guarantee the leftmost pivot.

**Time Complexity:**

O(n): total scan and pivot scan.

**Space Complexity:**

O(1) auxiliary.

---

### LeetCode 2574

**Question:**

Given an integer array `nums`, return a new array `answer`. For each index i, compute `leftSum` as the sum of values strictly before i and `rightSum` as the sum strictly after i; set `answer[i]=|leftSum-rightSum|`. Empty prefixes or suffixes have sum 0.

**Examples:**

- `nums=[10,4,8,3]` → `[15,1,11,22]`.
- `nums=[1]` → `[0]`.
- `nums=[1,2,3]` → `[5,2,3]`.

**Constraints:**

`1 ≤ nums.length ≤ 1000`; `1 ≤ nums[i] ≤ 10^5`.

**Why This Subpattern Applies:**

The same complement formula as the pivot test gives both sums; the desired output is now their absolute difference.

**Connection to Previous Solution:**

Keep 724’s `total` and `left` invariants. Replace the first-match equality test/return with `answer[i]=abs(left-(total-left-nums[i]))` at every position.

**Java Solution:**

```java
class Solution {
    public int[] leftRightDifference(int[] nums) {
        int n = nums.length;
        int[] result = new int[n];
        long total = 0;
        for (int x : nums) total += x;
        long left = 0;
        for (int i = 0; i < n; i++) {
            long right = total - left - nums[i];
            result[i] = (int) Math.abs(left - right);
            left += nums[i];
        }
        return result;
    }
}
```

**Key Logic:**

`left` and `right` exclude the current value, and abs produces the requested nonnegative difference. The stated maximum total fits in int, while long intermediates are robust.

**Time Complexity:**

O(n): two linear passes.

**Space Complexity:**

O(n) output; O(1) additional auxiliary beyond output.

---

## X2: Immutable range queries plus boundary preprocessing

### When to Apply

Numerous inclusive range queries target immutable data. Precompute cumulative contributions and subtract two exclusive-prefix values; if endpoints must meet conditions, precompute nearest valid boundaries.

### Basic Java Code Snippet

```java
long[] pref = new long[a.length + 1];
for (int i = 0; i < a.length; i++)
    pref[i + 1] = pref[i] + a[i];
long range = pref[r + 1] - pref[l];
// For bounded ranges, clamp l/r using nearest valid endpoints first.
```

### Core Logic

`pref[t]` covers elements with indices `< t`; hence `[l,r]` equals `pref[r+1]-pref[l]`. Boundary lookups exclude invalid endpoints.

### Questions Covered

LeetCode 303, LeetCode 2559, LeetCode 2055.

---

### LeetCode 303

**Question:**

Implement an immutable range-sum data structure initialized from an integer array. Construction takes `nums`; each call `sumRange(left,right)` must return the sum of **all** numbers in the inclusive interval `[left,right]`. There can be many queries, but the input never changes. The online judge creates `NumArray`, not `Solution`.

**Examples:**

- `NumArray([-2,0,3,-5,2,-1])`, `sumRange(0,2)` → `1`.
- On the same object, `sumRange(2,5)` → `-1`.
- On the same object, `sumRange(0,5)` → `-3`.

**Constraints:**

`1 ≤ nums.length ≤ 10^4`; `−10^5 ≤ nums[i] ≤ 10^5`; `0 ≤ left ≤ right < nums.length`; at most `10^4` queries.

**Why This Subpattern Applies:**

Static repeated inclusive range sums are exactly what a leading-zero prefix array answers in constant time.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
// LC 303 requires the judge-defined class name NumArray, not Solution.
class NumArray {
    private final long[] prefix;

    public NumArray(int[] nums) {
        prefix = new long[nums.length + 1];
        for (int i = 0; i < nums.length; i++) {
            prefix[i + 1] = prefix[i] + nums[i];
        }
    }

    public int sumRange(int left, int right) {
        return (int) (prefix[right + 1] - prefix[left]);
    }
}
```

**Key Logic:**

`prefix[0]=0` and `prefix[i+1]` includes indices 0..i. Subtracting at the exclusive boundaries isolates `[left,right]`. `long` prevents intermediate overflow; the prescribed judge API returns int.

**Time Complexity:**

O(n) construction, O(1) per query.

**Space Complexity:**

O(n) stored prefix array; O(1) per-query auxiliary.

---

### LeetCode 2559

**Question:**

You receive an array `words` of lowercase English strings and multiple inclusive index queries `[l,r]`. A word qualifies when **both its first and last characters are vowels** (a, e, i, o, u). For each query, count qualifying words between indices l and r, inclusive; return counts in query order.

**Examples:**

- `words=["aba","bcb","ece","aa","e"]`, `queries=[[0,2],[1,4],[1,1]]` → `[2,3,0]`.
- `words=["a","e","i"]`, `queries=[[0,2],[1,1]]` → `[3,1]`.
- `words=["cat","dog"]`, `queries=[[0,1]]` → `[0]`.

**Constraints:**

`1 ≤ words.length ≤ 10^5`; `1 ≤ words[i].length ≤ 40`; words contain lowercase English letters; `1 ≤ queries.length ≤ 10^5`; `0 ≤ l ≤ r < words.length`.

**Why This Subpattern Applies:**

Each word contributes an independent indicator 0 or 1, so range counts are prefix sums of those indicators.

**Connection to Previous Solution:**

Keep 303’s `prefix[r+1]-prefix[l]`. Replace integer `nums[i]` contribution with `isVowel(first)&&isVowel(last) ? 1 : 0`; answer all query intervals.

**Java Solution:**

```java
class Solution {
    public int[] vowelStrings(String[] words, int[][] queries) {
        int n = words.length;
        int[] prefix = new int[n + 1];
        for (int i = 0; i < n; i++) {
            String w = words[i];
            boolean qualifying = vowel(w.charAt(0)) && vowel(w.charAt(w.length() - 1));
            prefix[i + 1] = prefix[i] + (qualifying ? 1 : 0);
        }
        int[] answer = new int[queries.length];
        for (int q = 0; q < queries.length; q++) {
            int l = queries[q][0], r = queries[q][1];
            answer[q] = prefix[r + 1] - prefix[l];
        }
        return answer;
    }

    private boolean vowel(char c) {
        return c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u';
    }
}
```

**Key Logic:**

Classify words once by endpoints, record qualifying counts in the exclusive prefix, and subtract for each inclusive query. A one-letter vowel qualifies.

**Time Complexity:**

O(n+q), ignoring constant-time endpoint checks; n words and q queries.

**Space Complexity:**

O(n) prefix plus O(q) returned answers.

---

### LeetCode 2055

**Question:**

A string `s` contains plates `*` and candles `|`. Each query `[left,right]` selects an inclusive substring. Count plates that lie **strictly between two candles both within that substring**; any plates outside the outermost suitable candles are not counted. Return one count per query.

**Examples:**

- `s="**|**|***|"`, `queries=[[2,5],[5,9]]` → `[2,3]`.
- `s="***|**"`, `queries=[[0,5],[1,2]]` → `[0,0]` (too few candles).
- `s="|*|*|"`, `queries=[[0,4],[1,3]]` → `[2,0]`.

**Constraints:**

`1 ≤ s.length ≤ 10^5`; `s[i]` is `*` or `|`; `1 ≤ queries.length ≤ 10^5`; `0 ≤ left ≤ right < s.length`.

**Why This Subpattern Applies:**

Prefix counts answer plates in a range, but only after valid enclosing candle boundaries are chosen; nearest-candle arrays locate these in O(1).

**Connection to Previous Solution:**

Preserve 2559’s prefix subtraction; add `nearestRight[left]` and `nearestLeft[right]` to clamp the interval to candle boundaries before subtracting plate counts.

**Java Solution:**

```java
class Solution {
    public int[] platesBetweenCandles(String s, int[][] queries) {
        int n = s.length();
        int[] plates = new int[n + 1];
        int[] nearestLeft = new int[n];
        int[] nearestRight = new int[n];
        int last = -1;
        for (int i = 0; i < n; i++) {
            plates[i + 1] = plates[i] + (s.charAt(i) == '*' ? 1 : 0);
            if (s.charAt(i) == '|') last = i;
            nearestLeft[i] = last;
        }
        last = -1;
        for (int i = n - 1; i >= 0; i--) {
            if (s.charAt(i) == '|') last = i;
            nearestRight[i] = last;
        }
        int[] answer = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int l = nearestRight[queries[i][0]];
            int r = nearestLeft[queries[i][1]];
            if (l != -1 && r != -1 && l < r) {
                answer[i] = plates[r] - plates[l];
            }
        }
        return answer;
    }
}
```

**Key Logic:**

The first candle not before `left` and last candle not after `right` are the valid enclosure. If they are missing or not ordered, the result remains zero. The prefix subtraction excludes the candle endpoints.

**Time Complexity:**

O(n+q): prefix/boundary preprocessing and constant-time queries.

**Space Complexity:**

O(n) preprocessing arrays plus O(q) output.

---

## X3: Prefix/suffix exclusion product

### When to Apply

Compute all products except the current value without division; zeros make division unsafe. The current position must be excluded in both directions.

### Basic Java Code Snippet

```java
long[] result = new long[a.length];
long leftProduct = 1;
for (int i = 0; i < a.length; i++) {
    result[i] = leftProduct;
    leftProduct *= a[i];
}
long rightProduct = 1;
for (int i = a.length - 1; i >= 0; i--) {
    result[i] *= rightProduct;
    rightProduct *= a[i];
}
```

### Core Logic

Store the left-exclusive product first; on the reverse pass multiply by the right-exclusive product before incorporating the current value.

### Questions Covered

LeetCode 238.

---

### LeetCode 238

**Question:**

Given an integer array `nums`, return an array where `answer[i]` equals the product of every element **except** `nums[i]`. You must not use division and must run in O(n). Zero values are allowed; all prefix/suffix products and answers fit in a 32-bit integer under the problem guarantee.

**Examples:**

- `nums=[1,2,3,4]` → `[24,12,8,6]`.
- `nums=[-1,1,0,-3,3]` → `[0,0,9,0,0]`.
- `nums=[0,0,2]` → `[0,0,0]`.

**Constraints:**

`2 ≤ nums.length ≤ 10^5`; `−30 ≤ nums[i] ≤ 30`; relevant intermediate products are guaranteed to fit in a signed 32-bit integer.

**Why This Subpattern Applies:**

Every answer factors into a product strictly left of i and one strictly right of i, and these are available through directional running products without division.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] answer = new int[n];
        int left = 1;
        for (int i = 0; i < n; i++) {
            answer[i] = left;
            left *= nums[i];
        }
        int right = 1;
        for (int i = n - 1; i >= 0; i--) {
            answer[i] *= right;
            right *= nums[i];
        }
        return answer;
    }
}
```

**Key Logic:**

Store left-exclusive products in the output. On the backward pass multiply by right-exclusive products; multiply the current number into `right` only afterward. Zeros naturally zero out exactly the affected products.

**Time Complexity:**

O(n): one forward and one reverse scan.

**Space Complexity:**

O(1) auxiliary excluding the O(n) answer array.

---

## X4: Prefix/suffix weighted distance contributions

### When to Apply

Total movement or absolute-distance cost to many target locations is needed. For ordered source values, split sources around a target and derive left/right costs from counts and sums.

### Basic Java Code Snippet

```java
long[] pref = new long[sorted.length + 1];
for (int i = 0; i < sorted.length; i++)
    pref[i + 1] = pref[i] + sorted[i];
int split = 0; // lowerBound(sorted, target)
long leftCost = (long) split * target - pref[split];
long rightCost = pref[sorted.length] - pref[split]
               - (long) (sorted.length - split) * target;
long cost = leftCost + rightCost;
```

### Core Logic

Each distance is target−value on the left and value−target on the right. Prefix sums replace linear per-target summation.

### Questions Covered

LeetCode 1769, LeetCode 1685, LeetCode 2602.

---

### LeetCode 1769

**Question:**

A binary string `boxes` indicates whether each box contains one ball (`1`) or none (`0`). In one operation you may move one ball to an adjacent box. For every target box i, find the minimum total operations needed to move **all balls** to i, and return the resulting array. Balls can share boxes.

**Examples:**

- `boxes="110"` → `[1,1,3]`.
- `boxes="001011"` → `[11,8,5,4,3,4]`.
- `boxes="0"` → `[0]`.

**Constraints:**

`1 ≤ boxes.length ≤ 2000`; each character is `0` or `1`.

**Why This Subpattern Applies:**

Moving a target one position changes distance to every ball on each side predictably. Two directional running counts/costs replace O(n²) pairwise distances.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int[] minOperations(String boxes) {
        int n = boxes.length();
        int[] answer = new int[n];
        int count = 0, cost = 0;
        for (int i = 0; i < n; i++) {
            answer[i] += cost;
            if (boxes.charAt(i) == '1') count++;
            cost += count;
        }
        count = 0;
        cost = 0;
        for (int i = n - 1; i >= 0; i--) {
            answer[i] += cost;
            if (boxes.charAt(i) == '1') count++;
            cost += count;
        }
        return answer;
    }
}
```

**Key Logic:**

On the forward scan, `cost` at i equals the distance from all balls strictly to the left; after processing i, increment the future cost by the number of left-side balls. The backward scan adds the symmetric right-side cost.

**Time Complexity:**

O(n): two passes.

**Space Complexity:**

O(n) output; O(1) auxiliary beyond output.

---

### LeetCode 1685

**Question:**

An integer array `nums` is already sorted in nondecreasing order. For each index i, calculate the sum of absolute differences between `nums[i]` and every array element: `answer[i]=Σ_j |nums[i]−nums[j]|`. Return all answers.

**Examples:**

- `nums=[2,3,5]` → `[4,3,5]`.
- `nums=[1,4,6,8,10]` → `[24,15,13,15,21]`.
- `nums=[1,1,1]` → `[0,0,0]`.

**Constraints:**

`2 ≤ nums.length ≤ 10^5`; `1 ≤ nums[i] ≤ 10^4`; nums is sorted in nondecreasing order.

**Why This Subpattern Applies:**

Sorting order makes the sign of every difference to index i known. Prefix sums give the total differences from each side in constant time.

**Connection to Previous Solution:**

Generalize 1769’s left/right movement contributions: rather than accumulating costs between adjacent unit-spaced target boxes, derive exact weighted costs `i*x−prefix[i]` and `(prefix[n]−prefix[i+1])−(n−i−1)*x`.

**Java Solution:**

```java
class Solution {
    public int[] getSumAbsoluteDifferences(int[] nums) {
        int n = nums.length;
        long[] prefix = new long[n + 1];
        for (int i = 0; i < n; i++) {
            prefix[i + 1] = prefix[i] + nums[i];
        }
        int[] answer = new int[n];
        for (int i = 0; i < n; i++) {
            long left = (long) i * nums[i] - prefix[i];
            long right = prefix[n] - prefix[i + 1]
                    - (long) (n - i - 1) * nums[i];
            answer[i] = (int) (left + right);
        }
        return answer;
    }
}
```

**Key Logic:**

For sorted inputs every left term equals `x−nums[j]` and every right term equals `nums[j]−x`. `long` protects intermediate multiplication; under stated bounds each answer fits int.

**Time Complexity:**

O(n): prefix construction and one answer pass.

**Space Complexity:**

O(n) prefix storage and O(n) output.

---

### LeetCode 2602

**Question:**

Given an integer array `nums` and an array `queries`, answer each query independently. For target value q, you may increase or decrease any array element by one per operation; find the minimum total operations required to make **every** array element equal to q. The original array is restored for the next query.

**Examples:**

- `nums=[3,1,6,8]`, `queries=[1,5]` → `[14,10]`.
- `nums=[2,9,6,3]`, `queries=[10]` → `[20]`.
- `nums=[1,1,1]`, `queries=[1,2]` → `[0,3]`.

**Constraints:**

`1 ≤ nums.length, queries.length ≤ 10^5`; `1 ≤ nums[i], queries[i] ≤ 10^9`.

**Why This Subpattern Applies:**

The minimum total moves for a fixed target is the sum of absolute differences. Sorting and binary search partition values below/above each query target.

**Connection to Previous Solution:**

Retain 1685’s weighted left/right cost formulas, but targets no longer have known indices. Sort once, use `lowerBound` to obtain the split for each arbitrary q, and return `long[]` to prevent overflow.

**Java Solution:**

```java
class Solution {
    public long[] minOperations(int[] nums, int[] queries) {
        java.util.Arrays.sort(nums);
        int n = nums.length;
        long[] prefix = new long[n + 1];
        for (int i = 0; i < n; i++) prefix[i + 1] = prefix[i] + nums[i];
        long[] answer = new long[queries.length];
        for (int qi = 0; qi < queries.length; qi++) {
            long target = queries[qi];
            int split = lowerBound(nums, queries[qi]);
            long left = (long) split * target - prefix[split];
            long right = prefix[n] - prefix[split] - (long) (n - split) * target;
            answer[qi] = left + right;
        }
        return answer;
    }

    private int lowerBound(int[] a, int target) {
        int lo = 0, hi = a.length;
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (a[mid] < target) lo = mid + 1;
            else hi = mid;
        }
        return lo;
    }
}
```

**Key Logic:**

`split` is the first index with value ≥ target; values before it contribute `target−value`, and those at/after contribute `value−target`. Each query takes O(log n) after one sort and prefix build.

**Time Complexity:**

O(n log n + q log n) for n numbers and q queries.

**Space Complexity:**

O(n) prefix plus O(q) output; sorting primitive int[] uses O(log n) stack in Java’s dual-pivot quicksort implementation.

---

## X5: Prefix-state frequency for subarray counting

### When to Apply

Count subarrays that satisfy a sum, parity, or modular condition, including inputs with negative numbers. Every subarray is the difference of two prefix states; count matching earlier states.

### Basic Java Code Snippet

```java
java.util.Map<Long, Long> freq = new java.util.HashMap<>();
freq.put(0L, 1L);
long prefix = 0, ways = 0;
for (int x : a) {
    prefix += x;
    ways += freq.getOrDefault(prefix - target, 0L);
    freq.merge(prefix, 1L, Long::sum);
}
```

### Core Logic

Seed the empty prefix before iteration. Query preceding states before inserting the current state so each counted subarray is nonempty; adapt the state/key to parity or modulo.

### Questions Covered

LeetCode 1524, LeetCode 560, LeetCode 974, LeetCode 2845.

---

### LeetCode 1524

**Question:**

Given an array of positive integers `arr`, count all nonempty contiguous subarrays whose sum is odd. Since the number can be large, return it modulo `1_000_000_007`.

**Examples:**

- `arr=[1,3,5]` → `4` (three singletons and the length-3 subarray).
- `arr=[2,4,6]` → `0`.
- `arr=[1,2,3,4,5,6,7]` → `16`.

**Constraints:**

`1 ≤ arr.length ≤ 10^5`; `1 ≤ arr[i] ≤ 100`.

**Why This Subpattern Applies:**

A subarray sum is odd precisely when its two boundary prefix sums have opposite parity. Count earlier prefixes of the opposite parity.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int numOfSubarrays(int[] arr) {
        final int MOD = 1_000_000_007;
        long[] frequency = {1, 0}; // empty prefix is even
        int parity = 0;
        long answer = 0;
        for (int x : arr) {
            parity ^= (x & 1);
            answer = (answer + frequency[parity ^ 1]) % MOD;
            frequency[parity]++;
        }
        return (int) answer;
    }
}
```

**Key Logic:**

`parity` records the current prefix parity; an opposite earlier parity makes the intervening sum odd. Seed even frequency with one for the empty prefix and query before insertion.

**Time Complexity:**

O(n): one scan.

**Space Complexity:**

O(1): two frequencies and counters.

---

### LeetCode 560

**Question:**

Given integer array `nums` and integer `k`, count the number of nonempty contiguous subarrays whose elements sum **exactly** to k. Elements may be negative or zero; different positions count as different subarrays.

**Examples:**

- `nums=[1,1,1]`, `k=2` → `2`.
- `nums=[1,2,3]`, `k=3` → `2` (`[1,2]` and `[3]`).
- `nums=[1,-1,0]`, `k=0` → `3`.

**Constraints:**

`1 ≤ nums.length ≤ 2×10^4`; `−1000 ≤ nums[i] ≤ 1000`; `−10^7 ≤ k ≤ 10^7`.

**Why This Subpattern Applies:**

A qualifying subarray corresponds to an earlier prefix equal to `currentPrefix−k`. Negative numbers invalidate a monotone sliding-window alternative but not prefix differences.

**Connection to Previous Solution:**

Replace 1524’s two parity counts with a HashMap of complete prefix sums; replace opposite-parity lookup with exact `prefix-k` lookup. The empty-prefix seed remains.

**Java Solution:**

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        java.util.Map<Long, Integer> freq = new java.util.HashMap<>();
        freq.put(0L, 1);
        long prefix = 0;
        int answer = 0;
        for (int x : nums) {
            prefix += x;
            answer += freq.getOrDefault(prefix - k, 0);
            freq.merge(prefix, 1, Integer::sum);
        }
        return answer;
    }
}
```

**Key Logic:**

Before inserting the current prefix, the map records exactly the starting boundaries available for nonempty subarrays. Every earlier occurrence of `prefix-k` creates one valid interval ending here.

**Time Complexity:**

O(n) expected using a hash map.

**Space Complexity:**

O(n) map in the worst case.

---

### LeetCode 974

**Question:**

Given an integer array `nums` and positive integer `k`, count the nonempty contiguous subarrays whose sum is divisible by k, including sums of 0 or negative sums.

**Examples:**

- `nums=[4,5,0,-2,-3,1]`, `k=5` → `7`.
- `nums=[5]`, `k=9` → `0`.
- `nums=[-1,1]`, `k=2` → `1` (whole array sums to 0).

**Constraints:**

`1 ≤ nums.length ≤ 3×10^4`; `−10^4 ≤ nums[i] ≤ 10^4`; `2 ≤ k ≤ 10^4`.

**Why This Subpattern Applies:**

Two prefix sums yield a divisible difference iff they have equal normalized remainders modulo k.

**Connection to Previous Solution:**

Keep 560’s frequency-of-earlier-prefix mechanism, but key by normalized remainder instead of full prefix; match the same remainder rather than `prefix-k`.

**Java Solution:**

```java
class Solution {
    public int subarraysDivByK(int[] nums, int k) {
        int[] freq = new int[k];
        freq[0] = 1;
        int rem = 0, answer = 0;
        for (int x : nums) {
            rem = Math.floorMod(rem + x, k);
            answer += freq[rem];
            freq[rem]++;
        }
        return answer;
    }
}
```

**Key Logic:**

`rem` stays in `[0,k−1]`, even after negative elements. Every previous matching residue yields a divisible interval, and the seed handles intervals beginning at zero.

**Time Complexity:**

O(n) time.

**Space Complexity:**

O(k) remainder-frequency array.

---

### LeetCode 2845

**Question:**

Given a nonnegative integer array `nums`, integer `modulo`, and integer `k` with `0 ≤ k < modulo`, call a subarray **interesting** if the count of elements x in it satisfying `x % modulo == k` is itself congruent to k modulo `modulo`. Count all nonempty interesting contiguous subarrays; return the count as `long`.

**Examples:**

- `nums=[3,2,4]`, `modulo=2`, `k=1` → `3`.
- `nums=[3,1,9,6]`, `modulo=3`, `k=0` → `2`.
- `nums=[1,1]`, `modulo=2`, `k=0` → `3` (no value qualifies, so every subarray has qualifying count zero).

**Constraints:**

`1 ≤ nums.length ≤ 10^5`; `1 ≤ nums[i] ≤ 10^9`; `1 ≤ modulo ≤ 10^9`; `0 ≤ k < modulo`.

**Why This Subpattern Applies:**

Replace each number with a 0/1 indicator for the predicate. Valid subarrays need the difference between prefix indicator counts to equal k modulo `modulo`.

**Connection to Previous Solution:**

From 974, change the per-element contribution to `nums[i]%modulo==k ? 1 : 0`, and the matching prior remainder from `current` to `(current-k+modulo)%modulo`. Use a map because modulo may be huge.

**Java Solution:**

```java
class Solution {
    public long countInterestingSubarrays(java.util.List<Integer> nums, int modulo, int k) {
        java.util.Map<Integer, Long> freq = new java.util.HashMap<>();
        freq.put(0, 1L);
        int rem = 0;
        long answer = 0;
        for (int value : nums) {
            if (value % modulo == k) rem = (rem + 1) % modulo;
            int need = (int) Math.floorMod((long) rem - k, (long) modulo);
            answer += freq.getOrDefault(need, 0L);
            freq.merge(rem, 1L, Long::sum);
        }
        return answer;
    }
}
```

**Key Logic:**

`rem` is the number of qualifying elements in the processed prefix modulo `modulo`. A previous remainder `need` makes their difference congruent to k. Use long for counts and residue subtraction; the problem API accepts `List<Integer>`.

**Time Complexity:**

O(n) expected, where n is number of elements.

**Space Complexity:**

O(min(n, modulo)) distinct stored residues.

---

## X6: Earliest/latest prefix-state index for lengths

### When to Apply

Find the longest or shortest subarray satisfying a prefix-state relationship. Store indices of prior states (earliest for longest, latest for shortest), not their frequencies.

### Basic Java Code Snippet

```java
java.util.Map<Long, Integer> index = new java.util.HashMap<>();
index.put(0L, -1);
long prefix = 0;
for (int i = 0; i < a.length; i++) {
    prefix += a[i];
    Integer j = index.get(prefix - target);
    if (j != null) { /* evaluate length i - j */ }
    index.putIfAbsent(prefix, i); // earliest for longest
    // Use index.put(prefix, i) for shortest after querying.
}
```

### Core Logic

The sentinel index −1 represents the empty prefix. Match a prior state to derive an interval length and retain the appropriate extremal index.

### Questions Covered

LeetCode 525, LeetCode 523, LeetCode 1590, LeetCode 1124.

---

### LeetCode 525

**Question:**

Given a binary integer array `nums`, return the maximum length of a contiguous subarray containing **equal numbers of zeroes and ones**. Return 0 if no nonempty balanced interval exists.

**Examples:**

- `nums=[0,1]` → `2`.
- `nums=[0,1,0]` → `2`.
- `nums=[0,0,1,1,0,1]` → `6`.

**Constraints:**

`1 ≤ nums.length ≤ 10^5`; `nums[i]` is 0 or 1.

**Why This Subpattern Applies:**

Encode zero as −1 and one as +1. A balanced subarray has net score zero, i.e. equal prefix scores at its boundaries; earliest occurrence yields the longest span.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int findMaxLength(int[] nums) {
        java.util.Map<Integer, Integer> first = new java.util.HashMap<>();
        first.put(0, -1);
        int balance = 0, best = 0;
        for (int i = 0; i < nums.length; i++) {
            balance += nums[i] == 1 ? 1 : -1;
            Integer j = first.get(balance);
            if (j != null) best = Math.max(best, i - j);
            else first.put(balance, i);
        }
        return best;
    }
}
```

**Key Logic:**

`balance` counts ones minus zeroes in the prefix. Repeated balance marks a zero-net subarray. Never overwrite the first index; that index maximizes the length for future matches.

**Time Complexity:**

O(n) expected.

**Space Complexity:**

O(n) earliest-index map.

---

### LeetCode 523

**Question:**

Given nonnegative integer array `nums` and positive integer `k`, determine whether there exists a **contiguous subarray of length at least 2** whose sum is a multiple of k (including 0). Return true or false.

**Examples:**

- `nums=[23,2,4,6,7]`, `k=6` → `true` (2+4=6).
- `nums=[23,2,6,4,7]`, `k=13` → `false`.
- `nums=[0,0]`, `k=1` → `true` (two zeroes sum to 0).

**Constraints:**

`1 ≤ nums.length ≤ 10^5`; `0 ≤ nums[i] ≤ 10^9`; `1 ≤ k ≤ 2^31−1`.

**Why This Subpattern Applies:**

A sum is divisible by k iff the boundary prefix sums have the same remainder. Retain the earliest occurrence to maximize separation and enforce length ≥2.

**Connection to Previous Solution:**

Replace 525’s signed ±1 balance with normalized prefix remainders modulo k; keep the earliest-index map and seed `(0,−1)`, but require `i−firstIndex≥2`.

**Java Solution:**

```java
class Solution {
    public boolean checkSubarraySum(int[] nums, int k) {
        java.util.Map<Integer, Integer> first = new java.util.HashMap<>();
        first.put(0, -1);
        int rem = 0;
        for (int i = 0; i < nums.length; i++) {
            rem = (int) (((long) rem + nums[i]) % k);
            Integer j = first.get(rem);
            if (j != null) {
                if (i - j >= 2) return true;
            } else {
                first.put(rem, i);
            }
        }
        return false;
    }
}
```

**Key Logic:**

`rem` represents the cumulative sum modulo positive k. Equal residues at i and j imply an interval `(j,i]` divisible by k; use long before modulo to avoid overflow. The earliest index gives the largest gap.

**Time Complexity:**

O(n) expected.

**Space Complexity:**

O(min(n,k)) distinct remainder indices.

---

### LeetCode 1590

**Question:**

Given an integer array `nums` and positive integer p, remove the **shortest contiguous subarray** (possibly empty) so the sum of remaining elements is divisible by p. You may not remove the entire array. Return the minimum removal length, or −1 if impossible.

**Examples:**

- `nums=[3,1,4,2]`, `p=6` → `1` (remove 4).
- `nums=[6,3,5,2]`, `p=9` → `2` (remove [5,2]).
- `nums=[1,2,3]`, `p=3` → `0` (nothing to remove).

**Constraints:**

`1 ≤ nums.length ≤ 10^5`; `1 ≤ nums[i] ≤ 10^9`; `1 ≤ p ≤ 10^9`.

**Why This Subpattern Applies:**

If total remainder is r, the removed interval must have sum congruent to r modulo p. For shortest length, use the latest compatible preceding prefix index.

**Connection to Previous Solution:**

Retain 523’s modular-prefix indexing but match previous residue `(current−totalRem+p)%p`; overwrite each stored index (latest instead of earliest), minimize `i-j`, and forbid removing all n elements.

**Java Solution:**

```java
class Solution {
    public int minSubarray(int[] nums, int p) {
        long total = 0;
        for (int x : nums) total += x;
        int target = (int) (total % p);
        if (target == 0) return 0;
        java.util.Map<Integer, Integer> latest = new java.util.HashMap<>();
        latest.put(0, -1);
        int rem = 0, best = nums.length;
        for (int i = 0; i < nums.length; i++) {
            rem = (int) (((long) rem + nums[i]) % p);
            int need = (int) Math.floorMod((long) rem - target, (long) p);
            Integer j = latest.get(need);
            if (j != null) best = Math.min(best, i - j);
            latest.put(rem, i);
        }
        return best == nums.length ? -1 : best;
    }
}
```

**Key Logic:**

The total remaining sum is divisible by p exactly when the deleted interval remainder equals `target`. The most recent eligible start minimizes deleted length. The full array is disallowed, so a result n means impossible.

**Time Complexity:**

O(n) expected.

**Space Complexity:**

O(min(n,p)) remainder-index map.

---

### LeetCode 1124

**Question:**

An integer array `hours` records hours worked each day. A day is tiring if hours >8; otherwise it is non-tiring. Find the length of the longest contiguous interval containing **strictly more tiring days than non-tiring days**. Return 0 if none exists.

**Examples:**

- `hours=[9,9,6,0,6,6,9]` → `3`.
- `hours=[6,6,6]` → `0`.
- `hours=[9,9,9]` → `3`.

**Constraints:**

`1 ≤ hours.length ≤ 10^4`; `0 ≤ hours[i] ≤ 16`.

**Why This Subpattern Applies:**

Turn tiring days into +1 and other days into −1. An interval is valid if its prefix-score difference is positive; earliest occurrence of `score−1` yields the longest eligible interval when current score ≤0.

**Connection to Previous Solution:**

Replace 1590’s remainders/latest indices with signed score and earliest indices. Instead of a modular equality, accept the entire prefix when score>0; otherwise look for the earliest score−1 and maximize length.

**Java Solution:**

```java
class Solution {
    public int longestWPI(int[] hours) {
        java.util.Map<Integer, Integer> first = new java.util.HashMap<>();
        int score = 0, best = 0;
        for (int i = 0; i < hours.length; i++) {
            score += hours[i] > 8 ? 1 : -1;
            if (score > 0) {
                best = i + 1;
            } else {
                Integer j = first.get(score - 1);
                if (j != null) best = Math.max(best, i - j);
            }
            first.putIfAbsent(score, i);
        }
        return best;
    }
}
```

**Key Logic:**

A positive whole-prefix score makes `[0..i]` valid. Otherwise, because scores move by ±1, the earliest prior score exactly one less produces a valid positive-sum interval and is at least as early as any relevant lower score. Record only first occurrences.

**Time Complexity:**

O(n) expected.

**Space Complexity:**

O(n) earliest-score map.

---

## X7: Offline range additions with difference arrays

### When to Apply

Many independent range updates are given ahead of time; individual element-by-element updates would be slow. Add start events and inverse end events, then sweep their cumulative effect.

### Basic Java Code Snippet

```java
long[] diff = new long[n + 1];
for (int[] update : updates) {
    int l = update[0], r = update[1], delta = update[2];
    diff[l] += delta;
    if (r + 1 < n) diff[r + 1] -= delta; // inclusive
}
long active = 0;
for (int i = 0; i < n; i++) {
    active += diff[i];
    // Use active as the accumulated update at i.
}
```

### Core Logic

The inclusive interval `[l,r]` adds at `l` and cancels at `r+1`; half-open intervals instead cancel at `end`. A single sweep reconstructs values.

### Questions Covered

LeetCode 1109, LeetCode 1094, LeetCode 3355, LeetCode 2381.

---

### LeetCode 1109

**Question:**

There are n flights numbered **1 through n**, initially with no seats booked. Each booking `[first,last,seats]` reserves `seats` additional seats on **every flight from first to last inclusive**. Multiple bookings add. Return the final seats booked on each flight in flight-number order.

**Examples:**

- `bookings=[[1,2,10],[2,3,20],[2,5,25]]`, `n=5` → `[10,55,45,25,25]`.
- `bookings=[[1,2,10],[2,2,15]]`, `n=2` → `[10,25]`.
- `bookings=[[1,1,7]]`, `n=1` → `[7]`.

**Constraints:**

`1 ≤ n ≤ 2×10^4`; `1 ≤ bookings.length ≤ 2×10^4`; `1 ≤ first ≤ last ≤ n`; `1 ≤ seats ≤ 10^4`.

**Why This Subpattern Applies:**

Each booking updates an entire consecutive interval. Store boundary differences and prefix-accumulate once rather than touching every flight for every booking.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int[] corpFlightBookings(int[][] bookings, int n) {
        int[] diff = new int[n + 1];
        for (int[] b : bookings) {
            int left = b[0] - 1, right = b[1] - 1;
            diff[left] += b[2];
            diff[right + 1] -= b[2];
        }
        int[] answer = new int[n];
        int active = 0;
        for (int i = 0; i < n; i++) {
            active += diff[i];
            answer[i] = active;
        }
        return answer;
    }
}
```

**Key Logic:**

Convert one-based flight labels to zero-based indices. Add at the first flight and cancel immediately after the last; the cumulative `active` is the correct booked count for each flight.

**Time Complexity:**

O(n+b), b bookings.

**Space Complexity:**

O(n) difference array and O(n) output.

---

### LeetCode 1094

**Question:**

A vehicle has `capacity` passenger seats and travels only east. Each trip `[numPassengers,from,to]` has that many passengers board at position `from` and exit at `to`. A passenger occupies a seat on the **half-open** segment `[from,to)`; passengers leaving at a position free seats before others board there. Return whether every part of the trip stays at or below capacity.

**Examples:**

- `trips=[[2,1,5],[3,3,7]]`, `capacity=4` → `false` (five aboard from 3 to 5).
- Same trips, `capacity=5` → `true`.
- `trips=[[3,2,4],[3,4,6]]`, `capacity=3` → `true` (drop-off at 4).

**Constraints:**

`1 ≤ trips.length ≤ 1000`; `1 ≤ numPassengers ≤ 100`; `0 ≤ from < to ≤ 1000`; `1 ≤ capacity ≤ 10^5`.

**Why This Subpattern Applies:**

Passenger occupancy is the sum of boarding and drop-off events along a line; a difference array tracks concurrent occupancy.

**Connection to Previous Solution:**

From 1109, change inclusive cancellation at `r+1` to half-open cancellation at the actual drop-off `to`. Instead of returning each total, check `active>capacity` during the sweep.

**Java Solution:**

```java
class Solution {
    public boolean carPooling(int[][] trips, int capacity) {
        int[] diff = new int[1001];
        for (int[] trip : trips) {
            diff[trip[1]] += trip[0];
            diff[trip[2]] -= trip[0];
        }
        int aboard = 0;
        for (int change : diff) {
            aboard += change;
            if (aboard > capacity) return false;
        }
        return true;
    }
}
```

**Key Logic:**

The difference array cancels riders precisely where they disembark, before capacity is checked at that coordinate. Sweeping event sums finds maximum concurrent occupancy.

**Time Complexity:**

O(t+U), t trips and U=1001 coordinates.

**Space Complexity:**

O(U)=O(1) under fixed coordinate bound.

---

### LeetCode 3355

**Question:**

Given a nonnegative integer array `nums` and inclusive index ranges `queries`, each range permits decrementing **each covered index independently by at most 1** (or choosing not to decrement it) for that query. Determine whether the queries together can reduce every array element to zero. Do not change values below zero. Return a boolean.

**Examples:**

- `nums=[1,0,1]`, `queries=[[0,2]]` → `true` (decrement positions 0 and 2).
- `nums=[4,3,2,1]`, `queries=[[1,3],[0,2]]` → `false` (index 0 has insufficient coverage).
- `nums=[0,0]`, `queries=[]` → `true`.

**Constraints:**

`1 ≤ nums.length ≤ 10^5`; `0 ≤ nums[i] ≤ 10^5`; `0 ≤ queries.length ≤ 10^5`; each query has `0 ≤ l ≤ r < nums.length`.

**Why This Subpattern Applies:**

Each query contributes one independently usable decrement to each covered position. Feasibility is therefore equivalent to range-coverage count ≥ required initial value at every position.

**Connection to Previous Solution:**

Keep 1094’s difference sweep, but return to inclusive intervals by subtracting at `r+1`; replace the maximum-capacity check with the lower-bound condition `coverage>=nums[i]`.

**Java Solution:**

```java
class Solution {
    public boolean isZeroArray(int[] nums, int[][] queries) {
        int n = nums.length;
        int[] diff = new int[n + 1];
        for (int[] q : queries) {
            diff[q[0]]++;
            diff[q[1] + 1]--;
        }
        int coverage = 0;
        for (int i = 0; i < n; i++) {
            coverage += diff[i];
            if (coverage < nums[i]) return false;
        }
        return true;
    }
}
```

**Key Logic:**

Coverage is the count of queries that include the current position. Because each position can independently opt in to a decrement for each query, exactly `nums[i]` decrements are possible iff coverage is sufficient.

**Time Complexity:**

O(n+q): updates plus one scan.

**Space Complexity:**

O(n) difference array.

---

### LeetCode 2381

**Question:**

Given a lowercase string `s` and shifting commands `[start,end,direction]`, apply each shift to every character in the **inclusive** range `[start,end]`. Direction 1 advances one letter; direction 0 moves one letter backward, with cyclic wraparound (`z→a`, `a→z`). Return the final string after all shifts; shifts can overlap and cancel.

**Examples:**

- `s="abc"`, `shifts=[[0,1,0],[1,2,1],[0,2,1]]` → `"ace"`.
- `s="dztz"`, `shifts=[[0,0,0],[1,1,1]]` → `"catz"`.
- `s="a"`, `shifts=[[0,0,0]]` → `"z"`.

**Constraints:**

`1 ≤ s.length ≤ 5×10^4`; `1 ≤ shifts.length ≤ 5×10^4`; `0 ≤ start ≤ end < s.length`; direction is 0 or 1.

**Why This Subpattern Applies:**

Overlapping character shifts are additive signed range updates; a difference sweep calculates each character’s net rotation efficiently.

**Connection to Previous Solution:**

Keep 3355’s inclusive `diff[l]`/`diff[r+1]` range updates, replace +1 coverage with signed ±1 deltas, and normalize the final letter index modulo 26.

**Java Solution:**

```java
class Solution {
    public String shiftingLetters(String s, int[][] shifts) {
        int n = s.length();
        int[] diff = new int[n + 1];
        for (int[] q : shifts) {
            int delta = q[2] == 1 ? 1 : -1;
            diff[q[0]] += delta;
            diff[q[1] + 1] -= delta;
        }
        char[] answer = s.toCharArray();
        int active = 0;
        for (int i = 0; i < n; i++) {
            active += diff[i];
            int letter = Math.floorMod(answer[i] - 'a' + active, 26);
            answer[i] = (char) ('a' + letter);
        }
        return new String(answer);
    }
}
```

**Key Logic:**

A net signed shift at index i is the prefix of boundary events. `floorMod` wraps negative shifts as well as positive ones, and each character is transformed exactly once.

**Time Complexity:**

O(n+q), with n characters and q operations.

**Space Complexity:**

O(n) difference and character arrays, plus output string.

---

## X8: Forced local range updates via difference sweep

### When to Apply

You must choose length-k decrement windows while sweeping left to right. Once the scan reaches i, only windows starting at i can correct an unhandled residual at i.

### Basic Java Code Snippet

```java
long[] expiry = new long[n + 1];
long active = 0;
for (int i = 0; i < n; i++) {
    active += expiry[i];
    long need = a[i] - active;
    if (need < 0 || (need > 0 && i + k > n)) {
        // Impossible.
    }
    if (need > 0) {
        active += need;
        expiry[i + k] -= need;
    }
}
```

### Core Logic

`active` counts decrements already covering i. Exactly `need` new decrements are forced at i; schedule their expiration at i+k.

### Questions Covered

LeetCode 2772.

---

### LeetCode 2772

**Question:**

Given a nonnegative integer array `nums` and positive integer `k`, you may repeatedly choose **exactly k consecutive positions** and decrement every chosen value by 1. Determine whether some sequence of operations makes every value exactly zero. Values may not be made negative in a valid final solution.

**Examples:**

- `nums=[2,2,3,1,1,0]`, `k=3` → `true`.
- `nums=[1,3,1,1]`, `k=2` → `false`.
- `nums=[0,0,0]`, `k=2` → `true`.

**Constraints:**

`1 ≤ nums.length ≤ 10^5`; `1 ≤ nums[i] ≤ 10^6` in the original problem; `1 ≤ k ≤ nums.length`.

**Why This Subpattern Applies:**

At the leftmost not-yet-fixed position, all previous windows are already decided, so the residual value uniquely determines how many new k-windows must begin here.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public boolean checkArray(int[] nums, int k) {
        int n = nums.length;
        long[] expire = new long[n + 1];
        long active = 0;
        for (int i = 0; i < n; i++) {
            active += expire[i];
            long need = nums[i] - active;
            if (need < 0) return false;
            if (need == 0) continue;
            if (i + k > n) return false;
            active += need;
            expire[i + k] -= need;
        }
        return true;
    }
}
```

**Key Logic:**

`active` is the number of previously started decrements currently affecting i. A negative `need` means over-decrementing, and a positive `need` too near the end cannot be fulfilled. Otherwise start exactly `need` operations and expire them k positions later.

**Time Complexity:**

O(n): one sweep.

**Space Complexity:**

O(n) expiration events.

---

## X9: Contribution counting over all contiguous spans

### When to Apply

An aggregate over all intervals can be obtained by asking how often each element participates, without enumerating all subarrays.

### Basic Java Code Snippet

```java
long answer = 0;
for (int i = 0; i < a.length; i++) {
    long spans = (long) (i + 1) * (a.length - i);
    long qualifying = (spans + 1) / 2; // odd-length
    answer += qualifying * a[i];
}
```

### Core Logic

There are `(i+1)*(n-i)` intervals containing index i; `(spans+1)/2` of them have odd length. Sum weighted contributions.

### Questions Covered

LeetCode 1588.

---

### LeetCode 1588

**Question:**

Given an array `arr` of positive integers, consider **every contiguous subarray of odd length**, including length one. Add the sums of all such subarrays together and return the total. Count each distinct interval once even when its values match another interval.

**Examples:**

- `arr=[1,4,2,5,3]` → `58`.
- `arr=[1,2]` → `3` (only singletons).
- `arr=[10,11,12]` → `66` (singletons sum 33 plus length-3 sum 33).

**Constraints:**

`1 ≤ arr.length ≤ 100`; `1 ≤ arr[i] ≤ 1000`.

**Why This Subpattern Applies:**

Rather than enumerate intervals, count how many odd-length intervals contain each index and multiply that count by its value.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int sumOddLengthSubarrays(int[] arr) {
        long answer = 0;
        int n = arr.length;
        for (int i = 0; i < n; i++) {
            long allContaining = (long) (i + 1) * (n - i);
            long oddContaining = (allContaining + 1) / 2;
            answer += oddContaining * arr[i];
        }
        return (int) answer;
    }
}
```

**Key Logic:**

There are `i+1` choices of start and `n-i` choices of end. Exactly `(allContaining+1)/2` combinations give odd length. Summing their contributions counts every odd-length interval sum exactly once.

**Time Complexity:**

O(n) time.

**Space Complexity:**

O(1) auxiliary.

---

## X10: Prefix/suffix cut evaluation

### When to Apply

Choose or count a cut based on statistics of the prefix and suffix. The boundary moves once and the left/right aggregates can be updated in O(1) per cut.

### Basic Java Code Snippet

```java
long total = 0, left = 0;
for (int x : a) total += x;
for (int i = 0; i < a.length - 1; i++) {
    left += a[i];
    long right = total - left;
    // Evaluate cut just after index i.
}
```

### Core Logic

Track the accumulated left sum and derive the right sum from the total. Include endpoint cuts when the problem allows empty sides.

### Questions Covered

LeetCode 2256, LeetCode 2483, LeetCode 2270.

---

### LeetCode 2256

**Question:**

Given an integer array `nums`, define the average difference at index i as the absolute difference between the **integer-truncated average** of `nums[0..i]` and the integer-truncated average of `nums[i+1..n−1]`. For the last index the right average is defined as 0. Return the **smallest index** with minimum average difference.

**Examples:**

- `nums=[2,5,3,9,5,3]` → `3`.
- `nums=[0]` → `0`.
- `nums=[1,1,1]` → `0` (first two cuts tie with difference 0).

**Constraints:**

`1 ≤ nums.length ≤ 10^5`; `0 ≤ nums[i] ≤ 10^5`.

**Why This Subpattern Applies:**

One total and a running left sum give both segment sums at each possible cut. Integer division yields the prescribed truncated averages.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int minimumAverageDifference(int[] nums) {
        int n = nums.length;
        long total = 0;
        for (int x : nums) total += x;
        long left = 0, bestDiff = Long.MAX_VALUE;
        int bestIndex = 0;
        for (int i = 0; i < n; i++) {
            left += nums[i];
            long leftAvg = left / (i + 1);
            long rightAvg = i == n - 1 ? 0 : (total - left) / (n - i - 1);
            long diff = Math.abs(leftAvg - rightAvg);
            if (diff < bestDiff) {
                bestDiff = diff;
                bestIndex = i;
            }
        }
        return bestIndex;
    }
}
```

**Key Logic:**

`left` is inclusive through i; suffix is `total−left`. Evaluate last index separately because its right side is empty. Strict `<` retains the earliest index in a tie.

**Time Complexity:**

O(n): total and candidate scans.

**Space Complexity:**

O(1) auxiliary.

---

### LeetCode 2483

**Question:**

A shop has an hourly customer log `customers` made of `Y` (someone arrives) and `N` (no arrival). The shop may close at any integer hour j from 0 through n inclusive: open during hours `[0,j)` and closed during `[j,n)`. Penalty is **one per open hour with N** plus **one per closed hour with Y**. Return the earliest closing hour minimizing penalty.

**Examples:**

- `customers="YYNY"` → `2`.
- `customers="NNNNN"` → `0`.
- `customers="YYYY"` → `4`.

**Constraints:**

`1 ≤ customers.length ≤ 10^5`; characters are `Y` or `N`.

**Why This Subpattern Applies:**

Each potential closing hour is a split: one side penalizes noncustomers and the other side penalizes missed customers. The objective changes by only ±1 as the cut advances.

**Connection to Previous Solution:**

Keep 2256’s evaluation of successive cuts and earliest-tie preference; replace averages with an incremental penalty starting at hour 0 (number of all `Y`), then on crossing `Y` subtract 1 or crossing `N` add 1. Include cuts 0 and n.

**Java Solution:**

```java
class Solution {
    public int bestClosingTime(String customers) {
        int penalty = 0;
        for (int i = 0; i < customers.length(); i++) {
            if (customers.charAt(i) == 'Y') penalty++;
        }
        int best = penalty, bestHour = 0;
        for (int i = 0; i < customers.length(); i++) {
            penalty += customers.charAt(i) == 'Y' ? -1 : 1;
            if (penalty < best) {
                best = penalty;
                bestHour = i + 1;
            }
        }
        return bestHour;
    }
}
```

**Key Logic:**

At hour 0 all Y arrivals are missed. Moving the closing boundary past an hour converts a missed Y into a served Y (−1), or converts a closed N into an idle open N (+1). Strict improvement preserves earliest minimum.

**Time Complexity:**

O(n): initial penalty plus one pass.

**Space Complexity:**

O(1) auxiliary.

---

### LeetCode 2270

**Question:**

Given an integer array `nums`, count the indices i where splitting **immediately after i** creates two **nonempty** subarrays and the sum of the left subarray is at least the sum of the right subarray. Return the number of such split positions.

**Examples:**

- `nums=[10,4,-8,7]` → `2` (after indices 1 and 2).
- `nums=[2,3,1,0]` → `2` (after indices 1 and 2).
- `nums=[1,1]` → `1`.

**Constraints:**

`2 ≤ nums.length ≤ 10^5`; `−10^5 ≤ nums[i] ≤ 10^5`.

**Why This Subpattern Applies:**

All candidates are single cuts; use a running left sum and total to evaluate `left ≥ total−left` in O(1) per cut.

**Connection to Previous Solution:**

Retain 2483’s scanning cut boundary; replace dynamic penalty/minimization with a left/right sum inequality and increment a counter. Exclude the last boundary to keep the right side nonempty.

**Java Solution:**

```java
class Solution {
    public int waysToSplitArray(int[] nums) {
        long total = 0;
        for (int x : nums) total += x;
        long left = 0;
        int count = 0;
        for (int i = 0; i < nums.length - 1; i++) {
            left += nums[i];
            if (left >= total - left) count++;
        }
        return count;
    }
}
```

**Key Logic:**

`left` represents indices `[0..i]` and `total−left` the nonempty right side. Use long because a sum of 100000 values can exceed int.

**Time Complexity:**

O(n): two scans.

**Space Complexity:**

O(1) auxiliary.

---

## X11: Feasible multiple-prefix partition bounds

### When to Apply

Split a nonnegative sequence into three nonempty contiguous parts with inequalities between their sums. Nonnegative values make prefix sums monotone, so valid second cuts form a searchable interval.

### Basic Java Code Snippet

```java
long[] pref = new long[a.length + 1];
for (int i = 0; i < a.length; i++)
    pref[i + 1] = pref[i] + a[i];
for (int first = 1; first <= a.length - 2; first++) {
    // Binary-search first and last feasible second cut
    // using pref[second] - pref[first] and pref[n] - pref[second].
}
```

### Core Logic

Fix the first boundary and binary-search the lower and upper feasible second boundaries; add the number of valid indices.

### Questions Covered

LeetCode 1712.

---

### LeetCode 1712

**Question:**

Given a **nonnegative** integer array `nums`, split it into exactly **three nonempty contiguous subarrays** named left, middle, and right. Count the choices of two cut positions for which `sum(left) ≤ sum(middle) ≤ sum(right)`. Return the number of qualifying splits modulo `1_000_000_007`.

**Examples:**

- `nums=[1,1,1]` → `1`.
- `nums=[1,2,2,2,5,0]` → `3`.
- `nums=[3,2,1]` → `0`.

**Constraints:**

`3 ≤ nums.length ≤ 10^5`; `0 ≤ nums[i] ≤ 10^4`.

**Why This Subpattern Applies:**

For a fixed first cut, nonnegative values ensure prefix sums never decrease. Thus feasible second cuts form one interval located by two binary searches.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int waysToSplit(int[] nums) {
        final int MOD = 1_000_000_007;
        int n = nums.length;
        long[] prefix = new long[n + 1];
        for (int i = 0; i < n; i++) prefix[i + 1] = prefix[i] + nums[i];
        long ways = 0;
        for (int first = 1; first <= n - 2; first++) {
            long leftSum = prefix[first];
            // middle >= left: prefix[second] >= 2*prefix[first]
            int lo = lowerBound(prefix, first + 1, n, 2L * leftSum);
            // middle <= right: 2*prefix[second] <= prefix[n] + prefix[first]
            long maxSecondPrefix = (prefix[n] + leftSum) / 2;
            int hiExclusive = upperBound(prefix, first + 1, n, maxSecondPrefix);
            if (hiExclusive > lo) ways = (ways + hiExclusive - lo) % MOD;
        }
        return (int) ways;
    }

    private int lowerBound(long[] a, int from, int to, long target) {
        int l = from, r = to;
        while (l < r) {
            int m = l + (r - l) / 2;
            if (a[m] < target) l = m + 1;
            else r = m;
        }
        return l;
    }

    private int upperBound(long[] a, int from, int to, long target) {
        int l = from, r = to;
        while (l < r) {
            int m = l + (r - l) / 2;
            if (a[m] <= target) l = m + 1;
            else r = m;
        }
        return l;
    }
}
```

**Key Logic:**

With first cut at `first` and second cut at `second`, middle sum is `prefix[second]−prefix[first]`. First inequality requires `prefix[second]≥2*prefix[first]`; second requires `prefix[second]≤(prefix[n]+prefix[first])/2`. Binary-search in `[first+1,n)` to keep all three parts nonempty; add the size of the feasible intersection.

**Time Complexity:**

O(n log n): prefix build plus two bounds per first cut.

**Space Complexity:**

O(n) prefix array; O(1) auxiliary per binary search.

---

## X12: Endpoint-conditioned optimal prefix state

### When to Apply

An interval is permitted only if its endpoint values satisfy a relation; maximize its sum. For each possible starting value, keep the smallest prefix sum before that endpoint.

### Basic Java Code Snippet

```java
java.util.Map<Integer, Long> minPrefix = new java.util.HashMap<>();
long prefix = 0, best = Long.MIN_VALUE;
for (int value : a) {
    // Look up previous endpoint values related to value.
    // Candidate = prefix + value - minPrefix.get(startValue).
    minPrefix.merge(value, prefix, Math::min);
    prefix += value;
}
```

### Core Logic

The minimum prefix preceding a matching starting value yields the maximum sum for the current ending value; update the map only after checking the current interval.

### Questions Covered

LeetCode 3026.

---

### LeetCode 3026

**Question:**

Given integer array `nums` and positive integer k, a **good subarray** is a nonempty contiguous subarray whose **first and last elements differ in absolute value by exactly k**. Find the maximum sum among all good subarrays; return 0 when none exists. Values and sums may be negative.

**Examples:**

- `nums=[1,2,3,4,5,6]`, `k=1` → `11` (subarray [5,6]).
- `nums=[-1,3,2,4,5]`, `k=3` → `11` (subarray [2,4,5]).
- `nums=[1,2,3,4]`, `k=10` → `0`.

**Constraints:**

`2 ≤ nums.length ≤ 10^5`; `−10^9 ≤ nums[i] ≤ 10^9`; `1 ≤ k ≤ 10^9`.

**Why This Subpattern Applies:**

For ending value x, only starting endpoint values x−k or x+k can qualify. To maximize interval sum, subtract the smallest prefix before any matching starting endpoint.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public long maximumSubarraySum(int[] nums, int k) {
        java.util.Map<Integer, Long> bestStartPrefix = new java.util.HashMap<>();
        long prefix = 0;
        long best = Long.MIN_VALUE;
        for (int x : nums) {
            long endSum = prefix + x;
            long lowKey = (long) x - k;
            long highKey = (long) x + k;
            if (lowKey >= Integer.MIN_VALUE && lowKey <= Integer.MAX_VALUE) {
                Long start = bestStartPrefix.get((int) lowKey);
                if (start != null) best = Math.max(best, endSum - start);
            }
            if (highKey >= Integer.MIN_VALUE && highKey <= Integer.MAX_VALUE) {
                Long start = bestStartPrefix.get((int) highKey);
                if (start != null) best = Math.max(best, endSum - start);
            }
            bestStartPrefix.merge(x, prefix, Math::min);
            prefix = endSum;
        }
        return best == Long.MIN_VALUE ? 0 : best;
    }
}
```

**Key Logic:**

`prefix` is the sum strictly preceding the current index. A matching earlier endpoint value y stores the smallest prefix preceding some prior y, so `endSum−stored` maximizes the interval sum for this ending position. Query before insertion so only valid earlier starts are considered; guard x±k overflow.

**Time Complexity:**

O(n) expected hash-map operations.

**Space Complexity:**

O(n) mapping of distinct endpoint values.

---

## X13: Two-pass monotone streak lengths

### When to Apply

Each position must have enough consecutive neighbors to its left and right satisfying adjacent inequalities. Independent directional streak arrays allow O(1) validation per position.

### Basic Java Code Snippet

```java
int n = a.length;
int[] left = new int[n], right = new int[n];
for (int i = 1; i < n; i++)
    if (a[i] <= a[i - 1]) left[i] = left[i - 1] + 1;
for (int i = n - 2; i >= 0; i--)
    if (a[i] <= a[i + 1]) right[i] = right[i + 1] + 1;
// Keep indices with both streak lengths >= required time.
```

### Core Logic

Each array records how many adjacent qualifying steps extend from the given index in its direction; intersect the conditions.

### Questions Covered

LeetCode 2100.

---

### LeetCode 2100

**Question:**

Given an array `security` where `security[i]` is the number of guards on day i, and a nonnegative integer `time`, find every day i such that the **preceding `time` days are nonincreasing up to i** and the **following `time` days are nondecreasing from i**. Specifically, for the left side: `security[i−time]≥...≥security[i]`; for the right: `security[i]≤...≤security[i+time]`. Only indices with both full windows inside the array qualify. Return the valid indices in ascending order.

**Examples:**

- `security=[5,3,3,3,5,6,2]`, `time=2` → `[2,3]`.
- `security=[1,1,1,1,1]`, `time=0` → `[0,1,2,3,4]`.
- `security=[1,2,3,4,5,6]`, `time=2` → `[]`.

**Constraints:**

`1 ≤ security.length ≤ 10^5`; `0 ≤ security[i] ≤ 10^5`; `0 ≤ time ≤ 10^5`.

**Why This Subpattern Applies:**

A day qualifies when two independent consecutive-neighbor monotonic conditions have enough length. Store the directional streak lengths and intersect them.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public java.util.List<Integer> goodDaysToRobBank(int[] security, int time) {
        int n = security.length;
        int[] left = new int[n], right = new int[n];
        for (int i = 1; i < n; i++) {
            if (security[i] <= security[i - 1]) left[i] = left[i - 1] + 1;
        }
        for (int i = n - 2; i >= 0; i--) {
            if (security[i] <= security[i + 1]) right[i] = right[i + 1] + 1;
        }
        java.util.List<Integer> answer = new java.util.ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (left[i] >= time && right[i] >= time) answer.add(i);
        }
        return answer;
    }
}
```

**Key Logic:**

`left[i]` is the number of successive nonincreasing comparisons immediately to the left; `right[i]` is the number of successive nondecreasing comparisons immediately to the right. Both counts must reach `time`; `time=0` accepts every index.

**Time Complexity:**

O(n): forward, reverse, and collection passes.

**Space Complexity:**

O(n) left/right streak arrays plus O(n) output in the worst case.

---

## Final Question Registry

**All 32 LOCKED LeetCode IDs, exact curriculum order:**

| Order | Family | LeetCode | Included |
|---:|---|---:|---|
| 01 | X1 | 1480 | Yes |
| 02 | X1 | 1732 | Yes |
| 03 | X1 | 1413 | Yes |
| 04 | X1 | 724 | Yes |
| 05 | X1 | 2574 | Yes |
| 06 | X2 | 303 | Yes |
| 07 | X2 | 2559 | Yes |
| 08 | X2 | 2055 | Yes |
| 09 | X3 | 238 | Yes |
| 10 | X4 | 1769 | Yes |
| 11 | X4 | 1685 | Yes |
| 12 | X4 | 2602 | Yes |
| 13 | X5 | 1524 | Yes |
| 14 | X5 | 560 | Yes |
| 15 | X5 | 974 | Yes |
| 16 | X5 | 2845 | Yes |
| 17 | X6 | 525 | Yes |
| 18 | X6 | 523 | Yes |
| 19 | X6 | 1590 | Yes |
| 20 | X6 | 1124 | Yes |
| 21 | X7 | 1109 | Yes |
| 22 | X7 | 1094 | Yes |
| 23 | X7 | 3355 | Yes |
| 24 | X7 | 2381 | Yes |
| 25 | X8 | 2772 | Yes |
| 26 | X9 | 1588 | Yes |
| 27 | X10 | 2256 | Yes |
| 28 | X10 | 2483 | Yes |
| 29 | X10 | 2270 | Yes |
| 30 | X11 | 1712 | Yes |
| 31 | X12 | 3026 | Yes |
| 32 | X13 | 2100 | Yes |

**Registry verification:** 32 / 32 questions included, 13 / 13 algorithm families included, no missing IDs, duplicate IDs, or order changes.

**Progress note:** This is a complete reference workbook, not evidence that any question was practiced or completed.
