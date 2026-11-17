# T01 — Arrays: Cumulative Solutions

**Curriculum:** MASTER_DSA_CURRICULUM.md v1.1 | **Completed:** 29/29 | **Status:** COMPLETE (all 11 locked families)

## Family F1 — Extremum Tracking

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

