# T02 — Strings | Complete Solution-Based Progressive Workbook

**Curriculum:** `MASTER_DSA_CURRICULUM.md` v1.2 — locked T02 sequence, unchanged.  
**Prepared:** 18 November 2026 (Asia/Kolkata).  
**Coverage:** All **35** planned LeetCode questions, grouped into **18 algorithm families (S1–S18)**.  
**Actual practice progress as of preparation:** **19/35 completed** (through LeetCode 28). Solutions for questions 20–35 are included here as **advance reference**, not marked as practiced or completed.  
**Format for every family:** recognition clue + reusable Java 21 starter snippet; for every question: number, full plain-language statement, examples, applicable constraints, pattern fit, exact solution evolution, complete Java 21 LeetCode code, key logic, time and space complexity.  
**Time/space note:** `n`, `m`, and `L` are defined locally as needed; space refers to auxiliary space unless result/output storage is expressly included.  
**How to use:** Each code block labeled `Java Solution` is a separate LeetCode submission (`class Solution`). Study families in the locked order. This file is a downloadable copy; it does **not** alter Project files or progress automatically.

## Locked sequence at a glance

| Family | LeetCode IDs (exact order) |
|---|---|
| S1 Character-wise decoding/transformation | 709 → 1844 → 1309 |
| S2 Dual-input character merge | 1768 |
| S3 Word-boundary scanning | 58 → 151 → 557 |
| S4 Run-length scanning/emission | 443 → 38 |
| S5 Normalized palindrome comparison | 125 → 680 |
| S6 Palindrome center expansion | 647 → 5 |
| S7 Digit-string addition/carry | 415 → 67 |
| S8 Grade-school multiplication | 43 |
| S9 String-to-number lexical state | 8 → 65 |
| S10 KMP matching/borders | 28 → 459 → 1392 → 214 → 686 |
| S11 Z-function prefix alignment | 2223 → 3031 |
| S12 Rolling hash | 187 → 1044 |
| S13 Character contributions | 2262 → 828 |
| S14 Segmented formatting | 1592 → 482 |
| S15 Index-to-output layout | 6 |
| S16 Single-character lexicographic change | 1328 |
| S17 Periodic-string compatibility | 1071 |
| S18 String rotation via KMP | 796 |

---

## S1 — Character-wise decoding / transformation

**When to apply:** Each encoded character (or short token) independently produces an output character; preserve input order. Look ahead if token width changes.

**Basic Java 21 template (not a complete solution):**

```java
StringBuilder out = new StringBuilder();
for (int i = 0; i < s.length(); ) {
    char ch = s.charAt(i);
    out.append(ch);  // Substitute conversion; look ahead when needed.
    i++;             // Advance by decoded token width.
}
```

### LeetCode 709

**Question:** Given a string containing printable ASCII characters, return an equivalent string with every uppercase English letter (`A`–`Z`) replaced by its lowercase version. Leave all other characters unchanged.

**Examples:** `"Hello" → "hello"`; `"here" → "here"`; `"LOVELY" → "lovely"`.

**Constraints:** `1 <= s.length <= 100`; printable ASCII input.

**Why this subpattern applies:** Every character maps independently to an output character; a single scan and builder suffice.

**Connection to previous solution:** Foundation of this subpattern. Introduces scan → conditional character mapping → append.

**Java solution:**

```java
class Solution {
    public String toLowerCase(String s) {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            if (ch >= 'A' && ch <= 'Z') {
                ch = (char) (ch + 32);
            }
            sb.append(ch);
        }
        return sb.toString();
    }
}
```

**Key explanation:** Uppercase and lowercase English ASCII letters differ by 32; the range test prevents changing other characters.

**Time complexity:** `O(n)`; **space complexity:** `O(n)` for the result.

### LeetCode 1844

**Question:** Given a string alternating lowercase letters at even indices and digits at odd indices, replace each digit with the letter produced by shifting its immediately preceding letter forward by that digit's integer value. The result is guaranteed to remain within `a`–`z`.

**Examples:** `"a1c1e1" → "abcdef"`; `"a1b2c3d4e" → "abbdcfdhe"`.

**Constraints:** `1 <= s.length <= 100`; even positions are lowercase letters and odd positions are digits `0`–`9`.

**Why this subpattern applies:** Each input position yields one output character; digit positions additionally depend on the preceding source character.

**Connection to previous solution (709 → 1844):** Replace uppercase-range condition with odd-index condition; compute shifted output from `s.charAt(i - 1)` and numeric digit value.

**Java solution:**

```java
class Solution {
    public String replaceDigits(String s) {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            if (i % 2 == 1) {
                ch = (char) (s.charAt(i - 1) + (ch - '0'));
            }
            sb.append(ch);
        }
        return sb.toString();
    }
}
```

**Key explanation:** `ch - '0'` obtains the integer value of a digit character; add that offset to the preceding letter.

**Time complexity:** `O(n)`; **space complexity:** `O(n)`.

### LeetCode 1309

**Question:** Decode a string where `1`–`9` mean `a`–`i`, and `10#`–`26#` mean `j`–`z`. Return the decoded lowercase text. The input is always a valid encoding.

**Examples:** `"10#11#12" → "jkab"`; `"1326#" → "acz"`; `"25#" → "y"`.

**Constraints:** `1 <= s.length <= 1000`; string contains digits and `#` and has a valid encoding.

**Why this subpattern applies:** Each one- or three-character encoded token maps to a single output character, so scanning and appending still work if token length is chosen by lookahead.

**Connection to previous solution (1844 → 1309):** Replace fixed `i++` with a variable advance (`i++` or `i += 3`) based on whether `s.charAt(i + 2) == '#'`.

**Java solution:**

```java
class Solution {
    public String freqAlphabets(String s) {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < s.length(); ) {
            int num;
            if (i + 2 < s.length() && s.charAt(i + 2) == '#') {
                num = (s.charAt(i) - '0') * 10
                    + (s.charAt(i + 1) - '0');
                i += 3;
            } else {
                num = s.charAt(i) - '0';
                i++;
            }
            sb.append((char) ('a' + num - 1));
        }
        return sb.toString();
    }
}
```

**Key explanation:** Look two characters ahead for `#`, parse one or two digit characters as a number, then map 1 → `a`, 26 → `z`.

**Time complexity:** `O(n)`; **space complexity:** `O(n)`.

---

## S2 — Dual-input character merge

**When to apply:** Interleave characters from two inputs, preserving the internal order of each source, even if their lengths differ.

**Basic Java 21 template (not a complete solution):**

```java
int i = 0, j = 0;
StringBuilder out = new StringBuilder();
while (i < a.length() || j < b.length()) {
    if (i < a.length()) out.append(a.charAt(i++));
    if (j < b.length()) out.append(b.charAt(j++));
}
```

### LeetCode 1768

**Question:** Merge two strings by alternating characters, starting with `word1`. When one ends, append the rest of the other.

**Examples:** `("abc", "pqr") → "apbqcr"`; `("ab", "pqrs") → "apbqrs"`; `("abcd", "pq") → "apbqcd"`.

**Constraints:** `1 <= word1.length, word2.length <= 100`; lowercase English letters.

**Why this subpattern applies:** Two independent source cursors let the output alternate while correctly handling unequal source lengths.

**Connection to previous solution:** Foundation of a new algorithm family; unlike LC 1309, read from two source strings into one builder.

**Java solution:**

```java
class Solution {
    public String mergeAlternately(String word1, String word2) {
        StringBuilder sb = new StringBuilder();
        int i = 0, j = 0;
        while (i < word1.length() || j < word2.length()) {
            if (i < word1.length()) {
                sb.append(word1.charAt(i++));
            }
            if (j < word2.length()) {
                sb.append(word2.charAt(j++));
            }
        }
        return sb.toString();
    }
}
```

**Key explanation:** `||` continues until both strings are exhausted; each append has its own bounds check.

**Time complexity:** `O(n + m)`; **space complexity:** `O(n + m)`.

---

## S3 — Word-boundary scanning

**When to apply:** Words are separated by whitespace and the task depends on their lengths, positions, order, or character arrangement. Find token boundaries without splitting the entire string.

**Basic Java 21 template (not a complete solution):**

```java
int i = s.length() - 1;
while (i >= 0) {
    while (i >= 0 && s.charAt(i) == ' ') i--;
    int end = i;
    while (i >= 0 && s.charAt(i) != ' ') i--;
    if (end >= i + 1) {
        // Current word is s.substring(i + 1, end + 1).
    }
}
```

### LeetCode 58

**Question:** Return the length of the last word in a string of words and spaces. A word is a maximal consecutive sequence of non-space characters; leading, repeated and trailing spaces may occur.

**Examples:** `"Hello World" → 5`; `"   fly me   to   the moon  " → 4`; `"luffy is still joyboy" → 6`.

**Constraints:** `1 <= s.length <= 10^4`; English letters and spaces; at least one word.

**Why this subpattern applies:** Scanning backward skips trailing whitespace and reaches exactly the final token, so only that token needs to be measured.

**Connection to previous solution:** Foundation of new family; replace interleaving sources with backward word-boundary recognition.

**Java solution:**

```java
class Solution {
    public int lengthOfLastWord(String s) {
        int i = s.length() - 1;
        while (i >= 0 && s.charAt(i) == ' ') {
            i--;
        }
        int length = 0;
        while (i >= 0 && s.charAt(i) != ' ') {
            length++;
            i--;
        }
        return length;
    }
}
```

**Key explanation:** No `trim()` or `split()` is needed; skip trailing spaces, then count back to the preceding space.

