# Section 5: Binary Search

---

# 1. Binary Search (LeetCode 704)

## 1. Pattern
Binary Search (Divide and Conquer / Decrease and Conquer).

## 2. What is the interviewer asking?
- Given a sorted (in ascending order) integer array `nums` and a target value `target`.
- Search for `target` in `nums`. If it exists, return its index; otherwise, return `-1`.
- The solution must run in strictly $O(\log n)$ time.

## 3. Intuition
Since the array is already sorted, we can avoid checking every element linearly. By checking the element at the middle index:
- If the middle element is equal to the target, we have found it.
- If the middle element is smaller than the target, then the target must lie to the right of the middle element (since all elements to the left are even smaller).
- If the middle element is larger than the target, the target must lie to the left of the middle element.
This allows us to discard half of the search space at each step, achieving logarithmic time complexity.

## 4. Brute Force
### Approach:
Perform a linear scan from index `0` to `n - 1`. Compare each element with the target. If a match is found, return the index. If the loop completes without finding the target, return `-1`.

### Time:
$O(N)$

### Space:
$O(1)$

## 5. Optimized Approach
1. Initialize two pointers: `left = 0` and `right = nums.size() - 1`.
2. Enter a loop while `left <= right`.
3. Compute the middle index `mid` using `mid = left + (right - left) / 2` to prevent potential integer overflow.
4. Check if `nums[mid] == target`. If true, return `mid`.
5. If `nums[mid] < target`, update `left = mid + 1` to search in the right half.
6. If `nums[mid] > target`, update `right = mid - 1` to search in the left half.
7. If the loop terminates without finding the target, return `-1`.

## 6. Why does it work?
The correctness of binary search relies on the monotonicity of the array. Because the array is sorted, any element at index $i$ is greater than or equal to all elements at indices $j < i$, and less than or equal to all elements at indices $k > i$. Therefore, comparing the target against `nums[mid]` tells us definitively which half of the array can be safely eliminated without loss of correctness.

## 7. C++ Code
```cpp
int search(vector<int>& nums, int target) {
    int left = 0, right = nums.size() - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) return mid;
        if (nums[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return -1;
}
```

## 8. Dry Run
Input: `nums = [-1, 0, 3, 5, 9, 12]`, `target = 9`

- **Initial State**:
  `left = 0`, `right = 5`

- **Step 1**:
  - `mid = 0 + (5 - 0) / 2 = 2`
  - `nums[mid] = nums[2] = 3`
  - Since `3 < 9`, update `left = mid + 1 = 3`

- **Step 2**:
  - `left = 3`, `right = 5`
  - `mid = 3 + (5 - 3) / 2 = 4`
  - `nums[mid] = nums[4] = 9`
  - Since `nums[4] == 9`, target is found. Return `4`.

## 9. Complexity
- **Time**: $O(\log N)$ as the search space is halved at each step.
- **Space**: $O(1)$ auxiliary space since we only use three integer variables.

## 10. Edge Cases
- **Single Element**: `nums = [5]`, `target = 5` (should return `0`) or `target = 3` (should return `-1`).
- **Target Out of Bounds**: Target is smaller than the first element or larger than the last element.
- **Two Elements**: Array of size 2, checking boundary behavior.

## 11. Follow-up Questions
- How would you modify the binary search to return the insertion index of the target if it is not found? (Answer: Return `left`).
- What if the array contains duplicates? Does it guarantee finding the first occurrence? (Answer: No, it finds any arbitrary occurrence. To find the first occurrence, we must continue searching left when a match is found).
- Can we implement this recursively? What is the impact? (Answer: Yes, but it will use $O(\log N)$ stack space due to recursion call overhead).

## 12. Interview Explanation
"First, I would clarify the constraints of the problem, such as the maximum array size and if the numbers are unique. Then I'd explain the brute force approach where we do a linear scan from left to right, checking every element in $O(N)$ time. To optimize this, I would use Binary Search because the array is sorted. By calculating the midpoint and adjusting our search window, we can reduce the search space by half at each step. My C++ code would initialize two pointers, `left` and `right`, compute the midpoint safely to avoid overflow, and adjust these boundaries until the pointers cross or the target is found. This reduces the time complexity to $O(\log N)$ while maintaining a space complexity of $O(1)$."

