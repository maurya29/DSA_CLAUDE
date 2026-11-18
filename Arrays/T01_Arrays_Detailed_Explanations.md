# T01 — Arrays: Complete Detailed Solutions & Explanations

**Curriculum:** MASTER_DSA_CURRICULUM.md (locked T01 order) | **Completed:** 29/29 | **Status:** COMPLETE (all 11 families)

This is a **separate, expanded study edition**. It preserves all question statements, examples, constraints, Java solutions and complexity analyses from the existing solution file, and adds full subpattern teaching notes plus a detailed explanation for every problem. No question has been omitted or shortened.

## Family F1 — Extremum Tracking

**Detailed subpattern explanation:** Extremum tracking is a streaming reduction: after examining the first `i` values, a maintained maximum is the greatest among that prefix. When the question requires multiple ranked values, retain a fixed number of candidate slots and shift them whenever a stronger candidate arrives. Decide whether equal values count as separate candidates: they do for products of different *positions*, but not for the third *distinct* maximum. The important invariant is that every candidate accurately represents the processed prefix, so a complete sort is unnecessary. When negatives are permitted, choose sentinel values carefully; initializing a maximum to zero can be incorrect, and using `Long.MIN_VALUE` outside the input domain can distinguish an unfilled slot from `Integer.MIN_VALUE`.

**Interview recognition and trade-offs:** Use a single traversal when the answer depends only on a fixed number of extremes, not the complete ordering. Sorting costs O(n log n) and often changes the array; tracking three maxima and two minima takes O(n) time and O(1) space. For small fixed candidate counts, conditional assignments are clearer than a heap.

**When to apply:** The answer depends on the maximum/minimum or a fixed number of extreme values obtainable in one scan.

**Basic Java template:**
```java
int max = Integer.MIN_VALUE;
for (int value : nums) {
    max = Math.max(max, value);
}
```

### LeetCode 1431

**Question:** Given `candies[i]` held by each child and `extraCandies`, return a list of booleans indicating whether giving each child all the extras would make their count at least as large as the current maximum.

**Examples:** `candies=[2,3,5,1,3], extraCandies=3` → `[true,true,true,false,true]`; `candies=[4,2,1,1,2], extraCandies=1` → `[true,false,false,false,false]`.

### Detailed Explanation

**Reasoning and invariant:** Find the greatest current candy count once, then compare each child independently to that fixed threshold. The question asks whether *that one child*, after receiving all extras, can match or exceed the current leader; it does not redistribute extras across children. Scanning for the maximum inside each child’s comparison would repeat work and become O(n²).

**Worked example:** For `[2,3,5,1,3]`, the initial maximum is `5`. Adding `3` gives `[5,6,8,4,6]`; comparisons against `5` yield `[true,true,true,false,true]`. A tie is enough, so use `>=`, not `>`.

**Code evolution and important details:** The maximum scan depends only on values, not indices. The output list is part of the required result, whereas the algorithm itself keeps only a constant amount of auxiliary state.

**Java Solution:**
```java
import java.util.*;
class Solution {
    public List<Boolean> kidsWithCandies(int[] candies, int extraCandies) {
        int max = Integer.MIN_VALUE;
        for (int candy : candies) max = Math.max(max, candy);
        List<Boolean> result = new ArrayList<>();
        for (int candy : candies) result.add(candy + extraCandies >= max);
        return result;
    }
}
```

**Time:** O(n). **Space:** O(n) for output, O(1) auxiliary.

### LeetCode 747

**Question:** Given an array with a unique largest element, return its index if it is at least twice every other element; otherwise `-1`.

**Examples:** `[3,6,1,0]` → `1`; `[1,2,3,4]` → `-1`; `[1]` → `0`.

### Detailed Explanation

**Reasoning and invariant:** Maintain the largest value, its index, and the second-largest value. When a new maximum arrives, the previous maximum becomes the second maximum; when an intermediate value arrives, it may replace only the second maximum. The largest value is unique, and all numbers are nonnegative, so checking it against twice the second-largest is equivalent to checking it against every other value.

**Worked example:** For `[3,6,1,0]`, the maximum is `6` at index `1` and the second maximum is `3`; `6 >= 2*3`, so return `1`. For `[1,2,3,4]`, `4 < 2*3`, so return `-1`. The one-element case returns index `0` because there is no other number to violate the condition.

**Code evolution and important details:** This evolves the single `max` state from 1431 into three pieces of state. Use a sentinel smaller than legal elements; the constraints permit `-1` because all elements are at least zero.

**Java Solution:**
```java
class Solution {
    public int dominantIndex(int[] nums) {
        int max = -1, secondMax = -1, maxIndex = -1;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] > max) {
                secondMax = max;
                max = nums[i];
                maxIndex = i;
            } else if (nums[i] > secondMax) {
                secondMax = nums[i];
            }
        }
        return max >= 2 * secondMax ? maxIndex : -1;
    }
}
```

**Time:** O(n). **Space:** O(1).

### LeetCode 414

**Question:** Return the third **distinct** largest value in the array; if fewer than three distinct values exist, return the maximum.

**Examples:** `[3,2,1]` → `1`; `[1,2]` → `2`; `[2,2,3,1]` → `1`.

### Detailed Explanation

**Reasoning and invariant:** The answer depends on rank among **distinct values**, not rank among positions. Maintain `max`, `secondMax`, and `thirdMax`, and skip a number if it equals an already selected candidate. For a new largest value, shift the previous candidates downward; for an intermediate value, insert it at the appropriate rank.

**Worked example:** For `[2,2,3,1]`, the distinct candidates become `3`, `2`, and `1`; the repeated `2` does not take a second slot. For `[1,2]`, a third distinct value does not exist, so return `2`.

**Code evolution and important details:** The array can contain `Integer.MIN_VALUE`, so using an `int` sentinel equal to that number cannot reliably distinguish an unfilled slot. The provided solution uses `long` candidates initialized to `Long.MIN_VALUE`, safely outside the input range. This generalizes 747’s two candidate values to three, and adds duplicate rejection.

**Java Solution:**
```java
class Solution {
    public int thirdMax(int[] nums) {
        long max = Long.MIN_VALUE;
        long secondMax = Long.MIN_VALUE;
        long thirdMax = Long.MIN_VALUE;
        for (int num : nums) {
            if (num == max || num == secondMax || num == thirdMax) continue;
            if (num > max) {
                thirdMax = secondMax;
                secondMax = max;
                max = num;
            } else if (num > secondMax) {
                thirdMax = secondMax;
                secondMax = num;
            } else if (num > thirdMax) {
                thirdMax = num;
            }
        }
        return thirdMax == Long.MIN_VALUE ? (int) max : (int) thirdMax;
    }
}
```

**Time:** O(n). **Space:** O(1).

### LeetCode 628

**Question:** Choose exactly three array elements to maximize their product; values can be negative, zero, or positive.

**Examples:** `[1,2,3]` → `6`; `[1,2,3,4]` → `24`; `[-10,-10,5,2]` → `500`.

### Detailed Explanation

**Reasoning and invariant:** A maximum three-number product is not always formed by the three numerically largest values. Multiplying two large-magnitude negative values produces a positive result; consequently, only two forms need comparison: `max1*max2*max3` and `max1*min1*min2`. Track three largest and two smallest values in a single pass.

**Worked example:** For `[-10,-10,5,2]`, the top three numbers give `5*2*(-10)=-100`, while the two smallest with the largest give `(-10)*(-10)*5=500`. Return `500`. Unlike the previous question, duplicate *values* are allowed because they can occupy different array positions.

**Code evolution and important details:** Remove the duplicate-skipping rule from 414 and add two minimum candidate slots. The bound `|nums[i]| <= 1000` ensures the three-factor product fits in Java `int` here; in unconstrained production code, consider `long`.

