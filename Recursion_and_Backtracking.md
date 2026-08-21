# Section 7: Recursion and Backtracking

This section covers fundamental recursion and backtracking algorithms. Backtracking is a systematic way of searching the state space of a problem to find all (or some) solutions by building candidates incrementally and abandoning a candidate ("backtracking") as soon as it is determined that the candidate cannot lead to a valid solution.

---

# 1. Combination Sum (LeetCode 39)

## 1. Pattern
**Backtracking / Depth-First Search (DFS)**. The pattern involves exploring a decision tree where at each step, we can either choose a candidate number (allowing repeated selections) or skip to the next candidate number.

## 2. What is the interviewer asking?
- You are given an array of **distinct** positive integers `candidates` and a target integer `target`.
- You need to return a list of all **unique combinations** of `candidates` where the chosen numbers sum to `target`.
- **Constraint:** The same number may be chosen from `candidates` an **unlimited number of times**.
- Two combinations are unique if the frequency of at least one of the chosen numbers is different. All numbers in the input and the target are positive.

## 3. Intuition
Since all candidates are positive integers and we can reuse them, we can build combinations step-by-step. At each recursion level, we try adding elements to our current running combination. 
To avoid generating duplicate combinations (e.g., generating `[2, 2, 3]` and `[2, 3, 2]`), we impose a strict ordering: once we decide to move to the next candidate index, we never choose any previous candidate index again. This means from any candidate index `i`, we can only transition to candidate indices `j` such that `j >= i`.

## 4. Brute Force
Approach:
Generate all combinations of any size from the candidates (potentially with duplicates) by recursively branching for all elements at every step. If the sum of the current combination matches the target, we sort the combination and insert it into a set to filter out duplicate combinations. If the sum exceeds the target, we stop.

Time: O(N^(T/M + 1) * log(T/M)) where N is the number of candidates, T is the target, and M is the minimum value in `candidates`. Sorting each candidate combination of length up to T/M adds a logarithmic factor.
Space: O(N^(T/M + 1) * (T/M)) to store all combinations in a set.

## 5. Optimized Approach
We use backtracking with a `start` index to prevent duplicate combinations by construction. 
1. Define a helper function [`backtrack`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md) that tracks the remaining `target`, the `start` index in `candidates`, the `current` combination list, and the `result` matrix.
2. **Base Case:** If `target == 0`, we have found a valid combination. We add `current` to `result` and return.
3. **Recursive Step:** Loop through the candidates starting from index `start`.
   - If `candidates[i]` is larger than `target`, skip it (pruning the search tree since all numbers are positive).
   - Otherwise, push `candidates[i]` to `current`.
   - Recurse by calling [`backtrack`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md) with `target - candidates[i]` and the same index `i` (allowing reuse).
   - Backtrack by popping the last element from `current`.

## 6. Why does it work?
- **No duplicates:** By passing `i` as the `start` index to the next recursive call, we restrict the search to elements at or after index `i`. This guarantees that elements are chosen in non-decreasing order of their indices, preventing permutations of the same combination.
- **Completeness:** Since we try all possible valid candidates at each step and only backtrack when the target is exceeded or met, we exhaustively search all possible candidate selections.

## 7. C++ Code
```cpp
#include <vector>

using namespace std;

// Backtracking helper to explore combinations of candidates
void backtrack(vector<int>& candidates, int target, int start, vector<int>& current, vector<vector<int>>& result) {
    // If target is reached, save the path
    if (target == 0) {
        result.push_back(current);
        return;
    }
    
    for (int i = start; i < candidates.size(); ++i) {
        // Pruning: skip elements larger than the remaining target
        if (candidates[i] > target) continue;
        
        // Make decision
        current.push_back(candidates[i]);
        
        // Recurse: start is still 'i' since we can reuse the same candidate
        backtrack(candidates, target - candidates[i], i, current, result);
        
        // Undo decision
        current.pop_back();
    }
}

vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
    vector<vector<int>> result;
    vector<int> current;
    // Start backtracking from candidate index 0
    backtrack(candidates, target, 0, current, result);
    return result;
}
```

## 8. Dry Run
Input:
candidates = [2, 3, 6, 7], target = 7

Step 1:
backtrack(target=7, start=0, current=[])
- `i = 0` (val=2 <= 7): current=[2] -> recurse target=5, start=0

Step 2:
backtrack(target=5, start=0, current=[2])
- `i = 0` (val=2 <= 5): current=[2, 2] -> recurse target=3, start=0

Step 3:
backtrack(target=3, start=0, current=[2, 2])
- `i = 0` (val=2 <= 3): current=[2, 2, 2] -> recurse target=1, start=0

Step 4:
backtrack(target=1, start=0, current=[2, 2, 2])
- `i = 0` (2 > 1): skip
- `i = 1` (3 > 1): skip
- `i = 2` (6 > 1): skip
- `i = 3` (7 > 1): skip. Returns to Step 3.

Step 5:
Backtrack at Step 3: current=[2, 2]. Loop continues with `i = 1` (val=3 <= 3):
- current=[2, 2, 3] -> recurse target=0, start=1

Step 6:
backtrack(target=0, start=1, current=[2, 2, 3])
- target == 0: result.push_back([2, 2, 3]). Returns to Step 5.

Step 7:
Backtrack: current=[2, 2]. `i = 2` (6 > 3): skip; `i = 3` (7 > 3): skip. Returns to Step 2.

Step 8:
Backtrack at Step 2: current=[2]. Loop continues with `i = 1` (val=3 <= 5):
- current=[2, 3] -> recurse target=2, start=1

Step 9:
backtrack(target=2, start=1, current=[2, 3])
- `i = 1` (3 > 2): skip; `i = 2` (6 > 2): skip; `i = 3` (7 > 2): skip. Returns to Step 8.

Step 10:
Backtrack: current=[2]. Loop continues with `i = 2` (6 > 5): skip. Returns to Step 1.