**Time complexity:** `O(n)` worst case; **space complexity:** `O(1)`.

### LeetCode 151

**Question:** Reverse the order of words in a sentence and return it with exactly one space between adjacent words, and no leading or trailing spaces. Preserve characters inside each word.

**Examples:** `"the sky is blue" → "blue is sky the"`; `"  hello world  " → "world hello"`; `"a good   example" → "example good a"`.

**Constraints:** `1 <= s.length <= 10^4`; English letters, digits, spaces; at least one word.

**Why this subpattern applies:** Detecting words from right to left directly yields their required output order while allowing whitespace normalization.

**Connection to previous solution (58 → 151):** Generalize the backward last-word scan to a loop: locate and append every token rather than count only the final token.

**Java solution:**

```java
class Solution {
    public String reverseWords(String s) {
        StringBuilder sb = new StringBuilder();
        int i = s.length() - 1;
        while (i >= 0) {
            while (i >= 0 && s.charAt(i) == ' ') {
                i--;
            }
            if (i < 0) {
                break;
            }
            int end = i;
            while (i >= 0 && s.charAt(i) != ' ') {
                i--;
            }
            if (sb.length() > 0) {
                sb.append(' ');
            }
            sb.append(s, i + 1, end + 1);
        }
        return sb.toString();
    }
}
```

**Key explanation:** After scanning a word, `[i + 1, end + 1)` is its substring. Add separators only between emitted words.

**Time complexity:** `O(n)`; **space complexity:** `O(n)`.

### LeetCode 557

**Question:** Reverse characters inside each word, keeping the original order of the words and preserving their separating spaces.

**Examples:** `"Let's take LeetCode contest" → "s'teL ekat edoCteeL tsetnoc"`; `"Mr Ding" → "rM gniD"`.

**Constraints:** `1 <= s.length <= 5 * 10^4`; printable ASCII; words separated by single spaces, with no leading or trailing spaces.

**Why this subpattern applies:** Word boundaries isolate exactly the spans whose characters must be reversed; processing spans left to right preserves word order.

**Connection to previous solution (151 → 557):** Keep boundary detection but scan words forward, appending each word's characters backward rather than appending whole words in reverse order.

**Java solution:**

```java
class Solution {
    public String reverseWords(String s) {
        StringBuilder sb = new StringBuilder();
        int i = 0;
        while (i < s.length()) {
            int start = i;
            while (i < s.length() && s.charAt(i) != ' ') {
                i++;
            }
            int end = i;
            for (int j = end - 1; j >= start; j--) {
                sb.append(s.charAt(j));
            }
            if (i < s.length()) {
                sb.append(' ');
                i++;
            }
        }
        return sb.toString();
    }
}
```

**Key explanation:** Locate each word `[start, end)` and append its characters in descending index order. Leave separators between words unchanged.

**Time complexity:** `O(n)`; **space complexity:** `O(n)`.

---

## S4 — Run-length scanning and emission

**When to apply:** A maximal run of equal adjacent characters is the processing unit; count consecutive occurrences, not global frequency.

**Basic Java 21 template (not a complete solution):**

```java
for (int i = 0; i < s.length(); ) {
    int j = i;
    while (j < s.length() && s.charAt(j) == s.charAt(i)) j++;
    int runLength = j - i;
    // Emit or otherwise process the run of s.charAt(i).
    i = j;
}
```

### LeetCode 443

**Question:** Compress consecutive equal-character runs inside the given `char[]` in place. Write one copy of each character and, only for runs longer than one, the decimal digits of its count. Return the number of valid compressed elements; use only constant auxiliary space.

**Examples:** `["a","a","b","b","c","c","c"] → length 6, prefix ["a","2","b","2","c","3"]`; `["a"] → length 1`; one `a` followed by twelve `b` characters → length 4, prefix `["a","b","1","2"]`.

**Constraints:** `1 <= chars.length <= 2000`; entries may be letters, digits or symbols; compressed output fits in original array.

**Why this subpattern applies:** Every maximal run is independent; a read pointer counts a run and a write pointer emits its compressed representation.

**Connection to previous solution:** Foundation of a new family; switch from space-separated tokens to consecutive identical-character runs and in-place read/write state.

**Java solution:**

```java
class Solution {
    public int compress(char[] chars) {
        int read = 0;
        int write = 0;
        while (read < chars.length) {
            char ch = chars[read];
            int start = read;
            while (read < chars.length && chars[read] == ch) {
                read++;
            }
            int count = read - start;
            chars[write++] = ch;
            if (count > 1) {
                int divisor = 1;
                while (count / divisor >= 10) {
                    divisor *= 10;
                }
                while (divisor > 0) {
                    chars[write++] =
                        (char) ('0' + (count / divisor) % 10);
                    divisor /= 10;
                }
            }
        }
        return write;
    }
}
```

**Key explanation:** Count each run; write the character, followed by count digits (most significant first) only when count exceeds one. Using numeric divisors avoids allocating a count string.

**Time complexity:** `O(n)`; **space complexity:** `O(1)` auxiliary.

### LeetCode 38

**Question:** Starting from `"1"`, generate `n`th sequence term by repeatedly describing each maximal run in the previous term as **count followed by digit**, including counts of one.

**Examples:** `n = 1 → "1"`; `n = 4 → "1211"`; `n = 5 → "111221"`; `"111221" → "312211"` for the next term.

**Constraints:** `1 <= n <= 30`.

**Why this subpattern applies:** Each sequence term is exactly a run-length description of the previous term; the transform can be repeated iteratively.

**Connection to previous solution (443 → 38):** Keep the run counter but emit `count + character` in a fresh `StringBuilder` and repeat `n - 1` times instead of writing `character + optional count` in place.

**Java solution:**

```java
class Solution {
    public String countAndSay(int n) {
        String current = "1";
        for (int step = 2; step <= n; step++) {
            StringBuilder sb = new StringBuilder();
            int i = 0;
            while (i < current.length()) {
                int j = i;
                while (j < current.length()
                        && current.charAt(j) == current.charAt(i)) {
                    j++;
                }
                int count = j - i;
                sb.append(count).append(current.charAt(i));
                i = j;
            }
            current = sb.toString();
        }
        return current;
    }
}
```

**Key explanation:** The output for a run of three `1`s is `"31"`. Each generated term becomes the next iteration's input.

**Time complexity:** `O(n × L)` upper bound where `L` is the maximum generated term length up to `n`; **space complexity:** `O(L)`.

---

## S5 — Normalized palindrome comparison

**When to apply:** The answer depends on symmetric characters from both ends, possibly after skipping irrelevant characters or normalizing their case. Later variants may allow a limited mismatch or deletion.

**Basic Java 21 template (not a complete solution):**

```java
int left = 0, right = s.length() - 1;
while (left < right) {
    if (s.charAt(left) != s.charAt(right)) {
        // Handle mismatch under the problem's budget.
        break;
    }
    left++;
    right--;
}
```

### LeetCode 125

**Question:** Determine whether the string is a palindrome after ignoring non-alphanumeric characters and case. Return true if the remaining letters and digits read the same in both directions; the empty normalized sequence is a palindrome.

**Examples:** `"A man, a plan, a canal: Panama" → true`; `"race a car" → false`; `" " → true`.

**Constraints:** `1 <= s.length <= 2 * 10^5`; printable ASCII characters.

**Why this subpattern applies:** Only normalized, mirrored character pairs matter; two inward-moving pointers can skip punctuation without allocating another string.

**Connection to previous solution:** Foundation of a new family; replace run-length grouping with mirrored comparison and normalization.

**Java solution:**

```java
class Solution {
    public boolean isPalindrome(String s) {
        int left = 0;
        int right = s.length() - 1;
        while (left < right) {
            if (!Character.isLetterOrDigit(s.charAt(left))) {
                left++;
                continue;
            }
            if (!Character.isLetterOrDigit(s.charAt(right))) {
                right--;
                continue;
            }
            char a = Character.toLowerCase(s.charAt(left));
            char b = Character.toLowerCase(s.charAt(right));
            if (a != b) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }
}
```

**Key explanation:** Skip irrelevant characters on either side. Case-fold and compare meaningful characters only. If no mismatch is found, return true, including punctuation-only strings.

**Time complexity:** `O(n)`; **space complexity:** `O(1)` auxiliary.

### LeetCode 680

**Question:** Determine whether the lowercase string `s` is already a palindrome **or can be made into one by deleting at most one character** from any position. Deletion is optional; you cannot change or swap characters. Return a boolean.

**Examples:**

```text
s = "aba"   -> true      (zero deletions)
s = "abca"  -> true      (remove 'b' to get "aca", or 'c' to get "aba")
s = "abc"   -> false     (one removal is insufficient)
```

**Constraints:** `1 <= s.length <= 10^5`; only lowercase English letters.

**Why this subpattern applies:** Mirrored positions must match except for the one character we are allowed to discard. On the first mismatch, exactly two candidate intervals remain.