---

# 2. Find First and Last Position of Element in Sorted Array (LeetCode 34)

## 1. Pattern
Binary Search (Boundary Search / Double Binary Search).

## 2. What is the interviewer asking?
- Given an array of integers `nums` sorted in non-decreasing order.
- Find the starting (first) and ending (last) position of a given `target` value.
- If the target is not found in the array, return `[-1, -1]`.
- The algorithm must run in $O(\log n)$ runtime complexity.

## 3. Intuition
A standard binary search stops as soon as it finds the target. To find the first and last position of a target that appears multiple times:
- For the **first position**: when we find the target (or an element greater than or equal to the target), we must keep searching to the left to see if there is an even earlier occurrence. We record the candidate index whenever we see the target and shift `right = mid - 1`.
- For the **last position**: when we find the target (or an element less than or equal to the target), we must keep searching to the right to see if there is a later occurrence. We record the candidate index whenever we see the target and shift `left = mid + 1`.

## 4. Brute Force
### Approach:
Scan the array from left to right. The first index where `nums[i] == target` is the starting position. Scan from right to left. The first index from the back where `nums[j] == target` is the ending position. If not found, return `[-1, -1]`.

### Time:
$O(N)$

### Space:
$O(1)$

## 5. Optimized Approach
1. Initialize a result vector `result` of size 2 with `[-1, -1]`.
2. **Find First Position**:
   - Initialize `left = 0`, `right = nums.size() - 1`.
   - While `left <= right`, compute `mid`.
   - If `nums[mid] >= target`, search left by setting `right = mid - 1`.
   - If `nums[mid] < target`, search right by setting `left = mid + 1`.
   - If `nums[mid] == target`, update `result[0] = mid`.
3. **Find Last Position**:
   - Reset `left = 0`, `right = nums.size() - 1`.
   - While `left <= right`, compute `mid`.
   - If `nums[mid] <= target`, search right by setting `left = mid + 1`.
   - If `nums[mid] > target`, search left by setting `right = mid - 1`.
   - If `nums[mid] == target`, update `result[1] = mid`.
4. Return `result`.

## 6. Why does it work?
In the first search, when `nums[mid] == target`, we write the current `mid` to `result[0]`. By then moving `right = mid - 1`, we force the search space to shrink to the left half. If there is another occurrence of the target earlier in the array, the binary search will find it and overwrite `result[0]` with the smaller index. The same logic applies symmetrically to finding the last position.

## 7. C++ Code
```cpp
vector<int> searchRange(vector<int>& nums, int target) {
    int left = 0, right = nums.size() - 1;
    vector<int> result(2, -1);
    // Find first position
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] >= target) right = mid - 1;
        else left = mid + 1;
        if (nums[mid] == target) result[0] = mid;
    }
    left = 0; right = nums.size() - 1;
    // Find last position
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] <= target) left = mid + 1;
        else right = mid - 1;
        if (nums[mid] == target) result[1] = mid;
    }
    return result;
}
```

## 8. Dry Run
Input: `nums = [5, 7, 7, 8, 8, 10]`, `target = 8`

- **First Position Search**:
  - `left = 0`, `right = 5`
  - **Iteration 1**:
    - `mid = 2`, `nums[mid] = 7`.
    - Since `7 < 8`, `left = mid + 1 = 3`.
    - `nums[mid] != target`, no update to `result[0]`.
  - **Iteration 2**:
    - `left = 3`, `right = 5`. `mid = 4`, `nums[mid] = 8`.
    - Since `8 >= 8`, `right = mid - 1 = 3`.
    - Since `nums[mid] == 8`, `result[0] = 4`.
  - **Iteration 3**:
    - `left = 3`, `right = 3`. `mid = 3`, `nums[mid] = 8`.
    - Since `8 >= 8`, `right = mid - 1 = 2`.
    - Since `nums[mid] == 8`, `result[0] = 3`.
  - Loop terminates (`left > right`). First position is `3`.

