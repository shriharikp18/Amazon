# 1. Valid Parentheses (LeetCode 20)

## 1. Pattern
Stack, Hash Map.

## 2. What is the interviewer asking?
The interviewer wants to verify if an input string containing only brackets (`(`, `)`, `{`, `}`, `[`, `]`) is valid. A string is valid if:
- Open brackets are closed by the same type of brackets.
- Open brackets are closed in the correct order.
- Every close bracket has a corresponding open bracket of the same type.

## 3. Intuition
Brackets are nested, meaning the most recently opened bracket must be the first one to be closed. This Last-In, First-Out (LIFO) behavior naturally maps to a Stack data structure. As we iterate through the string:
- If we see an opening bracket, we store it to be matched later.
- If we see a closing bracket, we check if it matches the most recently stored opening bracket (which is at the top of our stack).
- Using a Hash Map allows us to quickly look up the corresponding opening bracket for any given closing bracket.

## 4. Brute Force
Approach:
1. Scan the string for any adjacent matching pairs: `()`, `[]`, or `{}`.
2. If a matching pair is found, remove it from the string.
3. Repeat this scanning and deletion process until no matching pairs are found.
4. If the string becomes empty, it is valid. If characters remain but no adjacent pairs exist, it is invalid.

Time: $O(N^2)$ because in the worst case we make $O(N)$ scans of the string, and each removal shifts the remaining characters, taking $O(N)$ time.
Space: $O(N)$ or $O(1)$ depending on whether we create new string copies or modify the original string in-place.

## 5. Optimized Approach
1. Initialize an empty stack of characters.
2. Initialize a hash map where keys are closing brackets and values are their corresponding opening brackets: `')' -> '('`, `'}' -> '{'`, `']' -> '['`.
3. Iterate through each character `c` in the string `s`:
   - Check if `c` is a closing bracket (i.e., it exists as a key in our map).
   - If it is a closing bracket, check if the stack is empty. If it is empty, there is no matching opening bracket, so return `false`.
   - If the stack is not empty, pop the top element and check if it matches the expected opening bracket `map[c]`. If it does not match, return `false`.
   - If `c` is an opening bracket (not in the map keys), push it onto the stack.
4. After processing the entire string, check if the stack is empty. If it is empty, return `true` (all brackets were matched). If elements remain, return `false`.

## 6. Why does it work?
The stack maintains the exact order of active, unmatched opening brackets. Because matching brackets must nest symmetrically, a closing bracket must match the most recently opened bracket. By looking up the matching pair in the hash map and comparing it to the top of the stack, we guarantee that brackets are closed in the correct, nested order. If the stack is empty at the end, it proves that no opening brackets were left unmatched.

## 7. C++ Code
```cpp
#include <string>
#include <stack>
#include <unordered_map>

using namespace std;

bool isValid(string s) {
    stack<char> st;
    // Map closing brackets to their corresponding opening brackets
    unordered_map<char, char> map = {{')', '('}, {'}', '{'}, {']', '['}};
    
    for (char c : s) {
        // If the character is a closing bracket
        if (map.count(c)) {
            // Stack cannot be empty, and top of stack must match corresponding opening bracket
            if (st.empty() || st.top() != map[c]) {
                return false;
            }
            st.pop(); // Successfully matched and closed
        } else {
            // If it's an opening bracket, push onto the stack
            st.push(c);
        }
    }
    // If stack is empty, all brackets were correctly matched
    return st.empty();
}
```

## 8. Dry Run
Input: `s = "()[]{}"`

- **Initialization**: `st = []`, `map = {')': '(', '}': '{', ']': '['}`
- **Step 1**: `c = '('`. Not a closing bracket (not in map keys). Push to stack.
  - `st = ['(']`
- **Step 2**: `c = ')'`. It is a closing bracket.
  - `st` is not empty. `st.top()` is `'('`.
  - `map[')']` is `'('`. They match!
  - Pop from stack. `st = []`
- **Step 3**: `c = '['`. Not a closing bracket. Push to stack.
  - `st = ['[']`
- **Step 4**: `c = ']'`. It is a closing bracket.
  - `st` is not empty. `st.top()` is `'['`.
  - `map[']']` is `'['`. They match!
  - Pop from stack. `st = []`
- **Step 5**: `c = '{'`. Not a closing bracket. Push to stack.
  - `st = ['{']`
- **Step 6**: `c = '}'`. It is a closing bracket.
  - `st` is not empty. `st.top()` is `'{'`.
  - `map['}']` is `'{'`. They match!
  - Pop from stack. `st = []`
- **End of Loop**: Stack `st` is empty. Return `true`.

## 9. Complexity
- **Time Complexity**: $O(N)$ where $N$ is the length of the string. We traverse the string exactly once, performing $O(1)$ push and pop operations per character.
- **Space Complexity**: $O(N)$ in the worst case where the string consists entirely of opening brackets (e.g., `"(({[[{..."`), requiring all characters to be pushed onto the stack.

## 10. Edge Cases
- **Single character**: `s = "["` or `s = "]"` (returns `false` immediately because either the stack remains non-empty or we try to pop from an empty stack).
- **Incorrect order of matches**: `s = "(]"` or `s = "([)]"` (returns `false` because the closing bracket does not match the top of the stack).
- **Only closing brackets**: `s = "]]}"` (returns `false` as the stack is empty when the first closing bracket is processed).
- **Empty string**: `s = ""` (returns `true` because the loop does not run and stack is empty).

## 11. Follow-up Questions
- **Can we optimize space if there is only one type of bracket (e.g., only `(` and `)`)?** Yes, we can replace the stack with a simple counter variable. Increment the counter for `(`, decrement for `)`. If the counter drops below zero at any point, or is non-zero at the end, return `false`. This reduces space complexity to $O(1)$.
- **What if there are other characters in the string (e.g., `"a(b)c"`)?** We can modify the loop to ignore non-bracket characters and only perform stack operations when encountering brackets.
- **How would you support custom brackets added at runtime?** We could pass the bracket pairs configuration as a map parameter to the function instead of hardcoding it.

## 12. Interview Explanation
"First, I would clarify the constraints of the problem, such as the maximum length of the string and if other characters can be present. Then, I'd explain the brute force approach where we repeatedly scan the string and remove adjacent matching pairs, which takes quadratic time. To optimize this, I would use a stack to keep track of the opening brackets in a LIFO manner, alongside a hash map to quickly map closing brackets to their corresponding opening brackets. As we iterate through the string, we push opening brackets to the stack and pop them when we encounter matching closing brackets. If we find a mismatch or if the stack is not empty at the end, the string is invalid. My C++ code uses this approach to achieve a linear time complexity of $O(N)$ and a space complexity of $O(N)$."

---

# 2. Valid Palindrome (LeetCode 125)

## 1. Pattern
Two Pointers.

## 2. What is the interviewer asking?
The interviewer wants to determine if a given string is a palindrome. A palindrome is a phrase that reads the same backward as forward. The key rules are:
- Ignore all non-alphanumeric characters (keep only letters and numbers).
- Ignore case sensitivity (treat uppercase and lowercase letters as identical).

## 3. Intuition
Instead of creating a modified copy of the string (which takes extra space), we can compare characters from both ends of the string and move inward. We use two pointers: one starting at the beginning (`left`) and one at the end (`right`). We skip non-alphanumeric characters on both sides and compare the alphanumeric characters. If they match (case-insensitively), we continue; otherwise, it is not a palindrome.