**Connection to previous solution:** **125 → 680:** Keep the inward-moving `left/right` comparison, remove punctuation/case normalization (input is lowercase), and replace immediate failure with `check(left+1,right) || check(left,right-1)`.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public boolean validPalindrome(String s) {
        int left = 0, right = s.length() - 1;
        while (left < right) {
            if (s.charAt(left) != s.charAt(right)) {
                return check(s, left + 1, right)
                    || check(s, left, right - 1);
            }
            left++;
            right--;
        }
        return true;
    }

    private boolean check(String s, int left, int right) {
        while (left < right) {
            if (s.charAt(left++) != s.charAt(right--)) return false;
        }
        return true;
    }
}
```

**Key logic / explanation:** The first mismatch must be resolved by dropping either the left character or the right character. The helper performs plain palindrome comparison with **no further deletions**, enforcing the one-deletion budget. There is no need to create substrings.

**Time complexity:** `O(n)`.  
**Space complexity:** `O(1) auxiliary`.

---

## S6 — Palindrome center expansion

**When to apply:** Use when the answer concerns **all contiguous palindromes** (count or longest): every palindrome is symmetric around either one character or a gap between two characters.

**Basic Java 21 Code (reusable subpattern snippet, NOT a complete problem solution):**

```java
for (int center = 0; center < 2 * s.length() - 1; center++) {
    int l = center / 2, r = l + center % 2;
    while (l >= 0 && r < s.length() && s.charAt(l) == s.charAt(r)) {
        // account for palindrome [l, r]
        l--; r++;
    }
}
```

### LeetCode 647

**Question:** Count **all palindromic substrings** of `s`. A substring is contiguous, and substrings at different positions count separately even when their text is identical. Return the number of valid substrings.

**Examples:**

```text
s = "abc" -> 3    ("a", "b", "c")
s = "aaa" -> 6    (three "a", two "aa", one "aaa")
s = "aba" -> 4    ("a", "b", "a", "aba")
```

**Constraints:** `1 <= s.length <= 1000`; lowercase English letters.

**Why this subpattern applies:** Every palindrome has an odd-character or even-gap center, so expanding around all `2n - 1` centers finds every possible palindromic interval.

**Connection to previous solution:** **Foundation of S6 (new algorithm family).** Unlike S5, begin at each possible center and expand **outward**, adding one for each valid expansion.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public int countSubstrings(String s) {
        int answer = 0;
        for (int center = 0; center < 2 * s.length() - 1; center++) {
            int left = center / 2;
            int right = left + center % 2;
            answer += expand(s, left, right);
        }
        return answer;
    }

    private int expand(String s, int left, int right) {
        int found = 0;
        while (left >= 0 && right < s.length()
                && s.charAt(left) == s.charAt(right)) {
            found++;
            left--;
            right++;
        }
        return found;
    }
}
```

**Key logic / explanation:** For even `center` use `(i,i)` (odd-length palindrome); for odd `center` use `(i,i+1)` (even-length palindrome). Count every successful match before moving both indices outward. Each substring corresponds to exactly one center and radius.

**Time complexity:** `O(n²)`.  
**Space complexity:** `O(1) auxiliary`.

### LeetCode 5

**Question:** Return the **longest contiguous palindromic substring** in `s`. A palindrome reads identically in both directions. If multiple longest answers exist, return any one of them.

**Examples:**

```text
s = "babad" -> "bab"   ("aba" is also valid)
s = "cbbd"  -> "bb"
s = "a"     -> "a"
s = "aaaa"  -> "aaaa"
```

**Constraints:** `1 <= s.length <= 1000`; English letters and digits.

**Why this subpattern applies:** Exactly the same odd/even center enumeration from 647 finds every palindromic interval; the required output is the maximum-length interval rather than the total count.

**Connection to previous solution:** **647 → 5:** Preserve center generation and outward matching; replace `found++` with `if (length > bestLength) { start = left; bestLength = length; }`.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public String longestPalindrome(String s) {
        int start = 0, maxLength = 0;
        for (int center = 0; center < 2 * s.length() - 1; center++) {
            int left = center / 2;
            int right = left + center % 2;
            while (left >= 0 && right < s.length()
                    && s.charAt(left) == s.charAt(right)) {
                int length = right - left + 1;
                if (length > maxLength) {
                    maxLength = length;
                    start = left;
                }
                left--;
                right++;
            }
        }
        return s.substring(start, start + maxLength);
    }
}
```

**Key logic / explanation:** Every successful expansion gives a real palindrome with length `right-left+1`. Keep only its start/length if it improves the best answer. `substring(start,start+maxLength)` is called only once after scanning, avoiding quadratic temporary string allocation.

**Time complexity:** `O(n²)`.  
**Space complexity:** `O(1) auxiliary; O(n) for returned String`.

---

## S7 — Digit-string addition and carry

**When to apply:** Use when input numbers are strings potentially exceeding numeric types. Process aligned least-significant digits from the right and propagate a carry of the appropriate base.

**Basic Java 21 Code (reusable subpattern snippet, NOT a complete problem solution):**

```java
int i = a.length() - 1, j = b.length() - 1, carry = 0, base = 10;
StringBuilder reversed = new StringBuilder();
while (i >= 0 || j >= 0 || carry != 0) {
    int sum = carry;
    if (i >= 0) sum += a.charAt(i--) - '0';
    if (j >= 0) sum += b.charAt(j--) - '0';
    reversed.append(sum % base);
    carry = sum / base;
}
```

### LeetCode 415

**Question:** Given two nonnegative integers `num1` and `num2` represented by decimal strings, return their exact sum **as a decimal string**. Do not convert entire inputs into built-in numeric types or use an arbitrary-precision library.

**Examples:**

```text
num1 = "11",  num2 = "123" -> "134"
num1 = "456", num2 = "77"  -> "533"
num1 = "999", num2 = "1"   -> "1000"
num1 = "0",   num2 = "0"   -> "0"
```

**Constraints:** `1 <= num1.length, num2.length <= 10^4`; digits only; no leading zero except `"0"`.

**Why this subpattern applies:** Each decimal column depends only on two digit values and a carry from the previous (right-hand) column, so one backward scan is sufficient.

**Connection to previous solution:** **Foundation of S7 (new family).** Two backward cursors, a carry, output digits appended in reverse, and a final reversal.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public String addStrings(String num1, String num2) {
        int i = num1.length() - 1, j = num2.length() - 1;
        int carry = 0;
        StringBuilder out = new StringBuilder();
        while (i >= 0 || j >= 0 || carry != 0) {
            int sum = carry;
            if (i >= 0) sum += num1.charAt(i--) - '0';
            if (j >= 0) sum += num2.charAt(j--) - '0';
            out.append(sum % 10);
            carry = sum / 10;
        }
        return out.reverse().toString();
    }
}
```

**Key logic / explanation:** `ch - '0'` converts a digit character to its integer value. `sum % 10` is the digit to write; `sum / 10` is the next carry. Continue while either input or the carry remains, so unequal lengths and `999 + 1` work.

**Time complexity:** `O(max(n,m))`.  
**Space complexity:** `O(max(n,m)) including result`.

### LeetCode 67

**Question:** Two binary strings `a` and `b` represent nonnegative integers using only `0` and `1`. Return their sum in base 2, as a binary string. Handle unequal lengths and final carry.

**Examples:**

```text
a = "11",   b = "1"    -> "100"
a = "1010", b = "1011" -> "10101"
a = "0",    b = "0"    -> "0"
```

**Constraints:** `1 <= a.length, b.length <= 10^4`; binary digits only; no leading zero except `"0"`.

**Why this subpattern applies:** The same column-wise addition and carry invariant holds as in decimal, but each column is in base 2.

**Connection to previous solution:** **415 → 67:** Reuse both cursors, digit conversion, and builder; only change `sum % 10`/`sum / 10` to `sum % 2`/`sum / 2`.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public String addBinary(String a, String b) {
        int i = a.length() - 1, j = b.length() - 1;
        int carry = 0;
        StringBuilder out = new StringBuilder();
        while (i >= 0 || j >= 0 || carry != 0) {
            int sum = carry;
            if (i >= 0) sum += a.charAt(i--) - '0';
            if (j >= 0) sum += b.charAt(j--) - '0';
            out.append(sum % 2);
            carry = sum / 2;
        }
        return out.reverse().toString();
    }
}
```

**Key logic / explanation:** Binary columns can total 0, 1, 2, or 3. Their output bits are respectively `sum % 2`, with carry `sum / 2`. The method is structurally identical to LC 415, making this a genuine implementation evolution.

**Time complexity:** `O(max(n,m))`.  
**Space complexity:** `O(max(n,m)) including result`.

---

## S8 — Grade-school string multiplication

**When to apply:** Use when multiplying two arbitrarily long digit strings; individual digit-pair products belong to fixed positions in an `m+n`-length decimal accumulator.

**Basic Java 21 Code (reusable subpattern snippet, NOT a complete problem solution):**

```java
int[] product = new int[a.length() + b.length()];
for (int i = a.length() - 1; i >= 0; i--)
    for (int j = b.length() - 1; j >= 0; j--) {
        int pos = i + j + 1;
        int sum = (a.charAt(i) - '0') * (b.charAt(j) - '0') + product[pos];
        product[pos] = sum % 10;
        product[pos - 1] += sum / 10;
    }
// skip leading zeros when constructing the result
```

### LeetCode 43

**Question:** Multiply two nonnegative decimal integers `num1` and `num2`, supplied as strings, and return their product as a string. Do not parse the entire numbers as primitive types and do not use `BigInteger`. Remove unnecessary leading zeros.

**Examples:**

```text
num1 = "2", num2 = "3"     -> "6"
num1 = "123", num2 = "456" -> "56088"
num1 = "0", num2 = "52"    -> "0"
```

**Constraints:** `1 <= num1.length, num2.length <= 200`; decimal digits only, without redundant leading zeros.

**Why this subpattern applies:** Unlike addition, each digit from the first input contributes to every digit in the second; positional accumulation is the natural extension of schoolbook multiplication.

**Connection to previous solution:** **Foundation of S8 (new algorithm family):** Use nested backward digit loops and an integer array of length `m+n`; this is **not** the carry algorithm of S7 reused unchanged.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public String multiply(String num1, String num2) {
        if (num1.equals("0") || num2.equals("0")) return "0";
        int m = num1.length(), n = num2.length();
        int[] product = new int[m + n];
        for (int i = m - 1; i >= 0; i--) {
            for (int j = n - 1; j >= 0; j--) {
                int value = (num1.charAt(i) - '0')
                          * (num2.charAt(j) - '0')
                          + product[i + j + 1];
                product[i + j + 1] = value % 10;
                product[i + j] += value / 10;
            }
        }
        StringBuilder answer = new StringBuilder();
        int i = 0;
        while (i < product.length && product[i] == 0) i++;
        while (i < product.length) answer.append(product[i++]);
        return answer.toString();
    }
}
```