- **Last Position Search**:
  - `left = 0`, `right = 5`
  - **Iteration 1**:
    - `mid = 2`, `nums[mid] = 7`.
    - Since `7 <= 8`, `left = mid + 1 = 3`.
    - `nums[mid] != target`, no update to `result[1]`.
  - **Iteration 2**:
    - `left = 3`, `right = 5`. `mid = 4`, `nums[mid] = 8`.
    - Since `8 <= 8`, `left = mid + 1 = 5`.
    - Since `nums[mid] == 8`, `result[1] = 4`.
  - **Iteration 3**:
    - `left = 5`, `right = 5`. `mid = 5`, `nums[mid] = 10`.
    - Since `10 > 8`, `right = mid - 1 = 4`.
    - `nums[mid] != target`, no update to `result[1]`.
  - Loop terminates. Last position is `4`.

Result returned is `[3, 4]`.

## 9. Complexity
- **Time**: $O(\log N)$ because we run two separate, standard binary searches, each taking $O(\log N)$ steps.
- **Space**: $O(1)$ auxiliary space as we modify the result vector directly and use a few pointers.

## 10. Edge Cases
- **Empty Array**: `nums = []` -> loop doesn't execute, returns `[-1, -1]`.
- **Target Not Present**: Target lies within the range of elements but does not exist (e.g., target 6 in `[5, 7]`). Returns `[-1, -1]`.
- **All Elements match target**: `nums = [8, 8, 8]`, returns `[0, 2]`.

## 11. Follow-up Questions
- Can you write this using a single binary search helper function? (Answer: Yes, write a helper function `findBound(nums, target, isFirst)` which takes a boolean flag to determine search direction, avoiding code duplication).
- How does `std::lower_bound` and `std::upper_bound` in C++ relate to this? (Answer: `std::lower_bound` finds the first element not less than target. `std::upper_bound` finds the first element strictly greater than target. The range is `[lower_bound, upper_bound - 1]`).

## 12. Interview Explanation
"First, I would clarify the constraints of the problem, such as whether the input array can be empty. Then I'd explain the brute force approach where we traverse the array from both ends to find the first and last indices of the target, resulting in $O(N)$ time. To optimize this, I would use two separate binary searches because the array is sorted. The first binary search finds the starting boundary by narrowing the search range to the left when a target is found. The second binary search finds the ending boundary by narrowing the search range to the right when a target is found. My C++ code would perform these two searches consecutively. This approach achieves $O(\log N)$ time and $O(1)$ auxiliary space."

---

# 3. Search a 2D Matrix (LeetCode 74)

## 1. Pattern
Binary Search on a flattened 2D array.

## 2. What is the interviewer asking?
- Determine if a value `target` exists in an `m x n` integer matrix `matrix`.
- The matrix is sorted such that:
  - Each row is sorted in non-decreasing order.
  - The first integer of each row is greater than the last integer of the previous row.

## 3. Intuition
The two properties of the matrix mean that if we lay down all rows side-by-side, we get a single, fully sorted 1D array of size $M \times N$.
Instead of copying the elements to a 1D array (which would consume $O(M \times N)$ space), we can map a virtual 1D index `mid` to its corresponding 2D coordinates:
- `row = mid / cols`
- `col = mid % cols`
Where `cols` is the number of columns in the matrix. This allows us to perform a standard binary search directly on the matrix.

## 4. Brute Force
### Approach:
Search every cell in the 2D matrix using nested loops.

### Time:
$O(M \times N)$

### Space:
$O(1)$

## 5. Optimized Approach
1. Store matrix dimensions: `rows = matrix.size()`, `cols = matrix[0].size()`.
2. Initialize binary search pointers: `left = 0`, `right = rows * cols - 1`.
3. Loop while `left <= right`:
   - Compute `mid = left + (right - left) / 2`.
   - Retrieve the matrix element: `midElement = matrix[mid / cols][mid % cols]`.
   - If `midElement == target`, return `true`.
   - If `midElement < target`, set `left = mid + 1`.
   - Else, set `right = mid - 1`.