## 4. Brute Force
Approach:
1. Iterate through the string and build a new string containing only lowercase alphanumeric characters.
2. Create a reversed copy of this new string.
3. Compare the filtered string with its reversed copy. If they are identical, return `true`; else, return `false`.

Time: $O(N)$ where $N$ is the length of the string. We iterate over the string to filter it, reverse it, and compare it.
Space: $O(N)$ to store the filtered and reversed strings.

## 5. Optimized Approach
1. Initialize two pointers: `left = 0` and `right = s.size() - 1`.
2. Run a loop while `left < right`:
   - Increment `left` if `s[left]` is not an alphanumeric character.
   - Decrement `right` if `s[right]` is not an alphanumeric character.
   - Once both pointers point to alphanumeric characters:
     - Compare their lowercase values using `tolower()`.
     - If they do not match, return `false`.
     - If they match, increment `left` and decrement `right` to continue checking the inner substring.
3. If the pointers meet or cross without any mismatch, return `true`.

## 6. Why does it work?
A palindrome is symmetric about its center. By checking characters starting from the extreme ends and moving inward, we check this symmetry directly. Skipping non-alphanumeric characters dynamically ensures we only compare the characters that matter, without allocating extra memory for a filtered string.

## 7. C++ Code
```cpp
#include <string>
#include <cctype>

using namespace std;

bool isPalindrome(string s) {
    int left = 0, right = s.size() - 1;
    
    while (left < right) {
        // Skip non-alphanumeric characters from the left
        while (left < right && !isalnum(s[left])) {
            ++left;
        }
        // Skip non-alphanumeric characters from the right
        while (left < right && !isalnum(s[right])) {
            --right;
        }
        // Compare lowercase values of characters
        if (tolower(s[left]) != tolower(s[right])) {
            return false;
        }
        // Move pointers inward
        ++left;
        --right;
    }
    return true;
}
```

## 8. Dry Run
Input: `s = "A man, a plan, a canal: Panama"`

- **Initialization**: `left = 0`, `right = 29`
- **Step 1**:
  - `s[left]` is `'A'` (alphanumeric).
  - `s[right]` is `'a'` (alphanumeric).
  - `tolower('A') == tolower('a')` (both are `'a'`).
  - Pointers move: `left = 1`, `right = 28`
- **Step 2**:
  - `s[left]` is `' '` (non-alphanumeric). Loop increments `left` to `2` (`s[2] = 'm'`).
  - `s[right]` is `'m'` (alphanumeric).
  - `tolower('m') == tolower('m')`.
  - Pointers move: `left = 3`, `right = 27`
- **Step 3**:
  - `s[left]` is `'a'` (alphanumeric).
  - `s[right]` is `'a'` (alphanumeric).
  - Match!
  - Pointers move: `left = 4`, `right = 26`
- **Step 4**:
  - `s[left]` is `'n'` (alphanumeric).
  - `s[right]` is `'n'` (alphanumeric).
  - Match!
  - Pointers move: `left = 5`, `right = 25`
- **Step 5**:
  - `s[left]` is `','` (non-alphanumeric). `left` advances to `6` (`' '`), then to `7` (`'a'`).
  - `s[right]` is `':'` (non-alphanumeric). `right` decreases to `24` (`' '`), then to `23` (`'a'`).
  - `tolower('a') == tolower('a')`.
  - Match!
  - Pointers move: `left = 8`, `right = 22`
- **Step 6**:
  - Continuing this process, all alphanumeric characters match symmetrically until pointers cross.
- **End of Loop**: `left >= right`. Return `true`.

## 9. Complexity
- **Time Complexity**: $O(N)$ where $N$ is the length of the string. In the worst case, each character is visited at most twice (once by the inner skipping loops, once by the main pointer traversal).
- **Space Complexity**: $O(1)$ as the operations are performed in-place using two pointers with no extra memory allocation.

## 10. Edge Cases
- **Empty string**: `s = ""` (returns `true` instantly).
- **No alphanumeric characters**: `s = ".,;?!"` (pointers meet in the middle without performing comparisons, returns `true`).
- **Single character**: `s = "a"` (returns `true`).
- **Mixed case and spaces**: `s = "Ab ba"` (correctly identifies as palindrome).

## 11. Follow-up Questions
- **How would you handle Unicode characters (like accented characters or emoji)?** Standard C++ `isalnum` and `tolower` handle ASCII characters. For Unicode, we would need a wide-character library or UTF-8 decoding to normalize and identify alphanumeric characters.
- **If we are allowed to remove at most one character to make it a palindrome, how does the logic change?** This is LeetCode 680 (Valid Palindrome II). If a mismatch occurs at `left` and `right`, we check if the remaining substring after deleting `s[left]` or `s[right]` is a palindrome.
- **How would you optimize this if memory accesses are slow?** We can fetch chunks of the string into CPU cache or process them in registers using SIMD operations if processing very large documents.

## 12. Interview Explanation
"To solve this problem, I would explain that a palindrome is symmetric. The brute force method is to construct a clean string with only alphanumeric characters, reverse it, and check if it equals the original clean string. This takes linear space. To optimize this to $O(1)$ space, I would use the Two Pointers technique. I'll place one pointer at the start and another at the end of the string. While the start pointer is less than the end pointer, I will increment the start pointer to skip non-alphanumeric characters, and decrement the end pointer for the same reason. When both point to alphanumeric characters, I compare them case-insensitively. If they differ, I return false. If they match, I move both pointers inward. My C++ implementation achieves $O(N)$ time complexity and $O(1)$ space complexity."

---

# 3. Valid Anagram (LeetCode 242)

## 1. Pattern
Frequency Array / Hash Map.

## 2. What is the interviewer asking?
The interviewer wants to know if string `t` is an anagram of string `s`. An anagram is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once. Essentially, do `s` and `t` contain the exact same characters with the exact same frequencies?

## 3. Intuition
If two strings are anagrams:
- They must be of the same length.
- The frequency of each character in both strings must be identical.
We can count the frequency of characters in `s` and compare them with the frequency of characters in `t`. Since the character set is lowercase English letters, a simple fixed-size array of size 26 acts as a highly optimized hash map.

## 4. Brute Force
Approach:
1. Sort both strings `s` and `t` alphabetically.
2. Compare the sorted strings. If they are equal, return `true`; otherwise, return `false`.

Time: $O(N \log N)$ where $N$ is the length of the strings, due to sorting.
Space: $O(1)$ if the sorting algorithm can sort in-place, or $O(N)$ if the language makes copies of strings during sorting.

## 5. Optimized Approach
1. If the size of `s` is not equal to the size of `t`, they cannot be anagrams. Return `false`.
2. Initialize an integer array (or vector) `count` of size 26 with all zeros to represent the letters 'a' through 'z'.
3. Loop through string `s` and increment the count of each character: `count[c - 'a']++`.
4. Loop through string `t` and decrement the count of each character: `count[c - 'a']--`.
   - If the count for any character becomes negative (drops below 0), it means `t` contains more occurrences of this character than `s` does. Return `false` immediately.
5. If the loop completes successfully, return `true`.

