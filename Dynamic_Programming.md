# Section 8: Dynamic Programming

This section covers essential Dynamic Programming patterns, detailing the core intuition, brute-force ideas, mathematical validation, clean C++ implementations, step-by-step dry runs, complexity, and common follow-ups for key DP problems.

---

# 1. Climbing Stairs (LeetCode 70)

## 1. Pattern
Dynamic Programming (Fibonacci sequence / State Compression).

## 2. What is the interviewer asking?
Given a staircase of $n$ steps, count how many distinct ways you can reach the top if you can climb either 1 or 2 steps at a time. The order of steps matters.

## 3. Intuition
To reach the $n$-th step, the final step must have been either a 1-step jump from step $n-1$ or a 2-step jump from step $n-2$. Therefore, the total number of ways to reach step $n$ is the sum of the ways to reach step $n-1$ and step $n-2$. This defines the classic recurrence:
$$dp[i] = dp[i-1] + dp[i-2]$$
with base cases $dp[1] = 1$ and $dp[2] = 2$.

## 4. Brute Force
Approach:
Use a simple recursive function to calculate the number of ways.
```cpp
int climbRecursive(int n) {
    if (n <= 2) return n;
    return climbRecursive(n - 1) + climbRecursive(n - 2);
}
```
This recalculates the answer for overlapping subproblems multiple times, resulting in a tree of redundant computations.

Time: $O(2^n)$
Space: $O(n)$ (Maximum depth of the recursion stack)

## 5. Optimized Approach
We notice that computing $dp[i]$ only requires the results of the previous two steps ($dp[i-1]$ and $dp[i-2]$). Instead of maintaining a full array of size $n+1$, we can keep track of these two values using two variables, `a` and `b`. In each iteration, we calculate the next value as `c = a + b`, then shift our window: `a = b` and `b = c`.

## 6. Why does it work?
The decision tree represents all valid combinations. Because each path to step $i$ must end in a step of size 1 (originating from $i-1$) or a step of size 2 (originating from $i-2$), the set of paths ending at $i$ is partitioned cleanly into these two disjoint and exhaustive subsets. Thus, adding their sizes is mathematically correct.

## 7. C++ Code
```cpp
#include <iostream>

int climbStairs(int n) {
    // Base cases: if n is 1 or 2, there are exactly 1 or 2 ways respectively
    if (n <= 2) return n;
    
    int a = 1; // Represents ways to reach step i - 2
    int b = 2; // Represents ways to reach step i - 1
    
    // Iteratively compute ways up to step n
    for (int i = 3; i <= n; ++i) {
        int c = a + b; // Current step count is sum of previous two
        a = b;         // Update (i - 2) to (i - 1)
        b = c;         // Update (i - 1) to current
    }
    
    return b; // b now holds the total ways to reach step n
}
```

## 8. Dry Run
Input: `n = 4`

- **Initial state**: `n` is greater than 2. Set `a = 1`, `b = 2`.
- **Step 1 ($i = 3$)**:
  - `c = a + b` $\rightarrow 1 + 2 = 3$
  - `a` becomes `b` $\rightarrow 2$
  - `b` becomes `c` $\rightarrow 3$
- **Step 2 ($i = 4$)**:
  - `c = a + b` $\rightarrow 2 + 3 = 5$
  - `a` becomes `b` $\rightarrow 3$
  - `b` becomes `c` $\rightarrow 5$
- **Termination**: Loop ends since $i > n$. Return `b` ($5$).

## 9. Complexity
Time: $O(n)$ — We perform a single loop from 3 to $n$.
Space: $O(1)$ — Only a constant amount of memory is used for tracking state variables.

## 10. Edge Cases
- `n = 1`: The function returns immediately with value 1.
- `n = 2`: The function returns immediately with value 2.
- Large value of `n`: If $n > 45$, standard 32-bit signed integers will overflow. However, standard LeetCode constraints limit $n \le 45$.

## 11. Follow-up Questions
- What if you could take 1, 2, or 3 steps? (This yields the Tribonacci recurrence: $dp[i] = dp[i-1] + dp[i-2] + dp[i-3]$).
- What if certain steps are broken and cannot be stepped on? (Store a list/set of broken steps; if the current step $i$ is broken, set $dp[i] = 0$).
- Can we solve this in $O(\log n)$ time? (Yes, by using Matrix Exponentiation on the Fibonacci state transitions).

## 12. Interview Explanation
"First, I would clarify the constraints of the problem, such as the maximum value of $n$. Then I'd explain the brute force approach where we recursively compute the sum of `climbStairs(n - 1)` and `climbStairs(n - 2)`. This has an exponential time complexity of $O(2^n)$ because of overlapping subproblems. To optimize this, I would use Dynamic Programming because the number of ways to reach step $i$ depends only on the ways to reach steps $i-1$ and $i-2$. Instead of using an $O(n)$ array, I can use two variables `a` and `b` to keep track of the previous two states. This reduces the space complexity to $O(1)$ and time complexity to $O(n)$. My C++ code handles values less than or equal to 2 as base cases, then loops from step 3 to $n$, updating the variables at each step."

---

# 2. House Robber (LeetCode 198)

## 1. Pattern
Dynamic Programming (1D State/Space Optimization).

## 2. What is the interviewer asking?
Given an integer array `nums` representing the money in each house, find the maximum money you can rob tonight without robbing two adjacent houses.

## 3. Intuition
At any house $i$, you have two options:
1. **Rob house $i$**: In this case, you cannot rob house $i-1$. The total value will be the money in house $i$ plus the optimal profit from houses up to $i-2$.
2. **Skip house $i$**: The total profit will be the optimal profit from houses up to $i-1$.

Thus, the recurrence relation is:
$$dp[i] = \max(dp[i-1], dp[i-2] + \text{nums}[i])$$

## 4. Brute Force
Approach:
Exhaustively search every valid subset of houses using recursion. For each house, branch into robbing (and skipping next) or skipping.
```cpp
int robHelper(vector<int>& nums, int i) {
    if (i < 0) return 0;
    return max(robHelper(nums, i - 1), robHelper(nums, i - 2) + nums[i]);
}
```
This visits the same houses repeatedly in different branches.

Time: $O(2^n)$
Space: $O(n)$ (Recursion call stack depth)

## 5. Optimized Approach
We only need to track the maximum profits of the last two house states to compute the next one. We can represent these states with two variables, `prev1` (representing $dp[i-1]$) and `prev2` (representing $dp[i-2]$). In each iteration over the array, we calculate a temporary value `temp = max(prev1, prev2 + num)`, then shift: `prev2 = prev1` and `prev1 = temp`.

## 6. Why does it work?
The optimal substructure property holds because the maximum profit robbable from the first $i$ houses depends only on the maximum profit robbable from the first $i-1$ or $i-2$ houses. By choosing the maximum of the two options at each step, we guarantee that we make the globally optimal choice up to the current index.