4. If loop terminates, return `false`.

## 6. Why does it work?
Because the first element of row $i+1$ is strictly greater than the last element of row $i$, the matrix represents a monotonically increasing sequence. The formula `mid / cols` computes the zero-indexed row index, and `mid % cols` computes the zero-indexed column index of the virtual 1D index `mid`. This bijective mapping ensures we can correctly locate elements without breaking the sorted ordering prerequisite for binary search.

## 7. C++ Code
```cpp
bool searchMatrix(vector<vector<int>>& matrix, int target) {
    int rows = matrix.size(), cols = matrix[0].size();
    int left = 0, right = rows * cols - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        int midElement = matrix[mid / cols][mid % cols];
        if (midElement == target) return true;
        if (midElement < target) left = mid + 1;
        else right = mid - 1;
    }
    return false;
}
```

## 8. Dry Run
Input: `matrix = [[1, 3, 5, 7], [10, 11, 16, 20], [23, 30, 34, 60]]`, `target = 3`

- **Dimensions**: `rows = 3`, `cols = 4`. Virtual 1D range: `0` to `11`.
- **Initial State**: `left = 0`, `right = 11`.

- **Step 1**:
  - `mid = 5`.
  - Row: `5 / 4 = 1`, Col: `5 % 4 = 1`.
  - `midElement = matrix[1][1] = 11`.
  - Since `11 > 3`, update `right = mid - 1 = 4`.

- **Step 2**:
  - `left = 0`, `right = 4`.
  - `mid = 2`.
  - Row: `2 / 4 = 0`, Col: `2 % 4 = 2`.
  - `midElement = matrix[0][2] = 5`.
  - Since `5 > 3`, update `right = mid - 1 = 1`.

- **Step 3**:
  - `left = 0`, `right = 1`.
  - `mid = 0`.
  - Row: `0 / 4 = 0`, Col: `0 % 4 = 0`.
  - `midElement = matrix[0][0] = 1`.
  - Since `1 < 3`, update `left = mid + 1 = 1`.

- **Step 4**:
  - `left = 1`, `right = 1`.
  - `mid = 1`.
  - Row: `1 / 4 = 0`, Col: `1 % 4 = 1`.
  - `midElement = matrix[0][1] = 3`.
  - Since `3 == 3`, target found! Return `true`.

## 9. Complexity
- **Time**: $O(\log(M \times N))$ since binary search is performed on $M \times N$ elements.
- **Space**: $O(1)$ auxiliary space.

## 10. Edge Cases
- **Single-cell matrix**: `matrix = [[5]]`, target `5` or `3`.
- **Single row / Single column matrix**.
- **Target is smaller than minimum element** or **larger than maximum element**.

## 11. Follow-up Questions
- What if the rows are sorted and columns are sorted, but the first element of a row is not greater than the last element of the previous row? (Answer: This is LeetCode 240: Search a 2D Matrix II, solved in $O(M + N)$ time by starting at the top-right corner).
- Can `rows * cols` overflow standard integer limits? (Answer: Yes, if $M$ and $N$ are up to $10^5$. In that case, we can perform a binary search on the first column to find the appropriate row first, then perform binary search within that row, resulting in $O(\log M + \log N)$ which avoids multiplication overflow).

## 12. Interview Explanation
"First, I would clarify the constraints of the problem... Then I'd explain the brute force approach where we search every row and column, taking $O(M \times N)$ time. To optimize this, I would leverage the sorting properties. Since the rows are sequentially ordered, we can treat the matrix as a flattened 1D sorted array. I can map a virtual 1D index `mid` to 2D row and column indices using integer division and modulo operations. My C++ code would perform standard binary search on this virtual 1D space. This achieves $O(\log(M \times N))$ time and uses $O(1)$ space."

---

