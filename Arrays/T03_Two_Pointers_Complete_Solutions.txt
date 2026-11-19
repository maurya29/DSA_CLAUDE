# T03 — Two Pointers: Complete Solutions

> **Source:** `MASTER_DSA_CURRICULUM.md` v1.3, locked T03 registry (P1–P10).
> **Scope:** All 26 locked questions. This is a reference workbook, not a claim that the questions have been practiced or completed.
> **Language:** Java 21-compatible solutions using the official LeetCode method signatures. Each solution is independent.

## Table of Contents

- [P1 — Symmetric swaps and constrained reversal](#p1-symmetric-swaps-and-constrained-reversal) — [LC 344](#leetcode-344), [LC 345](#leetcode-345), [LC 917](#leetcode-917), [LC 541](#leetcode-541)
- [P2 — Forward alignment of ordered sequences](#p2-forward-alignment-of-ordered-sequences) — [LC 392](#leetcode-392), [LC 2486](#leetcode-2486), [LC 2337](#leetcode-2337)
- [P3 — Reverse scanning with virtual deletions](#p3-reverse-scanning-with-virtual-deletions) — [LC 844](#leetcode-844)
- [P4 — Sorted exact-sum search and k-sum anchoring](#p4-sorted-exact-sum-search-and-k-sum-anchoring) — [LC 167](#leetcode-167), [LC 15](#leetcode-15), [LC 16](#leetcode-16), [LC 18](#leetcode-18)
- [P5 — Sorted two-pointer bulk counting](#p5-sorted-two-pointer-bulk-counting) — [LC 2824](#leetcode-2824), [LC 2563](#leetcode-2563), [LC 611](#leetcode-611), [LC 1498](#leetcode-1498)
- [P6 — In-place parity / three-way partition](#p6-in-place-parity--three-way-partition) — [LC 905](#leetcode-905), [LC 75](#leetcode-75)
- [P7 — Reverse-fill merging / magnitude comparison](#p7-reverse-fill-merging--magnitude-comparison) — [LC 977](#leetcode-977), [LC 88](#leetcode-88)
- [P8 — Opposite ends with boundary optimization](#p8-opposite-ends-with-boundary-optimization) — [LC 11](#leetcode-11), [LC 42](#leetcode-42)
- [P9 — Slow/fast traversal of implicit successor graph](#p9-slowfast-traversal-of-implicit-successor-graph) — [LC 202](#leetcode-202), [LC 287](#leetcode-287), [LC 457](#leetcode-457)
- [P10 — Implicit monotone endpoints](#p10-implicit-monotone-endpoints) — [LC 633](#leetcode-633)

---

## P1 — Symmetric swaps and constrained reversal

### When to Apply

Use when the relative order of a chosen set of characters must be reversed, and untouched characters must retain their exact positions. Two cursors begin at opposite ends, skip ineligible positions when necessary, exchange eligible values, and converge. For block reversals, apply the same primitive within separately bounded intervals.

### Basic Java Code Snippet

```java
static void reverse(char[] a, int left, int right) {
    while (left < right) {
        char t = a[left];
        a[left++] = a[right];
        a[right--] = t;
    }
}
// For selective reversal, skip noneligible positions before a swap.
```

### Core Logic

At every swap, positions strictly outside the current `[left,right]` interval already have their final contents; the remaining reversible positions lie inside.

### Questions Covered

LeetCode 344, LeetCode 345, LeetCode 917, LeetCode 541

---

### LeetCode 344

**Question:**

Given an array of characters `s`, reverse the entire array **in place**. The function must change the existing array rather than return a new string or a second character array; it must use only constant extra memory.

**Examples:**

1. **Input:** `s = ['h','e','l','l','o']`  
   **Output:** `s = ['o','l','l','e','h']`  
   **Explanation:** The first and last characters trade positions, then the next pair.

2. **Input:** `s = ['H','a','n','n','a','h']`  
   **Output:** `s = ['h','a','n','n','a','H']`  
   **Explanation:** The two middle `n` characters remain in place.

3. **Input:** `s = ['a']`  
   **Output:** `s = ['a']`  
   **Explanation:** A single character needs no swap.

**Constraints:**

`1 <= s.length <= 10^5`; each `s[i]` is a printable ASCII character.

**Why This Subpattern Applies:**

All positions are eligible for reversal. Every mirrored swap permanently places the correct values at both ends of the unfinished interval.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public void reverseString(char[] s) {
        int left = 0, right = s.length - 1;
        while (left < right) {
            char temp = s[left];
            s[left++] = s[right];
            s[right--] = temp;
        }
    }
}
```

**Key Logic:**

The two cursors start at the ends; after swapping, both move inward. When they meet or cross, every mirrored pair has been handled.

**Time Complexity:**

O(n): at most floor(n/2) swaps.

**Space Complexity:**

O(1) auxiliary; the supplied array is modified.

---

### LeetCode 345

**Question:**

Given a string `s`, reverse the **order of vowels only** and return the resulting string. Vowels are `a`, `e`, `i`, `o`, `u` in either case; every consonant, digit, space, and punctuation mark must stay in its original index. Uppercase and lowercase vowel characters are moved exactly as they appear.

**Examples:**

1. **Input:** `s = "hello"`  
   **Output:** `"holle"`  
   **Explanation:** The vowels `e,o` exchange positions.

2. **Input:** `s = "leetcode"`  
   **Output:** `"leotcede"`  
   **Explanation:** The vowel sequence `e,e,o,e` becomes `e,o,e,e`.

3. **Input:** `s = "aA"`  
   **Output:** `"Aa"`  
   **Explanation:** Both characters are vowels; preserve their original cases when swapping.

**Constraints:**

`1 <= s.length <= 3 * 10^5`; `s` consists of printable ASCII characters.

**Why This Subpattern Applies:**

Only eligible positions may change. Opposite pointers can skip non-vowels without allocating an index list.

**Connection to Previous Solution:**

Keep the mirrored swap from 344, but add `while` loops that advance `left` and `right` past non-vowels before exchanging characters.

**Java Solution:**

```java
class Solution {
    public String reverseVowels(String s) {
        char[] a = s.toCharArray();
        int left = 0, right = a.length - 1;
        while (left < right) {
            while (left < right && !isVowel(a[left])) left++;
            while (left < right && !isVowel(a[right])) right--;
            if (left < right) {
                char temp = a[left];
                a[left++] = a[right];
                a[right--] = temp;
            }
        }
        return new String(a);
    }

    private boolean isVowel(char c) {
        return c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u'
            || c == 'A' || c == 'E' || c == 'I' || c == 'O' || c == 'U';
    }
}
```

**Key Logic:**

Convert immutable `String` to a mutable array. Skip fixed non-vowel positions, swap the next vowel pair, and return a new string. `left < right` prevents crossing on inputs with zero or one vowel.

**Time Complexity:**

O(n): each cursor visits each index at most once.

**Space Complexity:**

O(n) for the mutable character array and returned string; O(1) auxiliary pointer state beyond those representations.

---

### LeetCode 917

**Question:**

Given an ASCII string, reverse the order of **English letters** (`A–Z`, `a–z`) while leaving every other character at exactly the same index. Punctuation, digits, hyphens, and symbols never move. Letter case stays attached to the character being moved.

**Examples:**

1. **Input:** `s = "ab-cd"`  
   **Output:** `"dc-ba"`  
   **Explanation:** The hyphen stays at index 2.

2. **Input:** `s = "a-bC-dEf-ghIj"`  
   **Output:** `"j-Ih-gfE-dCba"`  
   **Explanation:** Letters reverse across the hyphens.

3. **Input:** `s = "Test1ng-Leet=code-Q!"`  
   **Output:** `"Qedo1ct-eeLg=ntse-T!"`  
   **Explanation:** Nonletters retain their indexes.

**Constraints:**

`1 <= s.length <= 100`; `s` contains ASCII characters from ranges `[33,122]` (the stated English-letter definition applies).

**Why This Subpattern Applies:**

The locations that may change are defined by a character predicate, just as for vowels. A left and right cursor find the next movable letters and swap them.

**Connection to Previous Solution:**

Replace `isVowel(...)` from 345 with `Character.isLetter(...)` for the ASCII input; the two skipping loops and swap are unchanged.

**Java Solution:**

```java
class Solution {
    public String reverseOnlyLetters(String s) {
        char[] a = s.toCharArray();
        int left = 0, right = a.length - 1;
        while (left < right) {
            while (left < right && !Character.isLetter(a[left])) left++;
            while (left < right && !Character.isLetter(a[right])) right--;
            if (left < right) {
                char temp = a[left];
                a[left++] = a[right];
                a[right--] = temp;
            }
        }
        return new String(a);
    }
}
```

**Key Logic:**

Nonletters are never written. Pointer movement discards only fixed positions; each swap reverses the remaining letters' relative order.

**Time Complexity:**

O(n): one inward scan.

**Space Complexity:**

O(n) for `char[]` and returned string; O(1) auxiliary pointer state.

---

### LeetCode 541

**Question:**

Given a string `s` and a positive integer `k`, process successive groups of `2k` characters. Reverse only the **first `k` characters** of each complete `2k` group and leave its following `k` unchanged. In the final incomplete group, reverse all remaining characters if fewer than `k` remain, or reverse exactly its first `k` if between `k` and `2k` remain. Return the resulting string.

**Examples:**

1. **Input:** `s = "abcdefg", k = 2`  
   **Output:** `"bacdfeg"`  
   **Explanation:** Reverse `ab`, keep `cd`, reverse `ef`, keep `g`.

2. **Input:** `s = "abcd", k = 2`  
   **Output:** `"bacd"`  
   **Explanation:** The `ab` group reverses and `cd` remains.

3. **Input:** `s = "abcdef", k = 4`  
   **Output:** `"dcbaef"`  
   **Explanation:** Only the first four characters reverse in the incomplete eight-character block.

**Constraints:**

`1 <= s.length <= 10^4`; `s` contains lowercase English letters; `1 <= k <= 10^4`.

**Why This Subpattern Applies:**

Each affected segment is a local reversal, but unrelated middle segments must be untouched. The same two-cursor reverse primitive applies independently to each interval.

**Connection to Previous Solution:**

Replace 917's eligibility-skipping scan with an outer loop `start += 2*k`, and reverse the bounded interval `[start, min(start+k-1,n-1)]`.

**Java Solution:**

```java
class Solution {
    public String reverseStr(String s, int k) {
        char[] a = s.toCharArray();
        for (int start = 0; start < a.length; start += 2 * k) {
            reverse(a, start, Math.min(start + k - 1, a.length - 1));
        }
        return new String(a);
    }

    private void reverse(char[] a, int left, int right) {
        while (left < right) {
            char temp = a[left];
            a[left++] = a[right];
            a[right--] = temp;
        }
    }
}
```

**Key Logic:**

The outer index jumps over the untouched half of every block. Clamping the right endpoint handles a short final block; a group of one is naturally unchanged.

**Time Complexity:**

O(n): disjoint intervals contain at most n reversed characters.

**Space Complexity:**

O(n) for the string-to-array representation and returned string; O(1) additional work state.

---

## P2 — Forward alignment of ordered sequences

### When to Apply

Use when only the relative order of elements matters: you may skip source positions but may not rearrange matched elements. For two transformed configurations, advance independent cursors over ignorable blanks and compare the remaining ordered elements.

### Basic Java Code Snippet

```java
int i = 0, j = 0;
while (i < source.length() && j < target.length()) {
    if (source.charAt(i) == target.charAt(j)) j++;
    i++;
}
// j is the length of the target prefix matched in order.
```

### Core Logic

A target cursor records the longest prefix matched in order. A source character can either extend that prefix or be skipped; once passed it cannot help with an earlier target character.

### Questions Covered

LeetCode 392, LeetCode 2486, LeetCode 2337

---

### LeetCode 392

**Question:**

Given strings `s` and `t`, return whether `s` is a **subsequence** of `t`: it must be possible to delete zero or more characters from `t` while keeping all remaining characters in their original order and obtaining exactly `s`. Matches need not be adjacent. An empty `s` is a subsequence of any `t`.

**Examples:**

1. **Input:** `s = "abc", t = "ahbgdc"`  
   **Output:** `true`  
   **Explanation:** Match `a`, then `b`, then `c` in order.

2. **Input:** `s = "axc", t = "ahbgdc"`  
   **Output:** `false`  
   **Explanation:** The required `x` does not appear.

3. **Input:** `s = "", t = "abc"`  
   **Output:** `true`  
   **Explanation:** There are no required characters.

**Constraints:**

`0 <= s.length <= 100`; `0 <= t.length <= 10^4`; lowercase English letters.

**Why This Subpattern Applies:**

Skipping characters in `t` cannot invalidate an already matched prefix of `s`. A forward target cursor captures the maximum prefix matched so far.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public boolean isSubsequence(String s, String t) {
        int i = 0;
        for (int j = 0; j < t.length() && i < s.length(); j++) {
            if (s.charAt(i) == t.charAt(j)) i++;
        }
        return i == s.length();
    }
}
```

**Key Logic:**

`i` points to the next required character of `s`; a character of `t` advances `i` only on a match. Reaching `s.length()` proves an ordered embedding exists.

**Time Complexity:**

O(|t|), bounded more generally by O(|s|+|t|).

**Space Complexity:**

O(1) auxiliary.

---

### LeetCode 2486

**Question:**

Given nonempty strings `s` and `t`, you may append any characters to the **end** of `s`, one operation per appended character. Return the minimum number of operations needed so that `t` becomes a subsequence of the resulting string. Characters already in `s` may be skipped but not reordered, inserted ahead of the end, or modified.

**Examples:**

1. **Input:** `s = "coaching", t = "coding"`  
   **Output:** `4`  
   **Explanation:** Only the initial `co` of `coding` can be matched in order; append `ding`.

2. **Input:** `s = "abcde", t = "a"`  
   **Output:** `0`  
   **Explanation:** `a` is already a subsequence.

3. **Input:** `s = "z", t = "abcde"`  
   **Output:** `5`  
   **Explanation:** No required prefix is matched; append all five characters.

**Constraints:**

`1 <= s.length, t.length <= 10^5`; both are lowercase English-letter strings.

**Why This Subpattern Applies:**

Only an already matched **prefix** of `t` can be preserved when additions are restricted to the end of `s`. The rest must be appended.

**Connection to Previous Solution:**

Reuse 392's forward scan and matched-prefix counter; return `t.length() - matched` instead of a boolean.

**Java Solution:**

```java
class Solution {
    public int appendCharacters(String s, String t) {
        int matched = 0;
        for (int i = 0; i < s.length() && matched < t.length(); i++) {
            if (s.charAt(i) == t.charAt(matched)) matched++;
        }
        return t.length() - matched;
    }
}
```

**Key Logic:**

Consume every available matching character from `s` in order. The unmatched suffix of `t` is both sufficient and necessary to append.

**Time Complexity:**

O(|s|), with early exit when all of `t` matches.

**Space Complexity:**

O(1) auxiliary.

---

### LeetCode 2337

**Question:**

Two equal-length strings `start` and `target` represent a row containing `L` pieces, `R` pieces, and empty cells `_`. An `L` can move **only left** into an immediately adjacent `_`; an `R` can move **only right** into an adjacent `_`. Pieces cannot pass each other. Determine whether some sequence of legal moves transforms `start` into `target`.

**Examples:**

1. **Input:** `start = "_L__R__R_", target = "L______RR"`  
   **Output:** `true`  
   **Explanation:** `L` moves left and the `R` pieces move right without passing.

2. **Input:** `start = "R_L_", target = "__LR"`  
   **Output:** `false`  
   **Explanation:** The first `R` would have to pass the `L`.

3. **Input:** `start = "_R", target = "R_"`  
   **Output:** `false`  
   **Explanation:** `R` cannot move left.

**Constraints:**

`1 <= start.length == target.length <= 10^5`; both contain only `L`, `R`, and `_`.

**Why This Subpattern Applies:**

After deleting blanks, the sequences of pieces must be identical because pieces cannot cross. Each corresponding `L` can only end at an index no greater than its start; an `R` can only end at an index no smaller.

**Connection to Previous Solution:**

Generalize 2486's one-side skipping to skip `_` **independently in both strings**, compare corresponding pieces, and reject forbidden index directions.

**Java Solution:**

```java
class Solution {
    public boolean canChange(String start, String target) {
        int n = start.length(), i = 0, j = 0;
        while (i < n || j < n) {
            while (i < n && start.charAt(i) == '_') i++;
            while (j < n && target.charAt(j) == '_') j++;
            if (i == n || j == n) return i == n && j == n;

            char piece = start.charAt(i);
            if (piece != target.charAt(j)) return false;
            if (piece == 'L' && i < j) return false;
            if (piece == 'R' && i > j) return false;
            i++;
            j++;
        }
        return true;
    }
}
```

**Key Logic:**

`i,j` refer to corresponding nonblank pieces. Reject different piece orders, a rightward `L`, or a leftward `R`. Having one unmatched piece when the other sequence ends is also impossible.

**Time Complexity:**

O(n): each cursor advances at most n times.

**Space Complexity:**

O(1) auxiliary.

---

## P3 — Reverse scanning with virtual deletions

### When to Apply

Use when backspaces or erase operators only delete earlier characters. Reading from right to left exposes the effect of a backspace before its candidate deleted character, so you need not build an edited string.

### Basic Java Code Snippet

```java
int i = s.length() - 1, pending = 0;
while (i >= 0) {
    char ch = s.charAt(i);
    if (ch == '#') { pending++; i--; }
    else if (pending > 0) { pending--; i--; }
    else break; // Next surviving character.
}
```

### Core Logic

A pending-delete counter determines how many characters encountered while moving left must be ignored. The next nondeleted character can be compared directly against the other input.

### Questions Covered

LeetCode 844

---

### LeetCode 844

**Question:**

Strings `s` and `t` represent text typed into initially empty editors. A normal character is appended; `#` removes the immediately preceding character if one exists, otherwise has no effect. Return whether both editors contain the same final text after processing every character. The inputs may contain repeated and leading `#` characters.

**Examples:**

1. **Input:** `s = "ab#c", t = "ad#c"`  
   **Output:** `true`  
   **Explanation:** Both reduce to `ac`.

2. **Input:** `s = "ab##", t = "c#d#"`  
   **Output:** `true`  
   **Explanation:** Both reduce to empty text.

3. **Input:** `s = "a#c", t = "b"`  
   **Output:** `false`  
   **Explanation:** The resulting strings are `c` and `b`.

**Constraints:**

`1 <= s.length, t.length <= 200`; characters are lowercase English letters or `#`.

**Why This Subpattern Applies:**

An erase marker encountered while scanning backward specifies how many earlier characters must be ignored. This permits direct comparison of survivors without constructing the edited results.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public boolean backspaceCompare(String s, String t) {
        int i = s.length() - 1, j = t.length() - 1;
        while (i >= 0 || j >= 0) {
            i = previousVisible(s, i);
            j = previousVisible(t, j);
            if (i < 0 || j < 0) return i == j;
            if (s.charAt(i) != t.charAt(j)) return false;
            i--;
            j--;
        }
        return true;
    }

    private int previousVisible(String s, int index) {
        int erase = 0;
        while (index >= 0) {
            char c = s.charAt(index);
            if (c == '#') { erase++; index--; }
            else if (erase > 0) { erase--; index--; }
            else return index;
        }
        return -1;
    }
}
```

**Key Logic:**

`previousVisible` consumes a run of deleted characters and returns the next surviving index, or -1. The outer loop compares surviving characters in reverse order; both must exhaust simultaneously.

**Time Complexity:**

O(|s|+|t|): every character is visited at most once.

**Space Complexity:**

O(1) auxiliary (no edited copies).

---

## P4 — Sorted exact-sum search and k-sum anchoring

### When to Apply

Use when sorted values make the effect of a pointer move monotonic: moving left rightward increases a pair sum and moving right leftward decreases it. For three or four selected elements, fix earlier anchors and apply the same two-sum search to the remaining suffix.

### Basic Java Code Snippet

```java
int left = start, right = nums.length - 1;
while (left < right) {
    long sum = (long) nums[left] + nums[right];
    if (sum == target) { /* record pair */ left++; right--; }
    else if (sum < target) left++;
    else right--;
}
// For k-sum, fix one or more anchor indices outside this loop.
```

### Core Logic

Once a sum is too small, the current smallest candidate cannot pair with any available right value to reach a larger target; the symmetric argument holds for sums that are too large.

### Questions Covered

LeetCode 167, LeetCode 15, LeetCode 16, LeetCode 18

---

### LeetCode 167

**Question:**

Given a **nondecreasing sorted** integer array `numbers` and an integer `target`, find two elements at **distinct indices** whose sum is `target`. Exactly one valid answer is guaranteed. Return their positions as a two-element array using **1-based indices** (`index1 < index2`). Do not reuse the same element, and use constant auxiliary memory.

**Examples:**

1. **Input:** `numbers = [2,7,11,15], target = 9`  
   **Output:** `[1,2]`  
   **Explanation:** 2 + 7 = 9.

2. **Input:** `numbers = [2,3,4], target = 6`  
   **Output:** `[1,3]`  
   **Explanation:** 2 + 4 = 6.

3. **Input:** `numbers = [-1,0], target = -1`  
   **Output:** `[1,2]`  
   **Explanation:** -1 + 0 = -1.

**Constraints:**

`2 <= numbers.length <= 3 * 10^4`; `-1000 <= numbers[i], target <= 1000`; sorted nondecreasing; exactly one solution.

**Why This Subpattern Applies:**

Sorting guarantees monotonic pair sums: if the current pair is too small, advancing the smaller endpoint is the only move that can increase it; if too large, decrease the larger endpoint.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0, right = numbers.length - 1;
        while (left < right) {
            int sum = numbers[left] + numbers[right];
            if (sum == target) return new int[]{left + 1, right + 1};
            if (sum < target) left++;
            else right--;
        }
        return new int[0]; // Unreachable under the guaranteed solution.
    }
}
```

**Key Logic:**

Use inward pointers and discard an endpoint that cannot contribute to a solution with any remaining partner. Convert zero-based pointers to one-based indices on success.

**Time Complexity:**

O(n): each move shortens the interval.

**Space Complexity:**

O(1) auxiliary, excluding the fixed-size returned pair.

---

### LeetCode 15

**Question:**

Given an integer array `nums`, return **all distinct triplets of values** `[nums[i], nums[j], nums[k]]` (with distinct positions `i,j,k`) whose sum is zero. Triplets must not be duplicated even when input values repeat. The order of triplets and the order of values within a triplet are irrelevant.

**Examples:**

1. **Input:** `nums = [-1,0,1,2,-1,-4]`  
   **Output:** `[[-1,-1,2],[-1,0,1]]`  
   **Explanation:** Those are the two unique value triplets summing to zero.

2. **Input:** `nums = [0,1,1]`  
   **Output:** `[]`  
   **Explanation:** No three values sum to zero.

3. **Input:** `nums = [0,0,0,0]`  
   **Output:** `[[0,0,0]]`  
   **Explanation:** Multiple index triples represent only one unique value triplet.

**Constraints:**

`3 <= nums.length <= 3000`; `-10^5 <= nums[i] <= 10^5`.

**Why This Subpattern Applies:**

Sorting reduces a fixed-first-element triplet search to the exact-sum two-pointer search from 167. Duplicate suppression is required because answers are unique by values, not by indices.

**Connection to Previous Solution:**

Wrap 167's sorted two-sum loop in a `for` loop fixing `nums[i]`; search for `-nums[i]` in the suffix, and skip repeated anchors and endpoint values after a match.

**Java Solution:**

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> result = new ArrayList<>();
        int n = nums.length;
        for (int i = 0; i < n - 2; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            if (nums[i] > 0) break;
            int left = i + 1, right = n - 1;
            while (left < right) {
                int sum = nums[i] + nums[left] + nums[right];
                if (sum < 0) left++;
                else if (sum > 0) right--;
                else {
                    result.add(Arrays.asList(nums[i], nums[left], nums[right]));
                    left++;
                    right--;
                    while (left < right && nums[left] == nums[left - 1]) left++;
                    while (left < right && nums[right] == nums[right + 1]) right--;
                }
            }
        }
        return result;
    }
}
```

**Key Logic:**

The outer anchor fixes one value and the two pointers solve the remaining pair target. Skipping equal anchors and equal values after recording a hit emits each unique triplet once; positive anchors cannot produce a zero sum in the sorted suffix.

**Time Complexity:**

O(n²): sorting O(n log n), then O(n) per anchor.

**Space Complexity:**

O(log n) to O(n) auxiliary for Java's primitive-array sort implementation; O(k) output triplets, where k is the result count (sorting also mutates input).

---

### LeetCode 16

**Question:**

Given an integer array `nums` and an integer `target`, select **three different positions** and return the sum of their three values that is **closest** to `target` by absolute difference. Exactly one closest sum is guaranteed, so tie-breaking is unnecessary.

**Examples:**

1. **Input:** `nums = [-1,2,1,-4], target = 1`  
   **Output:** `2`  
   **Explanation:** -1 + 2 + 1 = 2, only 1 away.

2. **Input:** `nums = [0,0,0], target = 1`  
   **Output:** `0`  
   **Explanation:** The only triple totals zero.

3. **Input:** `nums = [1,1,1,0], target = -100`  
   **Output:** `2`  
   **Explanation:** The smallest achievable triple is 0 + 1 + 1.

**Constraints:**

`3 <= nums.length <= 500`; `-1000 <= nums[i] <= 1000`; `-10^4 <= target <= 10^4`; closest sum unique.

**Why This Subpattern Applies:**

For each fixed anchor, the sorted suffix gives monotone control of the remaining pair sum. Unlike exact-sum search, examine each candidate's distance and retain the closest.

**Connection to Previous Solution:**

Reuse 15's sorted outer anchor and inward pair-pointer scan, but remove duplicate-result emission; track `best` by absolute deviation and return immediately on exact equality.

**Java Solution:**

```java
import java.util.Arrays;

class Solution {
    public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        int best = nums[0] + nums[1] + nums[2];
        for (int i = 0; i < nums.length - 2; i++) {
            int left = i + 1, right = nums.length - 1;
            while (left < right) {
                int sum = nums[i] + nums[left] + nums[right];
                if (Math.abs(sum - target) < Math.abs(best - target)) best = sum;
                if (sum == target) return target;
                if (sum < target) left++;
                else right--;
            }
        }
        return best;
    }
}
```

**Key Logic:**

Initialize `best` with an existing triplet (not an arbitrary sentinel). After each candidate, move the pointer that can bring the sum toward the target. Return immediately if the difference reaches zero.

**Time Complexity:**

O(n²), including an O(n log n) sort.

**Space Complexity:**

O(log n) to O(n) auxiliary for sorting the primitive array; O(1) other state.

---

### LeetCode 18

**Question:**

Given an integer array `nums` and an integer `target`, return every **unique quadruplet of values** `[a,b,c,d]` drawn from four distinct indices such that `a+b+c+d == target`. Each unique value combination should appear once, even if the input contains duplicate entries. Output ordering is unrestricted.

**Examples:**

1. **Input:** `nums = [1,0,-1,0,-2,2], target = 0`  
   **Output:** `[[-2,-1,1,2],[-2,0,0,2],[-1,0,0,1]]`  
   **Explanation:** Three unique value quadruplets total zero.

2. **Input:** `nums = [2,2,2,2,2], target = 8`  
   **Output:** `[[2,2,2,2]]`  
   **Explanation:** Only one distinct value combination exists.

3. **Input:** `nums = [0,0,0,0], target = 1`  
   **Output:** `[]`  
   **Explanation:** No quadruplet has sum 1.

**Constraints:**

`1 <= nums.length <= 200`; `-10^9 <= nums[i], target <= 10^9`; use 64-bit arithmetic when summing.

**Why This Subpattern Applies:**

A pair of fixed anchors leaves an exact two-sum search in a sorted suffix. Without widening the sum to `long`, four extreme 32-bit values could overflow.

**Connection to Previous Solution:**

Extend 15's unique-triplet structure with a **second anchor loop**; retain duplicate-skipping in both anchor loops and the inner two-pointer search, using `long` for four-term sums.

**Java Solution:**

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        Arrays.sort(nums);
        List<List<Integer>> result = new ArrayList<>();
        int n = nums.length;
        for (int i = 0; i < n - 3; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            for (int j = i + 1; j < n - 2; j++) {
                if (j > i + 1 && nums[j] == nums[j - 1]) continue;
                int left = j + 1, right = n - 1;
                while (left < right) {
                    long sum = (long) nums[i] + nums[j] + nums[left] + nums[right];
                    if (sum < target) left++;
                    else if (sum > target) right--;
                    else {
                        result.add(Arrays.asList(nums[i], nums[j], nums[left], nums[right]));
                        left++;
                        right--;
                        while (left < right && nums[left] == nums[left - 1]) left++;
                        while (left < right && nums[right] == nums[right + 1]) right--;
                    }
                }
            }
        }
        return result;
    }
}
```

**Key Logic:**

The first two indices determine an ordered anchor pair; the innermost loop solves an exact-sum pair problem. Value duplicates are excluded at every selection level. The initial `(long)` cast prevents arithmetic overflow before comparison.

**Time Complexity:**

O(n³): two anchor loops and a linear two-pointer scan; sorting is lower order.

**Space Complexity:**

O(log n) to O(n) sorting auxiliary space; O(k) output for k quadruplets.

---

## P5 — Sorted two-pointer bulk counting

### When to Apply

Use when a sorted array allows one extreme pair to certify many valid combinations. Instead of enumerating each pair or subsequence, account for the whole admissible group at once; other problems adapt the count formula or inequality.

### Basic Java Code Snippet

```java
static long countLess(int[] a, long threshold) {
    int left = 0, right = a.length - 1;
    long count = 0;
    while (left < right) {
        if ((long) a[left] + a[right] < threshold) {
            count += right - left;
            left++;
        } else right--;
    }
    return count;
}
```

### Core Logic

If `sorted[left] + sorted[right] < threshold`, then this same left index pairs validly with every index between left+1 and right: add `right-left`, then increment left.

### Questions Covered

LeetCode 2824, LeetCode 2563, LeetCode 611, LeetCode 1498

---

### LeetCode 2824

**Question:**

Given a list of integers `nums` and an integer `target`, count index pairs `(i,j)` with `0 <= i < j < nums.length` for which `nums[i] + nums[j] < target`. Count **all index pairs**, including pairs formed from equal-valued elements; return the number as an integer.

**Examples:**

1. **Input:** `nums = [-1,1,2,3,1], target = 2`  
   **Output:** `3`  
   **Explanation:** The three valid index pairs are `(0,1)`, `(0,2)`, `(0,4)`.

2. **Input:** `nums = [-6,2,5,-2,-7,-1,3], target = -2`  
   **Output:** `10`  
   **Explanation:** Ten distinct index pairs total less than -2.

3. **Input:** `nums = [1,1,1], target = 3`  
   **Output:** `3`  
   **Explanation:** All three index pairs have sum 2.

**Constraints:**

`1 <= nums.length <= 50`; `-50 <= nums[i], target <= 50`; input is a list of integers.

**Why This Subpattern Applies:**

Once sorted, if the smallest and largest remaining values satisfy the strict bound, the smallest pairs with every interior right index also satisfy it. One update counts all those pairs.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
import java.util.Collections;
import java.util.List;

class Solution {
    public int countPairs(List<Integer> nums, int target) {
        Collections.sort(nums);
        int left = 0, right = nums.size() - 1, count = 0;
        while (left < right) {
            if (nums.get(left) + nums.get(right) < target) {
                count += right - left;
                left++;
            } else {
                right--;
            }
        }
        return count;
    }
}
```

**Key Logic:**

A successful extreme pair represents `right-left` pairs sharing `left`. On failure, even the smallest remaining left value is too large with the current right, so reduce `right`.

**Time Complexity:**

O(n log n) for sorting and O(n) for the scan.

**Space Complexity:**

O(n) auxiliary in the worst case for `Collections.sort` on a List; O(1) scan state; sorting mutates the input list.

---

### LeetCode 2563

**Question:**

Given an integer array `nums` and inclusive bounds `lower` and `upper`, count distinct index pairs `(i,j)` with `i < j` and `lower <= nums[i] + nums[j] <= upper`. Count pairs by indices, not by distinct values, and return a `long` because the count may exceed 32-bit range.

**Examples:**

1. **Input:** `nums = [0,1,7,4,4,5], lower = 3, upper = 6`  
   **Output:** `6`  
   **Explanation:** Six pairs have sums within [3,6].

2. **Input:** `nums = [1,7,9,2,5], lower = 11, upper = 11`  
   **Output:** `1`  
   **Explanation:** Only 9 + 2 equals 11.

3. **Input:** `nums = [0,0,0,0], lower = 0, upper = 0`  
   **Output:** `6`  
   **Explanation:** All C(4,2) pairs count.

**Constraints:**

`1 <= nums.length <= 10^5`; `-10^9 <= nums[i] <= 10^9`; `-10^9 <= lower <= upper <= 10^9`.

**Why This Subpattern Applies:**

The inclusive range count equals the number of sums strictly below `upper+1` minus the number strictly below `lower`. Each term admits the same sorted bulk-pair scan as 2824.

**Connection to Previous Solution:**

Extract 2824's counting loop into `countLess(nums, threshold)`; call it twice and subtract. Widen thresholds, pair sums, and pair counts to `long`.

**Java Solution:**

```java
import java.util.Arrays;

class Solution {
    public long countFairPairs(int[] nums, int lower, int upper) {
        Arrays.sort(nums);
        return countLess(nums, (long) upper + 1) - countLess(nums, lower);
    }

    private long countLess(int[] a, long threshold) {
        int left = 0, right = a.length - 1;
        long count = 0;
        while (left < right) {
            if ((long) a[left] + a[right] < threshold) {
                count += right - left;
                left++;
            } else {
                right--;
            }
        }
        return count;
    }
}
```

**Key Logic:**

`upper + 1L` converts the inclusive upper bound to a strict inequality without overflow. The subtraction removes pairs below the lower bound and retains only in-range sums.

**Time Complexity:**

O(n log n) for sorting plus two O(n) scans.

**Space Complexity:**

O(log n) to O(n) auxiliary for primitive-array sort; O(1) beyond sorting.

---

### LeetCode 611

**Question:**

Given an integer array of nonnegative side lengths, count the number of index triples `(i,j,k)` with distinct indices that can form a **nondegenerate triangle**. The three lengths must be strictly positive and satisfy the strict triangle inequality (the sum of any two sides must exceed the third). Return the count, counting different index selections separately.

**Examples:**

1. **Input:** `nums = [2,2,3,4]`  
   **Output:** `3`  
   **Explanation:** The two index-distinct `(2,3,4)` triples and `(2,2,3)` are valid.

2. **Input:** `nums = [4,2,3,4]`  
   **Output:** `4`  
   **Explanation:** All four possible triples form triangles.

3. **Input:** `nums = [0,1,1,1]`  
   **Output:** `1`  
   **Explanation:** Only the triple of three positive unit lengths works.

**Constraints:**

`1 <= nums.length <= 1000`; `0 <= nums[i] <= 1000`.

**Why This Subpattern Applies:**

For sorted sides `a <= b <= c`, only `a+b > c` needs checking. If `a[left]+a[right] > c`, every index between `left` and `right-1` also forms a valid pair with `right`.

**Connection to Previous Solution:**

Reuse 2563's bulk-count insight but **invert** the successful inequality to `a[left]+a[right] > largest`; fix each largest side in an outer loop and add `right-left` on success, then decrement `right`.

**Java Solution:**

```java
import java.util.Arrays;

class Solution {
    public int triangleNumber(int[] nums) {
        Arrays.sort(nums);
        int count = 0;
        for (int k = nums.length - 1; k >= 2; k--) {
            if (nums[k] == 0) continue;
            int left = 0, right = k - 1;
            while (left < right) {
                if (nums[left] + nums[right] > nums[k]) {
                    count += right - left;
                    right--;
                } else {
                    left++;
                }
            }
        }
        return count;
    }
}
```

**Key Logic:**

Each `k` is the largest side. On success, `right-left` choices of smaller side are valid because the sorted middle side is at least `nums[left]`. Zeros cannot satisfy strict triangle inequality.

**Time Complexity:**

O(n²), after sorting O(n log n).

**Space Complexity:**

O(log n) to O(n) sorting auxiliary space; O(1) scan state.

---

### LeetCode 1498

**Question:**

Given a positive-integer array `nums` and an integer `target`, count **nonempty subsequences** (selections of indices in their original relative order, not necessarily contiguous) whose **minimum plus maximum** value is at most `target`. Different index selections are distinct even if their values match. Return the count modulo `1_000_000_007`.

**Examples:**

1. **Input:** `nums = [3,5,6,7], target = 9`  
   **Output:** `4`  
   **Explanation:** `[3]`, `[3,5]`, `[3,6]`, and `[3,5,6]` qualify.

2. **Input:** `nums = [3,3,6,8], target = 10`  
   **Output:** `6`  
   **Explanation:** Six index selections satisfy the bound.

3. **Input:** `nums = [2,3,3,4,6,7], target = 12`  
   **Output:** `61`  
   **Explanation:** All but two nonempty subsequences qualify.

**Constraints:**

`1 <= nums.length <= 10^5`; `1 <= nums[i] <= 10^6`; `1 <= target <= 10^6`; answer modulo `10^9+7`.

**Why This Subpattern Applies:**

After sorting, if `nums[left] + nums[right] <= target`, every arbitrary selection of interior positions forms a valid subsequence containing the chosen smallest-position element. There are `2^(right-left)` such choices.

**Connection to Previous Solution:**

Change 611's bulk pair count (`right-left`) to precomputed `pow2[right-left]` for optional interior elements, use `min+max <= target`, and advance `left` when the group qualifies.

**Java Solution:**

```java
import java.util.Arrays;

class Solution {
    public int numSubseq(int[] nums, int target) {
        final int MOD = 1_000_000_007;
        Arrays.sort(nums);
        int n = nums.length;
        int[] pow2 = new int[n];
        pow2[0] = 1;
        for (int i = 1; i < n; i++) {
            pow2[i] = (int) (2L * pow2[i - 1] % MOD);
        }
        int left = 0, right = n - 1;
        long answer = 0;
        while (left <= right) {
            if (nums[left] + nums[right] <= target) {
                answer = (answer + pow2[right - left]) % MOD;
                left++;
            } else {
                right--;
            }
        }
        return (int) answer;
    }
}
```

**Key Logic:**

For a qualifying smallest index `left`, choose any subset of indices from `left+1` through `right`: `2^(right-left)` including the singleton. When the extreme pair fails, decrease `right` until all counted selections are valid. `left <= right` includes valid singletons.

**Time Complexity:**

O(n log n) sorting + O(n) power table and two-pointer pass.

**Space Complexity:**

O(n) auxiliary for `pow2`, plus sorting stack; input sorted in place.

---

## P6 — In-place parity / three-way partition

### When to Apply

Use when values must occupy a small number of categories, the input may be changed, and within-category stability is unnecessary. Swap incorrectly placed elements across partition boundaries; a third cursor supports three categories.

### Basic Java Code Snippet

```java
int left = 0, right = nums.length - 1;
while (left < right) {
    while (left < right && nums[left] % 2 == 0) left++;
    while (left < right && nums[right] % 2 != 0) right--;
    if (left < right) {
        int t = nums[left]; nums[left] = nums[right]; nums[right] = t;
    }
}
// Substitute a classification predicate for parity.
```

### Core Logic

Everything left of the processed left boundary is in the left category and everything right of the right boundary is in the right category. The middle is unclassified.

### Questions Covered

LeetCode 905, LeetCode 75

---

### LeetCode 905

**Question:**

Given an integer array `nums`, rearrange its elements so that **all even numbers precede all odd numbers**. The relative order among even values and among odd values does not matter; any valid partition is accepted. Return the rearranged array, and the input may be modified in place.

**Examples:**

1. **Input:** `nums = [3,1,2,4]`  
   **Output:** `[4,2,1,3]`  
   **Explanation:** All even values appear before odd values; other valid orders are accepted.

2. **Input:** `nums = [0]`  
   **Output:** `[0]`  
   **Explanation:** Zero is even.

3. **Input:** `nums = [1,3,5]`  
   **Output:** `[1,3,5]`  
   **Explanation:** An all-odd array is already partitioned.

**Constraints:**

`1 <= nums.length <= 5000`; `0 <= nums[i] <= 5000`.

**Why This Subpattern Applies:**

Only the category of each element matters; maintaining order inside a category is not required. A swap exchanges an odd misplaced on the left with an even misplaced on the right.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int[] sortArrayByParity(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            while (left < right && (nums[left] & 1) == 0) left++;
            while (left < right && (nums[right] & 1) == 1) right--;
            if (left < right) {
                int temp = nums[left];
                nums[left++] = nums[right];
                nums[right--] = temp;
            }
        }
        return nums;
    }
}
```

**Key Logic:**

Move `left` past correctly placed evens and `right` past correctly placed odds; swap when both point at a misplacement. Invariants are `nums[0..left-1]` even and `nums[right+1..n-1]` odd.

**Time Complexity:**

O(n): each pointer travels inward once.

**Space Complexity:**

O(1) auxiliary; sorting happens inside the input array.

---

### LeetCode 75

**Question:**

Given an array `nums` containing only `0`, `1`, and `2`, sort it **in place** so all `0` entries come first, followed by `1` entries, followed by `2` entries. Do not call a general-purpose sorting routine; solve in one linear pass with constant extra storage.

**Examples:**

1. **Input:** `nums = [2,0,2,1,1,0]`  
   **Output:** `[0,0,1,1,2,2]`  
   **Explanation:** All values are grouped in ascending category order.

2. **Input:** `nums = [2,0,1]`  
   **Output:** `[0,1,2]`  
   **Explanation:** Three distinct categories.

3. **Input:** `nums = [1,1,1]`  
   **Output:** `[1,1,1]`  
   **Explanation:** Already entirely in the middle category.

**Constraints:**

`1 <= nums.length <= 300`; each `nums[i]` is 0, 1, or 2.

**Why This Subpattern Applies:**

There are exactly three categories and stability is unnecessary. A Dutch-national-flag scan maintains finalized left and right partitions and an unclassified middle.

**Connection to Previous Solution:**

Extend 905's two-region partition to three regions: introduce `mid` and `low/high`; inspect `nums[mid]`, swap 0 left, advance past 1, or swap 2 right **without incrementing `mid`**.

**Java Solution:**

```java
class Solution {
    public void sortColors(int[] nums) {
        int low = 0, mid = 0, high = nums.length - 1;
        while (mid <= high) {
            if (nums[mid] == 0) {
                swap(nums, low++, mid++);
            } else if (nums[mid] == 1) {
                mid++;
            } else {
                swap(nums, mid, high--);
            }
        }
    }

    private void swap(int[] nums, int a, int b) {
        int temp = nums[a];
        nums[a] = nums[b];
        nums[b] = temp;
    }
}
```

**Key Logic:**

`[0,low)` are zeros, `[low,mid)` ones, `(high,n)` twos, and `[mid,high]` unknown. After swapping with `high`, reconsider `mid` because the incoming element was not classified.

**Time Complexity:**

O(n): each iteration increases `mid` or decreases `high`.

**Space Complexity:**

O(1) auxiliary, in place.

---

## P7 — Reverse-fill merging / magnitude comparison

### When to Apply

Use when the largest next output value is available at one of the edges of sorted inputs. Writing the largest remaining value into the highest unfilled output position avoids extra sorting and may avoid overwriting unread source values.

### Basic Java Code Snippet

```java
int left = 0, right = sorted.length - 1;
int[] out = new int[sorted.length];
for (int write = sorted.length - 1; write >= 0; write--) {
    int x = Math.abs(sorted[left]), y = Math.abs(sorted[right]);
    if (x > y) { out[write] = x * x; left++; }
    else { out[write] = y * y; right--; }
}
// Alternatively merge two sorted source tails into an existing buffer.
```

### Core Logic

The suffix of the output is finalized in sorted order; compare the relevant endpoint candidates and decrement the output cursor after selecting one.

### Questions Covered

LeetCode 977, LeetCode 88

---

### LeetCode 977

**Question:**

Given an integer array `nums` sorted in nondecreasing order, return a **new array** containing the square of each element, also sorted in nondecreasing order. Negative inputs become nonnegative after squaring, so directly squaring in the original order may not produce a sorted result.

**Examples:**

1. **Input:** `nums = [-4,-1,0,3,10]`  
   **Output:** `[0,1,9,16,100]`  
   **Explanation:** The largest magnitude, 10, produces the largest square.

2. **Input:** `nums = [-7,-3,2,3,11]`  
   **Output:** `[4,9,9,49,121]`  
   **Explanation:** Squared magnitudes are reordered.

3. **Input:** `nums = [0,1,2]`  
   **Output:** `[0,1,4]`  
   **Explanation:** Already sorted and nonnegative.

**Constraints:**

`1 <= nums.length <= 10^4`; `-10^4 <= nums[i] <= 10^4`; sorted nondecreasing.

**Why This Subpattern Applies:**

The maximum absolute value among the remaining sorted elements must lie at an endpoint. Writing its square from the output's right end avoids re-sorting.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        int n = nums.length;
        int[] result = new int[n];
        int left = 0, right = n - 1;
        for (int write = n - 1; write >= 0; write--) {
            int leftSquare = nums[left] * nums[left];
            int rightSquare = nums[right] * nums[right];
            if (leftSquare > rightSquare) {
                result[write] = leftSquare;
                left++;
            } else {
                result[write] = rightSquare;
                right--;
            }
        }
        return result;
    }
}
```

**Key Logic:**

Compare squares at the unread ends; the larger is the largest remaining square. Place it in the last empty output slot, then advance the endpoint that supplied it.

**Time Complexity:**

O(n): each input element is selected once.

**Space Complexity:**

O(n) for the required output array; O(1) auxiliary beyond output.

---

### LeetCode 88

**Question:**

Two integer arrays represent nondecreasing sorted sequences. `nums1` has physical length `m+n` but only its **first `m` entries** are valid; its last `n` entries are empty capacity. `nums2` contains `n` valid entries. Merge both sorted sequences into `nums1` in nondecreasing order **in place**. Return nothing; when either valid length is zero, handle it correctly.

**Examples:**

1. **Input:** `nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3`  
   **Output:** `nums1 = [1,2,2,3,5,6]`  
   **Explanation:** The six valid values are merged.

2. **Input:** `nums1 = [1], m = 1, nums2 = [], n = 0`  
   **Output:** `nums1 = [1]`  
   **Explanation:** No second-array entries.

3. **Input:** `nums1 = [0], m = 0, nums2 = [1], n = 1`  
   **Output:** `nums1 = [1]`  
   **Explanation:** The only valid value comes from nums2.

**Constraints:**

`nums1.length == m+n`; `nums2.length == n`; `0 <= m,n <= 200`; `1 <= m+n <= 200`; `-10^9 <= nums1[i], nums2[j] <= 10^9` for valid entries; inputs individually sorted.

**Why This Subpattern Applies:**

The largest remaining element comes from one of the two **last unread** positions. Filling `nums1` from the end prevents destroying unmerged values in its valid prefix.

**Connection to Previous Solution:**

Preserve 977's descending `write` position but replace one array's opposite-end magnitude candidates with **two separate array tails**, and write directly into `nums1` rather than a new output array.

**Java Solution:**

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int i = m - 1, j = n - 1, write = m + n - 1;
        while (j >= 0) {
            if (i >= 0 && nums1[i] > nums2[j]) {
                nums1[write--] = nums1[i--];
            } else {
                nums1[write--] = nums2[j--];
            }
        }
    }
}
```

**Key Logic:**

Write the largest unread value to the rightmost free slot. No further work is needed after `nums2` is exhausted because residual `nums1` elements are already in their correct positions.

**Time Complexity:**

O(m+n) worst case.

**Space Complexity:**

O(1) auxiliary; input `nums1` is the destination.

---

## P8 — Opposite ends with boundary optimization

### When to Apply

Use when the result is bounded by the two current edges: width times limiting height, or water determined by bounding walls. An end can be finalized when moving it cannot discard a better solution given the limiting boundary.

### Basic Java Code Snippet

```java
int left = 0, right = height.length - 1;
while (left < right) {
    if (height[left] <= height[right]) {
        // Resolve or discard left based on the limiting boundary.
        left++;
    } else {
        // Resolve or discard right.
        right--;
    }
}
// Rainwater additionally maintains leftMax and rightMax.
```

### Core Logic

For maximum container area, discard the shorter wall. For trapped water, maintain seen maxima and finalize the side whose maximum is lower.

### Questions Covered

LeetCode 11, LeetCode 42

---

### LeetCode 11

**Question:**

Given an array `height` where `height[i]` is the height of a vertical line at horizontal coordinate `i`, choose **two distinct lines** to form the sides of a container with the x-axis as its base. The container holds water equal to `min(height[i], height[j]) * (j-i)`; it may not be tilted. Return the maximum possible area.

**Examples:**

1. **Input:** `height = [1,8,6,2,5,4,8,3,7]`  
   **Output:** `49`  
   **Explanation:** Lines at indices 1 and 8 have width 7 and limiting height 7.

2. **Input:** `height = [1,1]`  
   **Output:** `1`  
   **Explanation:** Width and limiting height are both 1.

3. **Input:** `height = [4,3,2,1,4]`  
   **Output:** `16`  
   **Explanation:** The endpoint lines have width 4 and height 4.

**Constraints:**

`2 <= height.length <= 10^5`; `0 <= height[i] <= 10^4`.

**Why This Subpattern Applies:**

Area is bounded by the shorter wall. Moving the taller wall inward only reduces width while the shorter wall still limits height, so it cannot improve on the current pair.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public int maxArea(int[] height) {
        int left = 0, right = height.length - 1, best = 0;
        while (left < right) {
            int area = Math.min(height[left], height[right]) * (right - left);
            best = Math.max(best, area);
            if (height[left] <= height[right]) left++;
            else right--;
        }
        return best;
    }
}
```

**Key Logic:**

Evaluate the current widest interval, update `best`, and discard its limiting (shorter or equal) boundary. Width shrinks, but only replacing the limiting wall offers a chance of increasing the minimum height.

**Time Complexity:**

O(n): one endpoint advances per iteration.

**Space Complexity:**

O(1) auxiliary.

---

### LeetCode 42

**Question:**

A row of nonnegative bars has unit width and heights `height[i]`. After rain, water may accumulate **between** taller bars above shorter bars. Compute the total number of unit squares of trapped water over all positions; water cannot be retained beyond the ends. Return the integer total.

**Examples:**

1. **Input:** `height = [0,1,0,2,1,0,1,3,2,1,2,1]`  
   **Output:** `6`  
   **Explanation:** The depressions between taller boundaries retain six units.

2. **Input:** `height = [4,2,0,3,2,5]`  
   **Output:** `9`  
   **Explanation:** The right boundary of height 5 traps nine units across interior bars.

3. **Input:** `height = [1,2,3]`  
   **Output:** `0`  
   **Explanation:** Monotonically rising bars cannot trap water.

**Constraints:**

`1 <= height.length <= 2 * 10^4`; `0 <= height[i] <= 10^5`.

**Why This Subpattern Applies:**

Water at an index depends on the smaller of the best left and right boundary heights. Maintain both running maxima and finalize the side whose seen maximum is lower; that side's trapping capacity is determined.

**Connection to Previous Solution:**

Replace 11's single container-area calculation with `leftMax/rightMax` running states and per-position `boundaryMax - height[index]` accumulation; keep the inward two-pointer structure.

**Java Solution:**

```java
class Solution {
    public int trap(int[] height) {
        int left = 0, right = height.length - 1;
        int leftMax = 0, rightMax = 0, water = 0;
        while (left <= right) {
            if (leftMax <= rightMax) {
                leftMax = Math.max(leftMax, height[left]);
                water += leftMax - height[left];
                left++;
            } else {
                rightMax = Math.max(rightMax, height[right]);
                water += rightMax - height[right];
                right--;
            }
        }
        return water;
    }
}
```

**Key Logic:**

`leftMax` and `rightMax` bound the lower side's water level. On that side, updating its maximum before adding water guarantees a nonnegative contribution. Each position is processed once, including monotone or flat edges.

**Time Complexity:**

O(n): process each index once.

**Space Complexity:**

O(1) auxiliary.

---

## P9 — Slow/fast traversal of implicit successor graph

### When to Apply

Use when each state maps deterministically to exactly one following state, so repeated application eventually ends or loops. Floyd pointers find cycles using constant storage; additional rules may identify cycle entry or reject invalid cycles.

### Basic Java Code Snippet

```java
int slow = start, fast = start;
do {
    slow = next(slow);
    fast = next(next(fast));
} while (slow != fast);
// next is problem-specific. Check invalid transitions where applicable.
```

### Core Logic

A fast cursor traverses two successor edges for each slow edge. If a cycle is reachable, their relative positions eventually coincide; for entry-finding, restart one at the head and move both one edge per step.

### Questions Covered

LeetCode 202, LeetCode 287, LeetCode 457

---

### LeetCode 202

**Question:**

A positive integer is called **happy** if repeatedly replacing it by the **sum of the squares of its decimal digits** eventually produces 1. Once 1 is reached, further replacements still produce 1. Otherwise the sequence repeats within a cycle that never reaches 1. Given `n`, return whether it is happy; avoid a set by using constant-memory cycle detection.

**Examples:**

1. **Input:** `n = 19`  
   **Output:** `true`  
   **Explanation:** 19 → 82 → 68 → 100 → 1.

2. **Input:** `n = 2`  
   **Output:** `false`  
   **Explanation:** The digit-square sequence enters a repeating cycle without reaching 1.

3. **Input:** `n = 1`  
   **Output:** `true`  
   **Explanation:** Already at the terminating fixed point.

**Constraints:**

`1 <= n <= 2^31-1`.

**Why This Subpattern Applies:**

The digit-square function deterministically maps each state to its successor. If the chain never reaches 1, it must enter a cycle; Floyd's slow/fast pointers detect it without storing previous values.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public boolean isHappy(int n) {
        int slow = n, fast = n;
        do {
            slow = next(slow);
            fast = next(next(fast));
        } while (slow != fast);
        return slow == 1;
    }

    private int next(int n) {
        int sum = 0;
        while (n > 0) {
            int digit = n % 10;
            sum += digit * digit;
            n /= 10;
        }
        return sum;
    }
}
```

**Key Logic:**

`next` generates one digit-square successor. The fast pointer traverses two links per iteration; they meet at 1 for happy numbers or inside an alternative cycle otherwise. Even the largest 32-bit input maps to a small bounded sum after one step.

**Time Complexity:**

O(log n + 1) for fixed-width integer inputs: first digit processing is O(log n), after which the state is at most 810 and Floyd executes over a bounded state graph; commonly described as O(log n) time under fixed int constraints.

**Space Complexity:**

O(1) auxiliary.

---

### LeetCode 287

**Question:**

An array `nums` has length `n+1`, and every entry is an integer from `1` through `n` inclusive. **Exactly one distinct value is repeated**, possibly more than twice. Find and return that repeated value. Do not change the array and use only constant extra memory; sorting the input or building a set is not allowed.

**Examples:**

1. **Input:** `nums = [1,3,4,2,2]`  
   **Output:** `2`  
   **Explanation:** The value 2 occurs twice.

2. **Input:** `nums = [3,1,3,4,2]`  
   **Output:** `3`  
   **Explanation:** The value 3 occurs twice.

3. **Input:** `nums = [3,3,3,3,3]`  
   **Output:** `3`  
   **Explanation:** All entries contain the one repeated value.

**Constraints:**

`1 <= n <= 10^5`; `nums.length == n+1`; `1 <= nums[i] <= n`; exactly one repeated **value**; array read-only; O(1) auxiliary required.

**Why This Subpattern Applies:**

Treat index `i` as a node and `nums[i]` as its successor. Because every successor is within indices `1..n`, the walk starting at index 0 must enter a cycle, and the cycle entry corresponds to the duplicated value.

**Connection to Previous Solution:**

Replace 202's digit-square successor `next(x)` with `nums[x]`. After fast/slow meet, add the **second Floyd phase**: reset one pointer to index 0 and advance both one link at a time until they meet at the cycle entrance.

**Java Solution:**

```java
class Solution {
    public int findDuplicate(int[] nums) {
        int slow = nums[0];
        int fast = nums[0];
        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
        } while (slow != fast);

        int entry = nums[0];
        while (entry != slow) {
            entry = nums[entry];
            slow = nums[slow];
        }
        return entry;
    }
}
```

**Key Logic:**

The first phase finds a node inside the successor cycle. Starting another pointer at `nums[0]` and moving both one edge per step locates its entry, which is the repeated number. No array element is modified.

**Time Complexity:**

O(n): the functional graph walk and entry-finding each take linear time at most.

**Space Complexity:**

O(1) auxiliary, read-only input.

---

### LeetCode 457

**Question:**

Given a circular array of nonzero signed integers `nums`, position `i` moves to `(i + nums[i]) mod n`, wrapping around in either direction. Determine whether **some cycle** exists such that (1) it visits **more than one distinct index**, and (2) every step moves in the **same direction** (all positive or all negative). A one-index self-loop or a direction-changing loop is invalid. You may modify the array to mark paths known not to yield a valid cycle.

**Examples:**

1. **Input:** `nums = [2,-1,1,2,2]`  
   **Output:** `true`  
   **Explanation:** A length-greater-than-one cycle exists using only positive moves.

2. **Input:** `nums = [-1,-2,-3,-4,-5,6]`  
   **Output:** `false`  
   **Explanation:** Direction changes or a self-loop prevent a valid cycle.

3. **Input:** `nums = [1,-1,5,1,4]`  
   **Output:** `true`  
   **Explanation:** A positive-direction multi-index cycle exists.

**Constraints:**

`1 <= nums.length <= 5000`; `-1000 <= nums[i] <= 1000`; `nums[i] != 0` initially. Circular transitions use true nonnegative modular indices.

**Why This Subpattern Applies:**

Every index has one potential successor, making Floyd appropriate, but valid edges depend on the starting sign and must not form self-loops. Rejected paths can be marked to prevent revisiting them from other starts.

**Connection to Previous Solution:**

Replace 287's `nums[index]` successor with a **sign-aware, wrapped** `next` function that rejects self-loops and opposite-direction transitions; apply Floyd from each unmarked start and mark invalid paths as zero for O(n) total exploration.

**Java Solution:**

```java
class Solution {
    public boolean circularArrayLoop(int[] nums) {
        int n = nums.length;
        for (int start = 0; start < n; start++) {
            if (nums[start] == 0) continue;
            boolean forward = nums[start] > 0;
            int slow = start, fast = start;

            while (true) {
                slow = next(nums, forward, slow);
                if (slow == -1) break;
                fast = next(nums, forward, fast);
                if (fast == -1) break;
                fast = next(nums, forward, fast);
                if (fast == -1) break;
                if (slow == fast) return true;
            }

            int i = start;
            while (nums[i] != 0 && (nums[i] > 0) == forward) {
                int following = Math.floorMod(i + nums[i], n);
                nums[i] = 0;
                i = following;
            }
        }
        return false;
    }

    private int next(int[] nums, boolean forward, int i) {
        if (nums[i] == 0 || (nums[i] > 0) != forward) return -1;
        int j = Math.floorMod(i + nums[i], nums.length);
        if (i == j || nums[j] == 0 || (nums[j] > 0) != forward) return -1;
        return j;
    }
}
```

**Key Logic:**

`next` permits only sign-consistent transitions to distinct indices. A matching slow/fast pair proves a same-direction cycle of length at least two. After a failed attempt, zero-mark the same-direction path from its start; those visited nodes cannot be part of a valid cycle, preventing repeated scanning.

**Time Complexity:**

O(n): every unsuccessful start causes its reachable directed path to be zero-marked once; Floyd advances only along paths that have not yet been marked.

**Space Complexity:**

O(1) auxiliary; the input array is modified for marking.

---

## P10 — Implicit monotone endpoints

### When to Apply

Use when candidates form monotone sequences but need not be stored. Generate each endpoint on demand (here squares of nonnegative integers) and eliminate an endpoint based on comparison with a target.

### Basic Java Code Snippet

```java
long left = 0, right = (long) Math.sqrt(c);
while (left <= right) {
    long sum = left * left + right * right;
    if (sum == c) break;
    if (sum < c) left++;
    else right--;
}
```

### Core Logic

The candidate sum increases when left increases and decreases when right decreases, so discarding one extreme preserves the possibility of finding a match.

### Questions Covered

LeetCode 633

---

### LeetCode 633

**Question:**

Given a nonnegative integer `c`, determine whether two **nonnegative integers** `a` and `b` exist such that `a*a + b*b == c`. Both squares are allowed to be equal, and either integer may be zero. Return a boolean without materializing all squares.

**Examples:**

1. **Input:** `c = 5`  
   **Output:** `true`  
   **Explanation:** 1² + 2² = 5.

2. **Input:** `c = 3`  
   **Output:** `false`  
   **Explanation:** No pair of nonnegative integer squares sums to 3.

3. **Input:** `c = 0`  
   **Output:** `true`  
   **Explanation:** 0² + 0² = 0.

**Constraints:**

`0 <= c <= 2^31-1`; square computations must not overflow signed 32-bit integers.

**Why This Subpattern Applies:**

The possible values `0²,1²,...,floor(sqrt(c))²` are monotonically increasing and can be computed by index. Move the smaller endpoint right to increase the sum, or the larger endpoint left to decrease it.

**Connection to Previous Solution:**

Foundation of this subpattern.

**Java Solution:**

```java
class Solution {
    public boolean judgeSquareSum(int c) {
        long left = 0;
        long right = (long) Math.sqrt(c);
        while (left <= right) {
            long sum = left * left + right * right;
            if (sum == c) return true;
            if (sum < c) left++;
            else right--;
        }
        return false;
    }
}
```

**Key Logic:**

Generate candidate squares without allocating an array. Cast endpoints to `long` so even the sum of two near-limit squares is safe; `left <= right` permits equal-square representations and the `c=0` case.

**Time Complexity:**

O(sqrt(c)): at most floor(sqrt(c))+1 endpoint moves.

**Space Complexity:**

O(1) auxiliary.

---

## Final Question Registry

The following IDs are included exactly once, in the same family and order as the locked T03 registry.

| Order | Family | LeetCode |
|---:|---|---:|
| 01 | P1 | 344 |
| 02 | P1 | 345 |
| 03 | P1 | 917 |
| 04 | P1 | 541 |
| 05 | P2 | 392 |
| 06 | P2 | 2486 |
| 07 | P2 | 2337 |
| 08 | P3 | 844 |
| 09 | P4 | 167 |
| 10 | P4 | 15 |
| 11 | P4 | 16 |
| 12 | P4 | 18 |
| 13 | P5 | 2824 |
| 14 | P5 | 2563 |
| 15 | P5 | 611 |
| 16 | P5 | 1498 |
| 17 | P6 | 905 |
| 18 | P6 | 75 |
| 19 | P7 | 977 |
| 20 | P7 | 88 |
| 21 | P8 | 11 |
| 22 | P8 | 42 |
| 23 | P9 | 202 |
| 24 | P9 | 287 |
| 25 | P9 | 457 |
| 26 | P10 | 633 |

**Verification:** 26 / 26 locked IDs present; 10 / 10 families present; no additional or duplicated IDs.

**Practice status:** Unchanged. Generation of this workbook does not mark any question as completed.