## 6. Why does it work?
Because we first verify that `s` and `t` have the same length, if all counts are balanced back to zero, they must have the same characters with the same counts. Decrementing the frequency count as we traverse `t` allows us to catch any character excess in `t` immediately (when a count drops below zero). Since the total lengths are equal, if one character count drops below zero, some other character count must be above zero, meaning they cannot be anagrams.

## 7. C++ Code
```cpp
#include <string>
#include <vector>

using namespace std;

bool isAnagram(string s, string t) {
    // If lengths are different, they cannot be anagrams
    if (s.size() != t.size()) {
        return false;
    }
    
    // Frequency array for 26 lowercase English alphabets
    vector<int> count(26, 0);
    
    // Increment frequency counts based on string s
    for (char c : s) {
        count[c - 'a']++;
    }
    
    // Decrement counts based on string t
    for (char c : t) {
        // If a count goes below 0, it means t has more of character 'c' than s
        if (--count[c - 'a'] < 0) {
            return false;
        }
    }
    
    return true;
}
```

## 8. Dry Run
Input: `s = "anagram"`, `t = "nagaram"`

- **Step 1**: Compare sizes. `s.size() = 7`, `t.size() = 7`. Equal, continue.
- **Step 2**: Populate `count` for `s`:
  - `a`: 3, `n`: 1, `g`: 1, `r`: 1, `m`: 1 (other values 0)
- **Step 3**: Process `t` character by character:
  - `c = 'n'`: decrement count of `n` to 0. Is `0 < 0`? No.
  - `c = 'a'`: decrement count of `a` to 2. Is `2 < 0`? No.
  - `c = 'g'`: decrement count of `g` to 0. Is `0 < 0`? No.
  - `c = 'a'`: decrement count of `a` to 1. Is `1 < 0`? No.
  - `c = 'r'`: decrement count of `r` to 0. Is `0 < 0`? No.
  - `c = 'a'`: decrement count of `a` to 0. Is `0 < 0`? No.
  - `c = 'm'`: decrement count of `m` to 0. Is `0 < 0`? No.
- **Step 4**: Loop finished successfully. Return `true`.

## 9. Complexity
- **Time Complexity**: $O(N)$ where $N$ is the length of string `s` (or `t`). We loop through the strings of size $N$ at most twice.
- **Space Complexity**: $O(1)$ auxiliary space. The vector size is fixed at 26, which does not grow with the input size.

## 10. Edge Cases
- **Different lengths**: `s = "abc"`, `t = "ab"` (returns `false` immediately).
- **All characters same except one**: `s = "aab"`, `t = "aac"` (returns `false`).
- **Single character strings**: `s = "a"`, `t = "a"` (returns `true`).
- **Characters not present in 'a'-'z'**: (The problem statement usually restricts input to lowercase letters, but if not, this would cause out-of-bounds error. We handle this in follow-up questions).

## 11. Follow-up Questions
- **What if the inputs contain Unicode characters?** We cannot use a simple array of size 26. We should use an `unordered_map<char, int>` to count the frequencies. The logic remains the same, but lookup and insertion average $O(1)$ time complexity instead of absolute $O(1)$ array access.
- **Can we solve it with $O(1)$ space if we are not allowed to use extra arrays/hash maps?** Yes, by sorting the strings in place. Sorting takes $O(N \log N)$ time and $O(1)$ space (if in-place).
- **Can we sum the ASCII values of characters to verify?** No, because different combinations of characters can sum to the same ASCII value (e.g., `"ab"` sums to 97 + 98 = 195, and `"cc"` sums to 99 + 96 = 195, which is false positive).

## 12. Interview Explanation
"To check if two strings are anagrams, they must have the exact same characters with the same frequencies. A brute force approach is to sort both strings and compare them, which takes $O(N \log N)$ time. To optimize this, I can use a frequency map. Since the input contains only lowercase English letters, I can use a fixed-size integer array of size 26. I first check if the strings are of equal length. If they are, I loop through the first string to increment character frequencies. Then, I loop through the second string to decrement them. If any frequency drops below zero, it means the second string has an extra character, and I can return false immediately. This achieves $O(N)$ time complexity and $O(1)$ auxiliary space."

---

# 4. Group Anagrams (LeetCode 49)

## 1. Pattern
Hash Map, Sorting, Categorization.

## 2. What is the interviewer asking?
The interviewer wants to group a list of strings such that all strings that are anagrams of each other are placed together in the same list. The final return type is a list of lists of strings, and the order of the groups does not matter.

## 3. Intuition
To group anagrams together, we need a common "key" or "identifier" that is identical for all anagrams in a group.
- The sorted version of any anagram is identical (e.g., `"eat"`, `"tea"`, and `"ate"` all sort to `"aet"`).
- We can use this sorted string as a key in a Hash Map (`unordered_map`). The value mapped to this key will be a list (`vector<string>`) of all the original strings that sort to this key.

## 4. Brute Force
Approach:
1. For each string in the input list, compare it against all other strings to see if they are anagrams (using the Valid Anagram checker).
2. Maintain a list of groups. If a string is an anagram of an existing group's representative, add it to that group.
3. If it doesn't match any existing group, create a new group with this string.

Time: $O(N^2 \cdot K)$ where $N$ is the number of strings and $K$ is the maximum length of a string.
Space: $O(N \cdot K)$ to store the grouped results.

## 5. Optimized Approach
1. Initialize an `unordered_map<string, vector<string>> map`.
2. Iterate through each string `s` in the input vector `strs`:
   - Make a copy of `s` and name it `sorted`.
   - Sort the characters of `sorted` alphabetically using `sort()`.
   - Use `sorted` as the key and push the original string `s` into `map[sorted]`.
3. Initialize a 2D vector `result` to store the grouped anagrams.
4. Iterate through the map and add each group (`pair.second`) to `result`.
5. Return `result`.

## 6. Why does it work?
Sorting a string canonicalizes it. Since all anagrams share the exact same characters, sorting them produces the exact same sequence of characters. By using this sorted sequence as a key in a hash map, we ensure that all anagrams hash to the same bucket. When we collect the vectors stored under each key, we get the complete grouped lists.

## 7. C++ Code
```cpp
#include <vector>
#include <string>
#include <unordered_map>
#include <algorithm>

using namespace std;

vector<vector<string>> groupAnagrams(vector<string>& strs) {
    // Map from the sorted version of the string to its anagrams
    unordered_map<string, vector<string>> map;
    
    for (string s : strs) {
        string sorted = s;
        // Sort the string to create the canonical key
        sort(sorted.begin(), sorted.end());
        // Group the original string under its sorted key
        map[sorted].push_back(s);
    }
    
    vector<vector<string>> result;
    // Collect all groups from the hash map
    for (auto& pair : map) {
        result.push_back(pair.second);
    }
    
    return result;
}
```

## 8. Dry Run
Input: `strs = ["eat", "tea", "tan", "ate", "nat", "bat"]`

- **Step 1**: `s = "eat"`. `sorted = "aet"`.
  - `map["aet"] = ["eat"]`
- **Step 2**: `s = "tea"`. `sorted = "aet"`.
  - `map["aet"] = ["eat", "tea"]`
- **Step 3**: `s = "tan"`. `sorted = "ant"`.
  - `map["ant"] = ["tan"]`
- **Step 4**: `s = "ate"`. `sorted = "aet"`.
  - `map["aet"] = ["eat", "tea", "ate"]`
- **Step 5**: `s = "nat"`. `sorted = "ant"`.
  - `map["ant"] = ["tan", "nat"]`