**Java Solution:**
```java
class Solution {
    public int maximumProduct(int[] nums) {
        int max1 = Integer.MIN_VALUE, max2 = Integer.MIN_VALUE, max3 = Integer.MIN_VALUE;
        int min1 = Integer.MAX_VALUE, min2 = Integer.MAX_VALUE;
        for (int num : nums) {
            if (num > max1) {
                max3 = max2;
                max2 = max1;
                max1 = num;
            } else if (num > max2) {
                max3 = max2;
                max2 = num;
            } else if (num > max3) {
                max3 = num;
            }
            if (num < min1) {
                min2 = min1;
                min1 = num;
            } else if (num < min2) {
                min2 = num;
            }
        }
        return Math.max(max1 * max2 * max3, max1 * min1 * min2);
    }
}
```

**Time:** O(n). **Space:** O(1).

## Family F2 — One-Pass Running Optimum

**Detailed subpattern explanation:** A running optimum answers the question “what was the best *legal earlier* choice when I reach this index?” For each right-side index, evaluate the answer using the best state accumulated strictly before it; only afterward incorporate the current item into the state. That update order enforces `left < right`. The state might be a minimum purchase price, a maximum algebraic expression, or a suffix maximum if traversal is reversed. Unlike global extrema, legality depends on the index ordering.

**Interview recognition and trade-offs:** Look for pairs of indices `i < j` and an objective that separates into a left contribution and a right contribution. Algebraically rewrite the score to isolate the best previously seen term. This replaces a quadratic all-pairs search with a constant-space scan. When transforming the original array from right to left, save its current value before overwriting it so the running state remains based on original data.

**When to apply:** A choice at the current/right index must pair with the best **earlier** candidate, respecting left-before-right order. Maintain that earlier optimum while scanning.

**Basic Java template (generic difference objective):**
```java
int bestLeft = values[0];
int answer = Integer.MIN_VALUE;
for (int right = 1; right < values.length; right++) {
    answer = Math.max(answer, values[right] - bestLeft);
    bestLeft = Math.min(bestLeft, values[right]);
}
```

### LeetCode 121

**Question:** Given daily stock prices, buy once on an earlier day and sell once on a later day to maximize profit. Return `0` if no profitable trade exists. You may not sell before buying.

**Examples:** `prices=[7,1,5,3,6,4]` → `5` (buy at 1, sell at 6); `prices=[7,6,4,3,1]` → `0`.

**Constraints:** `1 <= prices.length <= 10^5`; `0 <= prices[i] <= 10^4`.

### Detailed Explanation

**Reasoning and invariant:** For any sell day, the best legal buy price is the smallest price on an earlier day. Keep `minPrice` and `maxProfit`. Evaluate `price - minPrice` first, then update `minPrice`, preserving the chronological ordering. Initialize `maxProfit` to zero because skipping the trade is allowed.

**Worked example:** In `[7,1,5,3,6,4]`, the running minimum falls to `1`; when the current price is `6`, the candidate profit is `6-1=5`. A descending array never produces a positive candidate and returns `0`.

**Code evolution and important details:** This is a new family because global extrema alone are insufficient: a low price *after* the intended sell day is not a valid buy. The state captures the best legal prefix candidate.

**Java Solution:**
```java
class Solution {
    public int maxProfit(int[] prices) {
        int minPrice = prices[0];
        int maxProfit = 0;
        for (int i = 1; i < prices.length; i++) {
            maxProfit = Math.max(maxProfit, prices[i] - minPrice);
            minPrice = Math.min(minPrice, prices[i]);
        }
        return maxProfit;
    }
}
```

**Time:** O(n). **Space:** O(1).

### LeetCode 1014

**Question:** Given an array `values`, select two sightseeing spots at indices `i < j` maximizing `values[i] + values[j] + i - j`. Return the maximum score.

**Examples:** `[8,1,5,2,6]` → `11` (indices 0 and 2); `[1,2]` → `2`.

**Constraints:** `2 <= values.length <= 5 * 10^4`; `1 <= values[i] <= 1000`.

### Detailed Explanation

**Reasoning and invariant:** Rewrite the score as `(values[i]+i) + (values[j]-j)` for `i<j`. At each right index `j`, only the first parenthesized term depends on the earlier index. Track `bestLeft = max(values[i]+i)` among already visited indices and combine it with the current `(values[j]-j)`.

**Worked example:** For `[8,1,5,2,6]`, at `j=2` the best earlier contribution is `8+0=8`; the current contribution is `5-2=3`, giving score `11`. Update `bestLeft` *after* checking the score to keep `i<j`.

**Code evolution and important details:** This is exactly the structural evolution from 121: instead of a minimum prefix price, the prefix state stores a maximum transformed expression. The algebraic separation is the reusable insight.

**Java Solution:**
```java
class Solution {
    public int maxScoreSightseeingPair(int[] values) {
        int bestLeft = values[0];
        int maxScore = 0;

        for (int j = 1; j < values.length; j++) {
            maxScore = Math.max(maxScore, bestLeft + values[j] - j);
            bestLeft = Math.max(bestLeft, values[j] + j);
        }

        return maxScore;
    }
}
```

**Time Complexity:** O(n). **Space Complexity:** O(1).

### LeetCode 1299

**Question:** Replace each element in `arr` with the greatest element strictly to its right; replace the final element with `-1`. Modify and return the input array.

**Examples:** `[17,18,5,4,6,1]` → `[18,6,6,6,1,-1]`; `[400]` → `[-1]`.

**Constraints:** `1 <= arr.length <= 10^4`; `1 <= arr[i] <= 10^5`.

### Detailed Explanation

**Reasoning and invariant:** The answer at index `i` is the maximum of elements strictly to its right. Scan backward, maintaining `maxRight` from positions already visited. Before updating that maximum with the current original value, overwrite the current cell with the old maximum.

**Worked example:** For `[17,18,5,4,6,1]`, start with `maxRight=-1` at the last position, replace `1` with `-1`, then carry maximum `1` left. At index containing `6`, write `1` and update the maximum to `6`; continue to obtain `[18,6,6,6,1,-1]`.

**Code evolution and important details:** Save `current=arr[i]` before writing `arr[i]=maxRight`, otherwise the original value is lost. Compared with 1014, the running optimum travels right-to-left and is written directly into the array.

**Java Solution:**
```java
class Solution {
    public int[] replaceElements(int[] arr) {
        int maxRight = -1;

        for (int i = arr.length - 1; i >= 0; i--) {
            int current = arr[i];
            arr[i] = maxRight;
            maxRight = Math.max(maxRight, current);
        }

        return arr;
    }
}
```

**Time Complexity:** O(n). **Space Complexity:** O(1) auxiliary.

## Family F3 — Ordered Candidate Thresholds

**Detailed subpattern explanation:** Ordered candidate thresholds summarize the smallest attainable tail of an increasing subsequence of each relevant length. For a triplet, `first` is the smallest single-element candidate, while `second` is the smallest ending value of an increasing *pair* that has occurred. A later value strictly larger than `second` completes a triplet. Updating `first` to a smaller later value does not invalidate the previously demonstrated pair for `second`; `second` was established using some earlier smaller value at its assignment.

**Interview recognition and trade-offs:** This fixed-state method is ideal when the target subsequence length is exactly three and you only need a boolean. Use `<=` in the threshold branches so equal values do not count as a strict increase. For arbitrary subsequence length, the more general LIS tails array and binary search is a different, broader algorithm.

**When to apply:** Determine whether an increasing sequence exists, preserving index order, using a small fixed number of minimal candidate thresholds.

**Basic Java template:**
```java
int first = Integer.MAX_VALUE;
int second = Integer.MAX_VALUE;
for (int num : nums) {
    if (num <= first) first = num;
    else if (num <= second) second = num;
    else {
        // A third value exceeds both ordered thresholds.
    }
}
```

### LeetCode 334

**Question:** Determine whether there exist three indices `i < j < k` with `nums[i] < nums[j] < nums[k]`. Return a boolean. The indices need not be adjacent.

**Examples:** `[1,2,3,4,5]` → `true`; `[5,4,3,2,1]` → `false`; `[2,1,5,0,4,6]` → `true`; `[1,1,1,1]` → `false`.