## 7. C++ Code
```cpp
#include <vector>
#include <algorithm>

int rob(std::vector<int>& nums) {
    int prev1 = 0; // Represents dp[i-1] (max profit up to previous house)
    int prev2 = 0; // Represents dp[i-2] (max profit up to two houses ago)
    
    for (int num : nums) {
        // At each house, choose between robbing it (prev2 + num) or skipping it (prev1)
        int temp = std::max(prev1, prev2 + num);
        prev2 = prev1;
        prev1 = temp;
    }
    
    return prev1; // prev1 contains the max loot from all houses
}
```

## 8. Dry Run
Input: `nums = [2, 7, 9, 3, 1]`

- **Initial state**: `prev1 = 0`, `prev2 = 0`.
- **Step 1 (`num = 2`)**:
  - `temp = max(prev1, prev2 + 2) = max(0, 0 + 2) = 2`
  - `prev2 = prev1` $\rightarrow 0$
  - `prev1 = temp` $\rightarrow 2$
- **Step 2 (`num = 7`)**:
  - `temp = max(prev1, prev2 + 7) = max(2, 0 + 7) = 7`
  - `prev2 = prev1` $\rightarrow 2$
  - `prev1 = temp` $\rightarrow 7$
- **Step 3 (`num = 9`)**:
  - `temp = max(prev1, prev2 + 9) = max(7, 2 + 9) = 11`
  - `prev2 = prev1` $\rightarrow 7$
  - `prev1 = temp` $\rightarrow 11$
- **Step 4 (`num = 3`)**:
  - `temp = max(prev1, prev2 + 3) = max(11, 7 + 3) = 11`
  - `prev2 = prev1` $\rightarrow 11$
  - `prev1 = temp` $\rightarrow 11$
- **Step 5 (`num = 1`)**:
  - `temp = max(prev1, prev2 + 1) = max(11, 11 + 1) = 12`
  - `prev2 = prev1` $\rightarrow 11$
  - `prev1 = temp` $\rightarrow 12$
- **Result**: Returns `prev1` ($12$).

## 9. Complexity
Time: $O(n)$ — Single traversal of the `nums` array.
Space: $O(1)$ — Only a few tracker variables are used.

## 10. Edge Cases
- `nums` is empty: Loop doesn't execute; returns 0.
- `nums` has one element `[x]`: Loop runs once, returning `x`.
- All houses have value 0: Returns 0.

## 11. Follow-up Questions
- What if the houses are arranged in a circle? (This is House Robber II. We solve it by running the standard linear version twice: once for `nums[0...n-2]` and once for `nums[1...n-1]`, then returning the maximum of the two).
- What if the houses are arranged in a binary tree? (This is House Robber III. We perform a post-order traversal where each node returns a pair: `{money_robbed, money_skipped}`).

## 12. Interview Explanation
"First, I would clarify if the values in the houses can be negative or if the houses form a circular structure. In a linear layout with positive values, the brute force approach is to check all valid subsets of houses, which takes $O(2^n)$ time. To optimize this, I would use Dynamic Programming because the decision at house $i$ only depends on the optimal profits calculated for the houses up to indices $i-1$ and $i-2$. By maintaining two variables, `prev1` and `prev2`, we can compute the current maximum profit iteratively without storing the entire DP table. My C++ code implements this space-optimized approach, running in $O(n)$ time and $O(1)$ space."

---

# 3. Coin Change (LeetCode 322)

## 1. Pattern
Dynamic Programming (Unbounded Knapsack variant).

## 2. What is the interviewer asking?
Given an integer array `coins` representing coin denominations and an integer `amount`, return the fewest number of coins needed to make up that amount. If the amount cannot be met by any combination of the coins, return `-1`.

## 3. Intuition
This is a minimization problem. If we want to find the minimum coins to make a value $j$, we can try subtracting each coin denomination $c$ from $j$. If we do, the remaining amount is $j-c$. The minimum coins to form $j$ using coin $c$ is the minimum coins for $j-c$ plus the 1 coin we just added. We minimize this value across all possible coins:
$$dp[j] = \min_{c \in coins} (dp[j], dp[j - c] + 1)$$
where $dp[0] = 0$ and $dp[j]$ is initialized to infinity (or a sentinel value like `amount + 1`).

## 4. Brute Force
Approach:
Recursively try every coin for the remaining amount.
```cpp
int minCoins(vector<int>& coins, int amount) {
    if (amount == 0) return 0;
    int res = INT_MAX;
    for (int coin : coins) {
        if (amount >= coin) {
            int sub_res = minCoins(coins, amount - coin);
            if (sub_res != INT_MAX) res = min(res, sub_res + 1);
        }
    }
    return res;
}
```
This leads to an explosive number of branches since the same sub-amounts are computed over and over.

Time: $O(N^{\text{amount}})$ where $N$ is the number of coins.
Space: $O(\text{amount})$ recursion stack.

## 5. Optimized Approach
We use bottom-up dynamic programming. We create an array `dp` of size `amount + 1`, initialized to `amount + 1` (since the maximum possible coins to make $j$ is $j$, a value of `amount + 1` acts as infinity). We set `dp[0] = 0`. Then, we iterate through each coin and update the DP table for all values from the coin's value up to `amount`.

## 6. Why does it work?
We build the solution bottom-up. The minimum coins required for a value $j$ must be built from some smaller value $j - coin$ plus that single coin. By computing and storing the optimal results for all smaller amounts first, we guarantee that when we evaluate $dp[j]$, the components $dp[j - coin]$ are already optimal.

## 7. C++ Code
```cpp
#include <vector>
#include <algorithm>

int coinChange(std::vector<int>& coins, int amount) {
    // dp[i] will store the minimum coins needed to make amount i
    // Initialize DP table with amount + 1, representing infinity
    std::vector<int> dp(amount + 1, amount + 1);
    
    // Base case: 0 coins needed to make amount 0
    dp[0] = 0;
    
    // For each coin, compute min coins for all amounts from coin value to target amount
    for (int coin : coins) {
        for (int j = coin; j <= amount; ++j) {
            dp[j] = std::min(dp[j], dp[j - coin] + 1);
        }
    }
    
    // If dp[amount] is still amount + 1, it's impossible to form the amount
    return dp[amount] > amount ? -1 : dp[amount];
}
```

## 8. Dry Run
Input: `coins = [1, 2, 5]`, `amount = 5`