Step 11:
Backtrack at Step 1: current=[]. Loop continues with `i = 1` (val=3 <= 7):
- current=[3] -> recurse target=4, start=1
- (Sub-branches for target=4 are checked, no matches found)

Step 12:
Backtrack: current=[]. Loop continues with `i = 3` (val=7 <= 7):
- current=[7] -> recurse target=0, start=3
- target == 0: result.push_back([7]). Returns.

Result: [[2, 2, 3], [7]]

## 9. Complexity
Time: O(N^(T/M + 1)) where N is the number of candidates, T is the target, and M is the minimum candidate value. The maximum height of the recursion tree is T/M, and the branching factor is at most N.
Space: O(T/M) representing the maximum depth of the call stack (recursion overhead) and the temporary combination storage path.

## 10. Edge Cases
- No valid combinations: If target is smaller than all candidates, the recursion terminates immediately, returning an empty list.
- Single element matching target: If `candidates = [7]` and `target = 7`, the algorithm correctly finds `[[7]]`.
- Large target with small candidates: e.g., `candidates = [2]`, `target = 100`. The recursion will go deep. The recursion depth constraint should be monitored.

## 11. Follow-up Questions
- What if the array `candidates` contains duplicate elements? (We must sort the array and skip duplicates using `if (i > start && candidates[i] == candidates[i-1]) continue;` and progress recursion with `i + 1`).
- Can we solve this problem without using recursion? (Yes, using dynamic programming similar to the Unbounded Knapsack problem, but reconstructing paths requires tracking parent states).
- How would you optimize search if `candidates` contains negative values? (If negative numbers are allowed, infinite cycles are possible unless constraints on combinations length are introduced).

## 12. Interview Explanation
"First, I would clarify if the input contains negative numbers or duplicates and verify if elements can be reused. Since candidates are unique positive values and reuse is allowed, this can be modeled as a decision tree.
A naive brute force approach would explore all combinations at every index and filter duplicates using a set, which is highly inefficient. 
Instead, I would use backtracking. To prevent duplicates, I will enforce an index order: in any recursive state, we only explore elements from the current index forward. If a candidate is larger than our remaining target, we prune that branch. My C++ code uses a recursive helper [`backtrack`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md) which maintains a `start` index to enforce this ordering, yielding an optimal and clean solution."

---

# 2. Permutations (LeetCode 46)

## 1. Pattern
**Backtracking / In-place Swapping**. This pattern is used to generate all N! orderings of a given array by swapping elements at each position of the array.

## 2. What is the interviewer asking?
- Given an array `nums` of **distinct** integers, return all possible permutations in any order.
- A permutation is an arrangement of all the elements of the array in some order.

## 3. Intuition
To generate all permutations of an array, we can decide the element for the first position, then recursively decide the element for the second position, and so on. 
Instead of allocating extra memory to track which numbers have been visited, we can perform this decision in-place by swapping elements. For a given index `start`, any element from index `start` to `nums.size() - 1` can be swapped into the `start` position. Once placed, we recurse on `start + 1`. After returning, we swap back to restore the original state (backtrack) and try the next choice.

## 4. Brute Force
Approach:
Use an auxiliary array `visited` of size N to track which elements are already in our current permutation path. Start with an empty list, iterate through `nums` from index 0 to N-1, and if `nums[i]` is not visited, mark it visited, append it to our path, and recurse. Once returned, unmark it and pop it from the path.

Time: O(N * N!) since there are N! permutations, and at each leaf, we copy a list of size N to the result.
Space: O(N) for the `visited` array and path list, plus O(N) for recursion stack.

## 5. Optimized Approach
We use the in-place swapping backtracking algorithm:
1. Define [`permuteHelper`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md) taking `nums`, `start` index, and `result`.
2. **Base Case:** If `start == nums.size()`, we have permuted all positions. Add `nums` to `result` and return.
3. **Recursive Step:** Loop index `i` from `start` to `nums.size() - 1`.
   - Swap `nums[start]` and `nums[i]`.
   - Recursively call [`permuteHelper`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md) with `start + 1`.
   - Backtrack by swapping `nums[start]` and `nums[i]` back to restore the original array structure.

## 6. Why does it work?
At each recursive step `start`, swapping `nums[start]` with `nums[i]` (where i >= start) guarantees that every remaining unplaced element has a turn to occupy the current `start` position. The subsequent recursive call handles all permutations of the remaining elements. Swapping back ensures that the original configuration is restored, so subsequent loop iterations correctly swap from the same pool of elements.

## 7. C++ Code
```cpp
#include <vector>
#include <algorithm>

using namespace std;

// Helper to generate permutations using in-place swap backtracking
void permuteHelper(vector<int>& nums, int start, vector<vector<int>>& result) {
    // Base Case: If we have fixed elements at all positions
    if (start == nums.size()) {
        result.push_back(nums);
        return;
    }
    
    for (int i = start; i < nums.size(); ++i) {
        // Swap to place the element at position 'i' into the 'start' position
        swap(nums[start], nums[i]);
        
        // Recurse for the next index
        permuteHelper(nums, start + 1, result);
        
        // Backtrack: swap back to restore original state
        swap(nums[start], nums[i]);
    }
}

vector<vector<int>> permute(vector<int>& nums) {
    vector<vector<int>> result;
    // Generate permutations starting from index 0
    permuteHelper(nums, 0, result);
    return result;
}
```

## 8. Dry Run
Input:
nums = [1, 2, 3]

Step 1:
permuteHelper(nums=[1, 2, 3], start=0)
- `i = 0`: swap `nums[0]` & `nums[0]` (no change). Recurse with start=1.

Step 2:
permuteHelper(nums=[1, 2, 3], start=1)
- `i = 1`: swap `nums[1]` & `nums[1]` (no change). Recurse with start=2.

Step 3:
permuteHelper(nums=[1, 2, 3], start=2)
- `i = 2`: swap `nums[2]` & `nums[2]` (no change). Recurse with start=3.