**Key logic / explanation:** Positions `i+j+1` and `i+j` hold the current product digit and its carry, respectively. The immediately preceding cell can receive further contributions from other products; the loop ordering ensures normalization. The result has at most `m+n` digits.

**Time complexity:** `O(m×n)`.  
**Space complexity:** `O(m+n) for accumulator and output`.

---

## S9 — String-to-number lexical state

**When to apply:** Use when parsing depends on **character position and grammar phase**: initial spaces/sign, integer digits, decimal point, or exponent; invalid transitions must be handled according to the problem.

**Basic Java 21 Code (reusable subpattern snippet, NOT a complete problem solution):**

```java
int state = 0;  // e.g. START, SIGN, DIGITS, EXPONENT, ...
for (int i = 0; i < s.length(); i++) {
    char c = s.charAt(i);
    // classify c and change state; reject invalid transitions
}
// accept only a valid terminal state
```

### LeetCode 8

**Question:** Implement `myAtoi(s)`: skip leading ASCII spaces; read an optional leading `+` or `-`; then parse the longest consecutive sequence of decimal digits. Stop when a non-digit is met. If no digits are read, return 0. Clamp values outside the 32-bit signed range to `Integer.MIN_VALUE` or `Integer.MAX_VALUE`. Leading zeros have no special value.

**Examples:**

```text
s = "42"           -> 42
s = "   -042"       -> -42
s = "1337c0d3"     -> 1337
s = "words and 987"-> 0
s = "-91283472332" -> -2147483648
```

**Constraints:** `0 <= s.length <= 200`; strings contain letters, digits, spaces, signs and decimal points.

**Why this subpattern applies:** The accepted character types depend on the stage of parsing: spaces first, optional sign once, then digits only. The value must be bounded before arithmetic overflows.

**Connection to previous solution:** **Foundation of S9 (new family).** Introduce ordered lexical phases plus safe digit accumulation; unlike 43, inputs are not guaranteed to contain only digits.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public int myAtoi(String s) {
        int i = 0, n = s.length();
        while (i < n && s.charAt(i) == ' ') i++;

        boolean negative = false;
        if (i < n && (s.charAt(i) == '+' || s.charAt(i) == '-')) {
            negative = s.charAt(i++) == '-';
        }
        int limit = negative ? Integer.MIN_VALUE : -Integer.MAX_VALUE;
        int result = 0; // Accumulate negatively to support MIN_VALUE.
        while (i < n) {
            char ch = s.charAt(i);
            if (ch < '0' || ch > '9') break;
            int digit = ch - '0';
            if (result < limit / 10
                    || (result == limit / 10 && digit > -(limit % 10))) {
                return negative ? Integer.MIN_VALUE : Integer.MAX_VALUE;
            }
            result = result * 10 - digit;
            i++;
        }
        return negative ? result : -result;
    }
}
```

**Key logic / explanation:** A negative accumulator can represent `-2147483648` even though positive `2147483648` cannot fit in an `int`. The pre-update check ensures `result*10-digit` never crosses the sign-dependent lower bound. Invalid characters after parsing begins stop conversion rather than invalidating prior digits.

**Time complexity:** `O(n)`.  
**Space complexity:** `O(1) auxiliary`.

### LeetCode 65

**Question:** Return whether **the entire string** is a syntactically valid decimal number. A valid number is an integer or decimal, optionally followed by `e`/`E` and an integer exponent. Signs may appear at the beginning or immediately after the exponent marker. Allowed mantissas include `"3"`, `"3."`, `".5"`, and `"-0.7"`; bare `"."`, missing exponent digits, embedded spaces, or misplaced signs make the string invalid.

**Examples:**

```text
s = "0"        -> true
s = "-90E3"    -> true
s = ".5"       -> true
s = "3."       -> true
s = "99e2.5"   -> false
s = "e"        -> false
s = "1e+"      -> false
```

**Constraints:** `1 <= s.length <= 20`; input contains digits, English letters, `+`, `-` and `.`.

**Why this subpattern applies:** After an exponent marker, for example, digits are required and a decimal point is forbidden. A deterministic finite-state machine explicitly encodes which characters may follow which phases.

**Connection to previous solution:** **8 → 65:** Replace digit accumulation and partial-input acceptance with a **transition table** over digit/sign/dot/exponent classes; accept only designated terminal states.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public boolean isNumber(String s) {
        // Character types: 0 digit, 1 sign, 2 '.', 3 'e'/'E'.
        // States: 0 start, 1 sign, 2 integer, 3 dot/no integer,
        // 4 dot/after integer, 5 fractional digits, 6 exponent marker,
        // 7 exponent sign, 8 exponent digits.
        int[][] next = {
            {2, 1, 3, -1},
            {2, -1, 3, -1},
            {2, -1, 4, 6},
            {5, -1, -1, -1},
            {5, -1, -1, 6},
            {5, -1, -1, 6},
            {8, 7, -1, -1},
            {8, -1, -1, -1},
            {8, -1, -1, -1}
        };
        int state = 0;
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            int type;
            if (c >= '0' && c <= '9') type = 0;
            else if (c == '+' || c == '-') type = 1;
            else if (c == '.') type = 2;
            else if (c == 'e' || c == 'E') type = 3;
            else return false;
            state = next[state][type];
            if (state == -1) return false;
        }
        return state == 2 || state == 4 || state == 5 || state == 8;
    }
}
```

**Key logic / explanation:** The table enforces a digit after a bare dot, prevents a sign after regular digits, and prevents a fractional exponent. Finishing in states 2, 4, 5, or 8 is valid; ending immediately after a sign, a bare dot, or `e` is invalid.

**Time complexity:** `O(n)`.  
**Space complexity:** `O(1) auxiliary (fixed 9×4 table)`.

---

## S10 — KMP prefix-function matching / borders

**When to apply:** Use when prefix/suffix overlap matters in pattern matching, repetition, borders, or repeated virtual text. An LPS array records the longest reusable proper prefix after each mismatch.

**Basic Java 21 Code (reusable subpattern snippet, NOT a complete problem solution):**

```java
int[] lps = new int[p.length()];
for (int i = 1, len = 0; i < p.length(); ) {
    if (p.charAt(i) == p.charAt(len)) lps[i++] = ++len;
    else if (len > 0) len = lps[len - 1];
    else lps[i++] = 0;
}
// use lps for streaming match, periodicity, or longest border
```

### LeetCode 28

**Question:** Given `haystack` (a text) and `needle` (a pattern), return the zero-based index of the **first** occurrence of `needle` in `haystack`, or `-1` if there is no match. A match must be a contiguous substring.

**Examples:**

```text
haystack = "sadbutsad", needle = "sad"   -> 0
haystack = "leetcode",  needle = "leeto" -> -1
haystack = "mississippi", needle = "issip" -> 4
```

**Constraints:** `1 <= haystack.length, needle.length <= 10^4`; lowercase English letters.

**Why this subpattern applies:** After a partial match fails, a repeated pattern prefix may still match the suffix of the matched portion. KMP uses that overlap to avoid rescanning text.

**Connection to previous solution:** **Foundation of S10 (new family).** Build the LPS array for the pattern, then stream through the text; on mismatch use `j = lps[j-1]` without moving text index `i` backward.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public int strStr(String haystack, String needle) {
        int n = haystack.length(), m = needle.length();
        int[] lps = new int[m];
        for (int i = 1, len = 0; i < m; ) {
            if (needle.charAt(i) == needle.charAt(len)) {
                lps[i++] = ++len;
            } else if (len > 0) {
                len = lps[len - 1];
            } else {
                lps[i++] = 0;
            }
        }
        int i = 0, j = 0;
        while (i < n) {
            if (haystack.charAt(i) == needle.charAt(j)) {
                i++;
                j++;
                if (j == m) return i - m;
            } else if (j > 0) {
                j = lps[j - 1];
            } else {
                i++;
            }
        }
        return -1;
    }
}
```

**Key logic / explanation:** `lps[t]` is the longest proper prefix of `needle[0..t]` also occurring as its suffix. With matched prefix length `j`, a mismatch falls back to `lps[j-1]`; this preserves all possible matches while scanning the haystack only once.

**Time complexity:** `O(n+m)`.  
**Space complexity:** `O(m) for LPS`.

### LeetCode 459

**Question:** Given a nonempty lowercase string `s`, determine whether there is a **shorter, nonempty substring** which can be repeated **two or more times** to produce all of `s` exactly. Return `true` or `false`. The repeating unit must cover the entire string without leftovers.

**Examples:**

```text
s = "abab"     -> true    ("ab" repeated twice)
s = "aba"      -> false
s = "abcabcabc"-> true    ("abc" repeated three times)
s = "a"        -> false   (unit cannot be shorter)
```

**Constraints:** `1 <= s.length <= 10^4`; lowercase English letters.

**Why this subpattern applies:** The final LPS value tells us the length of the longest repeated border. If a shorter period `n - lps[n-1]` divides `n`, the string consists of whole copies of that period.

**Connection to previous solution:** **28 → 459:** Keep **the same LPS construction**, eliminate the second-string KMP search, and use `border > 0 && n % (n-border) == 0`.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public boolean repeatedSubstringPattern(String s) {
        int n = s.length();
        int[] lps = new int[n];
        for (int i = 1, len = 0; i < n; ) {
            if (s.charAt(i) == s.charAt(len)) {
                lps[i++] = ++len;
            } else if (len > 0) {
                len = lps[len - 1];
            } else {
                lps[i++] = 0;
            }
        }
        int border = lps[n - 1];
        int period = n - border;
        return border > 0 && n % period == 0;
    }
}
```