# 4. Search in Rotated Sorted Array (LeetCode 33)

## 1. Pattern
Modified Binary Search.

## 2. What is the interviewer asking?
- Given an integer array `nums` sorted in ascending order with distinct values.
- The array is rotated at an unknown pivot index (e.g., `[4,5,6,7,0,1,2]`).
- Find the index of a given `target` value, or return `-1` if it is not present.
- The solution must run in $O(\log n)$ time.

## 3. Intuition
In a rotated sorted array, if we divide the array in half, at least one of the two halves will always be strictly sorted.
- If `nums[left] <= nums[mid]`, the left half is sorted.
  - If the target lies within the boundaries of the left half (`nums[left] <= target < nums[mid]`), we search the left half.
  - Otherwise, we search the right half.
- If `nums[left] > nums[mid]`, then the right half must be sorted.
  - If the target lies within the boundaries of the right half (`nums[mid] < target <= nums[right]`), we search the right half.
  - Otherwise, we search the left half.

## 4. Brute Force
### Approach:
Scan the entire array linearly to find the target.

### Time:
$O(N)$

### Space:
$O(1)$

## 5. Optimized Approach
1. Initialize `left = 0`, `right = nums.size() - 1`.
2. Loop while `left <= right`:
   - Compute `mid = left + (right - left) / 2`.
   - If `nums[mid] == target`, return `mid`.
   - Determine if the left side is sorted: `nums[left] <= nums[mid]`.
     - If sorted, check if target lies within the left boundaries: `nums[left] <= target && target < nums[mid]`.
       - If yes, set `right = mid - 1`.
       - Else, set `left = mid + 1`.
     - If left side is not sorted, right side must be sorted. Check if target lies within the right boundaries: `nums[mid] < target && target <= nums[right]`.
       - If yes, set `left = mid + 1`.
       - Else, set `right = mid - 1`.
3. If loop ends, return `-1`.

## 6. Why does it work?
Since the array is rotated at a single pivot point, splitting it at any index `mid` splits the rotation itself. Thus, one side of `mid` will remain unaffected by the pivot and be normally sorted. We can safely determine if target falls into this sorted half because its boundaries (`nums[left]` and `nums[mid]` or `nums[mid]` and `nums[right]`) are well-defined. If it does not, we discard the sorted half and continue in the unsorted half.

## 7. C++ Code
```cpp
int search(vector<int>& nums, int target) {
    int left = 0, right = nums.size() - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) return mid;
        if (nums[left] <= nums[mid]) { // Left side is sorted
            if (nums[left] <= target && target < nums[mid]) right = mid - 1;
            else left = mid + 1;
        } else { // Right side is sorted
            if (nums[mid] < target && target <= nums[right]) left = mid + 1;
            else right = mid - 1;
        }
    }
    return -1;
}
```

## 8. Dry Run
Input: `nums = [4, 5, 6, 7, 0, 1, 2]`, `target = 0`

- **Initial State**: `left = 0`, `right = 6`.

- **Step 1**:
  - `mid = 3`, `nums[mid] = 7`.
  - Compare `nums[left] = 4` with `nums[mid] = 7`. Since `4 <= 7`, the left side is sorted.
  - Is target `0` in `[4, 7)`? `4 <= 0 && 0 < 7` is false.
  - Update `left = mid + 1 = 4`.

- **Step 2**:
  - `left = 4`, `right = 6`.
  - `mid = 5`, `nums[mid] = 1`.
  - Compare `nums[left] = nums[4] = 0` with `nums[mid] = 1`. Since `0 <= 1`, the left side is sorted.
  - Is target `0` in `[0, 1)`? `0 <= 0 && 0 < 1` is true.
  - Update `right = mid - 1 = 4`.

- **Step 3**:
  - `left = 4`, `right = 4`.
  - `mid = 4`, `nums[mid] = 0`.
  - `nums[mid] == target` (0 == 0). Return `4`.

## 9. Complexity
- **Time**: $O(\log N)$ since the search space is divided in half at each iteration.
- **Space**: $O(1)$ auxiliary space.