- **Initial state**: `dp` of size 6 initialized to `[0, 6, 6, 6, 6, 6]`.
- **Iteration 1 (`coin = 1`)**:
  - `j = 1`: `dp[1] = min(6, dp[0] + 1) = 1`
  - `j = 2`: `dp[2] = min(6, dp[1] + 1) = 2`
  - `j = 3`: `dp[3] = min(6, dp[2] + 1) = 3`
  - `j = 4`: `dp[4] = min(6, dp[3] + 1) = 4`
  - `j = 5`: `dp[5] = min(6, dp[4] + 1) = 5`
  - `dp` array: `[0, 1, 2, 3, 4, 5]`
- **Iteration 2 (`coin = 2`)**:
  - `j = 2`: `dp[2] = min(2, dp[0] + 1) = 1`
  - `j = 3`: `dp[3] = min(3, dp[1] + 1) = 2`
  - `j = 4`: `dp[4] = min(4, dp[2] + 1) = 2`
  - `j = 5`: `dp[5] = min(5, dp[3] + 1) = 3`
  - `dp` array: `[0, 1, 1, 2, 2, 3]`
- **Iteration 3 (`coin = 5`)**:
  - `j = 5`: `dp[5] = min(3, dp[0] + 1) = 1`
  - `dp` array: `[0, 1, 1, 2, 2, 1]`
- **Result**: `dp[5] = 1`, which is returning `1` (one coin of value 5).

## 9. Complexity
Time: $O(N \times A)$ — Where $N$ is the number of coin types and $A$ is the target amount.
Space: $O(A)$ — For the 1D DP table of size $A+1$.

## 10. Edge Cases
- `amount = 0`: Returns 0 because `dp[0]` is initialized to 0.
- Coins cannot sum to amount (e.g. `coins = [2]`, `amount = 3`): Returns `-1`.
- Smallest coin is larger than the target amount (e.g. `coins = [5]`, `amount = 3`): Inner loop is skipped, returns `-1`.

## 11. Follow-up Questions
- How would you print the actual coins that make up the minimum combination? (Keep a `parent` or `choice` array where `choice[j]` stores the coin value used to update `dp[j]`).
- What if the number of coins is limited (0/1 Knapsack style)? (Iterate through coins in the outer loop, and iterate `j` backwards in the inner loop).

## 12. Interview Explanation
"First, I would clarify if the coin denominations are positive and whether we have an unlimited supply of each coin. The brute force approach recursively tries every coin, resulting in an exponential $O(N^{\text{amount}})$ time complexity. To optimize this, I would use Dynamic Programming because we have overlapping subproblems. I'll maintain a 1D DP table of size `amount + 1`, initializing it with `amount + 1` to represent infinity. The base case `dp[0] = 0` requires 0 coins. For each coin, I will update all DP positions starting from that coin's value up to the target amount. Finally, I will check if the target amount entry has been updated; if it's still greater than the amount, it's impossible, and I return -1. Otherwise, I return the computed minimum coins."

---

# 4. Longest Increasing Subsequence (LeetCode 300)

## 1. Pattern
Dynamic Programming (LIS Pattern / Coordinate DP).

## 2. What is the interviewer asking?
Given an integer array `nums`, return the length of the longest strictly increasing subsequence. A subsequence is a sequence that can be derived from an array by deleting some or no elements without changing the order of the remaining elements.

## 3. Intuition
For each element `nums[i]`, we want to find the longest increasing subsequence that ends at index $i$. This can be built by looking at all previous elements `nums[j]` where $j < i$. If `nums[i] > nums[j]`, then `nums[i]` can extend the subsequence ending at index $j$. We choose the maximum among all such extensions:
$$dp[i] = \max(dp[i], dp[j] + 1) \quad \text{for } 0 \le j < i \text{ and } \text{nums}[i] > \text{nums}[j]$$
Initially, each element is a subsequence of length 1, so `dp` is initialized with 1s.

## 4. Brute Force
Approach:
Generate all $2^n$ subsequences, check if each is strictly increasing, and find the maximum length.

Time: $O(2^n \times n)$
Space: $O(n)$ recursion stack.

## 5. Optimized Approach
We use Dynamic Programming. We create an array `dp` of size `nums.size()`, filled with 1. We run nested loops: the outer loop runs $i$ from 1 to $n-1$, and the inner loop runs $j$ from 0 to $i-1$. If `nums[i] > nums[j]`, we update `dp[i]` with `max(dp[i], dp[j] + 1)`. We keep track of the maximum value in `dp` throughout this process.

## 6. Why does it work?
Any LIS ending at index $i$ must have a second-to-last element at some index $j < i$ such that `nums[j] < nums[i]`. By checking every possible index $j$ before $i$, we guarantee that we cover all candidates for this second-to-last element, maintaining the optimal substructure.

## 7. C++ Code
```cpp
#include <vector>
#include <algorithm>

int lengthOfLIS(std::vector<int>& nums) {
    if (nums.empty()) return 0;
    
    // dp[i] stores the length of LIS ending at index i
    std::vector<int> dp(nums.size(), 1);
    int maxLength = 1;
    
    for (int i = 1; i < nums.size(); ++i) {
        for (int j = 0; j < i; ++j) {
            // If the current element is greater than the previous element,
            // it can extend the increasing subsequence ending at index j.
            if (nums[i] > nums[j]) {
                dp[i] = std::max(dp[i], dp[j] + 1);
            }
        }
        // Update the global maximum length found so far
        maxLength = std::max(maxLength, dp[i]);
    }
    
    return maxLength;
}
```

## 8. Dry Run
Input: `nums = [10, 9, 2, 5, 3, 7]`

- **Initial state**: `dp = [1, 1, 1, 1, 1, 1]`, `maxLength = 1`
- **Step 1 ($i = 1$, `nums[1] = 9`)**:
  - `j = 0` (`nums[0] = 10`): $9 > 10$ is false.
  - `dp` remains `[1, 1, 1, 1, 1, 1]`.
- **Step 2 ($i = 2$, `nums[2] = 2`)**:
  - `j = 0, 1` ($2 > 10$, $2 > 9$ are false).
  - `dp` remains `[1, 1, 1, 1, 1, 1]`.
- **Step 3 ($i = 3$, `nums[3] = 5`)**:
  - `j = 0, 1` (false).
  - `j = 2` (`nums[2] = 2`): $5 > 2$ is true. `dp[3] = max(dp[3], dp[2] + 1) = max(1, 2) = 2`.
  - `dp` becomes `[1, 1, 1, 2, 1, 1]`. `maxLength` becomes `max(1, 2) = 2`.
- **Step 4 ($i = 4$, `nums[4] = 3`)**:
  - `j = 2` (`nums[2] = 2`): $3 > 2$ is true. `dp[4] = max(1, dp[2] + 1) = 2`.
  - `dp` becomes `[1, 1, 1, 2, 2, 1]`.