- **Step 6**: `s = "bat"`. `sorted = "abt"`.
  - `map["abt"] = ["bat"]`
- **Step 7**: Iterate over map.
  - Add `["eat", "tea", "ate"]` to `result`.
  - Add `["tan", "nat"]` to `result`.
  - Add `["bat"]` to `result`.
- Return `result`.

## 9. Complexity
- **Time Complexity**: $O(N \cdot K \log K)$ where $N$ is the number of strings in `strs` and $K$ is the maximum length of a string. We loop through $N$ strings, and for each string, we sort it which takes $O(K \log K)$ time. Map insertion and lookup takes $O(K)$ time for the key hashing.
- **Space Complexity**: $O(N \cdot K)$ to store the strings and keys inside the hash map.

## 10. Edge Cases
- **Empty string list**: `strs = [""]` (correctly maps `"" -> [""]`).
- **Single string**: `strs = ["a"]` (returns `[["a"]]`).
- **No anagrams**: `strs = ["abc", "def", "ghi"]` (returns `[["abc"], ["def"], ["ghi"]]`).
- **All anagrams**: `strs = ["abc", "bca", "cab"]` (returns `[["abc", "bca", "cab"]]`).

## 11. Follow-up Questions
- **Can we improve the time complexity to $O(N \cdot K)$?** Yes, instead of sorting each string to create a key, we can generate a count-based key. Since the characters are lowercase English letters, we can count the frequency of each character (a-z) and represent it as a string of counts separated by delimiters (e.g., `"#1#0#2..."` or `1,0,2...`). Generating this key takes $O(K)$ time instead of $O(K \log K)$.
- **What if the characters are unicode characters?** We can represent the frequency map as a count array of size 256 (for extended ASCII) or use a map-based representation for key creation.
- **How would you scale this to group anagrams from a file containing billions of words?** We could use MapReduce. In the Map phase, emit `(sorted_word, original_word)`. In the Reduce phase, group values by `sorted_word`.

## 12. Interview Explanation
"To group anagrams, the key observation is that all anagrams, when sorted, become the exact same string. My approach is to iterate through each string, sort it to create a unique key, and insert it into a hash map where the key is the sorted string and the value is a vector of original strings. After processing all input strings, we extract the grouped strings from the map and return them. This takes $O(N \cdot K \log K)$ time, where $N$ is the number of strings and $K$ is the max length of a string. We can optimize this to $O(N \cdot K)$ time by using character count arrays as keys instead of sorting."

---

# 5. Longest Palindromic Substring (LeetCode 5)

## 1. Pattern
Dynamic Programming / Two Pointers (Expand Around Center).

## 2. What is the interviewer asking?
Given a string `s`, find the longest contiguous substring that reads the same backward as forward. If there are multiple longest palindromic substrings, return any one of them.

## 3. Intuition
A substring `s[i...j]` is a palindrome if:
1. The characters at the boundaries are equal: `s[i] == s[j]`.
2. The inner substring `s[i+1...j-1]` is also a palindrome.
This recursive subproblem structure makes it ideal for Dynamic Programming. We can define a 2D boolean array `dp` where `dp[i][j]` is `true` if `s[i...j]` is a palindrome, and `false` otherwise. We build solutions for smaller substrings first and expand.

## 4. Brute Force
Approach:
1. Generate all possible substrings of the string.
2. For each substring, check if it is a palindrome.
3. Track the longest palindrome found.

Time: $O(N^3)$ because there are $O(N^2)$ substrings, and checking each substring takes $O(N)$ time.
Space: $O(1)$ if the palindrome check is performed in-place.

## 5. Optimized Approach
1. Let `n` be the size of the string. Initialize `start = 0` and `maxLength = 1`.
2. Create a 2D DP table `dp` of size `n x n` initialized to `false`.
3. Base Case: Every single character is a palindrome, so set `dp[i][i] = true` for all `i`.
4. Outer loop `i` runs backward from `n-1` down to `0`.
5. Inner loop `j` runs forward from `i+1` to `n-1`.
6. For each pair `(i, j)`:
   - If `s[i] == s[j]`, then it is a palindrome if either:
     - The substring has length 2 or 3 (i.e., `j - i == 1` or `j - i == 2`).
     - The inner substring is a palindrome (`dp[i+1][j-1] == true`).
   - If it is a palindrome (`dp[i][j] = true`), check if its length (`j - i + 1`) is greater than `maxLength`. If so, update `start = i` and `maxLength = j - i + 1`.
7. Return `s.substr(start, maxLength)`.

## 6. Why does it work?
By iterating `i` backward from `n-1` to `0` and `j` forward from `i+1` to `n-1`, we guarantee that before we compute `dp[i][j]`, the state for the inner substring `dp[i+1][j-1]` has already been computed. This bottom-up approach allows us to solve the palindromic state of any substring in $O(1)$ time by referencing already computed subproblems.

## 7. C++ Code
```cpp
#include <string>
#include <vector>

using namespace std;

string longestPalindrome(string s) {
    int n = s.size();
    if (n == 0) return "";
    
    int start = 0, maxLength = 1;
    // dp[i][j] will be true if substring s[i..j] is a palindrome
    vector<vector<bool>> dp(n, vector<bool>(n, false));
    
    // Base Case: All single character substrings are palindromes
    for (int i = 0; i < n; ++i) {
        dp[i][i] = true;
    }
    
    // Fill the DP table
    for (int i = n - 1; i >= 0; --i) {
        for (int j = i + 1; j < n; ++j) {
            // Check if boundary characters match and the inner substring is a palindrome
            if (s[i] == s[j] && (j - i == 1 || dp[i + 1][j - 1])) {
                dp[i][j] = true;
                // Update longest palindrome substring parameters
                if (j - i + 1 > maxLength) {
                    start = i;
                    maxLength = j - i + 1;
                }
            }
        }
    }
    
    return s.substr(start, maxLength);
}
```

## 8. Dry Run
Input: `s = "babad"`

- **Initialization**: `n = 5`, `start = 0`, `maxLength = 1`, `dp` table set to `false`, with main diagonal `dp[0][0] = dp[1][1] = dp[2][2] = dp[3][3] = dp[4][4] = true`.
- **i = 3**:
  - `j = 4`: `s[3] = 'a'`, `s[4] = 'd'`. Mismatch. `dp[3][4] = false`.
- **i = 2**:
  - `j = 3`: `s[2] = 'b'`, `s[3] = 'a'`. Mismatch.
  - `j = 4`: `s[2] = 'b'`, `s[4] = 'd'`. Mismatch.
- **i = 1**:
  - `j = 2`: `s[1] = 'a'`, `s[2] = 'b'`. Mismatch.
  - `j = 3`: `s[1] = 'a'`, `s[3] = 'a'`. Match! Inner is `dp[2][2]` which is `true`. `dp[1][3] = true`. Length is `3 - 1 + 1 = 3 > maxLength (1)`. Update `start = 1`, `maxLength = 3`.
  - `j = 4`: `s[1] = 'a'`, `s[4] = 'd'`. Mismatch.
- **i = 0**:
  - `j = 1`: `s[0] = 'b'`, `s[1] = 'a'`. Mismatch.
  - `j = 2`: `s[0] = 'b'`, `s[2] = 'b'`. Match! Inner is `dp[1][1] = true`. `dp[0][2] = true`. Length `3`, not greater than `maxLength (3)`.
  - `j = 3`: `s[0] = 'b'`, `s[3] = 'a'`. Mismatch.
  - `j = 4`: `s[0] = 'b'`, `s[4] = 'd'`. Mismatch.