## 10. Edge Cases
- **Array size is 1 or 2**.
- **Array is not rotated** (already fully sorted: e.g., `[1, 2, 3]`).
- **Target is at the rotation point** (e.g., minimum or maximum element).

## 11. Follow-up Questions
- What if the array contains duplicates? (Answer: That is LeetCode 81. In the worst case, time complexity degrades to $O(N)$).
- How would you find the pivot index (minimum element) using binary search? (Answer: Find minimum element by checking if `nums[mid] > nums[right]`, which indicates the pivot is to the right).

## 12. Interview Explanation
"First, I would clarify the constraints of the problem... Then I'd explain the brute force approach where we perform a linear search in $O(N)$ time. To optimize this, I would use a modified binary search. In any rotated sorted array, splitting the array in half guarantees that at least one half is sorted. We can identify the sorted half by comparing the left element with the middle element. If the target lies within the boundaries of this sorted half, we narrow our search to this half; otherwise, we search the other half. My C++ code implements this division logic, ensuring we maintain $O(\log N)$ time complexity and $O(1)$ space complexity."

---

# 5. Search in Rotated Sorted Array II (LeetCode 81)

## 1. Pattern
Modified Binary Search (handling duplicates).

## 2. What is the interviewer asking?
- This is the same as "Search in Rotated Sorted Array", but the array `nums` may contain duplicate elements.
- We need to return `true` if `target` is in `nums`, and `false` otherwise.
- How does this affect the time complexity and what is the solution?

## 3. Intuition
When duplicates are present, we can encounter a case like `nums = [1, 0, 1, 1, 1]`, `target = 0`. Here, `nums[left] == nums[mid] == nums[right]`.
In this situation, we cannot determine whether the left half or the right half is sorted because both boundaries match the middle.
To handle this ambiguity:
- When `nums[left] == nums[mid] && nums[mid] == nums[right]`, we simply decrement `right` and increment `left` to shrink the search space.
- Otherwise, we proceed exactly like LeetCode 33 by checking which half is sorted.

## 4. Brute Force
### Approach:
Scan the entire array linearly to see if any element matches the target.

### Time:
$O(N)$

### Space:
$O(1)$

## 5. Optimized Approach
1. Initialize `left = 0`, `right = nums.size() - 1`.
2. Loop while `left <= right`:
   - Compute `mid = left + (right - left) / 2`.
   - If `nums[mid] == target`, return `true`.
   - Check if boundaries are identical to mid: `nums[left] == nums[mid] && nums[mid] == nums[right]`.
     - If so, execute `left++` and `right--` to eliminate duplicates.
   - Else if the left side is sorted: `nums[left] <= nums[mid]`.
     - Check if target is within the left boundaries: `nums[left] <= target && target < nums[mid]`.
       - If yes, set `right = mid - 1`.
       - Else, set `left = mid + 1`.
   - Else the right side is sorted:
     - Check if target is within the right boundaries: `nums[mid] < target && target <= nums[right]`.
       - If yes, set `left = mid + 1`.
       - Else, set `right = mid - 1`.
3. Return `false`.

## 6. Why does it work?
Shrinking the window by `left++` and `right--` when all three values are equal is safe because `nums[mid]` is not equal to `target` (we already checked `nums[mid] == target`). Therefore, removing these boundary elements does not eliminate the target if it exists elsewhere in the array. Eventually, the duplicate values at the boundaries are eliminated, allowing the binary search to identify which half is sorted.

## 7. C++ Code
```cpp
bool search(vector<int>& nums, int target) {
    int left = 0, right = nums.size() - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) return true;
        if (nums[left] == nums[mid] && nums[mid] == nums[right]) {
            left++; right--;
        } else if (nums[left] <= nums[mid]) {
            if (nums[left] <= target && target < nums[mid]) right = mid - 1;
            else left = mid + 1;
        } else {
            if (nums[mid] < target && target <= nums[right]) left = mid + 1;
            else right = mid - 1;
        }
    }
    return false;
}
```

