# T04 — Sliding Window: Complete Java 21 Solutions

**Locked curriculum:** 38 LeetCode questions · 15 algorithm families · master version 1.4

**Scope:** Exact frozen order from `MASTER_DSA_CURRICULUM.md`, T04. This is a reference workbook, **not** an indication that these questions were practiced or completed.

## Table of Contents

- [W1 — Fixed-size rolling numeric aggregate](#w1-family) — [LC 643](#leetcode-643), [LC 1343](#leetcode-1343), [LC 2090](#leetcode-2090)
- [W2 — Fixed-span categorical contribution / complement](#w2-family) — [LC 1456](#leetcode-1456), [LC 1052](#leetcode-1052), [LC 1423](#leetcode-1423)
- [W3 — Fixed-width distinctness and weighted distinct values](#w3-family) — [LC 1876](#leetcode-1876), [LC 2461](#leetcode-2461), [LC 2841](#leetcode-2841)
- [W4 — Fixed-width multiset matching](#w4-family) — [LC 438](#leetcode-438), [LC 567](#leetcode-567), [LC 30](#leetcode-30)
- [W5 — Variable window with distinctness bound](#w5-family) — [LC 3](#leetcode-3), [LC 1695](#leetcode-1695), [LC 904](#leetcode-904)
- [W6 — Variable window with replacement/deletion budget](#w6-family) — [LC 1004](#leetcode-1004), [LC 1493](#leetcode-1493), [LC 2024](#leetcode-2024), [LC 424](#leetcode-424)
- [W7 — Shortest feasible window with quantitative / coverage constraints](#w7-family) — [LC 209](#leetcode-209), [LC 76](#leetcode-76), [LC 1234](#leetcode-1234)
- [W8 — Counting windows via monotone numeric constraints](#w8-family) — [LC 713](#leetcode-713), [LC 2302](#leetcode-2302), [LC 2537](#leetcode-2537)
- [W9 — Exact-category counts via at-most difference](#w9-family) — [LC 930](#leetcode-930), [LC 1248](#leetcode-1248), [LC 992](#leetcode-992), [LC 2799](#leetcode-2799)
- [W10 — Bounded spread using monotonic deques](#w10-family) — [LC 1438](#leetcode-1438), [LC 2762](#leetcode-2762)
- [W11 — Sorted-window transform budget](#w11-family) — [LC 2779](#leetcode-2779), [LC 1838](#leetcode-1838), [LC 2968](#leetcode-2968)
- [W12 — Circular fixed-window defect minimization](#w12-family) — [LC 2134](#leetcode-2134)
- [W13 — Longest retained middle segment as complement](#w13-family) — [LC 1658](#leetcode-1658)
- [W14 — Minimum window with ordered event counts](#w14-family) — [LC 1358](#leetcode-1358)
- [W15 — Frequency-constrained substring counting](#w15-family) — [LC 2958](#leetcode-2958)

---

## W1 — Fixed-size rolling numeric aggregate
<a id="w1-family"></a>

### Subpattern W1: Fixed-size rolling numeric aggregate

**When to Apply:**
- **Recognition:** An exact `k` consecutive elements are required; maintain their sum in constant time per shift.
- **Central invariant:** The sum always represents only the most recent `k` entries.

**Basic Java Code Snippet:**

```java
long sum = 0;
for (int r = 0; r < a.length; r++) {
    sum += a[r];
    if (r >= k) sum -= a[r - k];
    if (r >= k - 1) { /* inspect exactly-k sum */ }
}
```

**Core Logic:**
The sum always represents only the most recent `k` entries. The snippet is a reusable pattern skeleton, not a standalone LeetCode submission. Adapt its variables and guard conditions to the exact question constraints.

**Questions Covered:** LeetCode 643, LeetCode 1343, LeetCode 2090

### LeetCode 643

**Question:**

Given an integer array `nums` and an integer `k`, choose exactly `k` adjacent elements and return the largest possible arithmetic average among all such subarrays. A subarray must be contiguous; return the average as a floating-point number, with answers accepted within `1e-5`. Negative values are allowed.

**Examples:**

- `nums=[1,12,-5,-6,50,3], k=4` → `12.75` (best sum 51).
- `nums=[5], k=1` → `5.0`.
- `nums=[-4,-2,-8], k=2` → `-3.0`.

**Constraints:**

`1 ≤ k ≤ nums.length ≤ 100000`; `-10000 ≤ nums[i] ≤ 10000`.

**Why This Subpattern Applies:**

All candidate windows have exactly the same size, so maximizing their average is equivalent to maximizing their sum; one incoming and one outgoing element suffice per slide.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public double findMaxAverage(int[] nums, int k) {
        long sum = 0;
        for (int i = 0; i < k; i++) sum += nums[i];
        long best = sum;
        for (int right = k; right < nums.length; right++) {
            sum += nums[right] - nums[right - k];
            best = Math.max(best, sum);
        }
        return (double) best / k;
    }
}
```

**Key Logic:**

Initialize the first window; each shift preserves exactly `k` terms. Track `best` as a sum, not an average, and divide once. A `long` accumulator is safe even if limits grow.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 1343

**Question:**

Given an integer array `arr`, a fixed subarray length `k`, and a threshold, count how many contiguous subarrays of length exactly `k` have an average **greater than or equal to** `threshold`. Count overlapping windows separately.

**Examples:**

- `arr=[2,2,2,2,5,5,5,8], k=3, threshold=4` → `3`.
- `arr=[11,13,17,23,29,31,7,5,2,3], k=3, threshold=5` → `6`.
- `arr=[1,1,1], k=2, threshold=2` → `0`.

**Constraints:**

`1 ≤ k ≤ arr.length ≤ 100000`; `1 ≤ arr[i] ≤ 10000`; `0 ≤ threshold ≤ 10000`.

**Why This Subpattern Applies:**

Exact width `k` is fixed. Comparing the sum to `k * threshold` avoids floating-point division.

**Connection to Previous Solution:**

Keep the same rolling-sum update as 643; replace `best = max(best,sum)` with `if (sum >= k*threshold) count++`.

**Java Solution:**

```java
class Solution {
    public int numOfSubarrays(int[] arr, int k, int threshold) {
        long sum = 0;
        for (int i = 0; i < k; i++) sum += arr[i];
        long required = (long) k * threshold;
        int count = sum >= required ? 1 : 0;
        for (int right = k; right < arr.length; right++) {
            sum += arr[right] - arr[right - k];
            if (sum >= required) count++;
        }
        return count;
    }
}
```

**Key Logic:**

The rolling sum always covers exactly `k` elements. Include the first window before sliding and use a scaled integer threshold to avoid rounding.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 2090

**Question:**

Given an array `nums` and nonnegative radius `k`, produce an equally long array. For each index `i`, if `k` elements exist to its left and `k` to its right, output the integer average of those `2*k+1` consecutive elements (truncate toward zero). Otherwise output `-1`. A radius zero means every output equals its input element.

**Examples:**

- `nums=[7,4,3,9,1,8,5,2,6], k=3` → `[-1,-1,-1,5,4,4,-1,-1,-1]`.
- `nums=[100000], k=0` → `[100000]`.
- `nums=[8], k=100000` → `[-1]`.

**Constraints:**

`1 ≤ nums.length ≤ 100000`; `0 ≤ nums[i] ≤ 100000`; `0 ≤ k ≤ 100000`.

**Why This Subpattern Applies:**

Every valid center is associated with a fixed window of length `2*k+1`; the center is `right-k` when that window ends at `right`.

**Connection to Previous Solution:**

Keep the rolling sum from 1343, but replace threshold counting with `answer[right-k] = (int)(sum / (2*k+1))` and use `long` to prevent overflow.

**Java Solution:**

```java
class Solution {
    public int[] getAverages(int[] nums, int k) {
        int n = nums.length;
        int[] answer = new int[n];
        java.util.Arrays.fill(answer, -1);
        long width = 2L * k + 1;
        if (width > n) return answer;
        long sum = 0;
        int w = (int) width;
        for (int right = 0; right < n; right++) {
            sum += nums[right];
            if (right >= w) sum -= nums[right - w];
            if (right >= w - 1) answer[right - k] = (int) (sum / width);
        }
        return answer;
    }
}
```

**Key Logic:**

Initialize all impossible centers to `-1`. If window width exceeds input size, return immediately. Each full window contributes once to its center. Division truncates toward zero as required.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(n) output; O(1) auxiliary`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

## W2 — Fixed-span categorical contribution / complement
<a id="w2-family"></a>

### Subpattern W2: Fixed-span categorical contribution / complement

**When to Apply:**
- **Recognition:** Each index yields a gain or loss, or the chosen end elements leave one contiguous middle span.
- **Central invariant:** Maintain a rolling contribution or its excluded complement.

**Basic Java Code Snippet:**

```java
long gain = 0, best = Long.MIN_VALUE;
for (int r = 0; r < n; r++) {
    gain += contribution(r);
    if (r >= width) gain -= contribution(r - width);
    if (r >= width - 1) best = Math.max(best, gain);
}
// contribution(index) is a problem-specific score; handle width==0.
```

**Core Logic:**
Maintain a rolling contribution or its excluded complement. The snippet is a reusable pattern skeleton, not a standalone LeetCode submission. Adapt its variables and guard conditions to the exact question constraints.

**Questions Covered:** LeetCode 1456, LeetCode 1052, LeetCode 1423

### LeetCode 1456

**Question:**

Given a lowercase English string `s` and integer `k`, find the maximum number of vowels (`a`, `e`, `i`, `o`, `u`) in any contiguous substring of length exactly `k`. Overlapping substrings count as different candidates.

**Examples:**

- `s="abciiidef", k=3` → `3` (`"iii"`).
- `s="aeiou", k=2` → `2`.
- `s="leetcode", k=3` → `2`.

**Constraints:**

`1 ≤ k ≤ s.length ≤ 100000`; only lowercase English letters.

**Why This Subpattern Applies:**

Each character contributes either zero or one vowel to its fixed-width window, so update the score when it enters or leaves.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int maxVowels(String s, int k) {
        int count = 0, best = 0;
        for (int right = 0; right < s.length(); right++) {
            if (isVowel(s.charAt(right))) count++;
            if (right >= k && isVowel(s.charAt(right - k))) count--;
            if (right >= k - 1) best = Math.max(best, count);
        }
        return best;
    }
    private boolean isVowel(char c) {
        return c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u';
    }
}
```

**Key Logic:**

The boolean `isVowel` is the per-position contribution predicate. Update the incoming and outgoing characters and inspect only full windows.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 1052

**Question:**

A bookstore records `customers[i]` arriving in each minute and whether the owner is grumpy in minute `i` (`grumpy[i] == 1`). Customers are satisfied automatically when the owner is not grumpy. The owner can suppress grumpiness for one contiguous stretch of exactly `minutes` minutes. Return the maximum total satisfied customers after choosing that stretch.

**Examples:**

- `customers=[1,0,1,2,1,1,7,5], grumpy=[0,1,0,1,0,1,0,1], minutes=3` → `16`.
- `customers=[1], grumpy=[0], minutes=1` → `1`.
- `customers=[4,3,2], grumpy=[1,1,1], minutes=2` → `7`.

**Constraints:**

`1 ≤ minutes ≤ customers.length ≤ 20000`; `0 ≤ customers[i] ≤ 1000`; `grumpy[i] ∈ {0,1}`.

**Why This Subpattern Applies:**

Non-grumpy minutes form a fixed baseline. The selected window gains only the customers who would otherwise be unhappy, an additive categorical contribution.

**Connection to Previous Solution:**

Replace W2's vowel indicator with `grumpy[i] == 1 ? customers[i] : 0`; add the independent always-satisfied baseline to the maximum window gain.

**Java Solution:**

```java
class Solution {
    public int maxSatisfied(int[] customers, int[] grumpy, int minutes) {
        int baseline = 0, gain = 0, bestGain = 0;
        for (int right = 0; right < customers.length; right++) {
            if (grumpy[right] == 0) baseline += customers[right];
            else gain += customers[right];
            if (right >= minutes && grumpy[right - minutes] == 1)
                gain -= customers[right - minutes];
            if (right >= minutes - 1) bestGain = Math.max(bestGain, gain);
        }
        return baseline + bestGain;
    }
}
```

**Key Logic:**

The baseline is counted exactly once for all normally satisfied minutes. Sliding `gain` counts only extra satisfaction from overriding grumpiness, so it does not double count baseline customers.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 1423

**Question:**

You have a row of cards with points `cardPoints[i]`. Exactly `k` times, take one card from either the left end or the right end of the remaining row. Maximize the sum of points taken. You may take all cards, or none if `k=0` (the official constraint requires at least one).

**Examples:**

- `cardPoints=[1,2,3,4,5,6,1], k=3` → `12`.
- `cardPoints=[2,2,2], k=2` → `4`.
- `cardPoints=[9,7,7,9,7,7,9], k=7` → `55`.

**Constraints:**

`1 ≤ cardPoints.length ≤ 100000`; `1 ≤ cardPoints[i] ≤ 10000`; `1 ≤ k ≤ cardPoints.length`.

**Why This Subpattern Applies:**

After removing `k` endpoints, the remaining `n-k` cards must form one contiguous middle subarray. Maximizing picked points is minimizing its excluded sum.

**Connection to Previous Solution:**

Turn 1052's maximum affected-window gain into the minimum sum of an excluded window of width `n-k`; compute `total - minExcluded`.

**Java Solution:**

```java
class Solution {
    public int maxScore(int[] cardPoints, int k) {
        int n = cardPoints.length;
        int total = 0;
        for (int value : cardPoints) total += value;
        int width = n - k;
        if (width == 0) return total;
        int excluded = 0;
        for (int i = 0; i < width; i++) excluded += cardPoints[i];
        int minExcluded = excluded;
        for (int right = width; right < n; right++) {
            excluded += cardPoints[right] - cardPoints[right - width];
            minExcluded = Math.min(minExcluded, excluded);
        }
        return total - minExcluded;
    }
}
```

**Key Logic:**

A sequence of endpoint picks uniquely leaves a middle segment, and any such segment is attainable. Handle `k=n` before attempting a zero-width window.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

## W3 — Fixed-width distinctness and weighted distinct values
<a id="w3-family"></a>

### Subpattern W3: Fixed-width distinctness and weighted distinct values

**When to Apply:**
- **Recognition:** A fixed-size segment must contain a required number of unique values, perhaps maximizing a sum.
- **Central invariant:** The frequency map describes exactly the current `k` positions.

**Basic Java Code Snippet:**

```java
java.util.Map<Integer,Integer> freq = new java.util.HashMap<>();
int left = 0;
long sum = 0;
for (int right = 0; right < a.length; right++) {
    freq.merge(a[right], 1, Integer::sum); sum += a[right];
    if (right - left + 1 > k) {
        int old = a[left++]; sum -= old;
        if (freq.merge(old, -1, Integer::sum) == 0) freq.remove(old);
    }
    if (right - left + 1 == k) { /* inspect freq.size() and sum */ }
}
```

**Core Logic:**
The frequency map describes exactly the current `k` positions. The snippet is a reusable pattern skeleton, not a standalone LeetCode submission. Adapt its variables and guard conditions to the exact question constraints.

**Questions Covered:** LeetCode 1876, LeetCode 2461, LeetCode 2841

### LeetCode 1876

**Question:**

Given a lowercase string, count contiguous substrings of length exactly three in which all three characters are different. Two equal characters at different positions invalidate that substring. Return zero if the string is shorter than three.

**Examples:**

- `s="xyzzaz"` → `1` (`"xyz"`).
- `s="aababcabc"` → `4`.
- `s="aaa"` → `0`.

**Constraints:**

`1 ≤ s.length ≤ 100`; lowercase English letters.

**Why This Subpattern Applies:**

All candidates have width three and require exactly three distinct characters. Maintain a small 26-element frequency table and the count of entries with positive frequency.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int countGoodSubstrings(String s) {
        int[] freq = new int[26];
        int distinct = 0, answer = 0;
        for (int right = 0; right < s.length(); right++) {
            int in = s.charAt(right) - 'a';
            if (freq[in]++ == 0) distinct++;
            if (right >= 3) {
                int out = s.charAt(right - 3) - 'a';
                if (--freq[out] == 0) distinct--;
            }
            if (right >= 2 && distinct == 3) answer++;
        }
        return answer;
    }
}
```

**Key Logic:**

A frequency going 0→1 increases distinctness; 1→0 decreases it. The table contains exactly the most recent three characters when checking validity.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1) (26-element alphabet)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 2461

**Question:**

Given positive integer array `nums` and length `k`, consider each contiguous subarray of exactly `k` entries. A candidate is valid only if all its values are pairwise different. Return the maximum sum among valid candidates, or zero if none exist.

**Examples:**

- `nums=[1,5,4,2,9,9,9], k=3` → `15` (`[4,2,9]`).
- `nums=[4,4,4], k=3` → `0`.
- `nums=[1,2,3], k=1` → `3`.

**Constraints:**

`1 ≤ k ≤ nums.length ≤ 100000`; `1 ≤ nums[i] ≤ 100000`.

**Why This Subpattern Applies:**

A fixed-length candidate is valid iff its frequency map has exactly `k` keys; the score additionally needs a rolling sum.

**Connection to Previous Solution:**

Extend 1876's fixed frequency window from width 3 to width `k`; maintain `long sum` and update `best` only when `freq.size() == k`.

**Java Solution:**

```java
class Solution {
    public long maximumSubarraySum(int[] nums, int k) {
        java.util.Map<Integer,Integer> freq = new java.util.HashMap<>();
        long sum = 0, best = 0;
        for (int right = 0; right < nums.length; right++) {
            int value = nums[right];
            freq.put(value, freq.getOrDefault(value, 0) + 1);
            sum += value;
            if (right >= k) {
                int out = nums[right - k];
                sum -= out;
                int remain = freq.get(out) - 1;
                if (remain == 0) freq.remove(out);
                else freq.put(out, remain);
            }
            if (right >= k - 1 && freq.size() == k) best = Math.max(best, sum);
        }
        return best;
    }
}
```

**Key Logic:**

Map size is the count of distinct values in exactly the current width-`k` window; only fully distinct windows can update the sum optimum. The return type is `long`.

**Time Complexity:**
`O(n) expected`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(min(n, k)) auxiliary`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 2841

**Question:**

Given positive integer array `nums` and integers `m` and `k`, look at every contiguous subarray of exactly `k` entries. Among those with **at least `m` distinct values**, return the maximum sum. Return zero when there is no eligible subarray.

**Examples:**

- `nums=[2,6,7,3,1,7], m=3, k=4` → `18` (`[2,6,7,3]`).
- `nums=[5,5,5,5], m=2, k=3` → `0`.
- `nums=[1,2,1,2,3], m=2, k=3` → `6`.

**Constraints:**

`1 ≤ m ≤ k ≤ nums.length ≤ 20000`; `1 ≤ nums[i] ≤ 1000000000`.

**Why This Subpattern Applies:**

Distinctness is a lower bound rather than a requirement for all values to differ. The same fixed-width frequency map and running sum are sufficient.

**Connection to Previous Solution:**

Change only the admission predicate from 2461: `freq.size() == k` → `freq.size() >= m`; keep `long` sum and the same add/remove logic.

**Java Solution:**

```java
class Solution {
    public long maxSum(java.util.List<Integer> nums, int m, int k) {
        java.util.Map<Integer,Integer> freq = new java.util.HashMap<>();
        long sum = 0, best = 0;
        for (int right = 0; right < nums.size(); right++) {
            int value = nums.get(right);
            sum += value;
            freq.put(value, freq.getOrDefault(value, 0) + 1);
            if (right >= k) {
                int out = nums.get(right - k);
                sum -= out;
                int remain = freq.get(out) - 1;
                if (remain == 0) freq.remove(out);
                else freq.put(out, remain);
            }
            if (right >= k - 1 && freq.size() >= m) best = Math.max(best, sum);
        }
        return best;
    }
}
```

**Key Logic:**

LeetCode's method accepts `List<Integer>`, unlike 2461's array parameter. The exact same rolling frequency and sum invariant holds; 64-bit arithmetic is needed for large values.

**Time Complexity:**
`O(n) expected`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(min(n, k)) auxiliary`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

## W4 — Fixed-width multiset matching
<a id="w4-family"></a>

### Subpattern W4: Fixed-width multiset matching

**When to Apply:**
- **Recognition:** Order within a block is irrelevant but character or word multiplicities must match.
- **Central invariant:** Match full occurrence counts rather than sets.

**Basic Java Code Snippet:**

```java
int[] need = new int[26], have = new int[26];
for (char c : pattern.toCharArray()) need[c - 'a']++;
for (int r = 0; r < text.length(); r++) {
    have[text.charAt(r) - 'a']++;
    if (r >= pattern.length()) have[text.charAt(r - pattern.length()) - 'a']--;
    if (r >= pattern.length() - 1 && java.util.Arrays.equals(need, have)) {
        // matching character multiset
    }
}
```

**Core Logic:**
Match full occurrence counts rather than sets. The snippet is a reusable pattern skeleton, not a standalone LeetCode submission. Adapt its variables and guard conditions to the exact question constraints.

**Questions Covered:** LeetCode 438, LeetCode 567, LeetCode 30

### LeetCode 438

**Question:**

Given strings `s` and `p` consisting of lowercase English letters, return all zero-based starting indices where a substring of `s` is an anagram of `p`. An anagram has the same character multiplicities, in any order. Every matching substring must have length `p.length()` and matches may overlap.

**Examples:**

- `s="cbaebabacd", p="abc"` → `[0,6]`.
- `s="abab", p="ab"` → `[0,1,2]`.
- `s="abc", p="abcd"` → `[]`.

**Constraints:**

`1 ≤ s.length, p.length ≤ 30000`; lowercase English letters.

**Why This Subpattern Applies:**

Anagrams are equal multisets. A fixed-width frequency array captures the exact count of each letter in the window.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public java.util.List<Integer> findAnagrams(String s, String p) {
        java.util.List<Integer> result = new java.util.ArrayList<>();
        int k = p.length();
        if (k > s.length()) return result;
        int[] required = new int[26], window = new int[26];
        for (int i = 0; i < k; i++) required[p.charAt(i) - 'a']++;
        for (int right = 0; right < s.length(); right++) {
            window[s.charAt(right) - 'a']++;
            if (right >= k) window[s.charAt(right - k) - 'a']--;
            if (right >= k - 1 && java.util.Arrays.equals(required, window))
                result.add(right - k + 1);
        }
        return result;
    }
}
```

**Key Logic:**

The current window has exactly `k` positions once full. Equal 26-entry counters certify both character identities and multiplicities; each matching start is recorded.

**Time Complexity:**
`O(|s| + |p|) (alphabet size fixed at 26)`. Sorting or hashing costs are included in the stated bound.

**Space Complexity:**
`O(1) auxiliary; O(number of matches) output`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 567

**Question:**

Given lowercase strings `s1` and `s2`, return whether **any** contiguous substring of `s2` is a permutation of `s1`. The substring must use every character of `s1` exactly as many times as it occurs in `s1`, regardless of order.

**Examples:**

- `s1="ab", s2="eidbaooo"` → `true` (`"ba"`).
- `s1="ab", s2="eidboaoo"` → `false`.
- `s1="aab", s2="xxaba"` → `true`.

**Constraints:**

`1 ≤ s1.length, s2.length ≤ 10000`; lowercase English letters.

**Why This Subpattern Applies:**

A substring is a permutation precisely when its fixed-width character-count vector matches the pattern vector.

**Connection to Previous Solution:**

Retain the identical 26-counter rolling window from 438, but replace appending every match with returning `true` immediately; return `false` if scanning ends.

**Java Solution:**

```java
class Solution {
    public boolean checkInclusion(String s1, String s2) {
        int k = s1.length();
        if (k > s2.length()) return false;
        int[] need = new int[26], window = new int[26];
        for (int i = 0; i < k; i++) need[s1.charAt(i) - 'a']++;
        for (int right = 0; right < s2.length(); right++) {
            window[s2.charAt(right) - 'a']++;
            if (right >= k) window[s2.charAt(right - k) - 'a']--;
            if (right >= k - 1 && java.util.Arrays.equals(need, window)) return true;
        }
        return false;
    }
}
```

**Key Logic:**

Check only after the full width has been reached. The first equal multiset is a complete witness; there is no need to store indices.

**Time Complexity:**
`O(|s1| + |s2|) (26 fixed)`. Sorting or hashing costs are included in the stated bound.

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 30

**Question:**

Given string `s` and an array `words`, all words have the same nonzero length. Find all starting indices where a contiguous substring is formed by concatenating **every word in `words` exactly once**, in any ordering, without gaps or extra characters. Some words may be repeated, and their required multiplicities must be respected.

**Examples:**

- `s="barfoothefoobarman", words=["foo","bar"]` → `[0,9]`.
- `s="wordgoodgoodgoodbestword", words=["word","good","best","good"]` → `[8]`.
- `s="barfoofoobarthefoobarman", words=["bar","foo","the"]` → `[6,9,12]`.

**Constraints:**

`1 ≤ s.length ≤ 10000`; `1 ≤ words.length ≤ 5000`; `1 ≤ words[i].length ≤ 30`; lowercase English letters; all words equal length.

**Why This Subpattern Applies:**

The matching unit is a whole word rather than one letter, and the required multiset contains entire word tokens. Scanning each residue modulo word length preserves token alignment.

**Connection to Previous Solution:**

Generalize 567's character-frequency multiset to a word-frequency map, move pointers by word length, and shrink repeatedly when a word is overrepresented; return all matching starts instead of a boolean.

**Java Solution:**

```java
class Solution {
    public java.util.List<Integer> findSubstring(String s, String[] words) {
        java.util.List<Integer> result = new java.util.ArrayList<>();
        int width = words[0].length(), targetCount = words.length;
        int neededLength = width * targetCount;
        if (neededLength > s.length()) return result;
        java.util.Map<String,Integer> need = new java.util.HashMap<>();
        for (String word : words) need.merge(word, 1, Integer::sum);
        for (int offset = 0; offset < width; offset++) {
            java.util.Map<String,Integer> window = new java.util.HashMap<>();
            int left = offset, count = 0;
            for (int right = offset; right + width <= s.length(); right += width) {
                String word = s.substring(right, right + width);
                if (!need.containsKey(word)) {
                    window.clear(); count = 0; left = right + width;
                    continue;
                }
                window.merge(word, 1, Integer::sum);
                count++;
                while (window.get(word) > need.get(word)) {
                    String out = s.substring(left, left + width);
                    window.put(out, window.get(out) - 1);
                    left += width; count--;
                }
                if (count == targetCount) {
                    result.add(left);
                    String out = s.substring(left, left + width);
                    window.put(out, window.get(out) - 1);
                    left += width; count--;
                }
            }
        }
        return result;
    }
}
```

**Key Logic:**

Each offset defines an aligned sequence of width-sized tokens. Unknown tokens reset the window; excess copies evict oldest tokens. Exactly `words.length` accepted tokens means the current multiset equals the target. Output order need not be sorted.

**Time Complexity:**
`O(|s|·w + total word chars) expected with Java substring/hash costs, where w is word length`. Sorting or hashing costs are included in the stated bound.

**Space Complexity:**
`O(u·w + u) auxiliary for u distinct words and stored key characters; O(matches) output`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

## W5 — Variable window with distinctness bound
<a id="w5-family"></a>

### Subpattern W5: Variable window with distinctness bound

**When to Apply:**
- **Recognition:** Find the longest contiguous segment with no duplicates or at most D distinct types.
- **Central invariant:** Shrink from the left until the frequency-based validity condition holds.

**Basic Java Code Snippet:**

```java
java.util.Map<Integer,Integer> freq = new java.util.HashMap<>();
int left = 0, best = 0;
for (int right = 0; right < a.length; right++) {
    freq.merge(a[right], 1, Integer::sum);
    while (freq.size() > distinctLimit) {
        int old = a[left++];
        if (freq.merge(old, -1, Integer::sum) == 0) freq.remove(old);
    }
    best = Math.max(best, right - left + 1);
}
// For no duplicates, violation is any frequency>1, not freq.size()>limit.
```

**Core Logic:**
Shrink from the left until the frequency-based validity condition holds. The snippet is a reusable pattern skeleton, not a standalone LeetCode submission. Adapt its variables and guard conditions to the exact question constraints.

**Questions Covered:** LeetCode 3, LeetCode 1695, LeetCode 904

### LeetCode 3

**Question:**

Given a string `s`, find the length of its longest contiguous substring in which no character appears twice. The substring may be empty only when the input is empty. Character order must remain unchanged.

**Examples:**

- `s="abcabcbb"` → `3` (`"abc"`).
- `s="bbbbb"` → `1`.
- `s="pwwkew"` → `3` (`"wke"`).

**Constraints:**

`0 ≤ s.length ≤ 50000`; English letters, digits, symbols, spaces.

**Why This Subpattern Applies:**

Appending a character can create one duplicate. Move the left endpoint until the new character has frequency one, preserving a unique-character window.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int[] freq = new int[128];
        int left = 0, best = 0;
        for (int right = 0; right < s.length(); right++) {
            char added = s.charAt(right);
            freq[added]++;
            while (freq[added] > 1) freq[s.charAt(left++)]--;
            best = Math.max(best, right - left + 1);
        }
        return best;
    }
}
```

**Key Logic:**

After each shrink, every character is unique. Only the just-added character can be duplicated since the preceding window was already valid. The ASCII frequency table matches the stated character set.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1) (fixed ASCII table)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 1695

**Question:**

Given an array of positive integers, select a nonempty contiguous subarray whose values are **all distinct**. Its score is the sum of its entries. Return the largest possible score. You may not reorder elements or omit values from inside the chosen interval.

**Examples:**

- `nums=[4,2,4,5,6]` → `17` (`[2,4,5,6]`).
- `nums=[5,2,1,2,5,2,1,2,5]` → `8`.
- `nums=[7,7,7]` → `7`.

**Constraints:**

`1 ≤ nums.length ≤ 100000`; `1 ≤ nums[i] ≤ 10000`.

**Why This Subpattern Applies:**

The validity invariant is identical to 3 (no duplicates), but the score depends on numeric values, not window length. Positive numbers make the largest valid sum achievable in a maximal valid window.

**Connection to Previous Solution:**

Keep 3's duplicate-expulsion loop; replace the length score with `long/int sum` updated on every right-add and left-remove, then maximize the score.

**Java Solution:**

```java
class Solution {
    public int maximumUniqueSubarray(int[] nums) {
        boolean[] seen = new boolean[10001];
        int left = 0, sum = 0, best = 0;
        for (int right = 0; right < nums.length; right++) {
            int value = nums[right];
            while (seen[value]) {
                seen[nums[left]] = false;
                sum -= nums[left++];
            }
            seen[value] = true;
            sum += value;
            best = Math.max(best, sum);
        }
        return best;
    }
}
```

**Key Logic:**

Since the incoming value is the only possible duplicate, evict until it is absent. The boolean lookup works under the bounded value constraint and avoids hashing overhead. Sum is updated symmetrically.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1) (bounded value array)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 904

**Question:**

Given `fruits`, where `fruits[i]` is the fruit type at tree `i`, choose one contiguous interval of trees and collect one fruit from every tree in it. You have two baskets and each basket may contain only a single fruit type. Return the maximum number of fruits collected; therefore the chosen interval may contain at most two distinct types.

**Examples:**

- `fruits=[1,2,1]` → `3`.
- `fruits=[0,1,2,2]` → `3`.
- `fruits=[1,2,3,2,2]` → `4`.

**Constraints:**

`1 ≤ fruits.length ≤ 100000`; `0 ≤ fruits[i] < fruits.length`.

**Why This Subpattern Applies:**

Validity depends on the **number of different types** in the window, not on duplicate occurrences. Shrink when the map has more than two nonzero keys.

**Connection to Previous Solution:**

Change 1695's rejection of any duplicate to `freq.size() > 2`; maintain multiplicities so a type disappears only after its last copy leaves.

**Java Solution:**

```java
class Solution {
    public int totalFruit(int[] fruits) {
        java.util.Map<Integer,Integer> freq = new java.util.HashMap<>();
        int left = 0, best = 0;
        for (int right = 0; right < fruits.length; right++) {
            int value = fruits[right];
            freq.put(value, freq.getOrDefault(value, 0) + 1);
            while (freq.size() > 2) {
                int out = fruits[left++];
                int remaining = freq.get(out) - 1;
                if (remaining == 0) freq.remove(out);
                else freq.put(out, remaining);
            }
            best = Math.max(best, right - left + 1);
        }
        return best;
    }
}
```

**Key Logic:**

The frequency map tracks active types in `[left,right]`. A third type invalidates the window, so expel whole old occurrences until only two types remain.

**Time Complexity:**
`O(n) expected`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1) active map (at most 3 types)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

## W6 — Variable window with replacement/deletion budget
<a id="w6-family"></a>

### Subpattern W6: Variable window with replacement/deletion budget

**When to Apply:**
- **Recognition:** You can repair a segment using at most `k` changes, flips, or a mandatory deletion.
- **Central invariant:** Window repair cost remains at most the stated budget.

**Basic Java Code Snippet:**

```java
int left = 0, bad = 0, best = 0;
for (int right = 0; right < s.length(); right++) {
    if (s.charAt(right) != target) bad++;
    while (bad > k) if (s.charAt(left++) != target) bad--;
    best = Math.max(best, right - left + 1);
}
```

**Core Logic:**
Window repair cost remains at most the stated budget. The snippet is a reusable pattern skeleton, not a standalone LeetCode submission. Adapt its variables and guard conditions to the exact question constraints.

**Questions Covered:** LeetCode 1004, LeetCode 1493, LeetCode 2024, LeetCode 424

### LeetCode 1004

**Question:**

Given a binary array `nums` and integer `k`, you may turn at most `k` zeros into ones. Return the maximum length of a contiguous run of ones obtainable after those flips. A window is feasible if it contains no more than `k` zeros.

**Examples:**

- `nums=[1,1,1,0,0,0,1,1,1,1,0], k=2` → `6`.
- `nums=[0,0,1,1,1,0,0], k=0` → `3`.
- `nums=[0,0,0], k=3` → `3`.

**Constraints:**

`1 ≤ nums.length ≤ 100000`; `nums[i] ∈ {0,1}`; `0 ≤ k ≤ nums.length`.

**Why This Subpattern Applies:**

The number of zeroes is exactly the number of repairs required. Expand right and shrink only if the zero budget is exceeded.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int longestOnes(int[] nums, int k) {
        int left = 0, zeros = 0, best = 0;
        for (int right = 0; right < nums.length; right++) {
            if (nums[right] == 0) zeros++;
            while (zeros > k) {
                if (nums[left++] == 0) zeros--;
            }
            best = Math.max(best, right - left + 1);
        }
        return best;
    }
}
```

**Key Logic:**

`zeros` counts exactly the current window's invalid entries. After each iteration, every element could be made 1 within the available number of flips.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 1493

**Question:**

Given a nonempty binary array, delete **exactly one** array element, then return the maximum length of a nonempty-or-empty contiguous subarray consisting entirely of ones in the remaining sequence. If deleting the only available element leaves no ones, return zero. Deleting a zero may join two runs of ones; if the input is all ones, you must still delete one one.

**Examples:**

- `nums=[1,1,0,1]` → `3`.
- `nums=[0,1,1,1,0,1,1,0,1]` → `5`.
- `nums=[1,1,1]` → `2`.

**Constraints:**

`1 ≤ nums.length ≤ 100000`; `nums[i] ∈ {0,1}`.

**Why This Subpattern Applies:**

A valid original window may contain at most one zero, because deleting one element can remove that obstacle. The deletion is compulsory, even for all-ones arrays.

**Connection to Previous Solution:**

Reuse 1004's zero-count window with `k = 1`, but update `best` using `right-left` instead of `right-left+1` to subtract one mandatory deleted element.

**Java Solution:**

```java
class Solution {
    public int longestSubarray(int[] nums) {
        int left = 0, zeros = 0, best = 0;
        for (int right = 0; right < nums.length; right++) {
            if (nums[right] == 0) zeros++;
            while (zeros > 1) {
                if (nums[left++] == 0) zeros--;
            }
            best = Math.max(best, right - left);
        }
        return best;
    }
}
```

**Key Logic:**

At most one zero remains in the window. Removing exactly one value leaves `windowLength - 1` possible adjacent ones; this also handles a length-one input and the all-ones case.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 2024

**Question:**

You are given a string `answerKey` consisting only of `T` and `F`, and integer `k`. You can change at most `k` characters to the opposite letter. Return the maximum length of a contiguous substring that can be made all `T` or all `F`. Choose whichever target gives the longer result.

**Examples:**

- `answerKey="TTFF", k=2` → `4`.
- `answerKey="TFFT", k=1` → `3`.
- `answerKey="TTFTTFTT", k=1` → `5`.

**Constraints:**

`1 ≤ answerKey.length ≤ 50000`; letters only `T`/`F`; `1 ≤ k ≤ answerKey.length`.

**Why This Subpattern Applies:**

For a fixed target letter, each unlike character needs one replacement. A sliding budget works independently for both possible target letters.

**Connection to Previous Solution:**

Generalize 1493's bad symbol `0` to `s.charAt(right) != target`; call the same maximum-valid-window helper once for `T` and once for `F`, without mandatory deletion adjustment.

**Java Solution:**

```java
class Solution {
    public int maxConsecutiveAnswers(String answerKey, int k) {
        return Math.max(longest(answerKey, k, 'T'), longest(answerKey, k, 'F'));
    }
    private int longest(String s, int k, char target) {
        int left = 0, changes = 0, best = 0;
        for (int right = 0; right < s.length(); right++) {
            if (s.charAt(right) != target) changes++;
            while (changes > k) {
                if (s.charAt(left++) != target) changes--;
            }
            best = Math.max(best, right - left + 1);
        }
        return best;
    }
}
```

**Key Logic:**

`changes` is the number of off-target characters requiring a flip. Since there are only two possible final letters, the maximum of two scans is optimal.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 424

**Question:**

Given an uppercase English string `s` and integer `k`, replace at most `k` individual characters with any uppercase English letters. Find the longest contiguous substring that can be made to contain just one repeated letter after these replacements. You may choose the final repeated letter independently for each candidate substring.

**Examples:**

- `s="ABAB", k=2` → `4`.
- `s="AABABBA", k=1` → `4`.
- `s="AAAA", k=0` → `4`.

**Constraints:**

`1 ≤ s.length ≤ 100000`; uppercase English letters; `0 ≤ k ≤ s.length`.

**Why This Subpattern Applies:**

The cheapest way to equalize a window is to keep its most common letter and change all others; required edits are `windowLength - maxFrequency`.

**Connection to Previous Solution:**

Replace 2024's two scans for fixed targets with one 26-letter frequency array and a nondecreasing historical `maxFreq`; shrink while `windowLength - maxFreq > k`.

**Java Solution:**

```java
class Solution {
    public int characterReplacement(String s, int k) {
        int[] freq = new int[26];
        int left = 0, best = 0, maxFreq = 0;
        for (int right = 0; right < s.length(); right++) {
            int index = s.charAt(right) - 'A';
            maxFreq = Math.max(maxFreq, ++freq[index]);
            while (right - left + 1 - maxFreq > k) {
                freq[s.charAt(left++) - 'A']--;
            }
            best = Math.max(best, right - left + 1);
        }
        return best;
    }
}
```

**Key Logic:**

The historical `maxFreq` need not decrease on left eviction: it may temporarily overestimate the current mode, but the maximum reported length is still achievable from a window where that frequency was attained. This yields O(n) without rescanning 26 counts.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

## W7 — Shortest feasible window with quantitative / coverage constraints
<a id="w7-family"></a>

### Subpattern W7: Shortest feasible window with quantitative / coverage constraints

**When to Apply:**
- **Recognition:** Find the shortest segment meeting a minimum positive sum or a frequency-cover requirement.
- **Central invariant:** Shrink while the coverage condition is still satisfied.

**Basic Java Code Snippet:**

```java
int left = 0, best = Integer.MAX_VALUE;
long sum = 0;
for (int right = 0; right < a.length; right++) {
    sum += a[right];
    while (sum >= target) { // replace with multiset-cover predicate
        best = Math.min(best, right - left + 1);
        sum -= a[left++];
    }
}
// Sum version requires positive elements.
```

**Core Logic:**
Shrink while the coverage condition is still satisfied. The snippet is a reusable pattern skeleton, not a standalone LeetCode submission. Adapt its variables and guard conditions to the exact question constraints.

**Questions Covered:** LeetCode 209, LeetCode 76, LeetCode 1234

### LeetCode 209

**Question:**

Given a positive integer `target` and an array of positive integers `nums`, find the smallest number of consecutive elements whose sum is at least `target`. If no nonempty contiguous subarray reaches the target, return zero. You must not reorder or skip internal elements.

**Examples:**

- `target=7, nums=[2,3,1,2,4,3]` → `2` (`[4,3]`).
- `target=4, nums=[1,4,4]` → `1`.
- `target=11, nums=[1,1,1,1,1,1,1,1]` → `0`.

**Constraints:**

`1 ≤ target ≤ 1000000000`; `1 ≤ nums.length ≤ 100000`; `1 ≤ nums[i] ≤ 10000`.

**Why This Subpattern Applies:**

All values are positive: adding right can only increase the sum and removing left can only decrease it. This monotonicity makes minimum-length feasible windows discoverable by contracting whenever the target is met.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int left = 0, best = Integer.MAX_VALUE;
        long sum = 0;
        for (int right = 0; right < nums.length; right++) {
            sum += nums[right];
            while (sum >= target) {
                best = Math.min(best, right - left + 1);
                sum -= nums[left++];
            }
        }
        return best == Integer.MAX_VALUE ? 0 : best;
    }
}
```

**Key Logic:**

Unlike longest-valid windows, this method shrinks while feasible and records each feasible length first. The positive-number constraint is essential; negative numbers would invalidate this simple monotone scheme.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 76

**Question:**

Given strings `s` and `t`, return the shortest contiguous substring of `s` containing every character of `t` with at least the multiplicity in `t`. Extra characters are allowed. Return the empty string if no covering window exists. If several minimum windows exist, the problem guarantees that the answer is unique.

**Examples:**

- `s="ADOBECODEBANC", t="ABC"` → `"BANC"`.
- `s="a", t="a"` → `"a"`.
- `s="a", t="aa"` → `""`.

**Constraints:**

`1 ≤ s.length, t.length ≤ 100000`; uppercase and lowercase English letters.

**Why This Subpattern Applies:**

The condition is a minimum coverage threshold for each required character, like a multidimensional version of the positive sum threshold in 209. Once the window covers all required occurrences, shrink to minimize it.

**Connection to Previous Solution:**

Replace 209's scalar `sum >= target` predicate with a frequency-demand array and an integer `missing` count; return the minimum substring boundaries instead of its length.

**Java Solution:**

```java
class Solution {
    public String minWindow(String s, String t) {
        int[] need = new int[128];
        for (int i = 0; i < t.length(); i++) need[t.charAt(i)]++;
        int missing = t.length(), left = 0, start = 0, best = Integer.MAX_VALUE;
        for (int right = 0; right < s.length(); right++) {
            if (need[s.charAt(right)]-- > 0) missing--;
            while (missing == 0) {
                int length = right - left + 1;
                if (length < best) { best = length; start = left; }
                if (++need[s.charAt(left++)] > 0) missing++;
            }
        }
        return best == Integer.MAX_VALUE ? "" : s.substring(start, start + best);
    }
}
```

**Key Logic:**

`need[c]` may be negative when the window contains excess copies. `missing` decrements only when an incoming char fills an unmet unit and increases when removing a necessary unit. Lowercase and uppercase are distinct indices.

**Time Complexity:**
`O(|s| + |t|)`. Sorting or hashing costs are included in the stated bound.

**Space Complexity:**
`O(1) (fixed ASCII array)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 1234

**Question:**

Given a string `s` of length divisible by four, containing only `Q`, `W`, `E`, and `R`, replace **one contiguous substring** with an arbitrary string of the same length (possibly length zero). Find the minimum length of a substring whose replacement can make the entire string balanced, meaning each of the four characters appears exactly `n/4` times.

**Examples:**

- `s="QWER"` → `0` (already balanced).
- `s="QQWE"` → `1`.
- `s="QQQQ"` → `3`.

**Constraints:**

`4 ≤ s.length ≤ 100000`; `s.length` divisible by 4; only `Q`, `W`, `E`, `R`.

**Why This Subpattern Applies:**

All counts above `n/4` must be captured inside the replaced window. Equivalently, the characters *outside* the window must each occur at most `n/4` times; contract while this is true.

**Connection to Previous Solution:**

Turn 76's externally supplied multiset target into excess requirements derived from global character counts; test outside frequencies instead of a `missing` counter.

**Java Solution:**

```java
class Solution {
    public int balancedString(String s) {
        int n = s.length(), target = n / 4;
        int[] outside = new int[128];
        for (int i = 0; i < n; i++) outside[s.charAt(i)]++;
        if (valid(outside, target)) return 0;
        int left = 0, best = n;
        for (int right = 0; right < n; right++) {
            outside[s.charAt(right)]--;
            while (left <= right && valid(outside, target)) {
                best = Math.min(best, right - left + 1);
                outside[s.charAt(left++)]++;
            }
        }
        return best;
    }
    private boolean valid(int[] cnt, int limit) {
        return cnt['Q'] <= limit && cnt['W'] <= limit &&
               cnt['E'] <= limit && cnt['R'] <= limit;
    }
}
```

**Key Logic:**

`outside` deliberately counts the complement of the candidate replacement window. If every outside count fits the target, the replacement can supply exactly the missing characters. Check zero length before starting.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

## W8 — Counting windows via monotone numeric constraints
<a id="w8-family"></a>

### Subpattern W8: Counting windows via monotone numeric constraints

**When to Apply:**
- **Recognition:** Count contiguous segments satisfying a monotone numeric score bound or minimum pair count.
- **Central invariant:** Count all valid starts ending at each right endpoint by maintaining a boundary.

**Basic Java Code Snippet:**

```java
int left = 0;
long state = 0, count = 0;
for (int right = 0; right < a.length; right++) {
    state += a[right];
    while (left <= right && state > budget) state -= a[left++];
    count += right - left + 1L;
}
// Replace additive state with product, length-weighted score, or pair counts.
```

**Core Logic:**
Count all valid starts ending at each right endpoint by maintaining a boundary. The snippet is a reusable pattern skeleton, not a standalone LeetCode submission. Adapt its variables and guard conditions to the exact question constraints.

**Questions Covered:** LeetCode 713, LeetCode 2302, LeetCode 2537

### LeetCode 713

**Question:**

Given an integer array of strictly positive numbers and integer `k`, count all nonempty contiguous subarrays whose product is **strictly less** than `k`. Count each start/end position separately. If `k ≤ 1`, no positive-product subarray can qualify.

**Examples:**

- `nums=[10,5,2,6], k=100` → `8`.
- `nums=[1,2,3], k=0` → `0`.
- `nums=[1,1,1], k=2` → `6`.

**Constraints:**

`1 ≤ nums.length ≤ 30000`; `1 ≤ nums[i] ≤ 1000`; `0 ≤ k ≤ 1000000`.

**Why This Subpattern Applies:**

With positive factors, removing an element cannot increase a product when all factors are at least one. After shrinking to `product < k`, every suffix of the window ending at `right` also qualifies.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int numSubarrayProductLessThanK(int[] nums, int k) {
        if (k <= 1) return 0;
        long product = 1;
        int left = 0, count = 0;
        for (int right = 0; right < nums.length; right++) {
            product *= nums[right];
            while (product >= k) product /= nums[left++];
            count += right - left + 1;
        }
        return count;
    }
}
```

**Key Logic:**

Shrink until the current product is below `k`; then every start from `left` through `right` gives a valid subarray. The product is bounded by `1000 * (k-1)` immediately after adding, safe in `long`.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 2302

**Question:**

Given an array `nums` of positive integers and a positive `long` threshold `k`, define a subarray's score as `(sum of elements) × (number of elements)`. Count the nonempty contiguous subarrays whose score is **strictly less** than `k`. An equal score does not count.

**Examples:**

- `nums=[2,1,4,3,5], k=10` → `6`.
- `nums=[1,1,1], k=5` → `5`.
- `nums=[5], k=5` → `0`.

**Constraints:**

`1 ≤ nums.length ≤ 100000`; `1 ≤ nums[i] ≤ 100000`; `1 ≤ k ≤ 10^15`.

**Why This Subpattern Applies:**

Because every entry is positive, removing the leftmost element lowers both sum and length, so the score is monotone under contraction. All suffixes of a valid window qualify.

**Connection to Previous Solution:**

Replace 713's multiplicative `product` with additive `sum`; change the rejection test to `sum * (right-left+1L) >= k`, retaining the same suffix-count update.

**Java Solution:**

```java
class Solution {
    public long countSubarrays(int[] nums, long k) {
        long sum = 0, count = 0;
        int left = 0;
        for (int right = 0; right < nums.length; right++) {
            sum += nums[right];
            while (left <= right && sum * (right - left + 1L) >= k)
                sum -= nums[left++];
            count += right - left + 1L;
        }
        return count;
    }
}
```

**Key Logic:**

The score test multiplies two `long`-compatible quantities. After contracting to a valid maximum-width ending interval, all its suffixes have lower or equal score; accumulate their count in `long`.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 2537

**Question:**

Given an integer array `nums` and integer `k`, a pair of positions `(i,j)` inside a subarray is equal if `i < j` and the two values are equal. Count contiguous subarrays containing **at least `k`** such pairs. Every pair of equal-position indices is counted, including several pairs contributed by a repeated value.

**Examples:**

- `nums=[1,1,1,1,1], k=10` → `1`.
- `nums=[3,1,4,3,2,2,4], k=2` → `4`.
- `nums=[1,2,3], k=1` → `0`.

**Constraints:**

`1 ≤ nums.length ≤ 100000`; `1 ≤ nums[i] ≤ 1000000000`; `1 ≤ k ≤ 10^9`.

**Why This Subpattern Applies:**

Pair count is monotone under window expansion and cannot increase when evicting from the left. Once the current window reaches `k` pairs, all starts earlier than its left boundary also satisfy the threshold.

**Connection to Previous Solution:**

Replace 2302's numeric score with an equal-pair count: on insertion add `freq[value]`, and on removal subtract the new reduced frequency; invert the shrink condition to `pairs >= k` and count valid earlier starts.

**Java Solution:**

```java
class Solution {
    public long countGood(int[] nums, int k) {
        java.util.Map<Integer,Integer> freq = new java.util.HashMap<>();
        long pairs = 0, answer = 0;
        int left = 0;
        for (int right = 0; right < nums.length; right++) {
            int value = nums[right];
            int old = freq.getOrDefault(value, 0);
            pairs += old;
            freq.put(value, old + 1);
            while (pairs >= k) {
                int out = nums[left++];
                int remaining = freq.get(out) - 1;
                pairs -= remaining;
                if (remaining == 0) freq.remove(out);
                else freq.put(out, remaining);
            }
            answer += left;
        }
        return answer;
    }
}
```

**Key Logic:**

Adding a value with `f` previous matches creates `f` new pairs; removing it with `f-1` remaining copies destroys `f-1` pairs. After shrinking past every qualifying start, exactly `left` starts (`0..left-1`) form good windows ending at `right`.

**Time Complexity:**
`O(n) expected`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(n) auxiliary`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

## W9 — Exact-category counts via at-most difference
<a id="w9-family"></a>

### Subpattern W9: Exact-category counts via at-most difference

**When to Apply:**
- **Recognition:** An exact nonnegative count is difficult to maintain directly; counting at most a limit is easier.
- **Central invariant:** exact(K) = atMost(K) − atMost(K−1); a complementary count can be equivalent.

**Basic Java Code Snippet:**

```java
static long atMost(int[] a, int k) {
    if (k < 0) return 0;
    int left = 0, sum = 0;
    long count = 0;
    for (int right = 0; right < a.length; right++) {
        sum += a[right]; // nonnegative contributions only
        while (sum > k) sum -= a[left++];
        count += right - left + 1L;
    }
    return count;
}
// exactK = atMost(a,k) - atMost(a,k-1).
```

**Core Logic:**
exact(K) = atMost(K) − atMost(K−1); a complementary count can be equivalent. The snippet is a reusable pattern skeleton, not a standalone LeetCode submission. Adapt its variables and guard conditions to the exact question constraints.

**Questions Covered:** LeetCode 930, LeetCode 1248, LeetCode 992, LeetCode 2799

### LeetCode 930

**Question:**

Given a binary array `nums` and a nonnegative integer `goal`, count nonempty contiguous subarrays whose entries sum to **exactly** `goal`. Subarrays with identical values but different positions count separately. Zeros may make many subarrays share the same sum.

**Examples:**

- `nums=[1,0,1,0,1], goal=2` → `4`.
- `nums=[0,0,0,0,0], goal=0` → `15`.
- `nums=[1,1,1], goal=1` → `3`.

**Constraints:**

`1 ≤ nums.length ≤ 30000`; `nums[i] ∈ {0,1}`; `0 ≤ goal ≤ nums.length`.

**Why This Subpattern Applies:**

Because entries are nonnegative, the number of subarrays with sum at most a bound is easily counted using a shrinking window. Taking the difference between at-most bounds isolates exact sums.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int numSubarraysWithSum(int[] nums, int goal) {
        return atMost(nums, goal) - atMost(nums, goal - 1);
    }
    private int atMost(int[] nums, int limit) {
        if (limit < 0) return 0;
        int left = 0, sum = 0, answer = 0;
        for (int right = 0; right < nums.length; right++) {
            sum += nums[right];
            while (sum > limit) sum -= nums[left++];
            answer += right - left + 1;
        }
        return answer;
    }
}
```

**Key Logic:**

For each right endpoint, all starts in `[left,right]` give sums ≤ limit. Subtract counts for `< goal` from counts for `≤ goal`. Guard negative limits to handle `goal=0`.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 1248

**Question:**

Given an integer array `nums` and positive integer `k`, count contiguous subarrays containing **exactly `k` odd values**. Even values may appear anywhere in a qualifying subarray and contribute zero to the odd count.

**Examples:**

- `nums=[1,1,2,1,1], k=3` → `2`.
- `nums=[2,4,6], k=1` → `0`.
- `nums=[2,2,2,1,2,2], k=1` → `12`.

**Constraints:**

`1 ≤ nums.length ≤ 50000`; `1 ≤ nums[i] ≤ 100000`; `1 ≤ k ≤ nums.length`.

**Why This Subpattern Applies:**

Oddness is a nonnegative per-element contribution (one for odd and zero for even). Hence exactly-K counting uses the same at-most difference as for binary sums.

**Connection to Previous Solution:**

In 930's `atMost`, change `sum += nums[right]` and corresponding removal to `sum += (nums[right] & 1)` and `sum -= (nums[left++] & 1)`; keep the subtraction formula.

**Java Solution:**

```java
class Solution {
    public int numberOfSubarrays(int[] nums, int k) {
        return atMost(nums, k) - atMost(nums, k - 1);
    }
    private int atMost(int[] nums, int limit) {
        if (limit < 0) return 0;
        int left = 0, odds = 0, count = 0;
        for (int right = 0; right < nums.length; right++) {
            odds += nums[right] & 1;
            while (odds > limit) odds -= nums[left++] & 1;
            count += right - left + 1;
        }
        return count;
    }
}
```

**Key Logic:**

The window budget counts odd positions rather than raw values. Even numbers can extend the window without using the budget, which the suffix counting automatically handles.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 992

**Question:**

Given an integer array `nums` and integer `k`, count contiguous subarrays containing **exactly `k` distinct integer values**. Repeated occurrences of an existing value do not increase the distinct count. Overlapping subarrays are counted independently.

**Examples:**

- `nums=[1,2,1,2,3], k=2` → `7`.
- `nums=[1,2,1,3,4], k=3` → `3`.
- `nums=[1,1,1], k=1` → `6`.

**Constraints:**

`1 ≤ k ≤ nums.length ≤ 20000`; `1 ≤ nums[i] ≤ nums.length`.

**Why This Subpattern Applies:**

Distinctness is monotone under adding elements, and shrinking cannot introduce a new distinct value. Therefore at-most-K subarray counts can again be differenced to obtain exactly K.

**Connection to Previous Solution:**

Replace 1248's additive odd counter with a frequency map where distinctness increments on `0→1` and decrements on `1→0`. Keep `atMost(k)−atMost(k−1)`.

**Java Solution:**

```java
class Solution {
    public int subarraysWithKDistinct(int[] nums, int k) {
        return atMost(nums, k) - atMost(nums, k - 1);
    }
    private int atMost(int[] nums, int limit) {
        if (limit < 0) return 0;
        int[] freq = new int[nums.length + 1];
        int left = 0, distinct = 0, total = 0;
        for (int right = 0; right < nums.length; right++) {
            if (freq[nums[right]]++ == 0) distinct++;
            while (distinct > limit) {
                if (--freq[nums[left++]] == 0) distinct--;
            }
            total += right - left + 1;
        }
        return total;
    }
}
```

**Key Logic:**

The bounded values allow an indexed frequency array instead of hashing. `distinct` tracks positive frequency entries; all suffixes of the valid current window have at most the requested number of types.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(n) auxiliary`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 2799

**Question:**

Given a positive integer array `nums`, a contiguous subarray is called complete if it contains **every distinct value appearing anywhere in the full array** at least once. Return the total number of complete subarrays. Repeated entries are permitted; the subarray need not contain every occurrence.

**Examples:**

- `nums=[1,3,1,2,2]` → `4`.
- `nums=[5,5,5,5]` → `10`.
- `nums=[1,2,3]` → `1`.

**Constraints:**

`1 ≤ nums.length ≤ 1000`; `1 ≤ nums[i] ≤ 2000`.

**Why This Subpattern Applies:**

Let D be the globally distinct count. A complete subarray has at least D distinct elements, so it is the complement of the subarrays with at most D−1 distinct values.

**Connection to Previous Solution:**

Reuse 992's `atMostDistinct` frequency window but compute D once and return `n(n+1)/2 − atMost(D−1)` instead of `atMost(D)−atMost(D−1)`.

**Java Solution:**

```java
class Solution {
    public int countCompleteSubarrays(int[] nums) {
        java.util.Set<Integer> values = new java.util.HashSet<>();
        for (int v : nums) values.add(v);
        int n = nums.length;
        return n * (n + 1) / 2 - atMost(nums, values.size() - 1);
    }
    private int atMost(int[] nums, int limit) {
        int[] freq = new int[2001];
        int left = 0, distinct = 0, answer = 0;
        for (int right = 0; right < nums.length; right++) {
            if (freq[nums[right]]++ == 0) distinct++;
            while (distinct > limit) {
                if (--freq[nums[left++]] == 0) distinct--;
            }
            answer += right - left + 1;
        }
        return answer;
    }
}
```

**Key Logic:**

Every subarray uses only the global D values. Thus a subarray is complete iff it is not counted by the D−1 upper bound. The official n≤1000 keeps the result within int.

**Time Complexity:**
`O(n) expected`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(n) auxiliary including the global set`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

## W10 — Bounded spread using monotonic deques
<a id="w10-family"></a>

### Subpattern W10: Bounded spread using monotonic deques

**When to Apply:**
- **Recognition:** Validity depends on the maximum minus minimum inside a variable window.
- **Central invariant:** Two index deques track min and max while discarded indices expire.

**Basic Java Code Snippet:**

```java
java.util.ArrayDeque<Integer> minQ = new java.util.ArrayDeque<>();
java.util.ArrayDeque<Integer> maxQ = new java.util.ArrayDeque<>();
int left = 0;
for (int right = 0; right < a.length; right++) {
    while (!minQ.isEmpty() && a[minQ.peekLast()] > a[right]) minQ.pollLast();
    while (!maxQ.isEmpty() && a[maxQ.peekLast()] < a[right]) maxQ.pollLast();
    minQ.addLast(right); maxQ.addLast(right);
    while ((long)a[maxQ.peekFirst()] - a[minQ.peekFirst()] > limit) {
        if (minQ.peekFirst() == left) minQ.pollFirst();
        if (maxQ.peekFirst() == left) maxQ.pollFirst();
        left++;
    }
    // current [left,right] is valid
}
```

**Core Logic:**
Two index deques track min and max while discarded indices expire. The snippet is a reusable pattern skeleton, not a standalone LeetCode submission. Adapt its variables and guard conditions to the exact question constraints.

**Questions Covered:** LeetCode 1438, LeetCode 2762

### LeetCode 1438

**Question:**

Given integer array `nums` and nonnegative `limit`, find the maximum length of a nonempty contiguous subarray such that the difference between its largest and smallest elements is at most `limit`. The values need not be sorted or distinct.

**Examples:**

- `nums=[8,2,4,7], limit=4` → `2`.
- `nums=[10,1,2,4,7,2], limit=5` → `4`.
- `nums=[4,2,2,2,4,4,2,2], limit=0` → `3`.

**Constraints:**

`1 ≤ nums.length ≤ 100000`; `1 ≤ nums[i] ≤ 1000000000`; `0 ≤ limit ≤ 1000000000`.

**Why This Subpattern Applies:**

The window's minimum and maximum must be updated when either endpoint moves. Two monotonic deques maintain both extremes in amortized O(1) per insertion/deletion.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int longestSubarray(int[] nums, int limit) {
        java.util.ArrayDeque<Integer> minQ = new java.util.ArrayDeque<>();
        java.util.ArrayDeque<Integer> maxQ = new java.util.ArrayDeque<>();
        int left = 0, best = 0;
        for (int right = 0; right < nums.length; right++) {
            while (!minQ.isEmpty() && nums[minQ.peekLast()] > nums[right]) minQ.pollLast();
            while (!maxQ.isEmpty() && nums[maxQ.peekLast()] < nums[right]) maxQ.pollLast();
            minQ.addLast(right);
            maxQ.addLast(right);
            while ((long) nums[maxQ.peekFirst()] - nums[minQ.peekFirst()] > limit) {
                if (minQ.peekFirst() == left) minQ.pollFirst();
                if (maxQ.peekFirst() == left) maxQ.pollFirst();
                left++;
            }
            best = Math.max(best, right - left + 1);
        }
        return best;
    }
}
```

**Key Logic:**

The min deque stores indices of nondecreasing values and max deque those of nonincreasing values. Their heads yield the current extrema; expired leftmost indices are discarded. Cast before subtracting to avoid overflow.

**Time Complexity:**
`O(n) amortized`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(n) auxiliary`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 2762

**Question:**

Given integer array `nums`, count all nonempty contiguous subarrays for which the difference between the maximum and minimum element is **at most 2**. Every interval is counted separately, including overlapping intervals and singletons.

**Examples:**

- `nums=[5,4,2,4]` → `8`.
- `nums=[1,2,3]` → `6`.
- `nums=[7]` → `1`.

**Constraints:**

`1 ≤ nums.length ≤ 100000`; `1 ≤ nums[i] ≤ 1000000000`.

**Why This Subpattern Applies:**

After maintaining max−min≤2, every suffix of the valid window ending at `right` also has spread≤2. Therefore `right-left+1` candidates can be counted at once.

**Connection to Previous Solution:**

Keep 1438's two deques and expiration logic; fix the limit to 2 and replace `best = max(best,length)` with `answer += length` using `long`.

**Java Solution:**

```java
class Solution {
    public long continuousSubarrays(int[] nums) {
        java.util.ArrayDeque<Integer> minQ = new java.util.ArrayDeque<>();
        java.util.ArrayDeque<Integer> maxQ = new java.util.ArrayDeque<>();
        int left = 0;
        long answer = 0;
        for (int right = 0; right < nums.length; right++) {
            while (!minQ.isEmpty() && nums[minQ.peekLast()] > nums[right]) minQ.pollLast();
            while (!maxQ.isEmpty() && nums[maxQ.peekLast()] < nums[right]) maxQ.pollLast();
            minQ.addLast(right); maxQ.addLast(right);
            while ((long) nums[maxQ.peekFirst()] - nums[minQ.peekFirst()] > 2) {
                if (minQ.peekFirst() == left) minQ.pollFirst();
                if (maxQ.peekFirst() == left) maxQ.pollFirst();
                left++;
            }
            answer += right - left + 1L;
        }
        return answer;
    }
}
```

**Key Logic:**

Window validity is hereditary under deletion, so every suffix from `left` to `right` is valid. Counting those endings produces the total in one scan.

**Time Complexity:**
`O(n) amortized`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(n) auxiliary`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

## W11 — Sorted-window transform budget
<a id="w11-family"></a>

### Subpattern W11: Sorted-window transform budget

**When to Apply:**
- **Recognition:** Choosing values close enough under an edit budget becomes contiguous after sorting.
- **Central invariant:** Contract while range width or equalization cost exceeds the budget.

**Basic Java Code Snippet:**

```java
java.util.Arrays.sort(a);
long sum = 0;
int left = 0, best = 0;
for (int right = 0; right < a.length; right++) {
    sum += a[right];
    while ((long)a[right] * (right - left + 1) - sum > budget) {
        sum -= a[left++];
    }
    best = Math.max(best, right - left + 1);
}
// For two-sided edits, use the median-cost formula instead.
```

**Core Logic:**
Contract while range width or equalization cost exceeds the budget. The snippet is a reusable pattern skeleton, not a standalone LeetCode submission. Adapt its variables and guard conditions to the exact question constraints.

**Questions Covered:** LeetCode 2779, LeetCode 1838, LeetCode 2968

### LeetCode 2779

**Question:**

Given an integer array `nums` and integer `k`, you may modify each position **at most once** by adding any integer between `-k` and `k`, inclusive. The beauty of the resulting array is the frequency of its most common value. Return the maximum beauty achievable after applying any permitted modifications.

**Examples:**

- `nums=[4,6,1,2], k=2` → `3`.
- `nums=[1,1,1,1], k=10` → `4`.
- `nums=[1,10], k=1` → `1`.

**Constraints:**

`1 ≤ nums.length ≤ 100000`; `0 ≤ nums[i] ≤ 100000`; `0 ≤ k ≤ 100000`.

**Why This Subpattern Applies:**

Each value can become any integer in `[value−k, value+k]`. A set of elements can be made equal iff their allowable intervals share an integer point, equivalently sorted max−min≤2k.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int maximumBeauty(int[] nums, int k) {
        java.util.Arrays.sort(nums);
        int left = 0, best = 0;
        for (int right = 0; right < nums.length; right++) {
            while ((long) nums[right] - nums[left] > 2L * k) left++;
            best = Math.max(best, right - left + 1);
        }
        return best;
    }
}
```

**Key Logic:**

Sorting places every feasible cluster contiguously. Since all edit radii are equal, only the smallest and largest original values determine common feasibility. Sorting mutates the input.

**Time Complexity:**
`O(n log n)`. Sorting or hashing costs are included in the stated bound.

**Space Complexity:**
`O(log n) Java dual-pivot quicksort stack for primitive int[] (implementation-dependent)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 1838

**Question:**

Given positive integer array `nums` and at most `k` operations, one operation increments any one element by exactly one. Return the maximum achievable frequency of any value after the operations. You cannot decrement elements; using fewer than `k` operations is permitted.

**Examples:**

- `nums=[1,2,4], k=5` → `3`.
- `nums=[1,4,8,13], k=5` → `2`.
- `nums=[3,9,6], k=2` → `1`.

**Constraints:**

`1 ≤ nums.length ≤ 100000`; `1 ≤ nums[i] ≤ 100000`; `1 ≤ k ≤ 100000`.

**Why This Subpattern Applies:**

After sorting, optimal increments raise a contiguous window to its rightmost (largest) value. Required operations equal `nums[right] * windowLength − windowSum`.

**Connection to Previous Solution:**

Replace 2779's spread bound `max−min ≤ 2k` with the total increment-cost bound `nums[right]*length−sum ≤ k`; maintain rolling `long sum`.

**Java Solution:**

```java
class Solution {
    public int maxFrequency(int[] nums, int k) {
        java.util.Arrays.sort(nums);
        long sum = 0;
        int left = 0, best = 0;
        for (int right = 0; right < nums.length; right++) {
            sum += nums[right];
            while ((long) nums[right] * (right - left + 1) - sum > k)
                sum -= nums[left++];
            best = Math.max(best, right - left + 1);
        }
        return best;
    }
}
```

**Key Logic:**

The target value is the right endpoint because decrements are forbidden. The right-end cost formula sums all per-element increments in O(1), and contraction restores budget feasibility.

**Time Complexity:**
`O(n log n)`. Sorting or hashing costs are included in the stated bound.

**Space Complexity:**
`O(log n) sorting stack; O(1) window auxiliary`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

### LeetCode 2968

**Question:**

Given integer array `nums` and at most `k` operations, one operation increments or decrements one array element by exactly one. Return the maximum possible frequency of any value after spending at most `k` operations. The target common value may differ from the original values.

**Examples:**

- `nums=[1,2,6,4], k=3` → `3`.
- `nums=[1,4,4,2,4], k=0` → `3`.
- `nums=[5], k=100` → `1`.

**Constraints:**

`1 ≤ nums.length ≤ 100000`; `1 ≤ nums[i] ≤ 10^9`; `0 ≤ k ≤ 10^14`.

**Why This Subpattern Applies:**

With both increments and decrements allowed, the median minimizes total absolute deviation. Sorting makes the candidate cluster contiguous, and prefix sums compute its equalization cost in O(1).

**Connection to Previous Solution:**

Generalize 1838's right-end-only increment cost to two-sided median cost: use `prefix` sums and split `[left,right]` at its median; shrink while this minimal cost exceeds `k`.

**Java Solution:**

```java
class Solution {
    public int maxFrequencyScore(int[] nums, long k) {
        java.util.Arrays.sort(nums);
        int n = nums.length;
        long[] prefix = new long[n + 1];
        for (int i = 0; i < n; i++) prefix[i + 1] = prefix[i] + nums[i];
        int left = 0, best = 0;
        for (int right = 0; right < n; right++) {
            while (cost(nums, prefix, left, right) > k) left++;
            best = Math.max(best, right - left + 1);
        }
        return best;
    }
    private long cost(int[] nums, long[] prefix, int left, int right) {
        int mid = left + (right - left) / 2;
        long target = nums[mid];
        long leftCost = target * (mid - left + 1L) - (prefix[mid + 1] - prefix[left]);
        long rightCost = (prefix[right + 1] - prefix[mid + 1]) - target * (right - mid);
        return leftCost + rightCost;
    }
}
```

**Key Logic:**

For sorted values, any median is optimal for L1 distance. The prefix-sum helper calculates total changes to the median without iterating through the window. `long` handles large values and budgets.

**Time Complexity:**
`O(n log n)`. Sorting or hashing costs are included in the stated bound.

**Space Complexity:**
`O(n) auxiliary prefix sums`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

## W12 — Circular fixed-window defect minimization
<a id="w12-family"></a>

### Subpattern W12: Circular fixed-window defect minimization

**When to Apply:**
- **Recognition:** Adjacent positions wrap around the array, and exactly n candidate starts exist.
- **Central invariant:** Use modular indices and update a fixed-width defect count.

**Basic Java Code Snippet:**

```java
int bad = 0;
for (int i = 0; i < width; i++) if (a[i] == 0) bad++;
int best = bad;
for (int start = 1; start < a.length; start++) {
    if (a[start - 1] == 0) bad--;
    if (a[(start + width - 1) % a.length] == 0) bad++;
    best = Math.min(best, bad);
}
// Guard width==0; width must be <= a.length.
```

**Core Logic:**
Use modular indices and update a fixed-width defect count. The snippet is a reusable pattern skeleton, not a standalone LeetCode submission. Adapt its variables and guard conditions to the exact question constraints.

**Questions Covered:** LeetCode 2134

### LeetCode 2134

**Question:**

Given a **circular** binary array `nums`, find the minimum number of swaps needed to make every `1` appear in one consecutive circular group. A swap may exchange **any two elements**, not necessarily adjacent. The first and last positions count as neighbors because the array is circular. Return zero when no swaps are necessary.

**Examples:**

- `nums=[0,1,0,1,1,0,0]` → `1`.
- `nums=[0,1,1,1,0,0,1,1,0]` → `2`.
- `nums=[1,1,1]` → `0`.

**Constraints:**

`1 ≤ nums.length ≤ 100000`; `nums[i] ∈ {0,1}`.

**Why This Subpattern Applies:**

If there are `ones` total ones, the final group occupies exactly `ones` consecutive circular positions. Every zero in that selected span must be swapped with a one outside; minimize this defect count.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int minSwaps(int[] nums) {
        int n = nums.length, ones = 0;
        for (int value : nums) ones += value;
        if (ones == 0 || ones == n) return 0;
        int zeros = 0;
        for (int i = 0; i < ones; i++) if (nums[i] == 0) zeros++;
        int best = zeros;
        for (int start = 1; start < n; start++) {
            if (nums[start - 1] == 0) zeros--;
            if (nums[(start + ones - 1) % n] == 0) zeros++;
            best = Math.min(best, zeros);
        }
        return best;
    }
}
```

**Key Logic:**

Evaluate each of the `n` circular starts once by dropping the outgoing entry and adding the wrapped incoming entry. The minimum zero count equals the number of arbitrary swaps required.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

## W13 — Longest retained middle segment as complement
<a id="w13-family"></a>

### Subpattern W13: Longest retained middle segment as complement

**When to Apply:**
- **Recognition:** Operations remove only the prefix and suffix; the unremoved values form one subarray.
- **Central invariant:** Maximize the retained contiguous subarray with sum `total − x`.

**Basic Java Code Snippet:**

```java
long total = 0;
for (int v : a) total += v;
long target = total - removedTarget, sum = 0;
int left = 0, best = -1;
for (int right = 0; right < a.length; right++) {
    sum += a[right];
    while (left <= right && sum > target) sum -= a[left++];
    if (sum == target) best = Math.max(best, right - left + 1);
}
// This shrinking logic requires nonnegative values; handle empty retained window.
```

**Core Logic:**
Maximize the retained contiguous subarray with sum `total − x`. The snippet is a reusable pattern skeleton, not a standalone LeetCode submission. Adapt its variables and guard conditions to the exact question constraints.

**Questions Covered:** LeetCode 1658

### LeetCode 1658

**Question:**

Given a positive integer array `nums` and target `x`, an operation removes exactly one element from **either the beginning or the end** and subtracts its value from `x`. Find the minimum number of such operations that reduce `x` to exactly zero. Return `-1` if it is impossible. You may remove all elements.

**Examples:**

- `nums=[1,1,4,2,3], x=5` → `2`.
- `nums=[5,6,7,8,9], x=4` → `-1`.
- `nums=[3,2,20,1,1,3], x=10` → `5`.

**Constraints:**

`1 ≤ nums.length ≤ 100000`; `1 ≤ nums[i] ≤ 10000`; `1 ≤ x ≤ 10^9`.

**Why This Subpattern Applies:**

All removals come from the two ends, leaving a contiguous retained subarray. Its sum must equal `total−x`; keeping it as long as possible minimizes removals.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int minOperations(int[] nums, int x) {
        long total = 0;
        for (int v : nums) total += v;
        long target = total - x;
        if (target < 0) return -1;
        if (target == 0) return nums.length;
        long sum = 0;
        int left = 0, best = -1;
        for (int right = 0; right < nums.length; right++) {
            sum += nums[right];
            while (left <= right && sum > target) sum -= nums[left++];
            if (sum == target) best = Math.max(best, right - left + 1);
        }
        return best == -1 ? -1 : nums.length - best;
    }
}
```

**Key Logic:**

Positive values guarantee that shrinking is safe when the retained sum exceeds the target. Explicitly handle negative targets and empty retained windows (`target==0`, meaning remove everything).

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

## W14 — Minimum window with ordered event counts
<a id="w14-family"></a>

### Subpattern W14: Minimum window with ordered event counts

**When to Apply:**
- **Recognition:** Every required symbol must have occurred before a substring ending at `right` can qualify.
- **Central invariant:** The minimum of the last-seen indices bounds all valid starts.

**Basic Java Code Snippet:**

```java
int[] last = {-1, -1, -1};
long count = 0;
for (int right = 0; right < s.length(); right++) {
    last[s.charAt(right) - 'a'] = right; // constraint: only a,b,c
    count += Math.min(last[0], Math.min(last[1], last[2])) + 1L;
}
```

**Core Logic:**
The minimum of the last-seen indices bounds all valid starts. The snippet is a reusable pattern skeleton, not a standalone LeetCode submission. Adapt its variables and guard conditions to the exact question constraints.

**Questions Covered:** LeetCode 1358

### LeetCode 1358

**Question:**

Given a string `s` containing only lowercase letters `a`, `b`, and `c`, count contiguous substrings containing **at least one occurrence of each** of the three letters. Substrings may overlap. Return the count across all possible starting and ending indices.

**Examples:**

- `s="abcabc"` → `10`.
- `s="aaacb"` → `3`.
- `s="abc"` → `1`.

**Constraints:**

`3 ≤ s.length ≤ 50000`; every character is `a`, `b`, or `c`.

**Why This Subpattern Applies:**

For a fixed right endpoint, all starts at or before the earliest last-seen required character include all three letters. The last occurrence indices directly give the number of valid starts.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int numberOfSubstrings(String s) {
        int[] last = {-1, -1, -1};
        int answer = 0;
        for (int right = 0; right < s.length(); right++) {
            last[s.charAt(right) - 'a'] = right;
            int earliest = Math.min(last[0], Math.min(last[1], last[2]));
            answer += earliest + 1;
        }
        return answer;
    }
}
```

**Key Logic:**

A missing required letter has last index `-1`, making its contribution zero. Once all are seen, choosing any start from zero through the earliest last index produces a valid ending substring.

**Time Complexity:**
`O(n)`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(1)`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

## W15 — Frequency-constrained substring counting
<a id="w15-family"></a>

### Subpattern W15: Frequency-constrained substring counting

**When to Apply:**
- **Recognition:** A valid segment allows at most `k` copies of every individual value.
- **Central invariant:** On adding a value, evict from the left while its frequency is too high.

**Basic Java Code Snippet:**

```java
java.util.Map<Integer,Integer> freq = new java.util.HashMap<>();
int left = 0, best = 0;
for (int right = 0; right < a.length; right++) {
    int value = a[right];
    freq.merge(value, 1, Integer::sum);
    while (freq.get(value) > k) {
        int old = a[left++];
        if (freq.merge(old, -1, Integer::sum) == 0) freq.remove(old);
    }
    best = Math.max(best, right - left + 1);
}
```

**Core Logic:**
On adding a value, evict from the left while its frequency is too high. The snippet is a reusable pattern skeleton, not a standalone LeetCode submission. Adapt its variables and guard conditions to the exact question constraints.

**Questions Covered:** LeetCode 2958

### LeetCode 2958

**Question:**

Given an integer array `nums` and positive integer `k`, find the maximum length of a contiguous subarray in which **every distinct number appears at most `k` times**. Multiple distinct values are allowed, provided each one's individual occurrence limit is respected.

**Examples:**

- `nums=[1,2,3,1,2,3,1,2], k=2` → `6`.
- `nums=[1,2,1,2,1,2,1,2], k=1` → `2`.
- `nums=[5,5,5], k=3` → `3`.

**Constraints:**

`1 ≤ nums.length ≤ 100000`; `1 ≤ nums[i] ≤ 10^9`; `1 ≤ k ≤ nums.length`.

**Why This Subpattern Applies:**

Only the newly added value can violate the per-value cap, because the previous window was valid. Shrink from the left until this value's frequency is back to at most `k`.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int maxSubarrayLength(int[] nums, int k) {
        java.util.Map<Integer,Integer> freq = new java.util.HashMap<>();
        int left = 0, best = 0;
        for (int right = 0; right < nums.length; right++) {
            int value = nums[right];
            freq.put(value, freq.getOrDefault(value, 0) + 1);
            while (freq.get(value) > k) {
                int out = nums[left++];
                int remaining = freq.get(out) - 1;
                if (remaining == 0) freq.remove(out);
                else freq.put(out, remaining);
            }
            best = Math.max(best, right - left + 1);
        }
        return best;
    }
}
```

**Key Logic:**

Because only the inserted value can breach its limit, the `while` condition needs to check that value, not scan the whole map. The frequency map always represents the current valid segment.

**Time Complexity:**
`O(n) expected`. Every array position is visited a constant number of times (amortized where deque operations apply).

**Space Complexity:**
`O(n) auxiliary`. Only auxiliary structures beyond the input are counted unless output storage is called out.

---

## Final Locked Question Registry

| Seq | Family | LeetCode | Included |
|---:|---|---:|:---:|
| 01 | W1 | 643 | ✓ |
| 02 | W1 | 1343 | ✓ |
| 03 | W1 | 2090 | ✓ |
| 04 | W2 | 1456 | ✓ |
| 05 | W2 | 1052 | ✓ |
| 06 | W2 | 1423 | ✓ |
| 07 | W3 | 1876 | ✓ |
| 08 | W3 | 2461 | ✓ |
| 09 | W3 | 2841 | ✓ |
| 10 | W4 | 438 | ✓ |
| 11 | W4 | 567 | ✓ |
| 12 | W4 | 30 | ✓ |
| 13 | W5 | 3 | ✓ |
| 14 | W5 | 1695 | ✓ |
| 15 | W5 | 904 | ✓ |
| 16 | W6 | 1004 | ✓ |
| 17 | W6 | 1493 | ✓ |
| 18 | W6 | 2024 | ✓ |
| 19 | W6 | 424 | ✓ |
| 20 | W7 | 209 | ✓ |
| 21 | W7 | 76 | ✓ |
| 22 | W7 | 1234 | ✓ |
| 23 | W8 | 713 | ✓ |
| 24 | W8 | 2302 | ✓ |
| 25 | W8 | 2537 | ✓ |
| 26 | W9 | 930 | ✓ |
| 27 | W9 | 1248 | ✓ |
| 28 | W9 | 992 | ✓ |
| 29 | W9 | 2799 | ✓ |
| 30 | W10 | 1438 | ✓ |
| 31 | W10 | 2762 | ✓ |
| 32 | W11 | 2779 | ✓ |
| 33 | W11 | 1838 | ✓ |
| 34 | W11 | 2968 | ✓ |
| 35 | W12 | 2134 | ✓ |
| 36 | W13 | 1658 | ✓ |
| 37 | W14 | 1358 | ✓ |
| 38 | W15 | 2958 | ✓ |

**Verified:** 38 of 38 locked LeetCode IDs included in exactly the master order; 15/15 subpattern introductions and templates included.

**Progress note:** Workbook generation does not update the interactive practice progress (still 0/38 until practice begins).