**Key logic / explanation:** For `"ababab"`, the final border is `4`, giving candidate period `6-4=2`; `6 % 2 == 0`, so the unit is `"ab"`. A nonzero border alone is insufficient; divisibility enforces complete repetition.

**Time complexity:** `O(n)`.  
**Space complexity:** `O(n) for LPS`.

### LeetCode 1392

**Question:** Given a string `s`, return its longest **proper prefix** that is also a suffix. A proper prefix cannot include the entire string. If no nonempty prefix equals a suffix, return an empty string. The prefix and suffix may overlap.

**Examples:**

```text
s = "level"      -> "l"
s = "ababab"     -> "abab"
s = "leetcodeleet"-> "leet"
s = "abcd"       -> ""
```

**Constraints:** `1 <= s.length <= 10^5`; lowercase English letters.

**Why this subpattern applies:** The desired answer is precisely the final LPS border: the longest proper prefix that is simultaneously a suffix.

**Connection to previous solution:** **459 → 1392:** Reuse identical LPS construction but **remove the divisibility/repetition test**; return `s.substring(0, lps[n-1])` directly.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public String longestPrefix(String s) {
        int n = s.length();
        int[] lps = new int[n];
        for (int i = 1, len = 0; i < n; ) {
            if (s.charAt(i) == s.charAt(len)) {
                lps[i++] = ++len;
            } else if (len > 0) {
                len = lps[len - 1];
            } else {
                lps[i++] = 0;
            }
        }
        return s.substring(0, lps[n - 1]);
    }
}
```

**Key logic / explanation:** `lps[n-1]` is the longest valid border even when its length does **not** divide `n`. For `"level"`, the border is `"l"`; for `"abcd"`, it has length 0. The whole string is never returned as its own proper prefix.

**Time complexity:** `O(n)`.  
**Space complexity:** `O(n) for LPS plus up to O(n) output`.

### LeetCode 214

**Question:** Given string `s`, you may insert characters **only at the beginning**. Return the **shortest possible palindrome** obtainable this way. You can prepend any characters, but you may not modify or append within the existing string. Preserve the original `s` at the end.

**Examples:**

```text
s = "aacecaaa" -> "aaacecaaa"
s = "abcd"     -> "dcbabcd"
s = "aba"      -> "aba"
```

**Constraints:** `0 <= s.length <= 5 * 10^4`; lowercase English letters.

**Why this subpattern applies:** The portion of `s` that can remain at the front without extra prefix additions is its **longest palindromic prefix**. KMP finds this as a cross-string border of `s + separator + reverse(s)`.

**Connection to previous solution:** **1392 → 214:** Keep the LPS builder, but apply it to `s + "#" + reverse(s)` so the final border measures prefix/palindromic-suffix alignment; prepend the reversed unmatched suffix.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public String shortestPalindrome(String s) {
        String reversed = new StringBuilder(s).reverse().toString();
        String combined = s + "#" + reversed; // '#' cannot occur in s.
        int[] lps = new int[combined.length()];
        for (int i = 1, len = 0; i < combined.length(); ) {
            if (combined.charAt(i) == combined.charAt(len)) {
                lps[i++] = ++len;
            } else if (len > 0) {
                len = lps[len - 1];
            } else {
                lps[i++] = 0;
            }
        }
        int palPrefixLength = lps[combined.length() - 1];
        return reversed.substring(0, s.length() - palPrefixLength) + s;
    }
}
```

**Key logic / explanation:** Because `#` is outside the guaranteed lowercase input alphabet, the matched border cannot cross it ambiguously. Its length gives the longest palindrome starting at index 0. All characters after that prefix must be mirrored by prepending them in reverse. For `"abcd"`, the palindromic prefix is `"a"` and we prepend `"dcb"`.

**Time complexity:** `O(n)`.  
**Space complexity:** `O(n) for reverse, combined string, LPS and answer`.

### LeetCode 686

**Question:** Given nonempty lowercase strings `a` and `b`, find the smallest positive number of times `a` must be repeated consecutively such that `b` appears as a **contiguous substring** of the repetition. Return `-1` when no number of copies works. A match may begin near the end of one copy and continue into the next.

**Examples:**

```text
a = "abcd", b = "cdabcdab" -> 3    ("abcdabcdabcd")
a = "a",    b = "aa"       -> 2
a = "abc",  b = "def"      -> -1
a = "abab", b = "baba"     -> 2
```

**Constraints:** `1 <= a.length, b.length <= 10^4`; lowercase English letters.

**Why this subpattern applies:** A repetition is a **virtual text** whose character at position `i` is `a[i % a.length()]`. KMP can search this text without constructing repeated strings.

**Connection to previous solution:** **214 → 686:** Reuse LPS construction and the streaming KMP matcher from 28, but read the text via modulo indexing and translate the matched endpoint into a minimum copy count. This returns to S10’s match-scanning use of LPS.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public int repeatedStringMatch(String a, String b) {
        int n = a.length(), m = b.length();
        int[] lps = new int[m];
        for (int i = 1, len = 0; i < m; ) {
            if (b.charAt(i) == b.charAt(len)) {
                lps[i++] = ++len;
            } else if (len > 0) {
                len = lps[len - 1];
            } else {
                lps[i++] = 0;
            }
        }
        int minCopies = (m + n - 1) / n;
        int limit = (minCopies + 1) * n;
        int matched = 0;
        for (int i = 0; i < limit; i++) {
            char c = a.charAt(i % n);
            while (matched > 0 && c != b.charAt(matched)) {
                matched = lps[matched - 1];
            }
            if (c == b.charAt(matched)) matched++;
            if (matched == m) return (i + n) / n; // ceil((i+1)/n)
        }
        return -1;
    }
}
```

**Key logic / explanation:** A substring can start at most `n-1` positions into the first copy; therefore `ceil(m/n)+1` copies cover every possible match alignment. The LPS fallback is identical to 28, and after the last matching text index `i`, the minimum number of copies is `ceil((i+1)/n) = (i+n)/n`.

**Time complexity:** `O(n+m)`.  
**Space complexity:** `O(m) for LPS; no repeated text is allocated`.

---

## S11 — Z-function prefix alignment

**When to apply:** Use when a string must be compared with **all of its suffixes** against the **same prefix**. The Z-array reuses the largest known interval of prefix matches for linear performance.

**Basic Java 21 Code (reusable subpattern snippet, NOT a complete problem solution):**

```java
int[] z = new int[s.length()];
for (int i = 1, l = 0, r = 0; i < s.length(); i++) {
    if (i <= r) z[i] = Math.min(r - i + 1, z[i - l]);
    while (i + z[i] < s.length() && s.charAt(z[i]) == s.charAt(i + z[i])) z[i]++;
    if (i + z[i] - 1 > r) { l = i; r = i + z[i] - 1; }
}
```

### LeetCode 2223

**Question:** A final lowercase string `s` was built by repeatedly **prepending** one character, so each intermediate string was a suffix of the final string. The **score** of an intermediate string is the length of its longest common prefix with the full final string `s`. Return the sum of scores of all intermediate strings, including `s` itself.

**Examples:**

```text
s = "babab"     -> 9
  suffix scores: "b"=1, "ab"=0, "bab"=3, "abab"=0, "babab"=5
s = "azbazbzaz" -> 14
```

**Constraints:** `1 <= s.length <= 10^5`; lowercase English letters; answer returned as `long`.

**Why this subpattern applies:** Every intermediate built string corresponds to a suffix `s[i..]`. Its score is exactly `Z[i]`, while the full string contributes `n`.

**Connection to previous solution:** **Foundation of S11 (new algorithm family):** Replace the KMP border array with a **Z array**; each `Z[i]` counts matching characters between `s` and suffix `s[i..]` using one reusable `[left,right]` interval.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public long sumScores(String s) {
        int n = s.length();
        int[] z = new int[n];
        long total = n; // Z[0] is conventionally 0; full string scores n.
        for (int i = 1, left = 0, right = 0; i < n; i++) {
            if (i <= right) {
                z[i] = Math.min(right - i + 1, z[i - left]);
            }
            while (i + z[i] < n
                    && s.charAt(z[i]) == s.charAt(i + z[i])) {
                z[i]++;
            }
            if (i + z[i] - 1 > right) {
                left = i;
                right = i + z[i] - 1;
            }
            total += z[i];
        }
        return total;
    }
}
```

**Key logic / explanation:** The Z value at `i` represents the longest prefix of `s` equal to a prefix of suffix `s[i..]`. When `i` is inside the current matched interval, begin from the known value `min(right-i+1, z[i-left])`; extend only beyond it. Summing `n + z[1] + ... + z[n-1]` gives the required answer; use `long` for large sums.