- **Result**: Substring starting at `1` of length `3` is `"aba"`. Return `"aba"`.

## 9. Complexity
- **Time Complexity**: $O(N^2)$ to fill the 2D DP table.
- **Space Complexity**: $O(N^2)$ to store the DP table.

## 10. Edge Cases
- **Single character string**: `s = "a"` (returns `"a"`).
- **All characters identical**: `s = "aaaa"` (returns `"aaaa"`).
- **No palindrome of length > 1**: `s = "abc"` (returns `"a"` or any single char).
- **Even length palindrome**: `s = "cbbd"` (returns `"bb"`).

## 11. Follow-up Questions
- **Can we optimize space complexity to $O(1)$?** Yes, by using the **Expand Around Center** approach. There are $2N-1$ possible centers (each character and each gap between characters). For each center, we expand outward using two pointers. This takes $O(N^2)$ time but only $O(1)$ space.
- **Is there an $O(N)$ solution?** Yes, **Manacher's Algorithm** finds the longest palindromic substring in linear time by reusing symmetry information.
- **What if the string contains thousands of characters?** The DP approach would lead to memory limit exceeded. We should definitely use Expand Around Center or Manacher's Algorithm.

## 12. Interview Explanation
"To solve the longest palindromic substring problem, I would explain that a substring `s[i...j]` is a palindrome if the ends match and the inner portion `s[i+1...j-1]` is also a palindrome. I'd mention the brute force approach checks all $O(N^2)$ substrings, taking $O(N^3)$ time. To optimize this, I can use Dynamic Programming. I maintain a 2D boolean array where `dp[i][j]` represents if the substring `s[i...j]` is a palindrome. By iterating backwards for `i` and forwards for `j`, we build up the solutions from smaller substrings. This improves the time complexity to $O(N^2)$, though it uses $O(N^2)$ space. If requested, I can optimize the space to $O(1)$ by expanding around centers."

---

# 6. Minimum Window Substring (LeetCode 76)

## 1. Pattern
Sliding Window, Two Pointers, Hash Map.

## 2. What is the interviewer asking?
Given two strings `s` and `t`, return the minimum window substring of `s` such that every character in `t` (including duplicates) is included in the window. If there is no such substring, return an empty string `""`.

## 3. Intuition
We want to find a window in `s` that contains all characters of `t`.
- We can expand our window to the right until it is "valid" (contains all characters of `t`).
- Once the window is valid, we try to shrink it from the left to find the smallest possible valid window.
- When it becomes invalid, we resume expanding to the right.
This dynamic sliding window approach avoids checking all possible substrings. We can keep track of character frequencies using hash maps.

## 4. Brute Force
Approach:
1. Generate all possible substrings of `s`.
2. For each substring, count its character frequencies and check if it contains all characters of `t` with the required frequencies.
3. Track the substring with the minimum length that meets the criteria.

Time: $O(N^2 \cdot M)$ where $N = |s|$ and $M = |t|$, because there are $O(N^2)$ substrings, and checking each takes $O(N + M)$ time.
Space: $O(K)$ where $K$ is the character alphabet size.

## 5. Optimized Approach
1. Initialize an `unordered_map<char, int> freq` to store character frequencies of `t`.
2. Set up variables:
   - `left = 0`: left boundary of the window.
   - `minLen = INT_MAX`: stores length of the minimum window.
   - `count = 0`: tracks how many characters of `t` are currently matched.
   - `start = 0`: tracks the starting index of the minimum window.
3. Iterate through `s` with pointer `right` from `0` to `s.size() - 1`:
   - Decrement the count of `s[right]` in `freq`.
   - If the decremented value is $\ge 0$, it means `s[right]` is a necessary character from `t`. Increment `count`.
   - While `count == t.size()` (window is valid):
     - Check if current window size (`right - left + 1`) is smaller than `minLen`. If so, update `minLen` and `start`.
     - Prepare to shrink the window by moving `left` forward. Increment `freq[s[left]]`.
     - If the incremented value of `freq[s[left]]` becomes $> 0$, it means a required character has been excluded from the window. Decrement `count`.
     - Increment `left`.
4. Return `minLen == INT_MAX ? "" : s.substr(start, minLen)`.

## 6. Why does it work?
The window expands to find a valid window and shrinks to find the optimal window size. Since `right` only moves forward and `left` only moves forward, the algorithm processes each character at most twice. The `freq` map dynamically tracks target frequencies. When a value in the map is negative, it indicates we have extra copies of that character in our window. When it is positive, we are missing that character to make the window valid.

## 7. C++ Code
```cpp
#include <string>
#include <unordered_map>
#include <climits>

using namespace std;

string minWindow(string s, string t) {
    // Frequency map for characters in t
    unordered_map<char, int> freq;
    for (char c : t) {
        freq[c]++;
    }
    
    int left = 0, minLen = INT_MAX, count = 0, start = 0;
    
    for (int right = 0; right < s.size(); ++right) {
        // If s[right] is part of t, decrementing it will keep it >= 0
        if (--freq[s[right]] >= 0) {
            ++count;
        }
        
        // When the current window contains all characters of t
        while (count == t.size()) {
            // Update the minimum window parameters
            if (minLen > right - left + 1) {
                minLen = right - left + 1;
                start = left;
            }
            
            // Try to shrink the window from the left
            if (++freq[s[left]] > 0) {
                // If s[left] was a required character, decrement the match count
                --count;
            }
            left++;
        }
    }
    
    return minLen == INT_MAX ? "" : s.substr(start, minLen);
}
```

## 8. Dry Run
Input: `s = "ADOBECODEBANC"`, `t = "ABC"`

- **t freq map**: `{'A': 1, 'B': 1, 'C': 1}`
- **right = 0**, `s[0] = 'A'`: `freq['A']` becomes 0. `count` becomes 1.
- **right = 1**, `s[1] = 'D'`: `freq['D']` becomes -1.
- **right = 2**, `s[2] = 'O'`: `freq['O']` becomes -1.
- **right = 3**, `s[3] = 'B'`: `freq['B']` becomes 0. `count` becomes 2.
- **right = 4**, `s[4] = 'E'`: `freq['E']` becomes -1.
- **right = 5**, `s[5] = 'C'`: `freq['C']` becomes 0. `count` becomes 3.
  - Window `s[0...5] = "ADOBEC"` is valid.
  - `minLen` updated to `6`, `start = 0`.
  - Try to shrink: `s[left=0] = 'A'`. `freq['A']` becomes 1. `count` becomes 2. `left` becomes 1.
- **right = 6 to 9**: `s = "CODEB"`. Eventually `B` is processed, and at `right = 10`, `s[10] = 'A'`.
  - Window `s[5...10] = "CODEBA"` is valid.
  - Try to shrink from left (`left = 5`): `s[5] = 'C'`. `freq['C']` becomes 1. `count` becomes 2. `left` becomes 6.
- **right = 12**, `s[12] = 'C'`: Window `s[8...12] = "BANC"` is valid.
  - `minLen` updated to `4`, `start = 9` (substring `"BANC"`).
- Return `"BANC"`.