Step 4:
permuteHelper(nums=[1, 2, 3], start=3)
- Base case reached: `result.push_back([1, 2, 3])`. Returns to Step 3.

Step 5:
Backtrack at Step 3: swap back `nums[2]` & `nums[2]`. Loop ends. Returns to Step 2.

Step 6:
Backtrack at Step 2: swap back `nums[1]` & `nums[1]`.
- Loop continues with `i = 2`: swap `nums[1]` & `nums[2]` -> nums=[1, 3, 2]. Recurse with start=2.

Step 7:
permuteHelper(nums=[1, 3, 2], start=2)
- `i = 2`: swap `nums[2]` & `nums[2]`. Recurse with start=3.
- Base case reached: `result.push_back([1, 3, 2])`. Returns.
- Backtrack: swap `nums[2]` & `nums[2]`. Returns to Step 6.

Step 8:
Backtrack at Step 6: swap `nums[1]` & `nums[2]` back -> nums=[1, 2, 3]. Returns to Step 1.

Step 9:
Backtrack at Step 1: swap `nums[0]` & `nums[0]`.
- Loop continues with `i = 1`: swap `nums[0]` & `nums[1]` -> nums=[2, 1, 3]. Recurse with start=1.
- (Subsequent recursive calls generate `[2, 1, 3]` and `[2, 3, 1]`)

Step 10:
Loop continues for `i = 2` at Step 1: swap `nums[0]` & `nums[2]` -> nums=[3, 2, 1].
- (Subsequent recursive calls generate `[3, 2, 1]` and `[3, 1, 2]`)

Result: [[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,2,1], [3,1,2]]

## 9. Complexity
Time: O(N * N!). There are N! leaves in the recursion tree. Copying the array of size N to the result matrix at each leaf takes O(N) time.
Space: O(N) recursion call stack space. If we consider output storage space, it is O(N * N!).

## 10. Edge Cases
- Single element: If `nums = [1]`, it returns `[[1]]` immediately as the loop terminates at the base case.
- Empty input: If `nums` is empty, returns `[[]]`.

## 11. Follow-up Questions
- What if the array contains duplicate elements? (We need to sort the array and skip swaps if we have seen the element at the current level, or use a set to track elements placed at `start` to avoid duplicates).
- Can we generate permutations iteratively? (Yes, by using Heap's Algorithm or C++'s `std::next_permutation`).
- Can we generate permutations in lexicographical order using backtracking? (In-place swapping does not generate permutations in lexicographical order. To do so, we must use a boolean visited array and traverse elements in sorted order).

## 12. Interview Explanation
"To solve the Permutations problem, the interviewer is asking for all possible orderings of a set of distinct integers.
A brute force approach involves maintaining a visited array to track which elements have been included in our path, which takes O(N) extra space.
To optimize, we can generate permutations in-place by swapping elements. Starting from index `start`, we swap the element at `start` with every subsequent element `i`, recursively call the helper for `start + 1`, and then swap back to backtrack. This avoids allocating a visited array, using only the implicit recursion call stack. My C++ code uses this in-place swap approach to generate all permutations in O(N * N!) time and O(N) space."

---

# 3. Subsets (LeetCode 78)

## 1. Pattern
**Backtracking / Power Set Generation**. This pattern generates all 2^N subsets (combinations of all lengths) of a set of unique elements.

## 2. What is the interviewer asking?
- Given an integer array `nums` of **unique** elements, return all possible subsets (the power set).
- The solution set **must not** contain duplicate subsets.
- You can return the solution in any order.

## 3. Intuition
For each element in the array, we can either include it in our subset or exclude it. This binary choice yields 2^N possible subsets.
By using backtracking, we start with an empty set. At each step, we record the current state of our subset, then iterate through the remaining elements, add one, recurse to explore subsets containing that element, and finally backtrack (remove it) to explore subsets without it.

## 4. Brute Force
Approach:
Generate all 2^N binary representations from 0 to 2^N - 1. For each number, if the j-th bit is set, include `nums[j]` in the subset. 

Time: O(N * 2^N) since we loop 2^N times and check N bits for each number.
Space: O(N * 2^N) to store all subsets.

## 5. Optimized Approach
We use backtracking to construct subsets dynamically:
1. Define a recursive function [`backtrack`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md) that accepts `nums`, `start` index, the `current` subset path, and the `result`.
2. **Step 1:** Every state visited in the backtracking tree is a valid subset. We immediately push a copy of `current` to `result`.
3. **Step 2:** Loop index `i` from `start` to `nums.size() - 1`.
   - Push `nums[i]` to `current`.
   - Recurse by calling [`backtrack`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md) with `i + 1` (to prevent reuse of the same element and ensure subsets are built in index order).
   - Pop the last element from `current` (backtrack).

## 6. Why does it work?
By adding `current` to `result` at the beginning of each recursive call, we record every prefix path of the recursion tree (including the empty set).
Passing `i + 1` as the next `start` index guarantees that elements in any subset are selected in strictly increasing order of their indices in `nums`. This prevents generating permutations of the same subset (e.g. `[1, 2]` and `[2, 1]`) because we only search forward.

## 7. C++ Code
```cpp
#include <vector>

using namespace std;

// Backtracking helper to generate all subsets
void backtrack(vector<int>& nums, int start, vector<int>& current, vector<vector<int>>& result) {
    // Record the current subset (covers empty set, single elements, and combinations)
    result.push_back(current);
    
    // Branch out for all elements after the current 'start' index
    for (int i = start; i < nums.size(); ++i) {
        // Choose step
        current.push_back(nums[i]);
        
        // Recur step: progress to 'i + 1' to avoid duplicate selection
        backtrack(nums, i + 1, current, result);
        
        // Backtrack step
        current.pop_back();
    }
}

vector<vector<int>> subsets(vector<int>& nums) {
    vector<vector<int>> result;
    vector<int> current;
    // Start generating subsets from index 0
    backtrack(nums, 0, current, result);
    return result;
}
```