**Time complexity:** `O(n)`.  
**Space complexity:** `O(n) for Z array`.

### LeetCode 3031

**Question:** You are given an initial string `word` and integer `k`. Every second, remove the **first `k` characters** of the current word and append **any `k` lowercase letters** to its end. The length stays constant. Find the **smallest positive number of seconds** after which the word can again equal its original value. You may choose the appended characters to help achieve equality.

**Examples:**

```text
word = "abacaba", k = 3 -> 2
word = "abacaba", k = 4 -> 1
word = "abcbabcd", k = 2 -> 4
```

**Constraints:** `1 <= word.length <= 10^6`; `1 <= k <= word.length`; lowercase English letters.

**Why this subpattern applies:** After `t` operations with `t*k < n`, the unchanged suffix is `word[t*k..]`. Restoration is possible exactly when this suffix matches the prefix of the original word, which is tested by `Z[t*k] >= n-t*k`.

**Connection to previous solution:** **2223 → 3031:** Reuse **the same linear Z construction** but replace the sum of Z values with checks only at multiples of `k`, returning the first feasible step or the full-replacement bound.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public int minimumTimeToInitialState(String word, int k) {
        int n = word.length();
        int[] z = new int[n];
        for (int i = 1, left = 0, right = 0; i < n; i++) {
            if (i <= right) {
                z[i] = Math.min(right - i + 1, z[i - left]);
            }
            while (i + z[i] < n
                    && word.charAt(z[i]) == word.charAt(i + z[i])) {
                z[i]++;
            }
            if (i + z[i] - 1 > right) {
                left = i;
                right = i + z[i] - 1;
            }
        }
        for (int removed = k; removed < n; removed += k) {
            if (z[removed] >= n - removed) {
                return removed / k;
            }
        }
        return (n + k - 1) / k; // All original characters replaced.
    }
}
```

**Key logic / explanation:** Test `removed=k,2k,...` in increasing time order: when the remaining original suffix matches the needed prefix, the arbitrary appended characters can fill the rest. After `ceil(n/k)` steps no original characters remain, so we can always append the desired content. The early return ensures the **minimum positive** time.

**Time complexity:** `O(n + n/k) = O(n)`.  
**Space complexity:** `O(n) for Z array`.

---

## S12 — Fixed/variable-length rolling hash

**When to apply:** Use when many **overlapping substrings** must be compared. Maintain a compact rolling fingerprint; for a variable target length, combine duplicate detection with binary search and collision verification.

**Basic Java 21 Code (reusable subpattern snippet, NOT a complete problem solution):**

```java
long hash = 0, base = 31, mod = 1_000_000_007L;
for (int i = 0; i < s.length(); i++) {
    hash = (hash * base + s.charAt(i)) % mod;
    // to slide fixed width L: subtract outgoing contribution and normalize
}
// verify equal hashes against the actual text when correctness requires it
```

### LeetCode 187

**Question:** A DNA string `s` contains only `A`, `C`, `G`, and `T`. Find all **distinct substrings of exactly 10 characters** that occur **more than once**. Overlapping occurrences count; each repeated ten-character sequence must appear only once in the returned list, and output order is arbitrary.

**Examples:**

```text
s = "AAAAACCCCCAAAAACCCCCCAAAAAGGGTTT"
  -> ["AAAAACCCCC", "CCCCCAAAAA"] (either order)
s = "AAAAAAAAAAAAA"
  -> ["AAAAAAAAAA"]
s = "ACGT"
  -> []
```

**Constraints:** `1 <= s.length <= 10^5`; characters only `A`, `C`, `G`, `T`.

**Why this subpattern applies:** Every length-10 DNA fragment can be represented exactly using 20 bits (2 bits per base). A bitwise rolling window updates its fingerprint in O(1), and duplicates are looked up in sets.

**Connection to previous solution:** **Foundation of S12 (new algorithm family).** Encode symbols into 2 bits each, maintain the last 10 symbols inside a 20-bit mask, and track whether each code has appeared once or more than once.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public java.util.List<String> findRepeatedDnaSequences(String s) {
        java.util.List<String> answer = new java.util.ArrayList<>();
        if (s.length() < 10) return answer;
        java.util.Set<Integer> seen = new java.util.HashSet<>();
        java.util.Set<Integer> reported = new java.util.HashSet<>();
        int mask = (1 << 20) - 1;
        int code = 0;
        for (int i = 0; i < s.length(); i++) {
            int value = switch (s.charAt(i)) {
                case 'A' -> 0;
                case 'C' -> 1;
                case 'G' -> 2;
                default -> 3; // 'T' by constraints
            };
            code = ((code << 2) | value) & mask;
            if (i >= 9 && !seen.add(code) && reported.add(code)) {
                answer.add(s.substring(i - 9, i + 1));
            }
        }
        return answer;
    }
}
```

**Key logic / explanation:** Shifting by 2 discards the old base position; `& ((1<<20)-1)` removes any bits older than 10 bases. This DNA representation is **collision-free** because 4 symbols × 2 bits fit exactly into 20 bits. `seen.add` is false on repeat; `reported.add` is true only for the first repeated encounter, preventing duplicate answers.

**Time complexity:** `O(n) expected with hash sets`.  
**Space complexity:** `O(min(n,4^10)) for sets, excluding output; at most 4^10 possible codes`.

### LeetCode 1044

**Question:** Given a lowercase string `s`, return **any longest substring** that appears at least twice in `s`. Different occurrences may **overlap**. If no nonempty substring repeats, return `""`. Example: two occurrences of `"ana"` in `"banana"` overlap, and they are valid.

**Examples:**

```text
s = "banana" -> "ana"
s = "abcd"   -> ""
s = "aaaa"   -> "aaa"
```

**Constraints:** `2 <= s.length <= 3×10^4`; lowercase English letters.

**Why this subpattern applies:** For a fixed length `L`, rolling hashes identify repeated windows quickly. Existence is monotonic in `L` (if a length-`L` repeat exists, shorter repeats do too), so binary search finds the maximum valid length.

**Connection to previous solution:** **187 → 1044:** Replace fixed width 10 with a candidate width `L`, replace the 2-bit DNA code with **general polynomial rolling hash**, and binary-search `L`. Verify matching substrings on hash collisions to keep the answer correct.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    // Two independent moduli reduce collisions; verify equal hashes before returning.
    private static final long MOD1 = 1_000_000_007L;
    private static final long MOD2 = 1_000_000_009L;
    private static final long BASE = 31L;

    public String longestDupSubstring(String s) {
        int n = s.length();
        long[] pow1 = new long[n + 1], pow2 = new long[n + 1];
        long[] pre1 = new long[n + 1], pre2 = new long[n + 1];
        pow1[0] = pow2[0] = 1;
        for (int i = 0; i < n; i++) {
            int value = s.charAt(i) - 'a' + 1;
            pow1[i + 1] = pow1[i] * BASE % MOD1;
            pow2[i + 1] = pow2[i] * BASE % MOD2;
            pre1[i + 1] = (pre1[i] * BASE + value) % MOD1;
            pre2[i + 1] = (pre2[i] * BASE + value) % MOD2;
        }
        int low = 1, high = n - 1, bestStart = -1, bestLen = 0;
        while (low <= high) {
            int len = low + (high - low) / 2;
            int start = findRepeat(s, len, pre1, pre2, pow1, pow2);
            if (start >= 0) {
                bestStart = start;
                bestLen = len;
                low = len + 1;
            } else {
                high = len - 1;
            }
        }
        return bestStart < 0 ? "" : s.substring(bestStart, bestStart + bestLen);
    }

    private int findRepeat(String s, int len, long[] pre1,
                           long[] pre2, long[] pow1, long[] pow2) {
        java.util.Map<Long, java.util.List<Integer>> seen = new java.util.HashMap<>();
        for (int i = 0; i + len <= s.length(); i++) {
            long a = (pre1[i + len] - pre1[i] * pow1[len] % MOD1 + MOD1) % MOD1;
            long b = (pre2[i + len] - pre2[i] * pow2[len] % MOD2 + MOD2) % MOD2;
            long key = (a << 32) | b;
            java.util.List<Integer> starts = seen.computeIfAbsent(
                    key, unused -> new java.util.ArrayList<>());
            for (int j : starts) {
                if (s.regionMatches(j, s, i, len)) return i;
            }
            starts.add(i);
        }
        return -1;
    }
}
```

**Key logic / explanation:** Precompute hash powers and prefixes, so any substring hash is O(1). Binary-search the substring length and store prior starting positions keyed by a pair of hashes. **Important:** equal hashes are only a candidate match; `regionMatches` confirms actual equality, so collisions cannot create false positives. A valid match at length `L` permits searching longer lengths.

**Time complexity:** `O(n log n) expected for double hashing with rare collisions; worst-case O(n² log n) if many candidate comparisons occur`.  
**Space complexity:** `O(n) auxiliary for prefix/power arrays and per-check map; up to O(n) output`.

---

## S13 — Character contribution by last occurrence

**When to apply:** Use when the sum over **all substrings** seems quadratic, but each new occurrence of a character changes contributions based only on its previous index or its two nearest occurrences.

**Basic Java 21 Code (reusable subpattern snippet, NOT a complete problem solution):**

```java
int[] last = new int[26];
java.util.Arrays.fill(last, -1);
long endingHere = 0, total = 0;
for (int i = 0; i < s.length(); i++) {
    int c = s.charAt(i) - 'a';  // only for lowercase-English constraints
    endingHere += i - last[c];
    total += endingHere;
    last[c] = i;
}
```

### LeetCode 2262

**Question:** The **appeal** of a string is the number of **distinct letters** present in it. Given lowercase `s`, return the sum of the appeal of **every nonempty contiguous substring** of `s`. Two equal-looking substrings at different positions count separately. Return the answer as a `long`.

**Examples:**

```text
s = "abbca" -> 28
s = "code"  -> 20
s = "aaa"   -> 6    (all six substrings have exactly one distinct letter)
```

**Constraints:** `1 <= s.length <= 10^5`; lowercase English letters.

**Why this subpattern applies:** The appearance of character `c` at index `i` adds one new distinct letter to exactly `i - last[c]` substrings ending at `i` because those starts lie strictly after the previous `c`.

**Connection to previous solution:** **Foundation of S13 (new family).** Replace enumerating all substrings with a **last-seen index array** and a running appeal total for substrings ending at the current index.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public long appealSum(String s) {
        int[] last = new int[26];
        java.util.Arrays.fill(last, -1);
        long endingHere = 0;
        long total = 0;
        for (int i = 0; i < s.length(); i++) {
            int c = s.charAt(i) - 'a';
            endingHere += i - last[c];
            total += endingHere;
            last[c] = i;
        }
        return total;
    }
}
```