## 9. Complexity
- **Time Complexity**: $O(N + M)$ where $N = |s|$ and $M = |t|$. We scan `t` once to build the frequency map. The sliding window pointers `left` and `right` traverse `s` at most once.
- **Space Complexity**: $O(K)$ where $K$ is the number of unique characters in `t` and `s`. In the worst case, $K$ is bounded by the alphabet size (constant, $O(1)$).

## 10. Edge Cases
- **No valid window**: `s = "a"`, `t = "aa"` (returns `""`).
- **t is longer than s**: `s = "a"`, `t = "ab"` (returns `""`).
- **Exact match**: `s = "a"`, `t = "a"` (returns `"a"`).
- **Multiple identical characters**: `s = "aaflshba"`, `t = "aa"` (returns `"aa"`).

## 11. Follow-up Questions
- **What if the alphabet size is very large (e.g. Unicode)?** The logic remains the same; using `unordered_map` accommodates Unicode character inputs seamlessly.
- **How would you optimize space if `s` contains mostly non-target characters?** We can pre-filter `s` and store the indices of characters that exist in `t` in a separate list. Then slide the window only over these filtered indices.
- **How does the approach change if the window must preserve the order of characters in `t`?** That would be a different problem (like finding a subsequence), which would be solved using dynamic programming instead of sliding window.

## 12. Interview Explanation
"To find the minimum window substring containing all characters of `t`, I'll use the Sliding Window pattern. The brute force approach checks all substrings, which is too slow. With Sliding Window, I expand a `right` pointer to find a window containing all target characters. Once the window is valid, I contract it from the `left` as much as possible while maintaining validity, updating our minimum window length along the way. I track the frequency of target characters using a hash map and a `count` variable. This gives a linear time complexity of $O(N + M)$ and uses $O(K)$ space."

---

# 7. Find the Index of the First Occurrence in a String (LeetCode 28)

## 1. Pattern
String Matching, Sliding Window.

## 2. What is the interviewer asking?
Given two strings `needle` and `haystack`, find the index of the first occurrence of `needle` in `haystack`. If `needle` is not part of `haystack`, return `-1`.

## 3. Intuition
We want to find if the pattern `needle` exists in the text `haystack`.
- We can slide a window of size `n` (length of `needle`) along `haystack`.
- At each starting position `i` from `0` to `m - n` (where `m` is the length of `haystack`), we check if the substring starting at `i` matches `needle`.

## 4. Brute Force
Approach:
1. Loop through `haystack` from index `i = 0` to `m - n`.
2. For each index `i`, check character-by-character if the next `n` characters match `needle`.
3. If they match completely, return `i`.
4. If the loop completes without a match, return `-1`.

Time: $O((M - N + 1) \cdot N)$ where $M$ is the size of `haystack` and $N$ is the size of `needle`.
Space: $O(1)$ auxiliary space.

## 5. Optimized Approach
The provided C++ code slides a window using the built-in string substring comparison:
1. Compute the lengths `m = haystack.size()` and `n = needle.size()`.
2. If `n` is larger than `m`, the needle cannot fit inside the haystack. Return `-1`.
3. Iterate `i` from `0` up to `m - n`:
   - Extract the substring of length `n` starting at index `i`: `haystack.substr(i, n)`.
   - Compare it to `needle`. If they are equal, return `i` immediately.
4. If no match is found after the loop, return `-1`.

## 6. Why does it work?
Since we are looking for a exact substring match, any potential starting index must be in the range `[0, m - n]`. By checking every starting index in this range and comparing the next `n` characters, we guarantee that we find the first occurrence.

## 7. C++ Code
```cpp
#include <string>

using namespace std;

int strStr(string haystack, string needle) {
    int m = haystack.size(), n = needle.size();
    
    // Check all possible starting positions where needle can fit
    for (int i = 0; i <= m - n; ++i) {
        // If substring from i of length n matches needle, return the index
        if (haystack.substr(i, n) == needle) {
            return i;
        }
    }
    return -1;
}
```

## 8. Dry Run
Input: `haystack = "sadbutsad"`, `needle = "sad"`

- **Initialization**: `m = 9`, `n = 3`. Loop runs for `i <= 6`.
- **i = 0**:
  - `haystack.substr(0, 3)` extracts `"sad"`.
  - Compare `"sad"` with `needle` (`"sad"`).
  - Match found! Return `0`.

## 9. Complexity
- **Time Complexity**: $O(M \cdot N)$ in the worst case (e.g., `haystack = "aaaaa"`, `needle = "aab"`).
- **Space Complexity**: $O(N)$ because the `substr` function creates a new string of size `N` for comparison.

## 10. Edge Cases
- **needle is longer than haystack**: `haystack = "aaa"`, `needle = "aaaa"` (loop does not run, returns `-1`).
- **Partial match at the end**: `haystack = "mississippi"`, `needle = "ippi"` (correctly matches at the end).
- **Multiple occurrences**: `haystack = "sadbutsad"`, `needle = "sad"` (correctly returns first index `0`).

## 11. Follow-up Questions
- **How can we optimize the time complexity to $O(M + N)$?** We can use more advanced string search algorithms like **Knuth-Morris-Pratt (KMP)**, **Rabin-Karp** (rolling hash), or **Boyer-Moore**. KMP uses a prefix table (LPS array) to avoid backtracking the haystack pointer.
- **How does Rabin-Karp work?** Rabin-Karp computes a hash value for the pattern and for each sliding window of length `n` in the text. If hash values match, it performs a character-by-character comparison. Using rolling hash, the hash value of the next window is calculated in $O(1)$ time.
- **What is C++ `std::string::find`'s complexity?** Typically, standard library implementations of `find` use highly optimized search loops (like Boyer-Moore-Horspool or simple loops) which run very fast in practice.

## 12. Interview Explanation
"To find the first occurrence of a substring, the simplest approach is to check every possible substring of length $N$ in the haystack of length $M$. If they match, we return the index. This takes $O(M \cdot N)$ time in the worst case. I would write code to perform this sliding check up to index $M-N$. If we need a more optimal solution, I could implement the Knuth-Morris-Pratt (KMP) algorithm. KMP preprocesses the needle to build a Longest Prefix Suffix (LPS) array, allowing us to skip comparisons and achieve $O(M + N)$ time complexity and $O(N)$ space complexity."

---

# 8. String Compression (LeetCode 443)

## 1. Pattern
Two Pointers, In-place Mutation.

## 2. What is the interviewer asking?
Given an array of characters `chars`, compress it in-place using run-length encoding.
- For each group of consecutive repeating characters:
  - If the group length is 1, write the character.
  - If the group length is greater than 1, write the character followed by the group's length.
- The group length must be split into digits if it is $\ge 10$ (e.g., length 12 becomes `'1'`, `'2'`).
- Modify the input array in-place and return the new length of the compressed array.

## 3. Intuition
Since we must compress in-place and the compressed array is always shorter than or equal to the original array, we can use two pointers:
- A read pointer `i` to scan the groups of repeating characters.
- A write pointer `index` to write the compressed characters.
Because `index <= i`, we will never overwrite characters that we haven't read yet.

## 4. Brute Force
Approach:
1. Create a new vector or string.
2. Iterate through `chars`, count consecutive characters, and append the character and its count to the new vector.
3. Copy the elements of the new vector back to `chars` and return its size.

Time: $O(N)$ where $N$ is the length of `chars`.
Space: $O(N)$ to store the temporary vector.