## 8. Dry Run
Input: `nums = [1, 0, 1, 1, 1]`, `target = 0`

- **Initial State**: `left = 0`, `right = 4`.

- **Step 1**:
  - `mid = 2`, `nums[mid] = 1`.
  - Compare boundaries: `nums[left] = 1`, `nums[mid] = 1`, `nums[right] = 1`.
  - Since `nums[left] == nums[mid] && nums[mid] == nums[right]`, increment `left` to `1` and decrement `right` to `3`.

- **Step 2**:
  - `left = 1`, `right = 3`. `nums = [1, 0, 1, 1, 1]`.
  - `mid = 2`, `nums[mid] = 1`.
  - Compare boundaries: `nums[left] = nums[1] = 0`, `nums[mid] = 1`, `nums[right] = nums[3] = 1`.
  - Since they are not all equal, check sorted half: `nums[left] <= nums[mid]` (0 <= 1) is true. Left side is sorted.
  - Check if target `0` is in `[0, 1)`? `0 <= 0 && 0 < 1` is true.
  - Update `right = mid - 1 = 1`.

- **Step 3**:
  - `left = 1`, `right = 1`.
  - `mid = 1`, `nums[mid] = 0`.
  - Since `nums[mid] == target` (0 == 0), return `true`.

## 9. Complexity
- **Time**: $O(\log N)$ on average. In the worst case where all elements are duplicates and we have to shrink both sides index by index (e.g., `nums = [1, 1, 1, 1, 1]`, target = 0), the time complexity becomes $O(N)$.
- **Space**: $O(1)$ auxiliary space.

## 10. Edge Cases
- **All elements are identical and equal to target** or **not equal to target**.
- **Single element array**.
- **Pivot occurs at duplicate values**.

## 11. Follow-up Questions
- Does duplicate values degrade binary search to linear search in all cases? (Answer: No, only when the target is not found or when we have long sequences of duplicate values at the boundaries of the search range).
- Why cannot we do this in $O(\log N)$ worst case? (Answer: Information theory. Without scanning the duplicate elements, there is no way to know whether the pivot is in the left half or the right half).

## 12. Interview Explanation
"First, I would clarify the constraints of the problem... Then I'd explain the brute force approach where we perform a linear search in $O(N)$ time. To optimize this, we can adapt the binary search logic for rotated sorted arrays. The core challenge is when the left, middle, and right elements are equal. In this scenario, we cannot identify the sorted half. To resolve this, we shrink our boundaries by incrementing `left` and decrementing `right` to skip duplicates. In other cases, we proceed as normal. My C++ code handles this check before executing the standard partition check. This achieves $O(\log N)$ average-case complexity, degrading to $O(N)$ in the worst-case, and uses $O(1)$ space."

---

# 6. Find Peak Element (LeetCode 162)

## 1. Pattern
Binary Search on Answer / Divide and Conquer.

## 2. What is the interviewer asking?
- A peak element is an element that is strictly greater than its neighbors.
- Given an integer array `nums`, find a peak element, and return its index.
- If the array contains multiple peaks, return the index to any of the peaks.
- You may imagine that `nums[-1] = nums[n] = -∞`.
- The algorithm must run in $O(\log n)$ time.

## 3. Intuition
Since the boundary elements are effectively bounded by $-∞$, any ascending sequence must eventually either go down (creating a peak) or continue to the boundary (where the last element is the peak).
This means:
- If `nums[mid] < nums[mid + 1]`, we are on an ascending slope, meaning a peak must exist to the right of `mid`.
- If `nums[mid] > nums[mid + 1]`, we are on a descending slope, meaning a peak must exist at `mid` or to the left of `mid`.
By checking the slope at `mid`, we can confidently discard half of the array.

## 4. Brute Force
### Approach:
Traverse the array from index `0` to `n - 1`. If `nums[i] > nums[i - 1]` and `nums[i] > nums[i + 1]` (handling edge bounds as $-∞$), return `i`.

### Time:
$O(N)$

### Space:
$O(1)$