**Key logic / explanation:** `endingHere` is the sum of distinct-character counts over all substrings ending at `i`. When a new `c` arrives, exactly the substrings beginning at indices `last[c]+1...i` gain one distinct character. `total += endingHere` aggregates these across all endpoints. The 26-entry array makes updates constant time.

**Time complexity:** `O(n)`.  
**Space complexity:** `O(1) auxiliary (26 entries)`.

### LeetCode 828

**Question:** For a string `s` consisting of uppercase letters, define the **unique-character count** of a substring as the number of letters occurring **exactly once** in it (not merely distinct letters). Return the sum of this count over **all nonempty contiguous substrings**, counting each occurrence/position separately.

**Examples:**

```text
s = "ABC"      -> 10
s = "ABA"      -> 8     ("ABA" contributes 1, not 2)
s = "LEETCODE" -> 92
```

**Constraints:** `1 <= s.length <= 10^5`; uppercase English letters; result fits 32-bit signed integer.

**Why this subpattern applies:** An occurrence at index `p` is unique only in substrings whose start is after the previous same letter and whose end is before the next same letter. Its contribution is `(p - previous) * (next - p)`.

**Connection to previous solution:** **2262 → 828:** Replace the single last-index contribution `i-last[c]` with **two prior positions for each letter**, finalizing the previous occurrence when the next occurrence arrives, then include right-boundary contributions.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public int uniqueLetterString(String s) {
        int[] prevPrev = new int[26];
        int[] prev = new int[26];
        java.util.Arrays.fill(prevPrev, -1);
        java.util.Arrays.fill(prev, -1);
        long answer = 0;
        for (int i = 0; i < s.length(); i++) {
            int c = s.charAt(i) - 'A';
            // The occurrence at prev[c] now knows its next occurrence i.
            answer += (long) (prev[c] - prevPrev[c]) * (i - prev[c]);
            prevPrev[c] = prev[c];
            prev[c] = i;
        }
        int n = s.length();
        for (int c = 0; c < 26; c++) {
            // Final occurrence has the right boundary at n.
            answer += (long) (prev[c] - prevPrev[c]) * (n - prev[c]);
        }
        return (int) answer;
    }
}
```

**Key logic / explanation:** At each new position `i`, the old position `prev[c]` now has its nearest same-character neighbors (`prevPrev[c]` and `i`). Count the choices of substring start and end that contain that occurrence exactly once. The final loop uses right sentinel `n` for each character’s last occurrence. For letters never seen, both previous positions are `-1`, yielding zero.

**Time complexity:** `O(n + 26) = O(n)`.  
**Space complexity:** `O(1) auxiliary (two 26-element arrays)`.

---

## S14 — Segmented string formatting

**When to apply:** Use when the answer requires keeping content units (words or characters) but rebuilding separators and groups according to an exact spacing, width, or capitalization rule.

**Basic Java 21 Code (reusable subpattern snippet, NOT a complete problem solution):**

```java
StringBuilder out = new StringBuilder();
int emitted = 0;
for (String unit : units) { // tokens produced by scanning input
    if (emitted++ > 0) out.append(' '); // substitute separator rule
    out.append(unit);
}
```

### LeetCode 1592

**Question:** You are given `text` containing lowercase words and spaces. Preserve the order of the words and use **all existing spaces** in the reconstructed string. Distribute spaces **as equally as possible between adjacent words**, placing leftover spaces at the **end**. If there is only one word, put every space after it. The final string must have the same total length as `text`.

**Examples:**

```text
text = "  this   is  a sentence "
  -> "this   is   a   sentence"
text = " practice   makes   perfect"
  -> "practice   makes   perfect "
text = "  hello" -> "hello  "
```

**Constraints:** `1 <= text.length <= 100`; lowercase words and spaces; at least one word.

**Why this subpattern applies:** Words remain unchanged and in order; only separator widths are recalculated. Therefore, tokenize the content, count all spaces, then reconstruct using a mathematically determined number of gaps.

**Connection to previous solution:** **Foundation of S14 (new family).** Extract words as units, compute `spaces / (wordCount-1)` and `spaces % (wordCount-1)`, then emit words and separators.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public String reorderSpaces(String text) {
        java.util.List<String> words = new java.util.ArrayList<>();
        int spaces = 0;
        int n = text.length();
        for (int i = 0; i < n; ) {
            if (text.charAt(i) == ' ') {
                spaces++;
                i++;
            } else {
                int start = i;
                while (i < n && text.charAt(i) != ' ') i++;
                words.add(text.substring(start, i));
            }
        }
        int gaps = words.size() - 1;
        int between = gaps == 0 ? 0 : spaces / gaps;
        int trailing = gaps == 0 ? spaces : spaces % gaps;
        StringBuilder out = new StringBuilder(n);
        for (int i = 0; i < words.size(); i++) {
            if (i > 0) out.append(" ".repeat(between));
            out.append(words.get(i));
        }
        out.append(" ".repeat(trailing));
        return out.toString();
    }
}
```

**Key logic / explanation:** Each space is counted, not discarded. There are `wordCount-1` gaps; distribute `spaces/gaps` to each and put the remainder after the last word. A single-word input has no gaps, so all spaces trail the word. `String.repeat` is available in Java 21.

**Time complexity:** `O(n)`.  
**Space complexity:** `O(n) for tokens and result`.

### LeetCode 482

**Question:** A license string `s` contains alphanumeric characters and hyphens, and integer `k` specifies the intended group size. Remove existing hyphens and **uppercase all letters**. Insert new hyphens so that groups are formed **from right to left**, each of size exactly `k` except possibly the **first** group, whose size may be between 1 and `k`. No group is empty.

**Examples:**

```text
s = "5F3Z-2e-9-w", k = 4 -> "5F3Z-2E9W"
s = "2-5g-3-J",   k = 2 -> "2-5G-3J"
s = "---",        k = 3 -> ""
```

**Constraints:** `1 <= s.length <= 10^5`; `1 <= k <= 10^4`; letters, digits, hyphens.

**Why this subpattern applies:** Grouping from the right makes every group except the first a fixed width `k`. A right-to-left scan removes old separators, normalizes case, and inserts the new separator after every `k` emitted alphanumeric characters.

**Connection to previous solution:** **1592 → 482:** Keep reconstructing content after removing old separators, but replace variable-sized word gaps with **fixed-width character groups from the right** and apply uppercasing.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public String licenseKeyFormatting(String s, int k) {
        StringBuilder reversed = new StringBuilder();
        int groupSize = 0;
        for (int i = s.length() - 1; i >= 0; i--) {
            char c = s.charAt(i);
            if (c == '-') continue;
            if (groupSize == k) {
                reversed.append('-');
                groupSize = 0;
            }
            reversed.append(Character.toUpperCase(c));
            groupSize++;
        }
        return reversed.reverse().toString();
    }
}
```

**Key logic / explanation:** Because construction runs right-to-left, insert a hyphen **before** the next alphanumeric character whenever `k` characters have already been added to the current group; this prevents trailing or leading hyphens. Reverse once at the end to restore order.

**Time complexity:** `O(n)`.  
**Space complexity:** `O(n) for result`.

---

## S15 — Index-to-output layout

**When to apply:** Use when characters must be assigned to output buckets based on a repeated positional traversal, while input order within each bucket is preserved.

**Basic Java 21 Code (reusable subpattern snippet, NOT a complete problem solution):**

```java
StringBuilder[] rows = new StringBuilder[rowCount];
for (int r = 0; r < rowCount; r++) rows[r] = new StringBuilder();
int row = 0, direction = 1;
for (int i = 0; i < s.length(); i++) {
    rows[row].append(s.charAt(i));
    // update row/direction, guarding rowCount == 1
}
```

### LeetCode 6

**Question:** Given a string `s` and number of rows `numRows`, write the string in a **zigzag pattern** by moving down the rows and then diagonally up repeatedly. Return the characters as if the rows were read top to bottom, left to right. Do not return the visual matrix; return the row-wise concatenated string.

**Examples:**

```text
s = "PAYPALISHIRING", numRows = 3 -> "PAHNAPLSIIGYIR"
s = "PAYPALISHIRING", numRows = 4 -> "PINALSIGYAHRPI"
s = "A",              numRows = 1 -> "A"
```

**Constraints:** `1 <= s.length <= 1000`; `1 <= numRows <= 1000`; English letters, commas and periods.

**Why this subpattern applies:** The zigzag is determined by a finite row position and direction, so characters can be appended directly to per-row builders, avoiding a full 2D grid.

**Connection to previous solution:** **Foundation of S15 (new family).** Maintain `row` and `direction`, append each input character to one of `numRows` output buckets, then concatenate the buckets.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public String convert(String s, int numRows) {
        if (numRows == 1 || numRows >= s.length()) return s;
        StringBuilder[] rows = new StringBuilder[numRows];
        for (int r = 0; r < numRows; r++) rows[r] = new StringBuilder();
        int row = 0, direction = 1;
        for (int i = 0; i < s.length(); i++) {
            rows[row].append(s.charAt(i));
            if (row == 0) direction = 1;
            else if (row == numRows - 1) direction = -1;
            row += direction;
        }
        StringBuilder out = new StringBuilder(s.length());
        for (StringBuilder bucket : rows) out.append(bucket);
        return out.toString();
    }
}
```