**Constraints:** `1 <= nums.length <= 5 * 10^5`; integer values may span the entire Java `int` range.

### Detailed Explanation

**Reasoning and invariant:** The two thresholds are not simply the smallest and second-smallest numerical elements. `first` is the smallest single-value prefix candidate; `second` is the smallest attainable ending value of an *ordered increasing pair*. Seeing any later `num > second` proves a valid increasing triplet.

**Worked example:** For `[2,1,5,0,4,6]`, the candidate pair initially reaches `1<5`, then `first` improves to `0`, and `second` improves to `4`. The next value `6` is greater than `second`, confirming `0<4<6`.

**Code evolution and important details:** Using `<=` in both threshold branches prevents repeated equal values from forming a strictly increasing sequence. No indices need be stored when the task asks only whether such a triplet exists.

**Java Solution:**
```java
class Solution {
    public boolean increasingTriplet(int[] nums) {
        int first = Integer.MAX_VALUE;
        int second = Integer.MAX_VALUE;

        for (int num : nums) {
            if (num <= first) {
                first = num;
            } else if (num <= second) {
                second = num;
            } else {
                return true;
            }
        }

        return false;
    }
}
```

**Time Complexity:** O(n). **Space Complexity:** O(1).

## Family F4 — Ordering Violations

**Detailed subpattern explanation:** Sorting requirements create order invariants. Local adjacent comparisons reveal drops, but a displaced value can force the unsorted interval to extend beyond the first local drop. A left-to-right prefix maximum identifies items smaller than *some earlier* value and thus bounds the right endpoint; a right-to-left suffix minimum symmetrically bounds the left endpoint. For a sorted-then-rotated array, the ordering invariant changes: at most one circular adjacent drop is allowed, including the comparison from final element back to first.

**Interview recognition and trade-offs:** Ask whether the question seeks *where* a full ordering fails or merely *whether* the data satisfies a rotation of that ordering. The first needs extrema-based boundary propagation; the second needs a circular break count. Use strict `>` for descending breaks because duplicates are allowed in a non-decreasing array.

**When to apply:** Detect elements out of expected sorted order, or evaluate whether an array can satisfy a sorted/rotated ordering condition.

**Basic Java template:**
```java
int breaks = 0;
for (int i = 1; i < nums.length; i++) {
    if (nums[i - 1] > nums[i]) breaks++;
}
```

### LeetCode 581

**Question:** Find the length of the shortest contiguous subarray which, if sorted in ascending order, would make the entire array non-decreasing. Return `0` if already sorted.

**Examples:** `[2,6,4,8,10,9,15]` → `5`; `[1,2,3,4]` → `0`; `[1]` → `0`; `[1,3,2,2,2]` → `4`.

**Constraints:** `1 <= nums.length <= 10^4`; `-10^5 <= nums[i] <= 10^5`.

### Detailed Explanation

**Reasoning and invariant:** The shortest fixable segment includes every element that is smaller than some prior maximum, and every element that is larger than some later minimum. Scan left-to-right to advance `right` whenever `nums[i]<maxSoFar`; simultaneously scan right-to-left to move `left` whenever `nums[j]>minSoFar`.

**Worked example:** For `[2,6,4,8,10,9,15]`, `4` violates the prefix maximum `6`, extending `right`; `9` violates prefix maximum `10`, extending it further. The backward minimum scan identifies `6` as part of the segment, giving boundaries `1..5` and length `5`.

**Code evolution and important details:** A single adjacent inversion is not enough to locate the entire needed segment: an earlier large value or later small value can extend its boundaries. If no prefix violation occurs, `right` stays `-1` and the answer is zero.

**Java Solution:**
```java
class Solution {
    public int findUnsortedSubarray(int[] nums) {
        int n = nums.length;
        int max = Integer.MIN_VALUE;
        int min = Integer.MAX_VALUE;
        int left = n;
        int right = -1;

        for (int i = 0; i < n; i++) {
            max = Math.max(max, nums[i]);
            if (nums[i] < max) right = i;

            int j = n - 1 - i;
            min = Math.min(min, nums[j]);
            if (nums[j] > min) left = j;
        }

        return right == -1 ? 0 : right - left + 1;
    }
}
```

**Time Complexity:** O(n). **Space Complexity:** O(1).

### LeetCode 1752

**Question:** Determine whether `nums` can be obtained by rotating an array sorted in non-decreasing order by any number of positions, including zero. Duplicate values are allowed.

**Examples:** `[3,4,5,1,2]` → `true`; `[2,1,3,4]` → `false`; `[1,2,3]` → `true`; `[1,1,1]` → `true`.

**Constraints:** `1 <= nums.length <= 100`; `1 <= nums[i] <= 100`.

### Detailed Explanation

**Reasoning and invariant:** A non-decreasing sorted array rotated at most once around a circular boundary has at most one adjacent decrease when the final element is compared back to the first. Traverse all circular edges, counting the positions where `nums[i] > nums[(i+1)%n]`; reject as soon as the count exceeds one.

**Worked example:** For `[3,4,5,1,2]`, the only drop is `5>1`, so return true. For already sorted `[1,2,3]`, the circular edge `3>1` is the one allowed drop; zero rotations still satisfy the property. `[2,1,3,4]` has drops at `2>1` and `4>2`, so return false.

**Code evolution and important details:** This simplifies the ordering-violation family from 581: instead of finding unsorted boundaries, count circular descending breaks. Duplicate neighbors are legal, so equality is not a violation.

**Java Solution:**
```java
class Solution {
    public boolean check(int[] nums) {
        int n = nums.length;
        int breaks = 0;

        for (int i = 0; i < n; i++) {
            if (nums[i] > nums[(i + 1) % n]) {
                breaks++;
            }
            if (breaks > 1) return false;
        }

        return true;
    }
}
```

**Time Complexity:** O(n). **Space Complexity:** O(1).

## Family F5 — In-Place Read/Write

**Detailed subpattern explanation:** Read/write compaction separates inspection from placement. `read` visits each original item in sequence; `write` marks the next free slot in a valid prefix. At each step, `0..write-1` satisfies the output specification and preserves the order of retained items. Changing the keep condition turns filtering into stable deduplication or nonzero compaction. A sorted input lets the keep condition compare against the last one or two already-written values.

**Expansion is different:** When one input item generates multiple output items, forward writing may overwrite unread values. Count the logical expanded length and write backward, ignoring destinations outside the fixed array bounds. In either direction, identify the invariant and clarify whether trailing slots are meaningful. These techniques use O(1) auxiliary space because they reuse the original storage.

**When to apply:** Filter, compact, or deduplicate an array while maintaining the relative order of retained elements and using constant auxiliary space. A `read` pointer examines elements; a `write` pointer identifies the next placement position.

**Basic Java template (reusable generic filter):**
```java
int write = 0;
for (int read = 0; read < nums.length; read++) {
    if (nums[read] != 0) { // replace with the problem-specific keep condition
        nums[write] = nums[read];
        write++;
    }
}
```

### LeetCode 27

**Difficulty:** Easy.

**Question:** Given an array `nums` and integer `val`, remove every occurrence of `val` **in place**. Return `k`, the count of retained elements. The first `k` entries of `nums` must be values other than `val`. Values at or beyond index `k` do not matter; LeetCode does not require the kept elements to retain their relative order, but this solution does preserve it. Do not create a separate storage array.

**Examples:**
```text
nums = [3,2,2,3], val = 3
return 2; first 2 slots = [2,2]

nums = [0,1,2,2,3,0,4,2], val = 2
return 5; first 5 slots = [0,1,3,0,4]

nums = [1,1,1], val = 1
return 0
```
**Constraints:** `0 <= nums.length <= 100`; `0 <= nums[i] <= 50`; `0 <= val <= 100`.

### Detailed Explanation