## 8. Dry Run
Input:
nums = [1, 2]

Step 1:
backtrack(start=0, current=[])
- `result.push_back([])` -> result = [ [] ]
- `i = 0`: current=[1] -> recurse start=1

Step 2:
backtrack(start=1, current=[1])
- `result.push_back([1])` -> result = [ [], [1] ]
- `i = 1`: current=[1, 2] -> recurse start=2

Step 3:
backtrack(start=2, current=[1, 2])
- `result.push_back([1, 2])` -> result = [ [], [1], [1, 2] ]
- Loop from `i = 2` to `1` is invalid. Returns to Step 2.

Step 4:
Backtrack at Step 2: current=[1]. Loop ends. Returns to Step 1.

Step 5:
Backtrack at Step 1: current=[].
- Loop continues with `i = 1`: current=[2] -> recurse start=2.

Step 6:
backtrack(start=2, current=[2])
- `result.push_back([2])` -> result = [ [], [1], [1, 2], [2] ]
- Loop from `i = 2` to `1` is invalid. Returns to Step 5.

Step 7:
Backtrack: current=[]. Loop ends. Returns to caller.

Result: [[], [1], [1, 2], [2]]

## 9. Complexity
Time: O(N * 2^N) since there are 2^N subsets in the power set, and it takes O(N) to copy each subset to the output list.
Space: O(N) for recursion call stack and temporary subset path vector.

## 10. Edge Cases
- Empty array: If `nums = []`, returns `[[]]`.
- Single element: If `nums = [1]`, returns `[[], [1]]`.

## 11. Follow-up Questions
- What if the input array has duplicate values? (We must sort the array first, then skip duplicates by checking if `i > start && nums[i] == nums[i-1]`).
- Can you solve this iteratively? (Yes, start with `[[]]`, and for each number, iterate through all existing subsets in the result list and append the current number to create new subsets).
- How does the time complexity change if we only want subsets of size K? (It would be O(N * nCr(N, K))).

## 12. Interview Explanation
"To generate all subsets of a set of unique numbers, we can traverse a decision tree where at each node we decide whether to include a number or not.
The brute force way is using bit manipulation to check all 2^N combinations, but this can be harder to generalize for other constraints.
Instead, we can use backtracking. At each recursive call, we immediately record the current path as a valid subset. We then loop from the current index `start` to the end of the array, add the element, recurse on `i + 1`, and backtrack. This systematic search guarantees that we explore all subsets without generating duplicates. My C++ implementation handles this recursively in O(N * 2^N) time and O(N) stack space."

---

# 4. N-Queens (LeetCode 51)

## 1. Pattern
**Backtracking / Constraint Satisfaction**. This pattern involves placing elements row-by-row on a grid and backtracking when conflicts occur.

## 2. What is the interviewer asking?
- Place N queens on an N x N chessboard such that no two queens attack each other.
- A queen can attack another queen if they share the same row, column, or diagonal.
- Return all distinct board configurations. Each solution contains a distinct board layout where `'Q'` represents a queen and `'.'` represents an empty space.

## 3. Intuition
Since every row must contain exactly one queen, we can solve this problem by placing queens row-by-row. When placing a queen at `(row, col)`, we verify if this position is safe from previously placed queens in rows `0` to `row-1`. If it is safe, we tentatively place the queen and proceed to `row + 1`. If no safe positions exist in subsequent rows, we backtrack.

## 4. Brute Force
Approach:
Generate all combinations of placing N queens on the N^2 chess cells. This is nCr(N^2, N) states. For N=8, this is nCr(64, 8) ≈ 4.4 * 10^9 possibilities. For each state, check if any two queens attack each other.

Time: O(N^2 * nCr(N^2, N)) which is computationally infeasible.
Space: O(N^2) to store the board.

## 5. Optimized Approach
We place exactly one queen per row, reducing the search space to N^N:
1. Initialize an N x N grid of strings containing `'.'`.
2. Define a recursive helper function [`solve`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md) that accepts the current `row`, the `board`, the `result` vector, and `n`.
3. **Base Case:** If `row == n`, we have successfully placed all N queens. Push the current `board` to `result` and return.
4. **Recursive Step:** Loop column `col` from `0` to `n - 1`.
   - Call [`isSafe`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md) to check if placing a queen at `(row, col)` conflicts with queens in the columns or diagonals of previous rows.
   - If safe, set `board[row][col] = 'Q'`.
   - Recurse with [`solve`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md)(row + 1, ...).
   - Backtrack by setting `board[row][col] = '.'`.

Safety Checks:
Since we process row-by-row, we only check for conflicts above the current row:
- Check same column: `board[i][col] == 'Q'` for 0 <= i < row.
- Check top-left diagonal: `board[i][j] == 'Q'` for decreasing i, j starting from row-1, col-1.
- Check top-right diagonal: `board[i][j] == 'Q'` for decreasing i and increasing j starting from row-1, col+1.

## 6. Why does it work?
- Placing one queen per row automatically satisfies the row conflict constraint.
- The safety checks ensure that column, positive diagonal, and negative diagonal constraints are satisfied at the moment of placement.
- By searching row-by-row and checking safety against previous rows, we prune search paths immediately upon encountering a conflict, saving millions of operations.