**Key logic / explanation:** Use row `0,1,...,numRows-1,numRows-2,...,1,0,...`. Reverse movement direction at the first and last row. Since each row builder stores precisely the characters that would appear in that visual row, concatenation produces the target without allocating the empty cells of a zigzag grid.

**Time complexity:** `O(n + r), r = numRows, with early return when r >= n`.  
**Space complexity:** `O(n + r) for row builders and result`.

---

## S16 — Single-character lexicographic change

**When to apply:** Use when exactly **one** character may be changed and the goal is the smallest lexicographic result subject to structural constraints; the earliest possible improvement dominates later changes.

**Basic Java 21 Code (reusable subpattern snippet, NOT a complete problem solution):**

```java
char[] a = s.toCharArray();
for (int i = 0; i < a.length; i++) {
    if (/* earliest useful position */ false) {
        a[i] = 'a';
        break;
    }
}
```

### LeetCode 1328

**Question:** You are given a palindrome `palindrome` consisting of lowercase letters. Change **exactly one** character to any **different** lowercase letter such that the resulting string is **not a palindrome** and is **lexicographically smallest** among all such results. If no such modification is possible, return `""`.

**Examples:**

```text
palindrome = "abccba" -> "aaccba"
palindrome = "a"      -> ""
palindrome = "aa"     -> "ab"
```

**Constraints:** `1 <= palindrome.length <= 1000`; lowercase English letters; input guaranteed palindrome.

**Why this subpattern applies:** Only changes in the first half can make an earlier lexicographic improvement using `a`. If the first half already contains only `a`, the smallest valid change is to modify the final character to `b`. The center of an odd-length palindrome must be ignored.

**Connection to previous solution:** **Foundation of S16 (new family).** Scan the **first half** for the earliest non-`a` character; otherwise change the last character. This is a lexicographic edit rule, not the zigzag routing algorithm from S15.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public String breakPalindrome(String palindrome) {
        int n = palindrome.length();
        if (n == 1) return "";
        char[] a = palindrome.toCharArray();
        for (int i = 0; i < n / 2; i++) {
            if (a[i] != 'a') {
                a[i] = 'a';
                return new String(a);
            }
        }
        a[n - 1] = 'b';
        return new String(a);
    }
}
```

**Key logic / explanation:** Changing the middle character of an odd-length palindrome does **not** break its symmetry, so only indices `< n/2` are candidates for an early `a`. If there is no such character, any change earlier than the final position would increase an `a`; changing the final `a` to `b` causes the smallest possible increase.

**Time complexity:** `O(n)`.  
**Space complexity:** `O(n) for char array and returned string`.

---

## S17 — Periodic string compatibility

**When to apply:** Use when **both** complete strings must be repetitions of one common nonempty block. Commuting concatenations test compatibility, and gcd of their lengths gives the greatest candidate block size.

**Basic Java 21 Code (reusable subpattern snippet, NOT a complete problem solution):**

```java
boolean compatible = (a + b).equals(b + a);
int x = a.length(), y = b.length();
while (y != 0) { int t = x % y; x = y; y = t; }
// if compatible, candidate common unit has length x
```

### LeetCode 1071

**Question:** For two strings `str1` and `str2`, a nonempty string `x` **divides** a string `y` if `y` consists of `x` repeated one or more times, with no extra characters. Return the **longest** string that divides **both** input strings, or `""` if no such common repeating unit exists.

**Examples:**

```text
str1 = "ABCABC", str2 = "ABC"    -> "ABC"
str1 = "ABABAB", str2 = "ABAB"   -> "AB"
str1 = "LEET",   str2 = "CODE"   -> ""
```

**Constraints:** `1 <= str1.length, str2.length <= 1000`; uppercase English letters.

**Why this subpattern applies:** If a common repeating block exists, `str1 + str2` and `str2 + str1` must be identical. When this compatibility holds, the longest possible block length is `gcd(len1,len2)`.

**Connection to previous solution:** **Foundation of S17 (new family).** Test concatenation commutativity and compute the length gcd using Euclid’s algorithm; not a variant of S16’s one-character editing strategy.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public String gcdOfStrings(String str1, String str2) {
        if (!(str1 + str2).equals(str2 + str1)) return "";
        int a = str1.length(), b = str2.length();
        while (b != 0) {
            int remainder = a % b;
            a = b;
            b = remainder;
        }
        return str1.substring(0, a);
    }
}
```

**Key logic / explanation:** The concatenation equality is the essential compatibility test: length gcd alone can incorrectly suggest a common block when letters differ. If compatible, a prefix with gcd length repeats to construct both strings, and any longer common divisor would require a larger common divisor of both lengths.

**Time complexity:** `O(n+m + log min(n,m)) = O(n+m)`.  
**Space complexity:** `O(n+m) for concatenations; O(min(n,m)) for returned substring`.

---

## S18 — String rotation as substring search

**When to apply:** Use when a string is allowed to undergo any number of **cyclic left rotations**. A target of equal length is a valid rotation precisely when it is a substring of `s+s`; reuse the already learned KMP matcher from S10.

**Basic Java 21 Code (reusable subpattern snippet, NOT a complete problem solution):**

```java
if (a.length() == b.length()) {
    String doubled = a + a;
    // search for b inside doubled using the S10 KMP matcher
}
```

### LeetCode 796

**Question:** Given lowercase strings `s` and `goal`, you may repeatedly **move the first character of `s` to the end**. Return `true` if zero or more such shifts can transform `s` into `goal`; otherwise return `false`. All other characters and their cyclic relative order must be preserved.

**Examples:**

```text
s = "abcde", goal = "cdeab" -> true
s = "abcde", goal = "abced" -> false
s = "a",     goal = "a"     -> true
```

**Constraints:** `1 <= s.length, goal.length <= 100`; lowercase English letters.

**Why this subpattern applies:** All rotations of `s` are length-`n` windows of `s+s`; after a length check, substring search is exactly the required test. KMP provides linear search without enumerating rotations.

**Connection to previous solution:** **Foundation of S18 (new family, deliberate cross-family reuse).** Double the source and invoke the **S10 KMP LPS + text scan**. This is NOT an evolution of immediately preceding LC 1071.

**Java Solution (complete, optimized, Java 21-compatible):**

```java
class Solution {
    public boolean rotateString(String s, String goal) {
        if (s.length() != goal.length()) return false;
        int m = goal.length();
        int[] lps = new int[m];
        for (int i = 1, len = 0; i < m; ) {
            if (goal.charAt(i) == goal.charAt(len)) {
                lps[i++] = ++len;
            } else if (len > 0) {
                len = lps[len - 1];
            } else {
                lps[i++] = 0;
            }
        }
        String text = s + s;
        int matched = 0;
        for (int i = 0; i < text.length(); i++) {
            while (matched > 0 && text.charAt(i) != goal.charAt(matched)) {
                matched = lps[matched - 1];
            }
            if (text.charAt(i) == goal.charAt(matched)) matched++;
            if (matched == m) return true;
        }
        return false;
    }
}
```

**Key logic / explanation:** The characters of any rotation appear consecutively somewhere in doubled `s`. Reject unequal lengths first. The exact KMP prefix fallback from LC 28 detects `goal` in O(n), using LPS for overlap; no per-rotation string generation is necessary.

**Time complexity:** `O(n+m), with n=m after length validation`.  
**Space complexity:** `O(n+m) including doubled text and LPS`.

---

## Status, ownership, and reference notes

- **Total locked content included:** 35 / 35 questions, exactly in the T02 master-registry order, with one source-aligned template per S1–S18 family.
- **Actual progress in the practice chat at preparation time:** **19/35 completed** through **LeetCode 28**. The remaining **459 → 796** entries above are ready-to-study reference answers. Exporting them does not count them as completed and does not skip them during subsequent `next` practice.
- **Core algorithm boundaries:** Anagram grouping/frequency-map variants belong to T06, sliding-window substring constraints to T04, stack parsing to T12, dictionary segmentation to T25, and generic reversed-character arrays to T03. They are not inserted as extra T02 questions.
- **Unicode caveat:** These optimized snippets follow the English/ASCII character constraints of their individual LeetCode inputs. Java `char` is a UTF-16 code unit and is not a general-purpose grapheme-cluster representation.
- **KMP, Z, rolling-hash tradeoff:** KMP matches a fixed pattern exactly and deterministically in linear time with an LPS array; Z computes prefix alignment of every suffix in linear time; rolling hash is useful for many window lengths and supports binary search but needs collision management.
- **Reusable file:** Save this `.md` to your own storage if you want it as an offline workbook. This session cannot automatically replace the Project’s master curriculum or edit your device files.