**Reasoning and invariant:** The `read` pointer examines every original value, and `write` grows only for a value not equal to `val`. At any point, the prefix `[0,write)` contains exactly the retained values from the processed portion, in the original order. Elements beyond the returned prefix are irrelevant.

**Worked example:** For `[3,2,2,3]` with `val=3`, skip the first `3`, copy both `2`s into positions `0` and `1`, skip the final `3`, and return `2`. If no values qualify, `write` remains zero.

**Code evolution and important details:** This creates the read/write family foundation. Although the platform does not demand stable order for this problem, stable compaction is simple and becomes directly reusable for later questions.

**Java Solution:**
```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int write = 0;
        for (int read = 0; read < nums.length; read++) {
            if (nums[read] != val) {
                nums[write] = nums[read];
                write++;
            }
        }
        return write;
    }
}
```

**Time Complexity:** O(n): each input element is visited once.  
**Space Complexity:** O(1): no extra array or collection; only pointers.

### LeetCode 283

**Difficulty:** Easy.

**Question:** Move every zero in `nums` to the end **in place**, preserving the original relative order of nonzero elements. You may not allocate another array to hold the rearranged values. The method returns nothing; it mutates `nums`.

**Examples:**
```text
[0,1,0,3,12] -> [1,3,12,0,0]
[0] -> [0]
[4,0,5,0,0,3] -> [4,5,3,0,0,0]
```
**Constraints:** `1 <= nums.length <= 10^4`; entries can span the full signed 32-bit `int` range.

### Detailed Explanation

**Reasoning and invariant:** Reuse the stable read/write compaction from 27, but keep only nonzero values. After all nonzero entries have been copied to the prefix, the remainder must be filled with zeros because this question specifies the whole final array, unlike 27.

**Worked example:** For `[0,1,0,3,12]`, compaction writes `[1,3,12,...]` and returns `write=3`. Filling positions `3` and `4` yields `[1,3,12,0,0]`; the relative order `1,3,12` remains intact.

**Code evolution and important details:** The exact change from 27 is a new keep-rule (`!=0`) plus the trailing zero-fill loop. Two linear passes are still O(n), and the algorithm does not allocate a second array.

**Java Solution:**
```java
class Solution {
    public void moveZeroes(int[] nums) {
        int write = 0;
        for (int read = 0; read < nums.length; read++) {
            if (nums[read] != 0) {
                nums[write++] = nums[read];
            }
        }
        while (write < nums.length) {
            nums[write++] = 0;
        }
    }
}
```

**Time Complexity:** O(n): compaction and trailing fill together touch O(n) entries.  
**Space Complexity:** O(1): updates happen in the original array.

### LeetCode 26

**Difficulty:** Easy.

**Question:** Given an integer array already sorted in non-decreasing order, remove duplicates **in place** so each distinct value occurs once. Preserve order and return the count `k` of unique values. The first `k` positions must be the distinct values; entries beyond that prefix are irrelevant.

**Examples:**
```text
[1,1,2] -> k=2, prefix [1,2]
[0,0,1,1,1,2,2,3,3,4] -> k=5, prefix [0,1,2,3,4]
[-3,-3,-1,0,0,2] -> k=4, prefix [-3,-1,0,2]
```
**Constraints:** `1 <= nums.length <= 3 * 10^4`; `-100 <= nums[i] <= 100`; input sorted in non-decreasing order.

### Detailed Explanation

**Reasoning and invariant:** Sorted input makes equal values adjacent. Keep the first value, then copy a new value only when it differs from the last already-written unique value `nums[write-1]`. The written prefix is always sorted and contains one copy of each value encountered so far.

**Worked example:** For `[0,0,1,1,1,2,2,3,3,4]`, start with `write=1`, ignore repeated zeroes, then write `1`, `2`, `3`, and `4` once each. The final logical prefix is `[0,1,2,3,4]`, so return `5`.

**Code evolution and important details:** Compared with 283, the condition depends on the last *retained* element rather than a fixed value, and no trailing fill is necessary. The input is guaranteed nonempty, which makes `write=1` valid.

**Java Solution:**
```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int write = 1;
        for (int read = 1; read < nums.length; read++) {
            if (nums[read] != nums[write - 1]) {
                nums[write++] = nums[read];
            }
        }
        return write;
    }
}
```

**Time Complexity:** O(n).  
**Space Complexity:** O(1).

### LeetCode 80

**Difficulty:** Medium.

**Question:** Given a non-decreasing sorted array, remove duplicates in place so each distinct value appears **at most twice**. Keep the original relative order of all retained values and return the resulting valid prefix length `k`. The rest of the array is irrelevant.

**Examples:**
```text
[1,1,1,2,2,3] -> k=5, prefix [1,1,2,2,3]
[0,0,1,1,1,1,2,3,3] -> k=7, prefix [0,0,1,1,2,3,3]
[1,1,1,1] -> k=2, prefix [1,1]
```
**Constraints:** `1 <= nums.length <= 3 * 10^4`; `-10^4 <= nums[i] <= 10^4`; input sorted.

### Detailed Explanation

**Reasoning and invariant:** Keep up to two copies of each value in the already-compacted prefix. The next value can be written if it differs from `nums[write-2]`; if it equals that element, writing it would create at least three copies of the same sorted value. Initialize `write=2` because the first two values can always remain.

**Worked example:** For `[1,1,1,2,2,3]`, the third `1` equals the element two written positions back and is skipped. Both `2`s and the `3` are kept, giving `[1,1,2,2,3]` with `k=5`. Arrays of length one or two return their length unchanged.

**Code evolution and important details:** This generalizes 26’s `nums[write-1]` comparison to `nums[write-2]`. More generally, “at most `m` duplicates” uses a comparison with `nums[write-m]` after seeding the first `m` values.

**Java Solution:**
```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if (nums.length <= 2) {
            return nums.length;
        }
        int write = 2;
        for (int read = 2; read < nums.length; read++) {
            if (nums[read] != nums[write - 2]) {
                nums[write++] = nums[read];
            }
        }
        return write;
    }
}
```

**Time Complexity:** O(n).  
**Space Complexity:** O(1).

### LeetCode 1089

**Difficulty:** Easy.

**Question:** In a **fixed-length** integer array, duplicate every zero while shifting subsequent elements to the right. Anything that would fall off the array's right edge is discarded. Modify the input in place, keep its length unchanged, preserve the original relative order of nonzero values, and use constant extra space.

**Examples:**
```text
[1,0,2,3,0,4,5,0] -> [1,0,0,2,3,0,0,4]
[1,2,3] -> [1,2,3]
[0,0,1] -> [0,0,0]
[1,0,2,0,3] -> [1,0,0,2,0]
```
**Constraints:** `1 <= arr.length <= 10^4`; `0 <= arr[i] <= 9`.

### Detailed Explanation

**Reasoning and invariant:** Duplicating zero expands the logical sequence, but the physical array keeps its original length. Forward expansion risks overwriting values not yet read. Count zeros first, set the write pointer to the last index of the *virtual expanded* sequence, and copy from right to left. Ignore writes whose destination is outside the physical array.

**Worked example:** For `[1,0,2,3,0,4,5,0]`, the virtual expansion contains three additional positions, making its logical end index `10` although the actual last index is `7`. Writing backward skips all destinations beyond `7`; zeros are written twice into in-bounds destinations. The final array is `[1,0,0,2,3,0,0,4]`.

**Code evolution and important details:** The new operation is expansion rather than compaction. Maintain the original source value for a zero test before overwriting could affect it; processing from the right and only writing at or to the right of the read position preserves unread data.

**Java Solution:**
```java
class Solution {
    public void duplicateZeros(int[] arr) {
        int n = arr.length;
        int zeros = 0;

        for (int num : arr) {
            if (num == 0) {
                zeros++;
            }
        }

        int read = n - 1;
        int write = n + zeros - 1;

        while (read >= 0) {
            if (write < n) {
                arr[write] = arr[read];
            }
            if (arr[read] == 0) {
                write--;
                if (write < n) {
                    arr[write] = 0;
                }
            }
            read--;
            write--;
        }
    }
}
```