- **Step 5 ($i = 5$, `nums[5] = 7`)**:
  - `j = 2` (`nums[2] = 2`): $7 > 2$ is true $\rightarrow dp[5] = \max(1, 2) = 2$.
  - `j = 3` (`nums[3] = 5`): $7 > 5$ is true $\rightarrow dp[5] = \max(2, dp[3] + 1) = 3$.
  - `j = 4` (`nums[4] = 3`): $7 > 3$ is true $\rightarrow dp[5] = \max(3, dp[4] + 1) = 3$.
  - `dp` becomes `[1, 1, 1, 2, 2, 3]`. `maxLength = 3`.
- **Result**: Return `maxLength = 3` (subsequence is `[2, 3, 7]` or `[2, 5, 7]`).

## 9. Complexity
Time: $O(n^2)$ — Two nested loops.
Space: $O(n)$ — Dynamic programming array of size $n$.

## 10. Edge Cases
- Array with 1 element: Loops don't run; returns 1.
- Array sorted in descending order (e.g. `[5, 4, 3, 2, 1]`): No updates trigger; returns 1.
- Array with identical elements (e.g. `[2, 2, 2]`): Strictly increasing condition fails; returns 1.

## 11. Follow-up Questions
- Can we solve this in $O(n \log n)$ time? (Yes, by maintaining a dynamic array of tails where we binary search (using `std::lower_bound`) to find the insertion position for each element).
- How do we print the lexicographically smallest LIS? (Track parent pointers and reconstruct).

## 12. Interview Explanation
"First, I would clarify if the subsequence must be strictly increasing. The brute force approach involves generating all $2^n$ subsequences and checking them, taking $O(2^n \cdot n)$ time. To optimize this, I would use Dynamic Programming because the length of the LIS ending at index $i$ depends on the LIS lengths calculated for indices before it. By defining a `dp` array where `dp[i]` is the length of the LIS ending at $i$, I can fill this table in $O(n^2)$ time. For each index $i$, I iterate through all preceding indices $j$, and if `nums[i] > nums[j]`, I update `dp[i]`. I would also note that this can be further optimized to $O(n \log n)$ using a binary search approach."

---

# 5. Longest Common Subsequence (LeetCode 1143)

## 1. Pattern
Dynamic Programming (2D Grid DP / String Alignment).

## 2. What is the interviewer asking?
Given two strings `text1` and `text2`, return the length of their longest common subsequence. A subsequence does not need to be contiguous.

## 3. Intuition
Let's analyze characters of `text1` at index $i-1$ and `text2` at index $j-1$:
1. **Characters match (`text1[i-1] == text2[j-1]`)**: This match must be part of the LCS. The LCS length is 1 plus the LCS of the prefixes excluding these two characters:
   $$dp[i][j] = dp[i-1][j-1] + 1$$
2. **Characters do not match**: We take the maximum LCS possible by either skipping the character in `text1` or skipping the character in `text2`:
   $$dp[i][j] = \max(dp[i-1][j], dp[i][j-1])$$

## 4. Brute Force
Approach:
Use recursion to test matches.
```cpp
int lcsHelper(string& s1, string& s2, int i, int j) {
    if (i == 0 || j == 0) return 0;
    if (s1[i - 1] == s2[j - 1]) return 1 + lcsHelper(s1, s2, i - 1, j - 1);
    return max(lcsHelper(s1, s2, i - 1, j), lcsHelper(s1, s2, i, j - 1));
}
```
This recomputes values for overlapping combinations of prefixes.

Time: $O(2^{m+n})$
Space: $O(m+n)$ recursion stack.

## 5. Optimized Approach
Use bottom-up 2D dynamic programming. We allocate a grid of size `(m+1) x (n+1)` initialized to 0. We run a nested loop with `i` from 1 to `m` and `j` from 1 to `n`. We apply the intuition rules to populate the grid and return `dp[m][n]`.

## 6. Why does it work?
The recurrence relations partition the search space completely. If the current characters match, they must be aligned to maximize the subsequence length. If they don't match, the optimal subsequence can only contain at most one of the two characters, which is covered by taking the maximum of the two choices.

## 7. C++ Code
```cpp
#include <string>
#include <vector>
#include <algorithm>

int longestCommonSubsequence(std::string text1, std::string text2) {
    int m = text1.size();
    int n = text2.size();
    
    // dp[i][j] stores the LCS length of text1[0...i-1] and text2[0...j-1]
    std::vector<std::vector<int>> dp(m + 1, std::vector<int>(n + 1, 0));
    
    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (text1[i - 1] == text2[j - 1]) {
                // If characters match, add 1 to the result of preceding prefixes
                dp[i][j] = dp[i - 1][j - 1] + 1;
            } else {
                // If they don't match, take the max from skipping one char of either string
                dp[i][j] = std::max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }
    
    return dp[m][n]; // Bottom-right corner contains the final LCS length
}
```

## 8. Dry Run
Input: `text1 = "abc"`, `text2 = "ac"`

- **Initial state**: `dp` table of size `4x3` initialized to 0.
- **Row 1 (`i = 1`, `text1[0] = 'a'`)**:
  - `j = 1` (`text2[0] = 'a'`): Match! `dp[1][1] = dp[0][0] + 1 = 1`.
  - `j = 2` (`text2[1] = 'c'`): No match. `dp[1][2] = max(dp[0][2], dp[1][1]) = max(0, 1) = 1`.
- **Row 2 (`i = 2`, `text1[1] = 'b'`)**:
  - `j = 1` ('a'): No match. `dp[2][1] = max(dp[1][1], dp[2][0]) = 1`.
  - `j = 2` ('c'): No match. `dp[2][2] = max(dp[1][2], dp[2][1]) = max(1, 1) = 1`.
- **Row 3 (`i = 3`, `text1[2] = 'c'`)**:
  - `j = 1` ('a'): No match. `dp[3][1] = max(dp[2][1], dp[3][0]) = 1`.
  - `j = 2` ('c'): Match! `dp[3][2] = dp[2][1] + 1 = 1 + 1 = 2`.
- **Result**: Returns `dp[3][2] = 2` (LCS is "ac").

## 9. Complexity
Time: $O(m \times n)$ — Nested loop running $m \times n$ times.
Space: $O(m \times n)$ — 2D DP array.

## 10. Edge Cases
- One or both strings are empty: Outer loops don't execute; returns 0.
- No characters match (e.g. `"abc"`, `"def"`): Every state resolves to the max of adjacent zeros; returns 0.
- Strings are identical: Diagonal accumulation leads to returning the string length.

## 11. Follow-up Questions
- How would you optimize the space complexity to $O(n)$? (Since we only reference the current and the previous row of the DP grid, we can maintain only two rows of size $n+1$, or a single row if we update it carefully).
- How do you reconstruct the actual LCS string? (Start from `dp[m][n]`. If `text1[i-1] == text2[j-1]`, prepend the character and move diagonally to `dp[i-1][j-1]`. Otherwise, move to the cell with the larger value: `dp[i-1][j]` or `dp[i][j-1]`).