## 5. Optimized Approach
1. Initialize `index = 0` (write pointer) and `n = chars.size()`.
2. Use a loop with `i = 0` to traverse the array:
   - Record the current character: `c = chars[i]`.
   - Initialize a local count to `0`.
   - While `i < n` and `chars[i] == c`, increment `i` and `count`.
   - Write the character to the write pointer: `chars[index++] = c`.
   - If `count > 1`:
     - Convert `count` to a string using `to_string(count)`.
     - Iterate through each digit in the count string and write it to `chars[index++]`.
3. Return `index` (the new size of the compressed array).

## 6. Why does it work?
Run-length compression reduces the size of repeating sequences. Even in the worst case where every character is unique, the write pointer `index` tracks the read pointer `i` exactly (no compression happens). In all other cases, `index` lags behind `i`. This allows us to overwrite the array in-place without losing any unread input data.

## 7. C++ Code
```cpp
#include <vector>
#include <string>

using namespace std;

int compress(vector<char>& chars) {
    int index = 0, n = chars.size();
    
    for (int i = 0; i < n; ) {
        char c = chars[i];
        int count = 0;
        
        // Count consecutive occurrences of character c
        while (i < n && chars[i] == c) {
            ++i;
            ++count;
        }
        
        // Write the character
        chars[index++] = c;
        
        // If count is greater than 1, write the digits of count
        if (count > 1) {
            for (char digit : to_string(count)) {
                chars[index++] = digit;
            }
        }
    }
    
    return index;
}
```

## 8. Dry Run
Input: `chars = ["a","a","b","b","c","c","c"]`

- **Initialization**: `index = 0`, `n = 7`, `i = 0`.
- **Iteration 1**:
  - `c = 'a'`.
  - Inner loop: `chars[0] == 'a'`, `chars[1] == 'a'`. `i` becomes `2`, `count` becomes `2`.
  - Write char: `chars[0] = 'a'`. `index` becomes `1`.
  - Since `count > 1`: write digit `'2'` to `chars[1]`. `index` becomes `2`.
- **Iteration 2**:
  - `i = 2`, `c = 'b'`.
  - Inner loop: `chars[2] == 'b'`, `chars[3] == 'b'`. `i` becomes `4`, `count` becomes `2`.
  - Write char: `chars[2] = 'b'`. `index` becomes `3`.
  - Write digit `'2'` to `chars[3]`. `index` becomes `4`.
- **Iteration 3**:
  - `i = 4`, `c = 'c'`.
  - Inner loop: `chars[4] == 'c'`, `chars[5] == 'c'`, `chars[6] == 'c'`. `i` becomes `7`, `count` becomes `3`.
  - Write char: `chars[4] = 'c'`. `index` becomes `5`.
  - Write digit `'3'` to `chars[5]`. `index` becomes `6`.
- **End of Loop**: `i == 7`. Return `index = 6`.
- Modified prefix of `chars` is `["a", "2", "b", "2", "c", "3"]`.

## 9. Complexity
- **Time Complexity**: $O(N)$ where $N$ is the number of characters. Although we have nested loops, each character is read exactly once and written at most twice.
- **Space Complexity**: $O(1)$ auxiliary space if we ignore the tiny temporary string created by `to_string(count)` (which takes $O(\log_{10} N)$ digits, max 5 characters for integer limits).

## 10. Edge Cases
- **Single character elements**: `["a"]` (returns `1`, list unchanged).
- **All unique characters**: `["a", "b", "c"]` (returns `3`, list unchanged).
- **Large repetition count**: `["a" x 12]` (returns `3` because we write `'a'`, `'1'`, `'2'`).
- **Empty list**: `[]` (not possible per constraint, but if so, returns `0`).

## 11. Follow-up Questions
- **What if we want to decompress the array in-place instead?** Decompressing in-place is much harder because the output array can be much longer than the input (e.g., `"a9"` becomes 9 `'a'`s). We would need to calculate the target length, resize the array, and write from right to left.
- **Can we count the digits without `to_string` to ensure absolute $O(1)$ space?** Yes, we can extract digits using division and modulo operations, reverse them, and write them to the array.
- **What if the characters are streaming inputs?** We can maintain a running count and emit character/count pairs as soon as the active character changes.

## 12. Interview Explanation
"To compress the character array in-place, I'll use a two-pointer technique. I maintain a write pointer `index` and a read pointer `i`. I iterate through the array, finding groups of consecutive matching characters. For each group, I count the repetitions. I write the group's character at the write pointer. If the count is greater than one, I convert the count to digits and write those digits sequentially at the write pointer. This is safe to do in-place because the write pointer will never overtake the read pointer. This solution runs in $O(N)$ time and uses $O(1)$ auxiliary space."

---

# 9. Longest Common Prefix (LeetCode 14)

## 1. Pattern
Strings, Horizontal Scanning.

## 2. What is the interviewer asking?
Find the longest common prefix string amongst an array of strings. If there is no common prefix, return an empty string `""`.

## 3. Intuition
The common prefix must be present in every single string in the list.
- We can initialize our prefix as the first string in the list.
- We then compare this prefix with the next string. We trim the prefix from the end until it matches the start of the next string.
- We repeat this process for all strings. If at any point the prefix becomes empty, we can stop and return `""`.

## 4. Brute Force
Approach:
1. Compare the characters vertically: check the first character of all strings, then the second character of all strings, etc.
2. Stop when we reach a mismatch or the end of any string.
3. The common characters before the mismatch form the longest common prefix.

Time: $O(S)$ where $S$ is the sum of all characters in all strings.
Space: $O(1)$ auxiliary space.

## 5. Optimized Approach
1. If the input list `strs` is empty, return `""`.
2. Initialize the `prefix` as `strs[0]`.
3. Loop through the remaining strings from `i = 1` to `strs.size() - 1`:
   - While the current string `strs[i]` does not start with `prefix` (i.e. `strs[i].find(prefix) != 0`):
     - Shrink the `prefix` by removing its last character: `prefix = prefix.substr(0, prefix.size() - 1)`.
     - If `prefix` becomes empty, return `""`.
4. Return `prefix`.

## 6. Why does it work?
If a prefix is common to all strings, it must be a prefix of the first string. By starting with the first string as our candidate prefix, and progressively trimming it down as we find mismatches in subsequent strings, we ensure that the prefix remains valid for all strings scanned so far. The first occurrence of `find(prefix) == 0` confirms that the prefix matches the beginning of the current string.

## 7. C++ Code
```cpp
#include <vector>
#include <string>

using namespace std;

string longestCommonPrefix(vector<string>& strs) {
    if (strs.empty()) return "";
    
    // Start by assuming the entire first string is the prefix
    string prefix = strs[0];
    
    for (int i = 1; i < strs.size(); ++i) {
        // Shrink the prefix until it is found at the beginning of strs[i]
        while (strs[i].find(prefix) != 0) {
            prefix = prefix.substr(0, prefix.size() - 1);
            // If there's no common prefix, return empty string
            if (prefix.empty()) {
                return "";
            }
        }
    }
    return prefix;
}
```

## 8. Dry Run
Input: `strs = ["flower","flow","flight"]`