## 7. C++ Code
```cpp
#include <vector>
#include <string>

using namespace std;

// Safety check to verify column and diagonal conflicts
bool isSafe(vector<string>& board, int row, int col, int n) {
    // Check same column above the current row
    for (int i = 0; i < row; ++i) {
        if (board[i][col] == 'Q') return false;
    }
    
    // Check top-left diagonal
    for (int i = row - 1, j = col - 1; i >= 0 && j >= 0; --i, --j) {
        if (board[i][j] == 'Q') return false;
    }
    
    // Check top-right diagonal
    for (int i = row - 1, j = col + 1; i >= 0 && j < n; --i, ++j) {
        if (board[i][j] == 'Q') return false;
    }
    
    return true;
}

// Backtracking solver
void solve(int row, vector<string>& board, vector<vector<string>>& result, int n) {
    // Base Case: All rows successfully filled
    if (row == n) {
        result.push_back(board);
        return;
    }
    
    // Try placing queen in each column of the current row
    for (int col = 0; col < n; ++col) {
        if (isSafe(board, row, col, n)) {
            // Place Queen
            board[row][col] = 'Q';
            
            // Recurse to next row
            solve(row + 1, board, result, n);
            
            // Backtrack
            board[row][col] = '.';
        }
    }
}

vector<vector<string>> solveNQueens(int n) {
    vector<vector<string>> result;
    // Initialize an empty board
    vector<string> board(n, string(n, '.'));
    // Start solving from row 0
    solve(0, board, result, n);
    return result;
}
```

## 8. Dry Run
Input:
n = 4

Step 1:
solve(row=0, board=[...., ...., ...., ....])
- `col = 0`: Safe. `board[0] = "Q..."`. Recurse row=1.

Step 2:
solve(row=1, board=["Q...", "....", "....", "...."])
- `col = 0`: Not safe (same column).
- `col = 1`: Not safe (diag from (0,0)).
- `col = 2`: Safe. `board[1] = "..Q."`. Recurse row=2.

Step 3:
solve(row=2, board=["Q...", "..Q.", "....", "...."])
- `col = 0`: Not safe (same col).
- `col = 1`: Not safe (diag).
- `col = 2`: Not safe.
- `col = 3`: Not safe (diag from (1,2)).
- Loop ends. Backtrack to row=1.

Step 4:
Backtrack at Step 2: `board[1] = "...."`.
- `col = 3`: Safe. `board[1] = "...Q"`. Recurse row=2.

Step 5:
solve(row=2, board=["Q...", "...Q", "....", "...."])
- `col = 0`: Not safe.
- `col = 1`: Safe. `board[2] = ".Q.."` -> Recurse row=3.

Step 6:
solve(row=3, board=["Q...", "...Q", ".Q..", "...."])
- `col = 0` to `3` are all blocked by existing queens.
- Returns to Step 5. Backtrack: `board[2] = "...."`.
- Returns to Step 1.

Step 7:
Backtrack at Step 1: `board[0] = "...."`.
- `col = 1`: Safe. `board[0] = ".Q.."`. Recurse row=1.

Step 8:
solve(row=1, board=[".Q..", "....", "....", "...."])
- `col = 3`: Safe. `board[1] = "...Q"`. Recurse row=2.

Step 9:
solve(row=2, board=[".Q..", "...Q", "....", "...."])
- `col = 0`: Safe. `board[2] = "Q..."`. Recurse row=3.

Step 10:
solve(row=3, board=[".Q..", "...Q", "Q...", "...."])
- `col = 2`: Safe. `board[3] = "..Q."`. Recurse row=4.

Step 11:
solve(row=4, ...) -> Base Case!
- `result.push_back([".Q..", "...Q", "Q...", "..Q."])`.

Result: Board configuration returned.

## 9. Complexity
Time: O(N!) worst case. The first queen has N positions, the second has at most N-2, the third has at most N-4, and so on.
Space: O(N^2) to store the board representation, and O(N) for the recursion stack.

## 10. Edge Cases
- $N = 1$: Trivial case, returns `[["Q"]]`.
- $N = 2, 3$: No valid configurations exist. Returns empty list `[]`.

## 11. Follow-up Questions
- How can we optimize the O(N) safety check? (We can use three boolean arrays/hashsets: one for columns, one for positive diagonals `row + col` which remains constant along the anti-diagonals, and one for negative diagonals `row - col` which remains constant along the main diagonals. This makes safety checks O(1)).
- How would you modify this to only count the number of solutions? (LeetCode 52: remove storing the board configurations, just increment a counter at the base case).
- Can you solve this using bitmasking? (Yes, tracking column and diagonal availability using bits of integers is highly optimized for N <= 32).

## 12. Interview Explanation
"To solve the N-Queens problem, we need to place N queens on an N x N board without any attacking each other.
A brute force approach of placing N queens in all possible grid combinations is way too slow.
Instead, we place one queen per row, ensuring we never check for row conflicts. We start from row 0 and try placing a queen in each column. If the placement is safe vertically and diagonally, we transition to the next row. If we hit a dead end, we backtrack by clearing the queen and trying the next column. My C++ code implements this backtracking model with a helper function [`isSafe`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md) to check diagonals and columns in O(N) time per check."

---

# 5. Letter Combinations of a Phone Number (LeetCode 17)

## 1. Pattern
**Backtracking / DFS on N-ary Tree**. This pattern is used to explore combinations of elements chosen from distinct sets (digit-to-letters mapping).

## 2. What is the interviewer asking?
- Given a string containing digits from `2-9` inclusive, return all possible letter combinations that the number could represent.
- The mapping of digits to letters is identical to a standard telephone keypad (e.g. `2` -> `abc`, `3` -> `def`).

## 3. Intuition
Each digit maps to a set of letters. If we have K digits, we must construct strings of length K by choosing one letter from the set of the first digit, one from the second, etc. 
This can be modeled as a tree traversal where the depth of the tree is the length of the digit string, and the branching factor at each level is the number of letters mapped to that digit (3 or 4).

## 4. Brute Force
Approach:
If we know the input string length, say 3, we can write three nested loops. However, because the input string length is variable, we cannot hardcode nested loops. The recursive implementation is the standard way to dynamically simulate variable nested loops.

Time: O(4^N * N) where N is the length of digits.
Space: O(N) recursion depth.