## 5. Optimized Approach
1. Initialize `left = 0`, `right = nums.size() - 1`.
2. Loop while `left < right`:
   - Compute `mid = left + (right - left) / 2`.
   - Compare `nums[mid]` with `nums[mid + 1]`.
   - If `nums[mid] > nums[mid + 1]`, we are on a downward slope. Set `right = mid`.
   - Else, we are on an upward slope. Set `left = mid + 1`.
3. When the loop terminates, `left` will equal `right`, pointing to a peak. Return `left`.

## 6. Why does it work?
This works because of the boundary condition `nums[-1] = nums[n] = -∞`. If `nums[mid] < nums[mid + 1]`, we know that the element at `mid + 1` is larger than the element at `mid`. Since the sequence must eventually drop before reaching the virtual $-∞$ boundary at index $N$, a peak is guaranteed to exist somewhere in the range `[mid + 1, right]`. Conversely, if `nums[mid] > nums[mid + 1]`, a peak must exist in the range `[left, mid]`. Shrinking the range this way guarantees convergence to a peak.

## 7. C++ Code
```cpp
int findPeakElement(vector<int>& nums) {
    int left = 0, right = nums.size() - 1;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] > nums[mid + 1]) right = mid;
        else left = mid + 1;
    }
    return left;
}
```

## 8. Dry Run
Input: `nums = [1, 2, 1, 3, 5, 6, 4]`

- **Initial State**: `left = 0`, `right = 6`.

- **Step 1**:
  - `mid = 3`, `nums[mid] = 3`.
  - Compare `nums[mid]` (3) with `nums[mid + 1]` (5).
  - Since `3 < 5` (upward slope), set `left = mid + 1 = 4`.

- **Step 2**:
  - `left = 4`, `right = 6`.
  - `mid = 5`, `nums[mid] = 6`.
  - Compare `nums[mid]` (6) with `nums[mid + 1]` (4).
  - Since `6 > 4` (downward slope), set `right = mid = 5`.

- **Step 3**:
  - `left = 4`, `right = 5`.
  - `mid = 4`, `nums[mid] = 5`.
  - Compare `nums[mid]` (5) with `nums[mid + 1]` (6).
  - Since `5 < 6` (upward slope), set `left = mid + 1 = 5`.

- Loop terminates because `left == right` (5 == 5). Return `5` (value is 6).

## 9. Complexity
- **Time**: $O(\log N)$ as we halve the search space at each iteration.
- **Space**: $O(1)$ auxiliary space.

## 10. Edge Cases
- **Single Element**: `nums = [1]`. The loop `left < right` does not execute, returns `0`.
- **Strictly Increasing Array**: `nums = [1, 2, 3, 4]`. The peak is at the last element (index 3).
- **Strictly Decreasing Array**: `nums = [4, 3, 2, 1]`. The peak is at the first element (index 0).

## 11. Follow-up Questions
- Can we use this binary search if the array contains duplicates and adjacent elements can be equal? (Answer: No. If adjacent elements can be equal, e.g., `[1, 2, 2, 2, 1]`, a local check at `mid` cannot determine which direction to go, and the worst-case time complexity becomes $O(N)$).
- How does 2D peak finding work? (Answer: We select the middle column, find its maximum element (which is a peak in that column), and compare it with its left and right column neighbors to decide which half of columns to search, taking $O(M \log N)$ time).

## 12. Interview Explanation
"First, I would clarify the constraints of the problem, such as whether adjacent elements can be equal. Then I'd explain the brute force approach where we iterate through the array and search for an element greater than its neighbors, taking $O(N)$ time. To optimize this, I would use binary search based on slopes. Since the array boundaries are virtual negative infinity, if we are on an ascending slope (`nums[mid] < nums[mid + 1]`), there must be a peak on the right. If we are on a descending slope (`nums[mid] > nums[mid + 1]`), there must be a peak on the left or at `mid`. My C++ code implements this by updating `left` or `right` based on the slope. This achieves $O(\log N)$ time and $O(1)$ space."