- **Initialization**: `prefix = "flower"`
- **i = 1**, `strs[1] = "flow"`:
  - `"flow".find("flower")` is not `0` (it's not found).
  - Truncate: `prefix` becomes `"flowe"`.
  - `"flow".find("flowe")` is not `0`.
  - Truncate: `prefix` becomes `"flow"`.
  - `"flow".find("flow")` is `0` (match!).
- **i = 2**, `strs[2] = "flight"`:
  - `"flight".find("flow")` is not `0`.
  - Truncate: `prefix` becomes `"flo"`.
  - `"flight".find("flo")` is not `0`.
  - Truncate: `prefix` becomes `"fl"`.
  - `"flight".find("fl")` is `0` (match!).
- **End of Loop**: Return `prefix = "fl"`.

## 9. Complexity
- **Time Complexity**: $O(S)$ where $S$ is the sum of all characters in all strings. In the worst case, we compare the prefix with all characters of all strings.
- **Space Complexity**: $O(1)$ auxiliary space (excluding the string memory allocated for the prefix).

## 10. Edge Cases
- **No common prefix**: `["dog", "racecar", "car"]` (returns `""`).
- **Single string**: `["apple"]` (returns `"apple"`).
- **Prefix is one of the strings**: `["flow", "flower"]` (returns `"flow"`).
- **Empty input array**: `[]` (returns `""`).

## 11. Follow-up Questions
- **Can we solve this using Divide and Conquer?** Yes. Split the array into two halves, find the longest common prefix of each half, and then find the common prefix of those two results.
- **How about Binary Search?** Yes, binary search on the prefix length. The range is `[0, min_len]` where `min_len` is the length of the shortest string. If the prefix of length `mid` is common to all, search `[mid + 1, min_len]`, else search `[0, mid - 1]`.
- **What if we search for prefixes dynamically?** We can build a Trie (Prefix Tree) with all the strings. The common prefix will be the path from root to the first node with more than one child or an end-of-word marker.

## 12. Interview Explanation
"To find the longest common prefix, I initialize the candidate prefix as the first string. Then, I iterate through the remaining strings, check if they start with the prefix, and if they don't, I truncate the prefix by one character from the end until it matches. If the prefix becomes empty, we return an empty string. This horizontal scanning approach is simple and operates in linear time relative to the total number of characters across all strings. Alternatives include vertical scanning, divide and conquer, or binary search on the prefix length."

---

# 10. Repeated Substring Pattern (LeetCode 459)

## 1. Pattern
String Manipulation, KMP Algorithm, Concatenation Trick.

## 2. What is the interviewer asking?
Given a string `s`, check if it can be constructed by taking a substring of it and repeating it multiple times (at least twice) to reconstruct the entire string.

## 3. Intuition
Let string `s` be composed of a repeating unit `P` repeated $k$ times ($k \ge 2$), so $s = P + P + \dots + P$.
- If we double the string: `s + s = P + P + ... + P + P + P + ... + P`.
- If we remove the very first character and the very last character of `s + s`, we destroy the first unit and the last unit.
- However, because $k \ge 2$, there will still be at least $2k - 2 \ge k$ whole consecutive units of `P` remaining in the middle.
- Therefore, the original string `s` (which requires $k$ units) will still be found as a substring inside this trimmed doubled string.

## 4. Brute Force
Approach:
1. Iterate through all possible substring lengths $L$ from $1$ up to $N/2$.
2. If the string length $N$ is divisible by $L$:
   - Extract the substring of length $L$ from the start.
   - Repeat it $N/L$ times and check if it equals `s`.
3. If any length matches, return `true`. If no lengths match, return `false`.

Time: $O(N \cdot \sqrt{N})$ or $O(N^2)$ due to repeated string creations and comparisons.
Space: $O(N)$ to build the repeated strings.

## 5. Optimized Approach
1. Concatenate string `s` with itself: `doubled = s + s`.
2. Extract the substring from index `1` to `doubled.size() - 2` (removing the first and last characters): `temp = doubled.substr(1, doubled.size() - 2)`.
3. Check if the original string `s` exists in `temp` using `find(s)`.
4. If it is found (`find(s) != string::npos`), return `true`; else return `false`.

## 6. Why does it work?
Let $s = P \times k$. If $k \ge 2$, then `s + s` contains $2k$ copies of $P$. Removing the first character of the first copy and the last character of the last copy leaves us with $2k - 2$ copies of $P$ plus two partial copies. Since $k \ge 2$, $2k - 2 \ge k$. This guarantees that at least one full sequence of $k$ copies of $P$ (which is `s`) remains intact in the middle. Conversely, if $s$ cannot be represented as a repeated pattern (i.e. $k = 1$), removing the boundaries will prevent us from finding `s` in the middle of `s+s`.

## 7. C++ Code
```cpp
#include <string>

using namespace std;

bool repeatedSubstringPattern(string s) {
    // Double the string
    string doubled = s + s;
    // Remove the first and last character of the doubled string, then search for s
    return doubled.substr(1, doubled.size() - 2).find(s) != string::npos;
}
```

## 8. Dry Run
Input: `s = "abab"`

- **Step 1**: `doubled = s + s = "abababab"`
- **Step 2**: Trim first and last character:
  - Remove first character (`'a'`) and last character (`'b'`).
  - `temp = "bababa"`
- **Step 3**: Search for `s = "abab"` inside `temp = "bababa"`.
  - `"bababa"` contains `"abab"` starting at index 1 (`ba[abab]a`).
  - `find(s)` returns `1` (which is not `string::npos`).
- **Result**: Return `true`.

## 9. Complexity
- **Time Complexity**: $O(N)$ because the string concatenation, substring creation, and search operation run in linear time relative to the length of the string.
- **Space Complexity**: $O(N)$ to store the doubled and sliced strings.

## 10. Edge Cases
- **Single character string**: `s = "a"` (returns `false` since `doubled = "aa"`, `temp = ""`, which doesn't contain `"a"`).
- **All identical characters**: `s = "aaaa"` (returns `true`).
- **No repeated pattern**: `s = "aba"` (returns `false`).
- **Large repeating patterns**: `s = "abcabcabc"` (returns `true`).

## 11. Follow-up Questions
- **How can we solve this using the KMP Algorithm?** We can build the KMP lookup array (the LPS - Longest Prefix Suffix array). If `lps[n-1]` (the length of the longest common prefix which is also a suffix) is greater than 0, and `n` is divisible by `n - lps[n-1]`, then the pattern repeats.
- **What is the space/time complexity of the KMP approach?** The KMP approach runs in $O(N)$ time and $O(N)$ space but avoids creating copies of the doubled string, making it more memory-efficient in practice.
- **What is the mathematical justification of `n % (n - lps[n-1]) == 0`?** `n - lps[n-1]` represents the length of the candidate repeating substring. If the remainder of the string length divided by this candidate length is 0, the prefix-suffix symmetry guarantees it repeats.

## 12. Interview Explanation
"To determine if a string is made of repeated substrings, I can use a clever string concatenation trick. If a string `s` is formed by repeating a pattern, then doubling `s` to get `s + s` and removing its first and last characters will still contain `s`. If the pattern does not repeat, we won't be able to find `s` inside the trimmed doubled string. In C++, I implement this by creating `doubled = s + s`, slicing it to exclude the first and last characters, and using `find` to look for `s`. This runs in $O(N)$ time and $O(N)$ space. Alternatively, we can use the KMP algorithm's prefix array to solve this in $O(N)$ time and space with fewer memory allocations."