## 5. Optimized Approach
We use backtracking:
1. Return `{}` if `digits` is empty.
2. Initialize the keypad mapping array `mapping` where index represents the digit.
3. Define the backtracking helper [`backtrack`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md) with parameters `digits`, `index`, `current` combination string, `result` list, and `mapping`.
4. **Base Case:** If `index == digits.size()`, we have chosen a letter for every digit. Add `current` to `result` and return.
5. **Recursive Step:**
   - Retrieve the candidate letters string: `mapping[digits[index] - '0']`.
   - Loop through each character `ch` in the letters string:
     - Push `ch` to `current`.
     - Recurse by calling [`backtrack`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md) with `index + 1`.
     - Pop the last character from `current` (backtrack).

## 6. Why does it work?
The recursion stack handles the variable number of digits. We traverse depth-first, ensuring that when we reach the base case, `current` contains exactly one character for each digit in the correct order. The backtracking step (`pop_back`) resets the state so that other character candidates at the same depth can be tried.

## 7. C++ Code
```cpp
#include <vector>
#include <string>

using namespace std;

// Backtracking helper to generate combinations
void backtrack(string& digits, int index, string& current, vector<string>& result, vector<string>& mapping) {
    // Base Case: All digits processed
    if (index == digits.size()) {
        result.push_back(current);
        return;
    }
    
    // Get letters associated with the current digit
    string letters = mapping[digits[index] - '0'];
    for (char ch : letters) {
        // Choose step
        current.push_back(ch);
        
        // Recur step
        backtrack(digits, index + 1, current, result, mapping);
        
        // Backtrack step
        current.pop_back();
    }
}

vector<string> letterCombinations(string digits) {
    if (digits.empty()) return {};
    
    // Mapping phone digits to letters
    vector<string> mapping = {"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
    vector<string> result;
    string current;
    
    // Start DFS from index 0
    backtrack(digits, 0, current, result, mapping);
    return result;
}
```

## 8. Dry Run
Input:
digits = "23"

Step 1:
backtrack(digits="23", index=0, current="")
- `digits[0] = '2'` -> letters = "abc"
- `ch = 'a'`: current="a" -> recurse index=1

Step 2:
backtrack(digits="23", index=1, current="a")
- `digits[1] = '3'` -> letters = "def"
- `ch = 'd'`: current="ad" -> recurse index=2

Step 3:
backtrack(digits="23", index=2, current="ad")
- Base Case: `result.push_back("ad")`. Returns.

Step 4:
Backtrack at Step 2: current="a".
- `ch = 'e'`: current="ae" -> recurse index=2 -> result.push_back("ae"). Returns.

Step 5:
Backtrack at Step 2: current="a".
- `ch = 'f'`: current="af" -> recurse index=2 -> result.push_back("af"). Returns.

Step 6:
Backtrack: current="". Loop for `index=0` continues.
- `ch = 'b'`: current="b" -> recurse index=1 -> generates "bd", "be", "bf".

Step 7:
Loop for `index=0` continues.
- `ch = 'c'`: current="c" -> recurse index=1 -> generates "cd", "ce", "cf".

Result: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"]

## 9. Complexity
Time: O(4^N * N) where N is the number of digits. In the worst case, a digit can map to 4 letters (7 and 9). The total number of leaves is 4^N, and copying each combination of length N takes O(N) time.
Space: O(N) representing the recursion call stack depth.

## 10. Edge Cases
- Empty string: If `digits = ""`, the algorithm returns `{}` directly.
- Digits without maps: Standard keypad mapping skips `0` and `1`. Input constraints ensure only digits `2-9` are tested.

## 11. Follow-up Questions
- Can you solve this iteratively? (Yes, using a FIFO queue. Start with an empty string in the queue, dequeue, append all characters for the next digit, and enqueue. Repeat until the length of the front element matches the digits length).
- How would you handle a dictionary check to return only valid words? (Filter combinations at the base case using a Trie or Hash Set of valid dictionary words).

## 12. Interview Explanation
"To solve the phone number letter combinations problem, we are looking to generate all possible string configurations.
Since the length of the inputs is variable, we cannot hardcode nested loops. Instead, I model the choices as a decision tree using recursion.
I set up a static lookup mapping for keypad numbers. My recursive helper function [`backtrack`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md) starts at digit index 0. For the current digit, we retrieve the corresponding letters and loop over them. At each step, we append the character to our buffer, recurse on the next digit index, and then backtrack by popping the character. This allows us to find all combinations in O(4^N * N) time and O(N) memory."

---

# 6. Subsets II (LeetCode 90)

## 1. Pattern
**Backtracking / Sorting & Duplicate Pruning**. This pattern is utilized when generating subsets from a candidate list containing duplicate values, requiring pruning of identical recursive branches.

## 2. What is the interviewer asking?
- Given an integer array `nums` that may contain **duplicate** elements, return all possible subsets (the power set).
- The solution set **must not** contain duplicate subsets.
- You can return the solution in any order.

## 3. Intuition
If the array contains duplicate elements (e.g. `[1, 2, 2]`), a standard subset generation algorithm will generate duplicates (e.g. including the first `2` vs. including the second `2` yields the same subset `[1, 2]`).
To prevent duplicate subsets, we first sort the array. This groups duplicate values together. 
When generating choices at a particular recursion level starting at index `start`, if the current element is identical to the previous element in the loop (`nums[i] == nums[i-1]`) and is NOT the first element we are trying for this position (`i > start`), we skip it.

## 4. Brute Force
Approach:
Generate all subsets using the standard backtracking template. Store each subset in a `std::set<vector<int>>` after sorting the subset. The set automatically deduplicates.

Time: O(N * 2^N * log(2^N)) = O(N^2 * 2^N) due to set insert operations and sorting subsets.
Space: O(N * 2^N) to store all subsets in the set.

## 5. Optimized Approach
We prune duplicate recursion paths by checking duplicate items at the same depth:
1. Sort the input array `nums` to ensure duplicates are adjacent.
2. Define the backtracking function [`backtrack`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md).
3. **Step 1:** Push `current` path to `result` immediately (since all prefix combinations are valid subsets).
4. **Step 2:** Loop index `i` from `start` to `nums.size() - 1`.
   - **Pruning condition:** If `i > start` and `nums[i] == nums[i-1]`, skip the iteration (`continue`).
   - Push `nums[i]` to `current`.
   - Recurse with [`backtrack`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md)(nums, i + 1, current, result).
   - Backtrack: pop the last element from `current`.