## 12. Interview Explanation
"First, I would clarify if the characters of the subsequence must remain contiguous. If they don't, the brute force approach is to generate all subsequences of one string and check their existence in the other, taking $O(2^m \cdot n)$ time. To optimize this, I would use Dynamic Programming because the LCS of two prefixes depends directly on the LCS of smaller prefixes. I'll construct a 2D grid `dp` where `dp[i][j]` is the LCS of `text1[0...i-1]` and `text2[0...j-1]`. If the characters match, I extend the subproblem diagonally by 1. Otherwise, I take the maximum of excluding one character. My C++ code uses a grid initialized to 0 and iterates through both strings to return the final value in $O(m \cdot n)$ time."

---

# 6. Unique Paths (LeetCode 62)

## 1. Pattern
Dynamic Programming (Grid Path Counting / Combinatorics).

## 2. What is the interviewer asking?
Given an $m \times n$ grid, you start at the top-left corner and want to reach the bottom-right corner. You can only move either down or right at any point. Return the total number of unique paths.

## 3. Intuition
To arrive at cell `(i, j)`, you must have come from either:
- The cell above it: `(i - 1, j)`
- The cell to the left of it: `(i, j - 1)`

Thus, the number of paths to `(i, j)` is the sum of the paths to those two cells:
$$dp[i][j] = dp[i-1][j] + dp[i][j-1]$$
Since we only need the values from the previous row and the current row's left cell, we can optimize this to a 1D array of size $n$.

## 4. Brute Force
Approach:
Recursively explore both right and down paths from the starting cell.
```cpp
int pathsRecursive(int i, int j, int m, int n) {
    if (i == m - 1 && j == n - 1) return 1;
    if (i >= m || j >= n) return 0;
    return pathsRecursive(i + 1, j, m, n) + pathsRecursive(i, j + 1, m, n);
}
```
This traverses duplicate paths repeatedly.

Time: $O(2^{m+n})$
Space: $O(m + n)$ recursion stack.

## 5. Optimized Approach
We use space-optimized DP. Allocate a 1D array `dp` of size $n$, initialized to 1 (since there is only 1 way to reach any cell in the first row — by moving strictly right). We then loop through the rows from 1 to $m-1$ and columns from 1 to $n-1$, updating `dp[j] = dp[j] + dp[j-1]`.

## 6. Why does it work?
In the 1D DP update, `dp[j]` on the right side of the equation represents the paths from the cell above (from the previous row), and `dp[j-1]` represents the paths from the left cell (already updated for the current row). Adding them updates `dp[j]` to store the correct paths for the current cell.

## 7. C++ Code
```cpp
#include <vector>

int uniquePaths(int m, int n) {
    // dp[j] will store the number of paths to reach the current cell in column j
    // Initialize the first row with 1s (only 1 way to move straight right)
    std::vector<int> dp(n, 1);
    
    // Process the grid row by row
    for (int i = 1; i < m; ++i) {
        for (int j = 1; j < n; ++j) {
            // Number of paths to (i, j) = paths from above (dp[j]) + paths from left (dp[j - 1])
            dp[j] += dp[j - 1];
        }
    }
    
    return dp[n - 1]; // The last cell contains the total unique paths
}
```

## 8. Dry Run
Input: `m = 3`, `n = 3`

- **Initial state**: `dp = [1, 1, 1]` (representing row 0).
- **Row 1 ($i = 1$)**:
  - `j = 1`: `dp[1] = dp[1] + dp[0] = 1 + 1 = 2`
  - `j = 2`: `dp[2] = dp[2] + dp[1] = 1 + 2 = 3`
  - `dp` state: `[1, 2, 3]`
- **Row 2 ($i = 2$)**:
  - `j = 1`: `dp[1] = dp[1] + dp[0] = 2 + 1 = 3`
  - `j = 2`: `dp[2] = dp[2] + dp[1] = 3 + 3 = 6`
  - `dp` state: `[1, 3, 6]`
- **Result**: Returns `dp[2] = 6`.

## 9. Complexity
Time: $O(m \times n)$ — Nested loop visiting each grid coordinate.
Space: $O(n)$ — Optimized 1D DP table of size $n$.

## 10. Edge Cases
- `m = 1` or `n = 1`: The loops don't execute; returns 1.
- Max size `m = 100, n = 100`: Fits within standard integer boundaries without overflow.

## 11. Follow-up Questions
- What if there are obstacles in the grid? (This is Unique Paths II. We set `dp[j] = 0` if the cell has an obstacle; otherwise, update normally).
- Can we solve this in $O(1)$ space and $O(\min(m, n))$ time? (Yes, using combinatorics. The number of moves is $(m-1) + (n-1)$. We choose $(m-1)$ down moves out of the total. The formula is $\binom{m+n-2}{m-1}$).

## 12. Interview Explanation
"First, I would clarify if the grid can contain obstacles. If the grid is free of obstacles, the brute force approach is to use recursion to branch into down and right movements, which takes $O(2^{m+n})$ time. To optimize, I would use Dynamic Programming because the paths to cell `(i, j)` depend only on its top and left neighbors. Although we could use a 2D array of size $m \times n$, we only need the values of the current row and the previous row. Thus, we can optimize space to a 1D array of size $n$ initialized to 1. My C++ code updates this row in place, achieving $O(m \cdot n)$ time and $O(n)$ space."

---

# 7. Longest Palindromic Substring (LeetCode 5 - DP Approach)

## 1. Pattern
Dynamic Programming (Interval DP).

## 2. What is the interviewer asking?
Given a string `s`, return the longest contiguous substring in `s` that is a palindrome.

## 3. Intuition
A substring $s[i...j]$ is a palindrome if:
1. The characters at both ends are identical: `s[i] == s[j]`
2. The inner substring is also a palindrome: $s[i+1...j-1]$ is a palindrome (i.e., `dp[i+1][j-1]` is true).

For base cases, all single characters are palindromes (`dp[i][i] = true`), and substrings of length 2 are palindromes if `s[i] == s[i+1]`.

## 4. Brute Force
Approach:
Generate all $O(n^2)$ substrings, and check each one for the palindromic property by comparing characters from both ends inward in $O(n)$ time.
```cpp
bool isPalindrome(const string& s, int l, int r) {
    while (l < r) {
        if (s[l++] != s[r--]) return false;
    }
    return true;
}
```

Time: $O(n^3)$
Space: $O(1)$