**Time Complexity:** O(n), including zero count and reverse processing.  
**Space Complexity:** O(1).

## Family F6 — Index Mapping and Permutation Composition

**Detailed subpattern explanation:** Index mapping constructs an output by a formula instead of searching. There are two distinct questions: “for source index `i`, where should its value go?” and “for output index `i`, which source value belongs here?” Concatenation and interleaving use the first; permutation composition `nums[nums[i]]` uses the second. For a valid zero-based permutation, every original value is below `n`, enabling temporary two-value encoding in each cell: `encoded = old + new*n`. `% n` restores `old` while the array is partially encoded; integer division by `n` extracts `new` afterward.

**Interview recognition and trade-offs:** Derive and test the mapping on indices before coding. Check that destinations are in bounds, that writes do not collide unexpectedly, and that in-place encoding cannot overflow the chosen numeric type under the constraints. If the value bound is too large, prefer a separate output array rather than an unsafe encoding trick.

**When to apply:** Every element has a known destination index, or every output element's source index is computable directly from a formula. Avoid searching or sorting when the mapping is explicit.

**Basic Java template:**
```java
int[] result = new int[nums.length];
for (int i = 0; i < nums.length; i++) {
    int destination = i; // replace with the needed mapping
    result[destination] = nums[i];
}
```

### LeetCode 1929

**Difficulty:** Easy.

**Question:** Given `nums` of length `n`, produce a new array of length `2n` that contains `nums` followed by a second copy of `nums`. Formally, for each `i` from `0` to `n-1`, assign `ans[i]=nums[i]` and `ans[i+n]=nums[i]`.

**Examples:**
```text
[1,2,1] -> [1,2,1,1,2,1]
[1,3,2,1] -> [1,3,2,1,1,3,2,1]
[5] -> [5,5]
```
**Constraints:** `1 <= nums.length <= 1000`; `1 <= nums[i] <= 1000`.

### Detailed Explanation

**Reasoning and invariant:** Each source element at index `i` has two predictable destinations: `i` in the first half and `i+n` in the second half. Allocate the required `2n` output cells, then fill both destinations in one pass.

**Worked example:** For `[1,2,1]`, index `0` writes `1` into output positions `0` and `3`, index `1` writes `2` into positions `1` and `4`, and index `2` writes `1` into positions `2` and `5`. The result is `[1,2,1,1,2,1]`.

**Code evolution and important details:** This is the foundation of direct index mapping. Unlike 1089, no in-place overwrite hazard exists: the specification explicitly requires a larger returned array.

**Java Solution:**
```java
class Solution {
    public int[] getConcatenation(int[] nums) {
        int n = nums.length;
        int[] ans = new int[2 * n];
        for (int i = 0; i < n; i++) {
            ans[i] = nums[i];
            ans[i + n] = nums[i];
        }
        return ans;
    }
}
```

**Time Complexity:** O(n).  
**Space Complexity:** O(n) for the returned array.

### LeetCode 1470

**Difficulty:** Easy.

**Question:** The array has `2n` entries arranged `[x1,...,xn,y1,...,yn]`. Return `[x1,y1,x2,y2,...,xn,yn]` by alternating elements from the two halves.

**Examples:**
```text
nums=[2,5,1,3,4,7], n=3 -> [2,3,5,4,1,7]
nums=[1,2,3,4,4,3,2,1], n=4 -> [1,4,2,3,3,2,4,1]
nums=[1,1,2,2], n=2 -> [1,2,1,2]
```
**Constraints:** `1 <= n <= 500`; `nums.length=2n`; `1 <= nums[i] <= 1000`.

### Detailed Explanation

**Reasoning and invariant:** Split the `2n`-element input into two conceptual halves. For each half-index `i`, place the first-half item at even output index `2*i` and the second-half item at odd output index `2*i+1`. This preserves ordering inside both halves while interleaving them.

**Worked example:** For `[2,5,1,3,4,7]` and `n=3`, write pairs `(2,3)` to positions `(0,1)`, `(5,4)` to `(2,3)`, and `(1,7)` to `(4,5)`. The result is `[2,3,5,4,1,7]`.

**Code evolution and important details:** The loop from 1929 is unchanged; only the mapping changes from concatenation offsets to alternating destination indices, and the second write reads from `nums[i+n]` rather than repeating `nums[i]`.

**Java Solution:**
```java
class Solution {
    public int[] shuffle(int[] nums, int n) {
        int[] ans = new int[2 * n];
        for (int i = 0; i < n; i++) {
            ans[2 * i] = nums[i];
            ans[2 * i + 1] = nums[i + n];
        }
        return ans;
    }
}
```

**Time Complexity:** O(n).  
**Space Complexity:** O(n) for the output array.

### LeetCode 1920

**Difficulty:** Easy.

**Question:** Given a zero-based permutation `nums` of length `n` (each integer from `0` to `n-1` occurs exactly once), return an array with `ans[i]=nums[nums[i]]` for each index. As an additional challenge, achieve O(1) auxiliary space by reusing the input array.

**Examples:**
```text
[0,2,1,5,3,4] -> [0,1,2,4,5,3]
[5,0,1,2,3,4] -> [4,5,0,1,2,3]
[0] -> [0]
```
**Constraints:** `1 <= nums.length <= 1000`; `0 <= nums[i] < n`; all entries distinct.

### Detailed Explanation

**Reasoning and invariant:** The destination-driven formula `ans[i]=nums[nums[i]]` composes the permutation with itself. A normal output array works, but the bounded range `0..n-1` allows in-place encoding. Write `old+new*n` into each cell: `%n` still retrieves the original value if a referenced cell has been encoded, and dividing each final cell by `n` extracts its new value.

**Worked example:** For `nums=[0,2,1,5,3,4]`, the unencoded output is `[0,1,2,4,5,3]`. At `i=3`, `nums[3]=5`, so the desired new value is original `nums[5]=4`; the encoding at index 3 stores both the original `5` and new `4`.

**Code evolution and important details:** This switches from 1470’s known destination indices to nested source lookup. In-place numeric encoding is safe only because values are below `n` and `n<=1000`; ensure no overflow for different constraints.

**Java Solution:**
```java
class Solution {
    public int[] buildArray(int[] nums) {
        int n = nums.length;
        for (int i = 0; i < n; i++) {
            nums[i] += (nums[nums[i]] % n) * n;
        }
        for (int i = 0; i < n; i++) {
            nums[i] /= n;
        }
        return nums;
    }
}
```

**Time Complexity:** O(n), two passes.  
**Space Complexity:** O(1) auxiliary.

## Family F7 — Reverse-Based In-Place Permutations

**Detailed subpattern explanation:** The `reverse(left,right)` primitive swaps symmetric positions inward and needs no extra array. Rotation follows from splitting an array into segments `A | B` where `B` is the last `k` values. Reversing `A|B` and then reversing the two resulting blocks separately yields `B|A` with each block's internal order restored. Next permutation uses a different reason for reversing: the suffix to the right of the rightmost increasing pivot is in non-increasing order; after a pivot/successor swap, reversing that suffix makes it the smallest possible arrangement.

**Interview recognition and trade-offs:** Use this technique for in-place block rearrangements or permutation transitions when a suffix already has exploitable order. Normalize rotations with `k %= n` when `n > 0`. For next permutation, find the pivot from the right, choose the rightmost element strictly greater than it, swap, and reverse exactly the suffix. Duplicates make strict inequality important.

**When to apply:** A required rearrangement can be implemented by reversing the whole array or a contiguous segment, keeping the original array and using constant auxiliary space.

**Basic Java template:**
```java
private void reverse(int[] nums, int left, int right) {
    while (left < right) {
        int temp = nums[left];
        nums[left] = nums[right];
        nums[right] = temp;
        left++;
        right--;
    }
}
```

### LeetCode 189

**Difficulty:** Medium.

**Question:** Rotate `nums` **to the right by `k` positions** in place. Each element shifts right by `k` with wraparound; implement with O(1) auxiliary space.