## 6. Why does it work?
- Sorting groups identical elements together.
- The condition `i > start` checks if we are moving *horizontally* in the recursive decision tree at the current level. If `nums[i] == nums[i-1]`, it means we already explored the branch starting with this duplicate value at this position. Thus, skipping it prevents duplicate subsets.
- We do not skip when `i == start`, because that represents moving *vertically* down the tree (including multiple copies of the duplicate element in the subset, e.g. `[2, 2]`), which is valid.

## 7. C++ Code
```cpp
#include <vector>
#include <algorithm>

using namespace std;

// Backtracking helper with duplicate pruning
void backtrack(vector<int>& nums, int start, vector<int>& current, vector<vector<int>>& result) {
    // Record the current subset
    result.push_back(current);
    
    for (int i = start; i < nums.size(); ++i) {
        // Skip duplicates horizontally in the search tree
        if (i > start && nums[i] == nums[i - 1]) continue;
        
        // Choose step
        current.push_back(nums[i]);
        
        // Recur step: progress forward
        backtrack(nums, i + 1, current, result);
        
        // Backtrack step
        current.pop_back();
    }
}

vector<vector<int>> subsetsWithDup(vector<int>& nums) {
    // Sort array to ensure duplicate values are adjacent
    sort(nums.begin(), nums.end());
    vector<vector<int>> result;
    vector<int> current;
    
    // Initiate recursive backtracking
    backtrack(nums, 0, current, result);
    return result;
}
```

## 8. Dry Run
Input:
nums = [2, 1, 2] -> Sorted nums = [1, 2, 2]

Step 1:
backtrack(start=0, current=[])
- `result.push_back([])` -> result = [ [] ]
- `i = 0` (val=1): current=[1] -> recurse start=1

Step 2:
backtrack(start=1, current=[1])
- `result.push_back([1])` -> result = [ [], [1] ]
- `i = 1` (val=2): current=[1, 2] -> recurse start=2

Step 3:
backtrack(start=2, current=[1, 2])
- `result.push_back([1, 2])` -> result = [ [], [1], [1, 2] ]
- `i = 2` (val=2): `i > start` (2 > 2 is false) -> no skip. current=[1, 2, 2] -> recurse start=3

Step 4:
backtrack(start=3, current=[1, 2, 2])
- `result.push_back([1, 2, 2])`. Returns.

Step 5:
Backtrack at Step 3: current=[1, 2]. Loop ends. Returns to Step 2.

Step 6:
Backtrack at Step 2: current=[1].
- `i = 2` (val=2): `i > start` (2 > 1 is true) and `nums[2] == nums[1]` -> SKIP (continue).
- Loop ends. Returns to Step 1.

Step 7:
Backtrack at Step 1: current=[].
- `i = 1` (val=2): current=[2] -> recurse start=2.
- (Generates `[2]` and `[2, 2]` downstream).

Result: [[], [1], [1, 2], [1, 2, 2], [2], [2, 2]]

## 9. Complexity
Time: O(N * 2^N) worst-case. There are at most 2^N subsets, and copying each to the results takes O(N) time. Sorting takes O(N log N), which is dominated by the backtracking steps.
Space: O(N) for recursion call stack and current vector.

## 10. Edge Cases
- All identical elements: e.g., `nums = [2, 2, 2]`. The code correctly generates only subsets containing 0, 1, 2, or 3 copies of `2`, yielding `[[], [2], [2, 2], [2, 2, 2]]`.
- Empty input: Returns `[[]]`.

## 11. Follow-up Questions
- How is the duplicate handling different here compared to Combination Sum II? (In Subsets II, we add the path to the result immediately at the start of recursion. In Combination Sum II, we only record the path when the target equals 0).
- Can you solve this iteratively? (Yes, sort the array, keep track of the start index of the subsets added in the previous step, and only append to those subsets when adding a duplicate element).

## 12. Interview Explanation
"The interviewer is asking to generate all unique subsets from a list that might contain duplicate values.
A brute force method would generate all subsets and use a set to filter duplicates, adding extra time complexity.
To optimize, we sort the array first. During backtracking, we loop from the `start` index. If the current element is equal to the previous element in the loop and its index `i` is strictly greater than `start`, we skip it. This ensures we don't explore duplicate paths horizontally at the same recursion depth. However, we still allow duplicates vertically (so `[2, 2]` is generated). My C++ implementation applies this pruning logic, achieving O(N * 2^N) time complexity and O(N) stack space."

---

# 7. Sudoku Solver (LeetCode 37)

## 1. Pattern
**Backtracking / DFS on 2D Matrix**. This pattern systematically attempts to fill cells on a grid, backtracking when a placement fails to satisfy the constraints.

## 2. What is the interviewer asking?
- Write a program to solve a Sudoku puzzle by filling empty cells.
- Empty cells are indicated by character `'.'`.
- The solution must satisfy the standard Sudoku rules:
  1. Each digit `1-9` must occur exactly once in each row.
  2. Each digit `1-9` must occur exactly once in each column.
  3. Each digit `1-9` must occur exactly once in each of the 9 `3x3` sub-boxes.
- Assume the board has a unique solution.

## 3. Intuition
We scan the grid for empty cells. When we find one, we try to place digits `1-9`. For each digit, we check if the placement is valid according to Sudoku rules. If it is valid, we recursively call the solver to solve the rest of the board. If that recursive call succeeds, the puzzle is solved. If it fails, we clear the cell (backtrack) and try the next digit.

## 4. Brute Force
Approach:
Try all permutations of digits 1-9 for all empty cells without validating early. If there are K empty cells, there are 9^K combinations. Check each combination for validity.