## 5. Optimized Approach
We use interval-based Dynamic Programming. We maintain a 2D boolean array `dp` of size $n \times n$. We set the diagonal values to `true` (all single characters). Then, we loop through all possible substring lengths `len` from 2 up to $n$. For each length, we find all possible starting positions $i$ and calculate ending positions $j = i + \text{len} - 1$. If `s[i] == s[j]` and the inner portion is valid (either `len == 2` or `dp[i+1][j-1]` is true), we set `dp[i][j] = true` and update the start index and maximum length of our result substring.

## 6. Why does it work?
We solve smaller subproblems (shorter lengths) first. Because the palindromic nature of $s[i...j]$ relies strictly on the smaller inner interval $s[i+1...j-1]$, resolving lengths in increasing order guarantees that `dp[i+1][j-1]` has already been computed before we evaluate `dp[i][j]`.

## 7. C++ Code
```cpp
#include <string>
#include <vector>

std::string longestPalindrome(std::string s) {
    int n = s.size();
    if (n == 0) return "";
    
    int maxLength = 1;
    int start = 0;
    
    // dp[i][j] will be true if substring s[i...j] is a palindrome
    std::vector<std::vector<bool>> dp(n, std::vector<bool>(n, false));
    
    // Base Case 1: Single characters are always palindromes
    for (int i = 0; i < n; ++i) {
        dp[i][i] = true;
    }
    
    // Check substrings of lengths from 2 to n
    for (int len = 2; len <= n; ++len) {
        for (int i = 0; i <= n - len; ++i) {
            int j = i + len - 1; // End index of the substring
            
            // Substring is a palindrome if outer characters match
            // and the inner substring is also a palindrome (or length is 2)
            if (s[i] == s[j] && (len == 2 || dp[i + 1][j - 1])) {
                dp[i][j] = true;
                if (len > maxLength) {
                    maxLength = len;
                    start = i;
                }
            }
        }
    }
    
    return s.substr(start, maxLength);
}
```

## 8. Dry Run
Input: `s = "babad"`

- **Initial state**: `dp` is a 5x5 grid of false. Diagonals `dp[i][i]` set to `true` for $i \in [0, 4]$. `maxLength = 1`, `start = 0`.
- **Length 2 (`len = 2`)**:
  - `i = 0` ("ba"): `s[0] != s[1]` $\rightarrow dp[0][1] = false$
  - `i = 1` ("ab"): `s[1] != s[2]` $\rightarrow dp[1][2] = false$
  - `i = 2` ("ba"): `s[2] != s[3]` $\rightarrow dp[2][3] = false$
  - `i = 3` ("ad"): `s[3] != s[4]` $\rightarrow dp[3][4] = false$
- **Length 3 (`len = 3`)**:
  - `i = 0` ("bab"): `s[0] == s[2]` ('b' == 'b') and `dp[1][1]` is true. `dp[0][2] = true`. `maxLength = 3`, `start = 0`.
  - `i = 1` ("aba"): `s[1] == s[3]` ('a' == 'a') and `dp[2][2]` is true. `dp[1][3] = true`. `maxLength` remains 3.
  - `i = 2` ("bad"): `s[2] != s[4]` ('b' != 'd') $\rightarrow dp[2][4] = false$.
- **Length 4 (`len = 4`)**:
  - `i = 0` ("baba"): `s[0] != s[3]` ('b' != 'a') $\rightarrow dp[0][3] = false$.
  - `i = 1` ("abad"): `s[1] != s[4]` ('a' != 'd') $\rightarrow dp[1][4] = false$.
- **Length 5 (`len = 5`)**:
  - `i = 0` ("babad"): `s[0] != s[4]` ('b' != 'd') $\rightarrow dp[0][4] = false$.
- **Result**: Returns `s.substr(0, 3)` which is `"bab"`.

## 9. Complexity
Time: $O(n^2)$ — Two nested loops covering lengths and starting indices.
Space: $O(n^2)$ — 2D DP array.

## 10. Edge Cases
- `s` is empty: Returns `""`.
- No palindromes of length $\ge 2$ (e.g. `"abcd"`): Returns the first character.
- String with all duplicate characters (e.g. `"aaaa"`): Successfully returns `"aaaa"`.