**Examples:**
```text
nums=[1,2,3,4,5,6,7], k=3 -> [5,6,7,1,2,3,4]
nums=[-1,-100,3,99], k=2 -> [3,99,-1,-100]
nums=[1,2,3], k=5 -> [2,3,1]
```
**Constraints:** `1 <= nums.length <= 10^5`; signed 32-bit integer values; `0 <= k <= 10^5`.

### Detailed Explanation

**Reasoning and invariant:** Let the last `k` items be block `B` and the preceding items block `A`. A right rotation must produce `B|A`. Reverse the entire array to obtain `reverse(B)|reverse(A)`, then reverse each resulting block individually to restore their internal order.

**Worked example:** For `[1,2,3,4,5,6,7]` with `k=3`, reversing all yields `[7,6,5,4,3,2,1]`; reversing the first three yields `[5,6,7,4,3,2,1]`; reversing the remainder yields `[5,6,7,1,2,3,4]`.

**Code evolution and important details:** Normalize `k` to `k%n` to support values larger than the length. This is a new family based on the reusable two-pointer `reverse` helper rather than index encoding.

**Java Solution:**
```java
class Solution {
    public void rotate(int[] nums, int k) {
        int n = nums.length;
        k %= n;
        reverse(nums, 0, n - 1);
        reverse(nums, 0, k - 1);
        reverse(nums, k, n - 1);
    }

    private void reverse(int[] nums, int left, int right) {
        while (left < right) {
            int temp = nums[left];
            nums[left] = nums[right];
            nums[right] = temp;
            left++;
            right--;
        }
    }
}
```

**Time Complexity:** O(n): three reversals over O(n) total elements.  
**Space Complexity:** O(1).

### LeetCode 31

**Difficulty:** Medium.

**Question:** Rearrange the permutation represented by `nums` into its **next lexicographically greater** permutation, changing the array in place. If no greater permutation exists, produce the lexicographically smallest arrangement (ascending order). Duplicates are allowed. Do not generate all permutations; use O(1) extra space.

**Examples:**
```text
[1,2,3] -> [1,3,2]
[3,2,1] -> [1,2,3]
[1,1,5] -> [1,5,1]
[1,3,5,4,2] -> [1,4,2,3,5]
```
**Constraints:** `1 <= nums.length <= 100`; `0 <= nums[i] <= 100`.

### Detailed Explanation

**Reasoning and invariant:** To get the immediately next permutation, find the rightmost place where an increase is possible: the first index from the right with `nums[i]<nums[i+1]`. Everything after that pivot is non-increasing. Swap the pivot with the smallest suffix value strictly larger than it (found by scanning from the right), then reverse the suffix to make it lexicographically minimal.

**Worked example:** For `[1,3,5,4,2]`, pivot `3` at index 1 can be increased. Swap it with suffix value `4`, producing `[1,4,5,3,2]`; reverse the suffix to `[1,4,2,3,5]`. For `[3,2,1]`, no pivot exists, so reversing the whole array gives the smallest permutation.

**Code evolution and important details:** Reuse 189’s `reverse` helper, but first add pivot detection and successor selection. The successor test must be strictly greater (`>`), not greater-or-equal, especially with duplicates.

**Java Solution:**
```java
class Solution {
    public void nextPermutation(int[] nums) {
        int n = nums.length;
        int i = n - 2;

        while (i >= 0 && nums[i] >= nums[i + 1]) {
            i--;
        }

        if (i >= 0) {
            int j = n - 1;
            while (nums[j] <= nums[i]) {
                j--;
            }
            swap(nums, i, j);
        }

        reverse(nums, i + 1, n - 1);
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }

    private void reverse(int[] nums, int left, int right) {
        while (left < right) {
            swap(nums, left, right);
            left++;
            right--;
        }
    }
}
```

**Time Complexity:** O(n): backward pivot scan, successor search, and suffix reverse.  
**Space Complexity:** O(1).

## Family F8 — Array-as-Hash Sign Marking

**Detailed subpattern explanation:** When values belong to `1..n`, an input array of length `n` doubles as a presence table: the value `x` owns position `x-1`. Negating the value at that position records that `x` was visited. Always read a source value with `Math.abs` because a previous visit may have changed its sign. An already-negative target signals another occurrence; a still-positive target after the marking pass signals absence.

**Interview recognition and trade-offs:** This offers O(n) time and O(1) auxiliary space without a set, but it deliberately mutates the input and relies on values being positive and bounded. It should not be used unchanged for arbitrary integers or zero-valued slots. A negative target is a one-bit presence marker—not a frequency count—so if a value could occur three or more times, special handling would be needed to avoid reporting a duplicate repeatedly.

**When to apply:** All values are in `1..n`, the input may be changed, and you need to discover which values appeared, repeated, or were absent without allocating a hash table.

**Basic Java template:**
```java
for (int value : nums) {
    int index = Math.abs(value) - 1;
    if (nums[index] > 0) {
        nums[index] = -nums[index];
    }
}
```

**Central invariant:** A negative value at index `v-1` means value `v` was encountered. The magnitude stored there is not a count; its sign is a presence bit. Always use `Math.abs` to read source values after mutation.

### LeetCode 448

**Question:** Given `n` values in the inclusive range `1..n`, some of which repeat, return every number in `1..n` absent from the array. Modify the input if useful; target O(n) time and O(1) auxiliary space (excluding the returned list).

**Examples:** `[4,3,2,7,8,2,3,1]` → `[5,6]`; `[1,1]` → `[2]`; `[1,2,3,4]` → `[]`.

### Detailed Explanation

**Reasoning and invariant:** Every number `x` in `1..n` maps to marker index `x-1`. As you inspect each value, negate the marker at its owning index if positive. During a second pass, any position still positive corresponds to a number never seen.

**Worked example:** For `[4,3,2,7,8,2,3,1]`, indices associated with 1, 2, 3, 4, 7 and 8 become negative. The slots at indices `4` and `5` remain positive, so the missing numbers are `5` and `6`.

**Code evolution and important details:** Always apply `Math.abs` to source values because prior marking may have negated them. This is a new family: array cells act as presence bits instead of being rearranged into numerical order.

**Java Solution:**
```java
import java.util.*;
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        List<Integer> result = new ArrayList<>();
        for (int i = 0; i < nums.length; i++) {
            int index = Math.abs(nums[i]) - 1;
            if (nums[index] > 0) nums[index] = -nums[index];
        }
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] > 0) result.add(i + 1);
        }
        return result;
    }
}
```

**Time Complexity:** O(n), two linear passes.  
**Space Complexity:** O(1) auxiliary; O(n) worst-case returned list.

### LeetCode 442

**Question:** Array of length `n`; each value lies in `1..n` and occurs once or twice. Return every value occurring twice, without allocating an additional set or frequency array.

**Examples:** `[4,3,2,7,8,2,3,1]` → `[2,3]` (order flexible); `[1,1,2]` → `[1]`; `[1]` → `[]`.

### Detailed Explanation

**Reasoning and invariant:** Reuse exactly the same value-to-index mapping and mark-on-first-visit operation as 448. The difference is *when* the result is detected: if the marker at `x-1` is already negative while processing `x`, then `x` has appeared before and belongs in the duplicates result.

**Worked example:** For `[4,3,2,7,8,2,3,1]`, the second occurrence of `2` finds index `1` already negative, and the second `3` finds index `2` negative; return `[2,3]`. With `[1]`, the one marker is newly negated and nothing is reported.

**Code evolution and important details:** Only negate a positive marker; negating a negative marker again would destroy the seen indication. The problem guarantees each number occurs at most twice, so a duplicate is added at most once.

**Java Solution:**
```java
import java.util.*;
class Solution {
    public List<Integer> findDuplicates(int[] nums) {
        List<Integer> result = new ArrayList<>();
        for (int i = 0; i < nums.length; i++) {
            int num = Math.abs(nums[i]);
            int index = num - 1;
            if (nums[index] < 0) result.add(num);
            else nums[index] = -nums[index];
        }
        return result;
    }
}
```