Time: O(9^K) where K is the number of empty cells. Since K <= 81, this is computationally prohibitive.
Space: O(1) auxiliary board storage.

## 5. Optimized Approach
We use backtracking with early validation pruning:
1. Define [`isValid`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md) to check if a character `c` can be placed at `board[row][col]`:
   - Iterate index `i` from 0 to 8:
     - Check row conflict: `board[row][i] == c`
     - Check column conflict: `board[i][col] == c`
     - Check sub-box conflict: `board[3 * (row / 3) + i / 3][3 * (col / 3) + i % 3] == c`
   - Return `false` if any conflict is found, otherwise `true`.
2. Define recursive [`solve`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md) function:
   - Loop `i` from 0 to 8 and `j` from 0 to 8:
     - If cell `board[i][j]` is `'.'`:
       - Loop char `c` from `'1'` to `'9'`:
         - If [`isValid`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md)(board, i, j, c) is `true`:
           - Set `board[i][j] = c`.
           - If [`solve`](file:///c:/Users/piseg/Desktop/budapest/Amazon_Interview_Revision/07_Recursion_and_Backtracking.md)(board) returns `true`, return `true`.
           - Otherwise, reset `board[i][j] = '.'` (backtrack).
       - Return `false` (if no digit works).
   - Return `true` (if all cells are successfully filled).

## 6. Why does it work?
- The sub-box calculation formula `3 * (row / 3) + i / 3` and `3 * (col / 3) + i % 3` maps index `i` from 0 to 8 directly onto the 9 cells of the corresponding `3x3` box.
- By verifying the constraints *before* placing a number and backtracking as soon as a cell cannot be filled, we prune invalid branches of the search space.
- Since the problem guarantees a unique solution, returning `true` on the first successful complete state stops all further search.

## 7. C++ Code
```cpp
#include <vector>

using namespace std;

// Checks if character 'c' can be safely placed at board[row][col]
bool isValid(vector<vector<char>>& board, int row, int col, char c) {
    for (int i = 0; i < 9; ++i) {
        // Check row, column, and 3x3 box
        if (board[i][col] == c || board[row][i] == c || 
            board[3 * (row / 3) + i / 3][3 * (col / 3) + i % 3] == c) {
            return false;
        }
    }
    return true;
}

// Backtracking function to solve the board
bool solve(vector<vector<char>>& board) {
    for (int i = 0; i < 9; ++i) {
        for (int j = 0; j < 9; ++j) {
            // Find an empty cell
            if (board[i][j] == '.') {
                // Try digits '1' to '9'
                for (char c = '1'; c <= '9'; ++c) {
                    if (isValid(board, i, j, c)) {
                        // Place choice
                        board[i][j] = c;
                        
                        // Recurse to solve remaining cells
                        if (solve(board)) return true;
                        
                        // Backtrack if choice failed
                        board[i][j] = '.';
                    }
                }
                // Return false if no digit 1-9 is valid at this cell
                return false;
            }
        }
    }
    // Return true if no empty cells remain
    return true;
}

void solveSudoku(vector<vector<char>>& board) {
    solve(board);
}
```

## 8. Dry Run
Input:
Partial Board configuration trace where cell (0,2) is checked.

Step 1:
Loop `c` from '1' to '9' at empty cell (0,2).
- Let's check `c = '5'`.
- Call `isValid(board, row=0, col=2, c='5')`
- Row check: `board[0][i]` for i in [0,8]. Suppose `board[0][0] = '5'`. The check fails. Returns false.

Step 2:
Next choice, `c = '6'`.
- Call `isValid(board, row=0, col=2, c='6')`
- Row check: OK (no '6' in row 0).
- Column check: `board[i][2]` for i in [0,8]. Suppose `board[4][2] = '6'`. Check fails. Returns false.

Step 3:
Next choice, `c = '7'`.
- Call `isValid(board, row=0, col=2, c='7')`
- Box check: Box is top-left (0,0) to (2,2). Suppose `board[1][1] = '7'`. Check fails. Returns false.

Step 4:
Next choice, `c = '8'`.
- Call `isValid(board, row=0, col=2, c='8')`
- Suppose no conflicts found. Returns true.
- Set `board[0][2] = '8'`.
- Recurse by calling `solve(board)`.

Step 5:
If solve(board) downstream fails:
- Backtrack: reset `board[0][2] = '.'`.
- Loop continues with `c = '9'`.

## 9. Complexity
Time: O(9^81) worst-case (though board constraints prune the search space heavily, making it run in milliseconds).
Space: O(81) = O(1) space for the recursion stack since the size of the board is always 9x9.

## 10. Edge Cases
- Nearly blank board: This represents the highest number of combinations and takes the longest to compute.
- Already solved board: The loops finish instantly and returns `true`.

## 11. Follow-up Questions
- How can we optimize the safety checking? (Instead of scanning the row, column, and sub-box in O(9) every time, we can maintain bitmasks: `row_mask[9]`, `col_mask[9]`, `box_mask[9]`. Checking if `c` is valid becomes an O(1) bitwise operation).
- What if the board does not have a unique solution? (The backtracking approach will still find one valid solution and stop, or return false if no solution exists).
- How does this compare to Dancing Links? (Sudoku can be mapped to an exact cover problem and solved using Knuth's Algorithm X/Dancing Links, which is much faster).

## 12. Interview Explanation
"The interviewer is asking to write a program to solve a Sudoku puzzle.
The brute force method of trying every combination of numbers in empty cells is infeasible.
I will use backtracking. We search for an empty cell `board[i][j]`. When we find one, we try to place digits `'1'` to `'9'`. Before placing, we check if it is valid in row `i`, column `j`, and the 3x3 box. We check the box using `3 * (row / 3) + i / 3` and `3 * (col / 3) + i % 3`. If valid, we place it and recurse. If the recursion succeeds, we return `true`. If not, we backtrack by resetting the cell to `'.'`. My C++ code implements this solution, running in O(1) auxiliary space."