## 11. Follow-up Questions
- Can we optimize the space to $O(1)$? (Yes, by expanding around the $2n-1$ potential palindrome centers: $n$ single-character centers and $n-1$ double-character centers).
- Can we solve this in $O(n)$ time? (Yes, using Manacher's Algorithm, which uses palindrome symmetry to avoid redundant center expansions).

## 12. Interview Explanation
"First, I would clarify if the input string can contain special characters. The brute force approach is to extract all substrings and check if they are palindromes, which takes $O(n^3)$ time. To optimize, we can use Dynamic Programming. We define a 2D boolean array `dp` where `dp[i][j]` is true if substring `s[i...j]` is a palindrome. We initialize the diagonal to true since single characters are palindromes. Then we iterate through all possible substring lengths from 2 to $n$. A substring `s[i...j]` is a palindrome if `s[i] == s[j]` and the inner portion `s[i+1...j-1]` is a palindrome. My C++ code tracks the start index and maximum length to slice and return the longest palindromic substring at the end."

---

# 8. Maximum Length of Repeated Subarray (LeetCode 718)

## 1. Pattern
Dynamic Programming (2D Grid DP / Longest Common Substring variant).

## 2. What is the interviewer asking?
Given two integer arrays `nums1` and `nums2`, return the maximum length of a subarray (which must be contiguous) that appears in both arrays.

## 3. Intuition
This is identical to the Longest Common Substring problem, but applied to integer arrays. If `nums1[i-1] == nums2[j-1]`, then the matching subarray ending at these indices can extend the matching subarray ending at `i-2` and `j-2`:
$$dp[i][j] = dp[i-1][j-1] + 1$$
If they do not match, the contiguous matching sequence is broken at these indices, so we set:
$$dp[i][j] = 0$$
We track the maximum value found in `dp` throughout.

## 4. Brute Force
Approach:
For every starting pair of indices in both arrays, count how many subsequent elements match consecutively.
```cpp
int getMaxCommonPrefix(vector<int>& n1, vector<int>& n2, int i, int j) {
    int count = 0;
    while (i < n1.size() && j < n2.size() && n1[i] == n2[j]) {
        count++; i++; j++;
    }
    return count;
}
```

Time: $O(M \times N \times \min(M, N))$
Space: $O(1)$

## 5. Optimized Approach
We use a 2D DP table `dp` of size `(m+1) x (n+1)`. We loop through both arrays. If `nums1[i-1] == nums2[j-1]`, we set `dp[i][j] = dp[i-1][j-1] + 1` and update the overall maximum length. Otherwise, `dp[i][j]` remains 0.

## 6. Why does it work?
The contiguity requirement means we cannot skip elements. Hence, when elements at `i-1` and `j-1` match, the maximum length of a common subarray ending at these indices is strictly determined by the common prefix ending at `i-2` and `j-2`. If they mismatch, no common subarray can end at these indices, validating the recurrence.

## 7. C++ Code
```cpp
#include <vector>
#include <algorithm>

int findLength(std::vector<int>& nums1, std::vector<int>& nums2) {
    int m = nums1.size();
    int n = nums2.size();
    int maxLength = 0;
    
    // dp[i][j] will store the length of the longest common prefix of 
    // subarrays nums1[i-1...] and nums2[j-1...]
    std::vector<std::vector<int>> dp(m + 1, std::vector<int>(n + 1, 0));
    
    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            // If elements match, extend the contiguous length from the diagonal cell
            if (nums1[i - 1] == nums2[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
                maxLength = std::max(maxLength, dp[i][j]);
            }
        }
    }
    
    return maxLength;
}
```

## 8. Dry Run
Input: `nums1 = [1, 2, 3]`, `nums2 = [2, 3, 4]`

- **Initial state**: `dp` of size `4x4` initialized to 0. `maxLength = 0`.
- **Row 1 (`i = 1`, `nums1[0] = 1`)**:
  - Compare with `nums2 = [2, 3, 4]`. No matches. Row 1 remains all 0.
- **Row 2 (`i = 2`, `nums1[1] = 2`)**:
  - `j = 1` (`nums2[0] = 2`): Match! `dp[2][1] = dp[1][0] + 1 = 1`. `maxLength = max(0, 1) = 1`.
  - `j = 2, 3`: No match.
- **Row 3 (`i = 3`, `nums1[2] = 3`)**:
  - `j = 1` (`nums2[0] = 2`): No match.
  - `j = 2` (`nums2[1] = 3`): Match! `dp[3][2] = dp[2][1] + 1 = 1 + 1 = 2`. `maxLength = max(1, 2) = 2`.
  - `j = 3` (`nums2[2] = 4`): No match.
- **Result**: Returns `maxLength = 2`.

## 9. Complexity
Time: $O(m \times n)$ — Nested loop over dimensions of both arrays.
Space: $O(m \times n)$ — Size of the 2D DP array.

## 10. Edge Cases
- One or both arrays are empty: Returns 0.
- No matching elements: DP stays all 0; returns 0.
- Fully identical arrays: Max length matches the length of the arrays.

## 11. Follow-up Questions
- Can we optimize the space to $O(\min(m, n))$? (Yes, since we only need the previous row's diagonal cell `dp[i-1][j-1]` to calculate the current row).
- Can we solve this in $O((m + n) \log(\min(m, n)))$ time? (Yes, by using binary search on the answer length combined with Rolling Hash/Rabin-Karp to check matches in linear time).

## 12. Interview Explanation
"First, I would clarify if the subarray must be contiguous, which it must. The brute force approach takes $O(N^3)$ time by checking all subarray pairs. To optimize, I would use Dynamic Programming. I will create a 2D array `dp` where `dp[i][j]` is the length of the common prefix of subarrays ending at index `i-1` and `j-1`. When `nums1[i-1]` equals `nums2[j-1]`, we add 1 to the diagonal value `dp[i-1][j-1]`. If they do not match, the value is 0. We update the maximum length whenever we find a match. This takes $O(m \cdot n)$ time and $O(m \cdot n)$ space."

---

# 9. Partition Equal Subset Sum (LeetCode 416)

## 1. Pattern
Dynamic Programming (0/1 Knapsack variant).

## 2. What is the interviewer asking?
Given a non-empty array `nums` containing only positive integers, determine if the array can be partitioned into two subsets such that the sum of elements in both subsets is equal.

## 3. Intuition
Let the total sum of the array be $S$.
- If $S$ is odd, we cannot divide it into two equal integer parts. Return `false`.
- If $S$ is even, the target sum for each subset is exactly $S / 2$.
The problem now reduces to: "Can we find a subset of elements that sum up to exactly $target = S / 2$?" This is the classic subset sum problem.

For each element `num`, we choose to either include it in our subset or exclude it:
$$dp[j] = dp[j] \lor dp[j - num]$$

## 4. Brute Force
Approach:
Use recursion to find all subset sums. At each step, either include or exclude the current element.
```cpp
bool subsetSum(vector<int>& nums, int i, int target) {
    if (target == 0) return true;
    if (i < 0 || target < 0) return false;
    return subsetSum(nums, i - 1, target - nums[i]) || subsetSum(nums, i - 1, target);
}
```

Time: $O(2^n)$
Space: $O(n)$ recursion stack.

## 5. Optimized Approach
We use space-optimized DP. Initialize a boolean 1D array `dp` of size `target + 1` with `dp[0] = true` and all other elements as `false`. For each number `num` in the array, we iterate `j` from `target` down to `num` (backwards) and update `dp[j] = dp[j] || dp[j - num]`.

## 6. Why does it work?
We must iterate `j` backwards. If we iterated forwards, we might use the current element `num` multiple times (e.g. updating `dp[j]` using `dp[j-num]` which was updated in the same loop iteration). Going backwards ensures that `dp[j-num]` represents a subset sum achieved *prior* to considering the current element.

## 7. C++ Code
```cpp
#include <vector>
#include <numeric>

bool canPartition(std::vector<int>& nums) {
    int sum = std::accumulate(nums.begin(), nums.end(), 0);
    
    // If the total sum is odd, it's impossible to partition into two equal subsets
    if (sum % 2 != 0) return false;
    
    int target = sum / 2;
    // dp[j] will be true if a subset sum of j is possible
    std::vector<bool> dp(target + 1, false);
    dp[0] = true; // Base case: 0 sum is always possible with an empty subset
    
    for (int num : nums) {
        // Iterate backwards to prevent using the same element multiple times
        for (int j = target; j >= num; --j) {
            dp[j] = dp[j] || dp[j - num];
        }
    }
    
    return dp[target];
}
```

## 8. Dry Run
Input: `nums = [1, 5, 11, 5]`

- **Sum check**: $1 + 5 + 11 + 5 = 22$ (even). `target = 11`.
- **Initial DP**: `dp` of size 12: `[T, F, F, F, F, F, F, F, F, F, F, F]`
- **Step 1 (`num = 1`)**:
  - `j` runs from 11 down to 1:
    - `dp[1] = dp[1] || dp[0]` $\rightarrow true$
  - `dp` state: `[T, T, F, F, F, F, F, F, F, F, F, F]`
- **Step 2 (`num = 5`)**:
  - `j` runs from 11 down to 5:
    - `dp[6] = dp[6] || dp[1]` $\rightarrow true$
    - `dp[5] = dp[5] || dp[0]` $\rightarrow true$
  - `dp` state: `[T, T, F, F, F, T, T, F, F, F, F, F]`
- **Step 3 (`num = 11`)**:
  - `j` runs from 11 down to 11:
    - `dp[11] = dp[11] || dp[0]` $\rightarrow true$
  - `dp` state: `[T, T, F, F, F, T, T, F, F, F, F, T]`
- **Step 4 (`num = 5`)**:
  - `j` runs from 11 down to 5.
  - `dp[11]` is already true.
  - `dp[10] = dp[10] || dp[5]` $\rightarrow true$.
  - Return `dp[11]` $\rightarrow$ `true`. (Subsets are `{1, 5, 5}` and `{11}`).

## 9. Complexity
Time: $O(N \times \text{target})$ — Where $N$ is the array size and $\text{target} = \text{sum} / 2$.
Space: $O(\text{target})$ — For the 1D boolean DP array.

## 10. Edge Cases
- Sum is odd: Instantly returns `false`.
- Array has only one element: Sum is odd or even, but partition is impossible. The logic returns `false`.
- Largest element exceeds `target`: The inner loop will not execute for this element.

## 11. Follow-up Questions
- How would you partition the array into two subsets such that the absolute difference of their sums is minimized? (This is the Last Stone Weight II problem. Find the largest sum `j` $\le S/2$ where `dp[j]` is true. The minimal difference is $S - 2j$).
- What if we want to partition the array into $k$ equal sum subsets? (This requires backtracking with bitmask state compression, LeetCode 698).

## 12. Interview Explanation
"First, I would calculate the total sum of the array. If it's odd, it is mathematically impossible to divide the numbers into two subsets with equal integer sums, so I'd return `false`. If it is even, my target is `sum / 2`. This transforms the problem into a 0/1 Knapsack problem where we want to find a subset that sums up exactly to this target. I would initialize a boolean DP array of size `target + 1` with `dp[0] = true`. For each number in the input, I'll iterate backwards from `target` to `num` and set `dp[j] = dp[j] || dp[j - num]`. The backward iteration is vital because it prevents the reuse of the current element. My C++ code returns `dp[target]` in $O(n \cdot target)$ time and $O(target)$ space."

---

# 10. Maximum Subarray (LeetCode 53 - DP Approach)

## 1. Pattern
Dynamic Programming (Kadane's Algorithm / Prefix Sums).

## 2. What is the interviewer asking?
Given an integer array `nums`, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

## 3. Intuition
Let's define $dp[i]$ as the maximum subarray sum ending at index $i$.
At index $i$, we have two choices:
1. **Extend the existing subarray ending at $i-1$**: The sum is $dp[i-1] + \text{nums}[i]$.
2. **Start a new subarray from index $i$**: The sum is $\text{nums}[i]$.

We choose the option that gives the maximum sum:
$$dp[i] = \max(\text{nums}[i], dp[i-1] + \text{nums}[i])$$
Since $dp[i]$ only depends on $dp[i-1]$, we can optimize space to $O(1)$ by using a single variable `currentSum`.

## 4. Brute Force
Approach:
Calculate the sum of every possible subarray and track the maximum sum.
```cpp
int maxSubArrayBrute(vector<int>& nums) {
    int max_sum = INT_MIN;
    for (int i = 0; i < nums.size(); ++i) {
        int current = 0;
        for (int j = i; j < nums.size(); ++j) {
            current += nums[j];
            max_sum = max(max_sum, current);
        }
    }
    return max_sum;
}
```

Time: $O(n^2)$
Space: $O(1)$

## 5. Optimized Approach
We use Kadane's algorithm, which is a space-optimized DP approach. We initialize `maxSum` and `currentSum` to `nums[0]`. Then we iterate through the array starting from index 1. At each element, we update `currentSum = max(nums[i], currentSum + nums[i])` and update `maxSum = max(maxSum, currentSum)`.

## 6. Why does it work?
If `currentSum` becomes negative, adding it to `nums[i]` will make the total sum smaller than `nums[i]` itself. Therefore, it is always better to reset the subarray and start a new one at `nums[i]`. This local greedy choice guarantees the global maximum.

## 7. C++ Code
```cpp
#include <vector>
#include <algorithm>

int maxSubArray(std::vector<int>& nums) {
    // Initialize maxSum and currentSum with the first element
    int maxSum = nums[0];
    int currentSum = nums[0];
    
    // Iterate through the array starting from the second element
    for (int i = 1; i < nums.size(); ++i) {
        // Decide whether to extend the previous subarray or start a new one
        currentSum = std::max(nums[i], currentSum + nums[i]);
        
        // Update the global maximum subarray sum
        maxSum = std::max(maxSum, currentSum);
    }
    
    return maxSum;
}
```

## 8. Dry Run
Input: `nums = [-2, 1, -3, 4]`

- **Initial state**: `maxSum = -2`, `currentSum = -2`
- **Step 1 ($i = 1$, `nums[1] = 1`)**:
  - `currentSum = max(1, -2 + 1) = max(1, -1) = 1`
  - `maxSum = max(-2, 1) = 1`
- **Step 2 ($i = 2$, `nums[2] = -3`)**:
  - `currentSum = max(-3, 1 + -3) = max(-3, -2) = -2`
  - `maxSum = max(1, -2) = 1`
- **Step 3 ($i = 3$, `nums[3] = 4`)**:
  - `currentSum = max(4, -2 + 4) = max(4, 2) = 4`
  - `maxSum = max(1, 4) = 4`
- **Result**: Returns `maxSum = 4`.

## 9. Complexity
Time: $O(n)$ — Single pass through the array.
Space: $O(1)$ — Uses only two integer variables.

## 10. Edge Cases
- Single element array (e.g. `[5]`): Loop doesn't execute; returns 5.
- All negative elements (e.g. `[-5, -2, -3]`): Correctly returns `-2` (the least negative value).
- All positive elements: Accumulates the sum of the entire array.

## 11. Follow-up Questions
- How would you modify this to return the starting and ending indices of the maximum subarray? (Keep a start pointer `s` which resets to `i` whenever `currentSum` resets to `nums[i]`, and update `start_index` and `end_index` variables whenever `maxSum` is updated).
- How would you solve this using Divide and Conquer? (Split the array into halves. The maximum subarray is either in the left half, in the right half, or crosses the midpoint. This yields $O(n \log n)$ time complexity).

## 12. Interview Explanation
"First, I would clarify if the array can contain both negative and positive numbers. The brute force approach is to compute the sum of every possible subarray, taking $O(n^2)$ time. To optimize this, I would use Dynamic Programming, commonly known as Kadane's Algorithm. The key insight is that at each index $i$, we decide whether to extend the existing subarray or start a new subarray from the current element. We do this by calculating `currentSum = max(nums[i], currentSum + nums[i])`. We also maintain a `maxSum` to store the maximum subarray sum encountered so far. My C++ code runs in $O(n)$ time and uses $O(1)$ space."