**Time Complexity:** O(n).  
**Space Complexity:** O(1) auxiliary; the output list may hold O(n) values.

### LeetCode 645

**Question:** Numbers should contain each integer from 1 to `n` exactly once, but one number is duplicated and another absent. Return the two-element array `[duplicate, missing]`.

**Examples:** `[1,2,2,4]` → `[2,3]`; `[1,1]` → `[1,2]`; `[2,2]` → `[2,1]`.

### Detailed Explanation

**Reasoning and invariant:** This combines the detection times of 442 and 448. During the marking scan, a negative marker identifies the one duplicated number. After all marks, the one remaining positive marker identifies the missing number through index+1. Return the two in the specified order.

**Worked example:** For `[1,2,2,4]`, number `2` is detected twice because its marker is negative on the second visit. After marking, position `2` is positive, so the missing value is `3`; return `[2,3]`.

**Code evolution and important details:** Store the duplicate in a scalar instead of a list, and add the final missing-number scan. Do not restore signs before that second scan, since the signs encode absence.

**Java Solution:**
```java
class Solution {
    public int[] findErrorNums(int[] nums) {
        int duplicate = -1, missing = -1;
        for (int i = 0; i < nums.length; i++) {
            int num = Math.abs(nums[i]);
            int index = num - 1;
            if (nums[index] < 0) duplicate = num;
            else nums[index] = -nums[index];
        }
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] > 0) {
                missing = i + 1;
                break;
            }
        }
        return new int[]{duplicate, missing};
    }
}
```

**Time Complexity:** O(n), two scans.  
**Space Complexity:** O(1), including the fixed two-element result.

## Family F9 — Cyclic Placement

**Detailed subpattern explanation:** Cyclic placement establishes a stronger condition than sign marking: if a valid value `x` has a natural target position, move it there. For the range `1..n`, `x` belongs at `x-1`; for `0..n-1`, `x` belongs at `x`. At each current position, swap until the value is correctly placed, a duplicate blocks it, or the value has no valid target. Do not advance immediately after swapping: the received value has not been processed yet.

**Why the inner swaps remain linear:** Each successful swap fixes at least one new position permanently. There are at most `n` positions to fix, plus at most `n` pointer advances, so the whole process is O(n), even with a `while` loop. Check the allowable value range *before* indexing and check the destination value to avoid infinite loops on duplicates.

**When to apply:** A number `v` has a natural owning index (commonly `v-1` or `v`), and in-place swaps can establish that mapping before searching for a mismatch.

**Basic Java template:**
```java
int i = 0;
while (i < nums.length) {
    int value = nums[i];
    if (value >= 1 && value <= nums.length && nums[value - 1] != value) {
        int target = value - 1;
        int temp = nums[i];
        nums[i] = nums[target];
        nums[target] = temp;
    } else {
        i++;
    }
}
```

**Central invariant:** A successful swap installs at least one value in its owning position. Do not advance `i` immediately after swapping, since the new value at `i` may still require placement; duplicates must not swap indefinitely.

### LeetCode 41

**Question:** From an unsorted array with any 32-bit integers, find the smallest missing *positive* integer in O(n) time and O(1) auxiliary space. The input may contain negatives, zeros, duplicates and values larger than `n`.

**Examples:** `[1,2,0]` → `3`; `[3,4,-1,1]` → `2`; `[7,8,9,11,12]` → `1`; `[1,1]` → `2`.

### Detailed Explanation

**Reasoning and invariant:** Only positive integers from `1` through `n` can prevent the first missing positive from being `1..n`; larger values, negatives and zeros cannot fill any required position. Swap each valid `x` into index `x-1`, staying on the same read index after a swap. Then scan from left for the first place whose value differs from `i+1`.

**Worked example:** For `[3,4,-1,1]`, place `3` into index `2`, place `4` into index `3`, then move `1` into index `0`. The resulting placement `[1,-1,3,4]` disagrees with expected value `2` at index `1`; answer `2`.

**Code evolution and important details:** Test `1<=x<=n` before accessing `nums[x-1]`. If that destination already contains `x`, do not swap; the check prevents a loop on duplicates. This starts cyclic placement rather than sign marking.

**Java Solution:**
```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        int n = nums.length, i = 0;
        while (i < n) {
            int num = nums[i];
            if (num >= 1 && num <= n && nums[num - 1] != num) {
                int target = num - 1;
                int tmp = nums[i];
                nums[i] = nums[target];
                nums[target] = tmp;
            } else {
                i++;
            }
        }
        for (i = 0; i < n; i++) {
            if (nums[i] != i + 1) return i + 1;
        }
        return n + 1;
    }
}
```

**Time Complexity:** O(n); each swap places a value, followed by one linear scan.  
**Space Complexity:** O(1).

### LeetCode 268

**Question:** An array of length `n` contains distinct numbers from the range `0..n`, exactly one of which is missing. Return the missing number. Use in-place cyclic placement for this family (although XOR or arithmetic alternatives exist).

**Examples:** `[3,0,1]` → `2`; `[0,1]` → `2`; `[9,6,4,2,3,5,7,0,1]` → `8`; `[1]` → `0`.

### Detailed Explanation

**Reasoning and invariant:** Values span `0..n`, but an array of length `n` has valid indices only `0..n-1`. Place each value `x<n` at index `x`; treat `n` as a sentinel with no destination. Afterward, find the first index `i` that does not contain `i`.

**Worked example:** For `[3,0,1]`, skip `3` while it has no destination, place `0` at index `0`, and place `1` at index `1`, leaving `[0,1,3]`. Index `2` is incorrect, so the missing number is `2`. If all indices match, the missing number is `n`.

**Code evolution and important details:** This is the exact evolution from 41: change the natural target from `x-1` to `x`, the valid placement range from `1..n` to `0..n-1`, and the final expected value from `i+1` to `i`.

**Java Solution:**
```java
class Solution {
    public int missingNumber(int[] nums) {
        int n = nums.length, i = 0;
        while (i < n) {
            int num = nums[i];
            if (num < n && nums[num] != num) {
                int tmp = nums[i];
                nums[i] = nums[num];
                nums[num] = tmp;
            } else {
                i++;
            }
        }
        for (i = 0; i < n; i++) {
            if (nums[i] != i) return i;
        }
        return n;
    }
}
```

**Time Complexity:** O(n), swaps plus scan.  
**Space Complexity:** O(1).

## Family F10 — Majority Vote

**Detailed subpattern explanation:** Boyer–Moore cancels occurrences of different values to retain only plausible high-frequency candidates. With a threshold above `n/2`, one majority—if it exists—survives pairwise cancellation. With a threshold above `n/3`, there can be at most two qualifying distinct values; extend the algorithm to two counters and cancel triples consisting of two candidates and a third different element. The counters are cancellation balances, not exact frequencies.

**Interview recognition and trade-offs:** Voting is useful when the required threshold is high enough to bound the number of candidate slots and memory must be constant. If the problem promises a majority, the candidate can be returned directly; otherwise a second count pass is mandatory. Always compare with existing candidates before filling a zero-count slot to avoid inconsistent assignment.

**When to apply:** A frequency threshold is high enough that only a few distinct values can qualify, and canceling differing values leaves the possible winners.

**Basic Java template:**
```java
int candidate = 0, votes = 0;
for (int value : nums) {
    if (votes == 0) candidate = value;
    votes += (value == candidate) ? 1 : -1;
}
// Verify the candidate in a second scan unless majority is guaranteed.
```

### LeetCode 169

**Question:** An array contains a guaranteed element appearing strictly more than `n/2` times. Return it.

**Examples:** `[3,2,3]` → `3`; `[2,2,1,1,1,2,2]` → `2`; `[5]` → `5`.

### Detailed Explanation

**Reasoning and invariant:** A value appearing more than half the time outnumbers all other values combined. Pair one vote for the current candidate with a different value to cancel both. When the count becomes zero, select a new candidate; a guaranteed majority cannot be eliminated entirely through such pairings.

**Worked example:** For `[2,2,1,1,1,2,2]`, the first two `2`s build count 2, the next two `1`s cancel it, and the remaining `1,2,2` leave candidate `2`. The returned candidate is valid because the problem guarantees a strict majority exists.

**Code evolution and important details:** This is a new algorithm family. The count is a cancellation balance rather than a frequency count; in an application without a guaranteed majority, recount the final candidate before returning it.

**Java Solution:**
```java
class Solution {
    public int majorityElement(int[] nums) {
        int candidate = 0, votes = 0;
        for (int num : nums) {
            if (votes == 0) candidate = num;
            votes += (num == candidate) ? 1 : -1;
        }
        return candidate;
    }
}
```

**Time Complexity:** O(n).  
**Space Complexity:** O(1).

### LeetCode 229

**Question:** Find every element appearing strictly more than `floor(n/3)` times. The array may have zero, one or two valid answers; return them in any order.

**Examples:** `[3,2,3]` → `[3]`; `[1]` → `[1]`; `[1,2]` → `[1,2]`; `[1,2,3,4]` → `[]`; `[1,2,3,1,2,1,2]` → `[1,2]` (order may vary).

### Detailed Explanation

**Reasoning and invariant:** With threshold strictly above `n/3`, at most two distinct values can qualify. Maintain two candidate slots and their cancellation counts. A number matching either candidate increases that count; a number arriving while a counter is empty occupies its slot; a third distinct value when both counts are active decrements both counts (removing one effective triple).

**Worked example:** For `[1,2,3,1,2,1,2]`, the `1,2,3` group cancels, and subsequent appearances leave `1` and `2` as candidates. Recounting finds each appears three times, greater than `floor(7/3)=2`, so both are returned.

**Code evolution and important details:** Unlike 169, no candidate is guaranteed to qualify. Perform a second pass for actual frequencies, then compare each against `n/3`. Check equality to existing candidates before testing empty slots, so a repeated candidate is never assigned inconsistently.

**Java Solution:**
```java
import java.util.*;
class Solution {
    public List<Integer> majorityElement(int[] nums) {
        int candidate1 = 0, candidate2 = 1;
        int count1 = 0, count2 = 0;
        for (int num : nums) {
            if (num == candidate1) count1++;
            else if (num == candidate2) count2++;
            else if (count1 == 0) {
                candidate1 = num;
                count1 = 1;
            } else if (count2 == 0) {
                candidate2 = num;
                count2 = 1;
            } else {
                count1--;
                count2--;
            }
        }
        count1 = 0;
        count2 = 0;
        for (int num : nums) {
            if (num == candidate1) count1++;
            else if (num == candidate2) count2++;
        }
        List<Integer> result = new ArrayList<>();
        int threshold = nums.length / 3;
        if (count1 > threshold) result.add(candidate1);
        if (count2 > threshold) result.add(candidate2);
        return result;
    }
}
```

**Time Complexity:** O(n), voting and verification.  
**Space Complexity:** O(1), including at most two returned values.

## Family F11 — Digit-Array Carry

**Detailed subpattern explanation:** Decimal digits are stored most-significant first, but addition starts at the least-significant end. For each column, combine the current digit with carry or the corresponding addend digit, emit `sum % 10`, and propagate `sum / 10`. Adding exactly one is a special case: turn trailing nines into zeros and increment the first non-nine encountered. If every digit is nine, the result requires an additional leading one.

**Interview recognition and trade-offs:** Avoid converting long digit arrays to `int` or `long`; those types overflow for sufficiently long inputs. When adding an arbitrary integer `k`, you can carry the still-unprocessed addend and carry together as `k`: emit `k % 10`, then divide by ten. Build a list backward and reverse it (or use a deque) because the final representation must be most-significant first.

**When to apply:** A nonnegative integer is represented as individual base-10 digits, so addition must propagate right-to-left carries without converting the entire number to `long` or `BigInteger`.

**Basic Java template:**
```java
int carry = 1; // adapt to the actual addend
for (int i = digits.length - 1; i >= 0 && carry > 0; i--) {
    int sum = digits[i] + carry;
    digits[i] = sum % 10;
    carry = sum / 10;
}
// If carry remains beyond index 0, prepend another digit.
```

### LeetCode 66

**Question:** Given a nonempty array of base-10 digits, most significant first and without leading zero, add exactly one and return the resulting digit array. The original number can exceed integer type limits.

**Examples:** `[1,2,3]` → `[1,2,4]`; `[4,3,2,1]` → `[4,3,2,2]`; `[9]` → `[1,0]`; `[9,9,9]` → `[1,0,0,0]`.

### Detailed Explanation

**Reasoning and invariant:** Adding one begins at the units digit. A digit below nine can be incremented without any carry, finishing the operation. A nine becomes zero, and the carry continues one place left. If every digit is nine, the number gains a new leading digit `1`.

**Worked example:** For `[1,2,9,9]`, the two trailing nines turn into zeros, then the `2` becomes `3`; return `[1,3,0,0]`. For `[9,9,9]`, all slots become zero and the result is `[1,0,0,0]` in a newly allocated array.

**Code evolution and important details:** The source may represent far more than a `long` can hold; do not parse it. This is the foundation of digit-array carry. Extra allocation happens only if the length increases.

**Java Solution:**
```java
class Solution {
    public int[] plusOne(int[] digits) {
        for (int i = digits.length - 1; i >= 0; i--) {
            if (digits[i] < 9) {
                digits[i]++;
                return digits;
            }
            digits[i] = 0;
        }
        int[] result = new int[digits.length + 1];
        result[0] = 1;
        return result;
    }
}
```

**Time Complexity:** O(n) worst case, O(1) when the last digit is under 9.  
**Space Complexity:** O(1) excluding result; O(n) output allocation only when all digits are nine.

### LeetCode 989

**Question:** An array `num` holds the decimal digits of a nonnegative integer in most-significant-first order. Add a second nonnegative integer `k` and return the resulting digit list. Do not convert the digit array into a primitive integer.

**Examples:** `num=[1,2,0,0], k=34` → `[1,2,3,4]`; `num=[2,7,4], k=181` → `[4,5,5]`; `num=[2,1,5], k=806` → `[1,0,2,1]`; `num=[9,9,9], k=1` → `[1,0,0,0]`.

**Constraints:** `1 <= num.length <= 10^4`; digits are 0..9; `num` has no leading zeros; `0 <= k <= 10^4`.

### Detailed Explanation

**Reasoning and invariant:** Generalize the previous plus-one carry to an arbitrary nonnegative integer `k`. Starting from the final array digit, add that digit into the current `k`; emit the units digit with `%10`; update `k` with integer division by ten. Here `k` simultaneously represents unprocessed higher-order addend digits and the carry. Continue until both the input digits and `k` are exhausted.

**Worked example:** For `[2,7,4]` and `k=181`: from the units place, `181+4=185` emits `5`, leaving `18`; then `18+7=25` emits `5`, leaving `2`; then `2+2=4` emits `4`. Reverse collected digits `[5,5,4]` to `[4,5,5]`.

**Code evolution and important details:** Compared with 66, the addend can span several columns; a loop over both remaining digits and remaining `k` replaces the early-return logic. The output list grows as needed, including a final carry beyond the original most-significant digit.

**Java Solution:**
```java
import java.util.*;
class Solution {
    public List<Integer> addToArrayForm(int[] num, int k) {
        List<Integer> result = new ArrayList<>();
        int i = num.length - 1;
        while (i >= 0 || k > 0) {
            if (i >= 0) {
                k += num[i];
                i--;
            }
            result.add(k % 10);
            k /= 10;
        }
        Collections.reverse(result);
        return result;
    }
}
```

**Time Complexity:** O(n + digits(k)), including output reversal; under the given bounds, O(n).  
**Space Complexity:** O(n + digits(k)) for the returned list; O(1) auxiliary beyond output.

