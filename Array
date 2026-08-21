# Amazon Interview Revision - Section 1: Arrays

This document contains a comprehensive review of fundamental array problems commonly asked in Amazon technical interviews. Each problem is broken down into its key components, pattern, intuition, complexity analysis, and a structured explanation to help you ace your interviews.

---

# 1. Two Sum (LeetCode 1)

## 1. Pattern
**Hash Map (Single Pass)**

## 2. What is the interviewer asking?
- Given an array of integers `nums` and an integer `target`, return the indices of the two numbers such that they add up to `target`.
- You may assume that each input has **exactly one solution**, and you may not use the same element twice.
- The return order of the indices does not matter.

## 3. Intuition
Instead of checking every possible pair of numbers (which is slow), we can look for the "complement" value for each element. For a number `x`, its complement is `target - x`. If we store the numbers we have already seen in a hash map along with their index, we can check if the complement of the current element exists in the hash map in $O(1)$ average time.

## 4. Brute Force
### Approach:
1. Use nested loops to generate all possible pairs.
2. The outer loop selects index `i` from `0` to `n-1`.
3. The inner loop selects index `j` from `i+1` to `n-1`.
4. If `nums[i] + nums[j] == target`, return `{i, j}`.

### Complexity:
- **Time:** $O(N^2)$ where $N$ is the number of elements in `nums`.
- **Space:** $O(1)$ auxiliary space.

## 5. Optimized Approach
### Approach:
1. Initialize an empty hash map `map` where the key is the number and the value is its index.
2. Iterate through the array `nums` using index `i`.
3. For each element `nums[i]`, calculate its complement: `target - nums[i]`.
4. Search for this complement in the hash map.
5. If the complement is found, return the indices `{map[complement], i}`.
6. If not found, insert the current element and its index into the hash map: `map[nums[i]] = i`.

## 6. Why does it work?
By checking the hash map *before* inserting the current element, we ensure that:
- We don't pair an element with itself (since it isn't in the map yet).
- We look backward at all previously visited elements. Since there is exactly one solution, the second element of the pair will always find its complement in the map.

## 7. C++ Code
```cpp
#include <vector>
#include <unordered_map>

using namespace std;

vector<int> twoSum(vector<int>& nums, int target) {
    unordered_map<int, int> map; // Value to index map
    for (int i = 0; i < nums.size(); ++i) {
        int complement = target - nums[i];
        // Check if complement already exists in our visited elements map
        if (map.find(complement) != map.end()) {
            return {map[complement], i};
        }
        // Store current element and its index
        map[nums[i]] = i;
    }
    return {};
}
```

## 8. Dry Run
**Input:** `nums = [2, 7, 11, 15]`, `target = 9`

- **Initialization:** `map = {}`
- **Step 1:** `i = 0`, `nums[0] = 2`.
  - `complement = 9 - 2 = 7`.
  - Is `7` in `map`? No.
  - Insert `2` into `map`: `map = {2: 0}`.
- **Step 2:** `i = 1`, `nums[1] = 7`.
  - `complement = 9 - 7 = 2`.
  - Is `2` in `map`? Yes, `map[2]` is `0`.
  - Return `{0, 1}`.

## 9. Complexity
- **Time:** $O(N)$ because we traverse the list containing $N$ elements only once. Map lookups/insertions take $O(1)$ time on average.
- **Space:** $O(N)$ because in the worst case, we might store $N-1$ elements in the hash map before finding the match.

## 10. Edge Cases
- **Duplicate elements that sum to target:** (e.g., `nums = [3, 3]`, `target = 6`). When we reach the second `3` at index 1, the first `3` is already in the map at index 0. The map lookup succeeds and returns `{0, 1}`.
- **Negative numbers:** (e.g., `nums = [-3, 4, 3, 90]`, `target = 0`). The complement is calculated correctly as `0 - (-3) = 3`, and the logic holds.
- **Minimum size array:** Array containing exactly two elements. Handled correctly on the second iteration.

## 11. Follow-up Questions
- **What if the input array is already sorted?**
  - We can solve it in $O(N)$ time and $O(1)$ space using the Two Pointers technique (one pointer at the start, one at the end, moving them inward based on the current sum relative to the target).
- **Can we have multiple pairs? How would you return all unique pairs?**
  - Sort the array first, then use the Two Pointers approach. To avoid duplicates, skip elements that are identical to their predecessors.

## 12. Interview Explanation
"First, I would clarify the constraints of the problem, such as the size of the array and if there can be multiple solutions. Then I'd explain the brute force approach where we check all pairs using nested loops, which takes $O(N^2)$ time. To optimize this, I would use a hash map because it allows us to check for the presence of the complement of each element in $O(1)$ average time. My C++ code would iterate through the array once, calculating the complement of the current element. If it's already in our map, we return the stored index and the current index. Otherwise, we store the current element with its index. This achieves $O(N)$ time complexity and $O(N)$ space complexity."

---

# 2. Best Time to Buy and Sell Stock (LeetCode 121)

## 1. Pattern
**Greedy / Single Pass**

## 2. What is the interviewer asking?
- You are given an array `prices` where `prices[i]` is the price of a given stock on the $i$-th day.
- You want to maximize your profit by choosing a **single day** to buy one stock and choosing a **different day in the future** to sell that stock.
- Return the maximum profit you can achieve. If you cannot achieve any profit, return `0`.

## 3. Intuition
To maximize profit, we want to buy at the lowest possible price and sell at the highest possible price in the future. As we scan the array from left to right, we can greedily track the lowest price we have seen so far (`minPrice`). For each day, we calculate the potential profit if we were to sell on that day (`price - minPrice`). We update our maximum profit if the potential profit exceeds the maximum profit seen so far.

## 4. Brute Force
### Approach:
1. Use nested loops to calculate the profit for every possible pair of buying and selling days.
2. The outer loop selects the buy day `i` from `0` to `n-2`.
3. The inner loop selects the sell day `j` from `i+1` to `n-1`.
4. Calculate `profit = prices[j] - prices[i]`.
5. Keep track of the maximum profit.

### Complexity:
- **Time:** $O(N^2)$
- **Space:** $O(1)$

## 5. Optimized Approach
### Approach:
1. Initialize `minPrice` to `INT_MAX` to track the minimum price seen so far.
2. Initialize `maxProfit` to `0` to track the maximum profit obtained.
3. Traverse the `prices` array. For each price:
   - Update `minPrice = min(minPrice, price)`.
   - Calculate potential profit: `price - minPrice`.
   - Update `maxProfit = max(maxProfit, price - minPrice)`.
4. Return `maxProfit`.

## 6. Why does it work?
We must buy before we can sell. By tracking the minimum price seen *prior to or on* the current day, we ensure that when we evaluate selling at `prices[i]`, we are pairing it with the absolute best (lowest) buying price available in the past. This guarantees we check the maximum possible profit for every potential selling day.

## 7. C++ Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>

using namespace std;

int maxProfit(vector<int>& prices) {
    int minPrice = INT_MAX, maxProfit = 0;
    for (int price : prices) {
        // Track the lowest buying price seen so far
        minPrice = min(minPrice, price);
        // Track the maximum profit we can get by selling at the current price
        maxProfit = max(maxProfit, price - minPrice);
    }
    return maxProfit;
}
```

## 8. Dry Run
**Input:** `prices = [7, 1, 5, 3, 6, 4]`

- **Initialization:** `minPrice = INT_MAX`, `maxProfit = 0`
- **Step 1:** `price = 7`
  - `minPrice = min(INT_MAX, 7) = 7`
  - `maxProfit = max(0, 7 - 7) = 0`
- **Step 2:** `price = 1`
  - `minPrice = min(7, 1) = 1`
  - `maxProfit = max(0, 1 - 1) = 0`
- **Step 3:** `price = 5`
  - `minPrice = min(1, 5) = 1`
  - `maxProfit = max(0, 5 - 1) = 4`
- **Step 4:** `price = 3`
  - `minPrice = min(1, 3) = 1`
  - `maxProfit = max(4, 3 - 1) = 4`
- **Step 5:** `price = 6`
  - `minPrice = min(1, 6) = 1`
  - `maxProfit = max(4, 6 - 1) = 5`
- **Step 6:** `price = 4`
  - `minPrice = min(1, 4) = 1`
  - `maxProfit = max(5, 4 - 1) = 5`
- **Result:** `5` (Buy at 1, sell at 6).

## 9. Complexity
- **Time:** $O(N)$ because we scan the array of size $N$ exactly once.
- **Space:** $O(1)$ because we only use two scalar variables.

## 10. Edge Cases
- **Strictly decreasing prices:** (e.g., `prices = [7, 6, 4, 3, 1]`). `minPrice` keeps updating, but `price - minPrice` is always 0. The program returns `0` correctly.
- **Flat prices:** (e.g., `[3, 3, 3, 3]`). Maximum profit remains `0`.
- **Single day:** If the array contains only one price, the loop runs once, and `maxProfit` remains `0`.

## 11. Follow-up Questions
- **What if you can buy and sell multiple times? (Stock II)**
  - We can sum up all positive daily price differences (i.e., whenever `prices[i] > prices[i-1]`, add the difference to total profit).
- **What if you can make at most two transactions? (Stock III)**
  - We use dynamic programming, maintaining four states: first buy, first sell, second buy, second sell.

## 12. Interview Explanation
"First, I would clarify if we can complete multiple transactions or just one. Since we can only do one transaction, I'd explain the brute force approach where we check every buy/sell combination, taking $O(N^2)$ time. To optimize this to $O(N)$, I would use a single-pass greedy approach. As we traverse the array, we keep track of the minimum stock price seen so far. At each step, we calculate the profit if we were to sell today (which is the current price minus the minimum price seen so far) and update our maximum profit. My C++ code initializes `minPrice` to `INT_MAX` and `maxProfit` to `0`, iterating through the prices to update these values. This runs in $O(N)$ time and $O(1)$ auxiliary space."

---

# 3. Merge Sorted Array (LeetCode 88)

## 1. Pattern
**Two Pointers (From End)**

## 2. What is the interviewer asking?
- You are given two sorted integer arrays `nums1` and `nums2` of size `m + n` and `n` respectively.
- `nums1` contains `m` elements to merge, and the remaining `n` spaces are initialized to `0`.
- Merge `nums2` into `nums1` **in-place** so that `nums1` becomes a single sorted array.

## 3. Intuition
If we try to merge the arrays from the beginning, we will have to shift elements in `nums1` to make room for smaller elements from `nums2`. Shifting takes $O(M)$ time for each insertion, making it inefficient.
However, the end of `nums1` has `n` empty spaces. If we compare the largest elements of both arrays (which are at the ends since they are sorted) and place them at the end of `nums1` first, we can merge the arrays in-place in $O(M+N)$ time without shifting elements or using extra memory.

## 4. Brute Force
### Approach:
1. Copy the elements of `nums2` into the last `n` positions of `nums1`.
2. Sort the entire `nums1` array of size `m + n`.

### Complexity:
- **Time:** $O((M + N) \log(M + N))$ due to the sorting step.
- **Space:** $O(1)$ (or $O(M+N)$ depending on the sorting algorithm).

## 5. Optimized Approach
### Approach:
1. Initialize three pointers:
   - `i = m - 1` (points to the last active element in `nums1`).
   - `j = n - 1` (points to the last element in `nums2`).
   - `k = m + n - 1` (points to the last available position in `nums1`).
2. While both `i >= 0` and `j >= 0`:
   - Compare `nums1[i]` and `nums2[j]`.
   - Place the larger element at `nums1[k]`.
   - Decrement the pointer from which the element was taken (`i` or `j`), and decrement the write pointer `k`.
3. If there are leftover elements in `nums2` (i.e., `j >= 0`), copy them to the remaining positions in `nums1` (from index `j` down to `0`).
4. If there are leftover elements in `nums1` (i.e., `i >= 0`), they are already in their correct places.

## 6. Why does it work?
Since `nums1` has `n` extra spaces at the end, the write pointer `k` starts at `m + n - 1`. Because `k = i + j + 1` and `j >= 0`, `k` is always strictly greater than `i`. This mathematical property guarantees we will never overwrite an unexamined element of `nums1`.

## 7. C++ Code
```cpp
#include <vector>

using namespace std;

void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
    int i = m - 1;     // Pointer for last active element in nums1
    int j = n - 1;     // Pointer for last element in nums2
    int k = m + n - 1; // Pointer for write position in nums1
    
    // Compare from the end and place the larger element at the write position
    while (i >= 0 && j >= 0) {
        nums1[k--] = (nums1[i] > nums2[j]) ? nums1[i--] : nums2[j--];
    }
    
    // If nums2 has leftover elements, copy them. 
    // Leftover elements in nums1 are already in their correct positions.
    while (j >= 0) {
        nums1[k--] = nums2[j--];
    }
}
```

## 8. Dry Run
**Input:** `nums1 = [1, 2, 3, 0, 0, 0]`, `m = 3`, `nums2 = [2, 5, 6]`, `n = 3`

- **Initialization:** `i = 2`, `j = 2`, `k = 5`
- **Step 1:**
  - Compare `nums1[2] (3)` and `nums2[2] (6)`.
  - Since `6 > 3`, `nums1[5] = nums2[2] (6)`.
  - Update: `j` becomes `1`, `k` becomes `4`.
  - `nums1 = [1, 2, 3, 0, 0, 6]`
- **Step 2:**
  - Compare `nums1[2] (3)` and `nums2[1] (5)`.
  - Since `5 > 3`, `nums1[4] = nums2[1] (5)`.
  - Update: `j` becomes `0`, `k` becomes `3`.
  - `nums1 = [1, 2, 3, 0, 5, 6]`
- **Step 3:**
  - Compare `nums1[2] (3)` and `nums2[0] (2)`.
  - Since `3 > 2`, `nums1[3] = nums1[2] (3)`.
  - Update: `i` becomes `1`, `k` becomes `2`.
  - `nums1 = [1, 2, 3, 3, 5, 6]`
- **Step 4:**
  - Compare `nums1[1] (2)` and `nums2[0] (2)`.
  - Since they are equal, the code takes from `nums2`: `nums1[2] = nums2[0] (2)`.
  - Update: `j` becomes `-1`, `k` becomes `1`.
  - `nums1 = [1, 2, 2, 3, 5, 6]`
- **End:** Loop terminates as `j` is `-1`. Remaining `nums2` copying does nothing. Output: `[1, 2, 2, 3, 5, 6]`.

## 9. Complexity
- **Time:** $O(M + N)$ as we do a single pass over the elements of both arrays.
- **Space:** $O(1)$ as the merge is done in-place inside `nums1`.

## 10. Edge Cases
- **`m = 0` (empty `nums1` elements):** (e.g., `nums1 = [0]`, `m = 0`, `nums2 = [1]`, `n = 1`). The first loop is skipped. The second loop copies `nums2[0]` into `nums1[0]`. Correctly yields `[1]`.
- **`n = 0` (empty `nums2` elements):** (e.g., `nums1 = [1]`, `m = 1`, `nums2 = []`, `n = 0`). Both loops are skipped. Correctly yields `[1]`.
- **All elements in `nums2` smaller than `nums1`:** (e.g., `nums1 = [4, 5, 6, 0, 0, 0]`, `nums2 = [1, 2, 3]`). The first loop copies all of `nums1` to the back, then the second loop copies all of `nums2` to the front.

## 11. Follow-up Questions
- **How would you merge $K$ sorted arrays?**
  - We can use a Min-Heap (Priority Queue) containing the first element of each array. We extract the minimum, insert it into the output, and add the next element of that array to the heap. This takes $O(Total\_Elements \log K)$ time.

## 12. Interview Explanation
"First, I would clarify if `nums1` has enough memory allocated to store the merged result, which it does. Then I'd explain the brute force approach where we append `nums2` to `nums1` and sort, taking $O((M+N) \log(M+N))$ time. To optimize this, I would use three pointers to merge the arrays from the end. Since the end of `nums1` has empty spaces, merging backwards prevents overwriting any unvisited elements in `nums1`. My C++ code compares the largest elements from the ends of both arrays and writes them to the back of `nums1`. If `nums2` has any remaining elements after the main loop, we copy them directly. This achieves $O(M+N)$ time complexity and $O(1)$ auxiliary space."

---

# 4. Contains Duplicate (LeetCode 217)

## 1. Pattern
**Hash Set**

## 2. What is the interviewer asking?
- Given an integer array `nums`, return `true` if any value appears **at least twice** in the array.
- Return `false` if every element is distinct.

## 3. Intuition
To find duplicates, we need to keep track of the elements we have seen so far. A hash set is an ideal data structure for this task because it stores unique elements and provides $O(1)$ average time complexity for both insertion and lookup. As we traverse the array, we check if the current element is already in the set. If it is, we have found a duplicate.

## 4. Brute Force
### Approach:
1. Use nested loops to compare every pair of elements.
2. The outer loop selects index `i` from `0` to `n-1`.
3. The inner loop selects index `j` from `i+1` to `n-1`.
4. If `nums[i] == nums[j]`, return `true`.
5. If the loops finish, return `false`.

### Complexity:
- **Time:** $O(N^2)$
- **Space:** $O(1)$

## 5. Optimized Approach
### Approach:
1. Initialize an empty hash set `set`.
2. Iterate through each number `num` in `nums`.
3. Check if `num` is already present in `set` using `set.count(num)`.
4. If it is present, return `true` immediately.
5. If not, insert `num` into `set`.
6. If the loop completes without finding duplicates, return `false`.

## 6. Why does it work?
A hash set uses a hash function to map elements to buckets, allowing $O(1)$ search on average. Thus, we can dynamically build a database of "seen" numbers. If we encounter a number that is already in our set, it means it appeared earlier in the array, making it a duplicate.

## 7. C++ Code
```cpp
#include <vector>
#include <unordered_set>

using namespace std;

bool containsDuplicate(vector<int>& nums) {
    unordered_set<int> set;
    for (int num : nums) {
        // If the number is already in the set, it's a duplicate
        if (set.count(num)) return true;
        // Otherwise, insert it into the set
        set.insert(num);
    }
    return false;
}
```

## 8. Dry Run
**Input:** `nums = [1, 2, 3, 1]`

- **Initialization:** `set = {}`
- **Step 1:** `num = 1`. `set.count(1)` is `0`. Insert `1`. `set = {1}`.
- **Step 2:** `num = 2`. `set.count(2)` is `0`. Insert `2`. `set = {1, 2}`.
- **Step 3:** `num = 3`. `set.count(3)` is `0`. Insert `3`. `set = {1, 2, 3}`.
- **Step 4:** `num = 1`. `set.count(1)` is `1`. Duplicate found! Return `true`.

## 9. Complexity
- **Time:** $O(N)$ on average, because hash set lookups and insertions take $O(1)$ time on average, and we iterate through the array of size $N$ once.
- **Space:** $O(N)$ because, in the worst case (all elements unique), we store all $N$ elements in the set.

## 10. Edge Cases
- **Empty or single-element array:** The loop runs $0$ or $1$ times and returns `false`.
- **All elements identical:** (e.g., `[5, 5, 5]`). Returns `true` on the second element.
- **Negative and positive duplicates:** (e.g., `[-1, 1, -1]`). Handled correctly by hash codes of negative numbers.

## 11. Follow-up Questions
- **How would you solve this if you cannot use extra space?**
  - We can sort the array in-place. After sorting, duplicate elements will be adjacent. We can check if `nums[i] == nums[i-1]` for `i` from `1` to `n-1`. This takes $O(N \log N)$ time and $O(1)$ extra space (or $O(\log N)$ space depending on the sorting algorithm stack).
- **When would you use `std::set` instead of `std::unordered_set`?**
  - We use `std::set` (self-balancing BST) if we need to keep the elements sorted or if we want to avoid the worst-case $O(N)$ lookup time of hash tables (which can happen during heavy hash collisions). `std::set` has $O(\log N)$ guaranteed time complexity for lookups/insertions.

## 12. Interview Explanation
"First, I would clarify if the array is read-only and if there are memory constraints. Then I'd explain the brute force approach where we compare all pairs, taking $O(N^2)$ time. To optimize this, I would use an `unordered_set` to store the elements as we iterate through the array. For each element, we check if it is already in the set in $O(1)$ average time. If it is, we return `true`. If the loop finishes, we return `false`. This achieves $O(N)$ time complexity and $O(N)$ space complexity. If space is constrained, I would suggest sorting the array first and checking adjacent elements, which takes $O(N \log N)$ time and $O(1)$ space."

---

# 5. Product of Array Except Self (LeetCode 238)

## 1. Pattern
**Prefix and Suffix Products**

## 2. What is the interviewer asking?
- Given an integer array `nums`, return an array `answer` such that `answer[i]` is equal to the product of all the elements of `nums` except `nums[i]`.
- **Constraints:** You must run in $O(N)$ time and **cannot use the division operation**.

## 3. Intuition
The product of all elements except `nums[i]` is equivalent to:
$$\text{Product Except Self} = (\text{Product of all elements to the left of } i) \times (\text{Product of all elements to the right of } i)$$
By building prefix products in a forward pass and suffix products in a backward pass, we can calculate the result. To do this in $O(1)$ auxiliary space, we can store the prefix products in the output array, and maintain the suffix product in a single sliding variable as we iterate backward.

## 4. Brute Force
### Approach:
1. For each index `i`, run a nested loop over index `j` from `0` to `n-1`.
2. If `i != j`, multiply `nums[j]` to a running product.
3. Store the result in `answer[i]`.

### Complexity:
- **Time:** $O(N^2)$
- **Space:** $O(1)$ (excluding output array).

## 5. Optimized Approach
### Approach:
1. Initialize the result array `res` of size $N$ with all 1s.
2. Initialize `left = 1`. Traverse from `0` to `n-1`:
   - Multiply `res[i]` by `left`.
   - Update `left *= nums[i]` (this prepares the prefix product for the next element).
3. Initialize `right = 1`. Traverse from `n-1` down to `0`:
   - Multiply `res[i]` by `right`.
   - Update `right *= nums[i]` (this prepares the suffix product for the next element).
4. Return `res`.

## 6. Why does it work?
In the first pass: `res[i]` stores the product of all elements from index `0` to `i-1`.
In the second pass: we multiply `res[i]` by `right`, which is the product of all elements from index `i+1` to `n-1`.
Thus, `res[i]` correctly holds the product of all elements except `nums[i]`.

## 7. C++ Code
```cpp
#include <vector>

using namespace std;

vector<int> productExceptSelf(vector<int>& nums) {
    int n = nums.size();
    vector<int> res(n, 1);
    
    int left = 1, right = 1;
    // Step 1: Compute prefix products and store in res
    for (int i = 0; i < n; ++i) {
        res[i] *= left;
        left *= nums[i];
    }
    // Step 2: Compute suffix products on the fly and multiply with res
    for (int i = n - 1; i >= 0; --i) {
        res[i] *= right;
        right *= nums[i];
    }
    
    return res;
}
```

## 8. Dry Run
**Input:** `nums = [1, 2, 3, 4]`

- **Initialization:** `res = [1, 1, 1, 1]`, `left = 1`, `right = 1`
- **First Pass (Left to Right):**
  - `i = 0`: `res[0] *= left` -> `res[0] = 1`. Update `left *= nums[0]` -> `left = 1 * 1 = 1`.
  - `i = 1`: `res[1] *= left` -> `res[1] = 1`. Update `left *= nums[1]` -> `left = 1 * 2 = 2`.
  - `i = 2`: `res[2] *= left` -> `res[2] = 2`. Update `left *= nums[2]` -> `left = 2 * 3 = 6`.
  - `i = 3`: `res[3] *= left` -> `res[3] = 6`. Update `left *= nums[3]` -> `left = 6 * 4 = 24`.
  - `res` after prefix pass: `[1, 1, 2, 6]`
- **Second Pass (Right to Left):**
  - `i = 3`: `res[3] *= right` -> `res[3] = 6 * 1 = 6`. Update `right *= nums[3]` -> `right = 1 * 4 = 4`.
  - `i = 2`: `res[2] *= right` -> `res[2] = 2 * 4 = 8`. Update `right *= nums[2]` -> `right = 4 * 3 = 12`.
  - `i = 1`: `res[1] *= right` -> `res[1] = 1 * 12 = 12`. Update `right *= nums[1]` -> `right = 12 * 2 = 24`.
  - `i = 0`: `res[0] *= right` -> `res[0] = 1 * 24 = 24`. Update `right *= nums[0]` -> `right = 24 * 1 = 24`.
- **Result:** `[24, 12, 8, 6]`

## 9. Complexity
- **Time:** $O(N)$ because we make exactly two linear passes over the array.
- **Space:** $O(1)$ auxiliary space. The output array `res` does not count towards the space complexity.

## 10. Edge Cases
- **Array containing one zero:** (e.g., `nums = [1, 2, 0, 4]`). The output is `[0, 0, 8, 0]`. Since the product of elements excluding `0` is `8`, and all other positions include `0` in their product, they become `0`. The code handles this naturally.
- **Array containing multiple zeros:** (e.g., `nums = [1, 0, 3, 0]`). The output is `[0, 0, 0, 0]`. Both `left` and `right` variables will become `0` and carry it through.
- **Negative numbers:** Handled correctly since signs follow normal multiplication laws.

## 11. Follow-up Questions
- **Why is division not allowed in the problem?**
  - If division were allowed, we could compute the total product of the array and then divide by `nums[i]`. However, this approach fails if any element is `0` (division by zero error) and requires complex special-case logic.

## 12. Interview Explanation
"First, I would note that division is prohibited, which means we cannot divide a global product by the current element. I'd explain the brute force approach where we calculate the product of all elements except the current one for each index, taking $O(N^2)$ time. To optimize this, I would use prefix and suffix products. The product except `nums[i]` is the product of everything to its left times everything to its right. We can compute the prefix products in one forward pass and store them in our result array. Then, in a backward pass, we maintain the suffix product in a variable and multiply it into our result array. This achieves $O(N)$ time complexity and $O(1)$ auxiliary space."

---

# 6. Maximum Subarray (LeetCode 53)

## 1. Pattern
**Kadane's Algorithm (Dynamic Programming)**

## 2. What is the interviewer asking?
- Given an integer array `nums`, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

## 3. Intuition
Suppose we are at index `i`. The maximum contiguous subarray ending at `i` must either:
1. Extend the maximum subarray ending at `i-1`.
2. Start fresh with the single element `nums[i]`.
If the sum accumulated so far (`currentSum`) becomes negative, it will only decrease the sum of any subsequent subarray. In that case, we should reset `currentSum` to `nums[i]` (start fresh).

## 4. Brute Force
### Approach:
1. Use nested loops to generate all possible subarrays.
2. The outer loop selects the start index `i`.
3. The inner loop selects the end index `j` and adds `nums[j]` to a running sum.
4. Update `maxSum` if the current subarray sum is greater.

### Complexity:
- **Time:** $O(N^2)$
- **Space:** $O(1)$

## 5. Optimized Approach
### Approach:
1. Initialize `currentSum` and `maxSum` to the value of the first element `nums[0]`.
2. Iterate through the array starting from index `1` to `n-1`.
3. For each element `nums[i]`:
   - Update `currentSum = max(nums[i], currentSum + nums[i])`.
   - Update `maxSum = max(maxSum, currentSum)`.
4. Return `maxSum`.

## 6. Why does it work?
If `currentSum` is negative, adding it to the next element `nums[i]` will make the result smaller than `nums[i]` itself. Thus, `max(nums[i], currentSum + nums[i])` will correctly discard the negative history and start a new subarray at `i`. By checking this at every element and storing the peak value in `maxSum`, we find the global maximum contiguous subarray.

## 7. C++ Code
```cpp
#include <vector>
#include <algorithm>

using namespace std;

int maxSubArray(vector<int>& nums) {
    int maxSum = nums[0], currentSum = nums[0];
    for (int i = 1; i < nums.size(); ++i) {
        // Decide whether to extend the existing subarray or start a new one
        currentSum = max(nums[i], currentSum + nums[i]);
        // Update the global maximum subarray sum
        maxSum = max(maxSum, currentSum);
    }
    return maxSum;
}
```

## 8. Dry Run
**Input:** `nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]`

- **Initialization:** `maxSum = -2`, `currentSum = -2`
- **Step 1:** `i = 1`, `nums[1] = 1`
  - `currentSum = max(1, -2 + 1) = 1`
  - `maxSum = max(-2, 1) = 1`
- **Step 2:** `i = 2`, `nums[2] = -3`
  - `currentSum = max(-3, 1 + -3) = -2`
  - `maxSum = max(1, -2) = 1`
- **Step 3:** `i = 3`, `nums[3] = 4`
  - `currentSum = max(4, -2 + 4) = 4` (starts a new subarray)
  - `maxSum = max(1, 4) = 4`
- **Step 4:** `i = 4`, `nums[4] = -1`
  - `currentSum = max(-1, 4 - 1) = 3`
  - `maxSum = max(4, 3) = 4`
- **Step 5:** `i = 5`, `nums[5] = 2`
  - `currentSum = max(2, 3 + 2) = 5`
  - `maxSum = max(4, 5) = 5`
- **Step 6:** `i = 6`, `nums[6] = 1`
  - `currentSum = max(1, 5 + 1) = 6`
  - `maxSum = max(5, 6) = 6` (subarray is `[4, -1, 2, 1]`)
- **Step 7:** `i = 7`, `nums[7] = -5`
  - `currentSum = max(-5, 6 - 5) = 1`
  - `maxSum = max(6, 1) = 6`
- **Step 8:** `i = 8`, `nums[8] = 4`
  - `currentSum = max(4, 1 + 4) = 5`
  - `maxSum = max(6, 5) = 6`
- **Result:** `6`

## 9. Complexity
- **Time:** $O(N)$ since we traverse the array of size $N$ once.
- **Space:** $O(1)$ because we only track two variables.

## 10. Edge Cases
- **All elements are negative:** (e.g., `nums = [-2, -3, -1, -5]`). `currentSum` at `i=2` becomes `max(-1, -3 + -1) = -1`. The code correctly returns `-1` (the maximum single element), which is the correct contiguous subarray.
- **Single element:** (e.g., `[5]`). Loop does not run. Returns `5`.

## 11. Follow-up Questions
- **How would you return the actual subarray index range?**
  - Track a temporary start index `tempStart = 0`. Whenever `currentSum` is reset to `nums[i]`, update `tempStart = i`. Whenever `maxSum` is updated, store `start = tempStart` and `end = i`.
- **Can we solve this using Divide and Conquer?**
  - Yes. Split the array in half. The max subarray is either entirely in the left half, entirely in the right half, or crosses the midpoint. This takes $O(N \log N)$ time.

## 12. Interview Explanation
"First, I would clarify if the subarray must contain at least one element. Then I'd explain the brute force approach where we check every possible subarray, taking $O(N^2)$ time. To optimize this, I would use Kadane's algorithm, which is a dynamic programming approach. We maintain a running sum of the current subarray. At each element, we decide whether to add the current element to our running sum or start a new subarray. If the running sum falls below zero, we discard it and start fresh at the current element. My C++ code implements this in a single loop, tracking both the local running sum and the global maximum. This runs in $O(N)$ time and $O(1)$ auxiliary space."

---

# 7. 3Sum (LeetCode 15)

## 1. Pattern
**Sorting + Two Pointers**

## 2. What is the interviewer asking?
- Given an integer array `nums`, return all unique triplets `[nums[i], nums[j], nums[k]]` such that `i != j`, `i != k`, `j != k`, and `nums[i] + nums[j] + nums[k] == 0`.
- The solution set **must not contain duplicate triplets**.

## 3. Intuition
If we sort the array first, we can fix the first element of the triplet, say `nums[i]`. The problem then simplifies to finding two numbers in the remaining array (`nums[i+1]` to `nums[n-1]`) that sum up to `-nums[i]`. We can find these two numbers efficiently in $O(N)$ time using two pointers (one at the left and one at the right). Sorting also allows us to easily skip duplicate elements to avoid duplicate triplets in the output.

## 4. Brute Force
### Approach:
1. Use three nested loops to check all triplets `(i, j, k)`.
2. Check if `nums[i] + nums[j] + nums[k] == 0`.
3. To prevent duplicate triplets, sort each valid triplet and insert it into a set.

### Complexity:
- **Time:** $O(N^3 \log(\text{unique triplets}))$
- **Space:** $O(N)$ to store unique triplets in the set.

## 5. Optimized Approach
### Approach:
1. Sort the array `nums` in non-decreasing order.
2. Loop `i` from `0` to `n - 3`:
   - If `nums[i] > 0`, break early (since the array is sorted, three positive numbers cannot sum to 0).
   - If `i > 0` and `nums[i] == nums[i-1]`, skip the iteration to avoid duplicate triplets.
   - Initialize two pointers: `left = i + 1` and `right = n - 1`.
   - While `left < right`:
     - Calculate `sum = nums[i] + nums[left] + nums[right]`.
     - If `sum == 0`:
       - Push `{nums[i], nums[left], nums[right]}` to the result.
       - Move pointers: `left++`, `right--`.
       - Skip duplicates for `left` by checking if the new `nums[left] == nums[left - 1]`.
       - Skip duplicates for `right` by checking if the new `nums[right] == nums[right + 1]`.
     - If `sum < 0`, we need a larger value, so `left++`.
     - If `sum > 0`, we need a smaller value, so `right--`.
3. Return the result list.

## 6. Why does it work?
Sorting organizes the numbers, allowing us to use a two-pointer search where we increase the sum by moving `left` rightward, and decrease it by moving `right` leftward. Skipping consecutive identical elements for `i`, `left`, and `right` guarantees that we never process the same value combination twice, eliminating duplicates without the overhead of a set.

## 7. C++ Code
```cpp
#include <vector>
#include <algorithm>

using namespace std;

vector<vector<int>> threeSum(vector<int>& nums) {
    // Check edge case for size less than 3
    if (nums.size() < 3) return {};
    
    sort(nums.begin(), nums.end());
    vector<vector<int>> res;
    
    for (int i = 0; i < (int)nums.size() - 2; ++i) {
        // If the smallest number is greater than 0, no triplet can sum to 0
        if (nums[i] > 0) break;
        // Skip duplicate values for the first element
        if (i > 0 && nums[i] == nums[i - 1]) continue;
        
        int left = i + 1, right = nums.size() - 1;
        while (left < right) {
            int sum = nums[i] + nums[left] + nums[right];
            if (sum == 0) {
                res.push_back({nums[i], nums[left], nums[right]});
                // Shift pointers past duplicates
                while (left < right && nums[left] == nums[left + 1]) ++left;
                while (left < right && nums[right] == nums[right - 1]) --right;
                ++left; --right;
            } else if (sum < 0) {
                ++left; // Need a larger sum
            } else {
                --right; // Need a smaller sum
            }
        }
    }
    return res;
}
```

## 8. Dry Run
**Input:** `nums = [-1, 0, 1, 2, -1, -4]`

- **Step 1: Sort:** `nums = [-4, -1, -1, 0, 1, 2]`
- **Step 2: Loop:**
  - `i = 0`, `nums[0] = -4`:
    - `left = 1` (value `-1`), `right = 5` (value `2`).
    - `sum = -4 + -1 + 2 = -3 < 0` -> `left` becomes `2` (value `-1`).
    - `sum = -4 + -1 + 2 = -3 < 0` -> `left` becomes `3` (value `0`).
    - `sum = -4 + 0 + 2 = -2 < 0` -> `left` becomes `4` (value `1`).
    - `sum = -4 + 1 + 2 = -1 < 0` -> `left` becomes `5`. Loop ends (`left == right`).
  - `i = 1`, `nums[1] = -1`:
    - `left = 2` (value `-1`), `right = 5` (value `2`).
    - `sum = -1 + -1 + 2 = 0`. **Triplet found: `[-1, -1, 2]`**.
    - Skip duplicate `left`: `nums[left] == nums[left+1]`? No (`-1 != 0`).
    - Skip duplicate `right`: `nums[right] == nums[right-1]`? No (`2 != 1`).
    - Pointers update: `left = 3`, `right = 4`.
    - `left = 3` (value `0`), `right = 4` (value `1`).
    - `sum = -1 + 0 + 1 = 0`. **Triplet found: `[-1, 0, 1]`**.
    - Pointers update: `left = 4`, `right = 3`. Loop ends.
  - `i = 2`, `nums[2] = -1`:
    - Since `nums[2] == nums[1]`, skip to avoid duplicate triplets.
  - `i = 3`, `nums[3] = 0`:
    - `left = 4` (value `1`), `right = 5` (value `2`).
    - `sum = 0 + 1 + 2 = 3 > 0` -> `right` becomes `4`. Loop ends.
- **Result:** `[[-1, -1, 2], [-1, 0, 1]]`

## 9. Complexity
- **Time:** $O(N^2)$ because we sort the array in $O(N \log N)$ time, and then run a nested loop where the outer loop runs $N$ times and the inner two-pointer search runs in $O(N)$ time.
- **Space:** $O(\log N)$ to $O(N)$ for sorting stack space, depending on the implementation.

## 10. Edge Cases
- **Less than three elements:** (e.g., `nums = [1, 2]`). Checked and returns `{}` immediately.
- **All elements are identical:** (e.g., `[0, 0, 0, 0]`). Returns `[[0, 0, 0]]` because the duplication checks prevent duplicate triplets.
- **No triplets exist:** Returns `{}`.

## 11. Follow-up Questions
- **How would you solve 4Sum?**
  - Sort the array, and use two nested loops to fix the first two elements. Then use two pointers for the remaining elements. This takes $O(N^3)$ time.
- **Can we solve 3Sum in $O(N^2)$ time without sorting?**
  - Yes, using a hash set. For each element `nums[i]`, we can look for two numbers that sum to `-nums[i]` using a hash map. However, handling duplicates is much more complex and requires $O(N)$ extra space.

## 12. Interview Explanation
"First, I would clarify if the output triplets need to be sorted and how we should handle duplicate values. I'd explain the brute force approach where we check all possible triplets, which takes $O(N^3)$ time. To optimize this, I would sort the array first. This allows us to fix the first element of our triplet and search for the other two using a two-pointer approach from the ends of the remaining array. Sorting also helps us easily skip duplicates by ignoring consecutive identical elements. My C++ code uses this sorted two-pointer approach, which reduces the time complexity to $O(N^2)$ and requires $O(1)$ auxiliary space."

---

# 8. Merge Intervals (LeetCode 56)

## 1. Pattern
**Sorting + Interval Merge**

## 2. What is the interviewer asking?
- Given an array of `intervals` where `intervals[i] = [start_i, end_i]`.
- Merge all overlapping intervals and return an array of the non-overlapping intervals that cover all the input intervals.

## 3. Intuition
If we sort the intervals based on their start times, overlapping intervals will become adjacent. Once sorted, we can initialize our list of merged intervals. For each subsequent interval, if its start time is less than or equal to the end time of the last merged interval, they overlap. We can then merge them by updating the end time of the last merged interval. If it starts after the last merged interval ends, there is no overlap, and we can start a new interval group.

## 4. Brute Force
### Approach:
1. Represent the intervals as nodes in a graph. Draw an edge between two nodes if they overlap.
2. Find the connected components of this graph (e.g., using DFS).
3. For each connected component, find the minimum start time and maximum end time, and output that merged interval.

### Complexity:
- **Time:** $O(N^2)$ to build the graph.
- **Space:** $O(N^2)$ to store the adjacency list.

## 5. Optimized Approach
### Approach:
1. Sort the `intervals` vector in ascending order. Since intervals are represented as `vector<int>`, sorting will order them by their first element (start time).
2. Create an empty vector of vectors `merged` to store the result.
3. Iterate through each `interval` in `intervals`:
   - If `merged` is empty, or the start time of the current `interval` is greater than the end time of the last interval in `merged` (i.e., `merged.back()[1] < interval[0]`), we insert `interval` into `merged`.
   - Otherwise, there is an overlap. Update the end time of the last interval in `merged` to be the maximum of its current end time and the current interval's end time: `merged.back()[1] = max(merged.back()[1], interval[1])`.
4. Return `merged`.

## 6. Why does it work?
Sorting by start time ensures that if an interval starts after the current merged interval ends, all subsequent intervals will also start after it ends (since they have even larger start times). Therefore, we can safely conclude that the current interval group cannot be extended further, and we can start a new one.

## 7. C++ Code
```cpp
#include <vector>
#include <algorithm>

using namespace std;

vector<vector<int>> merge(vector<vector<int>>& intervals) {
    if (intervals.empty()) return {};
    
    // Sort intervals by their start times
    sort(intervals.begin(), intervals.end());
    
    vector<vector<int>> merged;
    for (auto& interval : intervals) {
        // If merged is empty or current interval doesn't overlap with the last merged one
        if (merged.empty() || merged.back()[1] < interval[0]) {
            merged.push_back(interval);
        } else {
            // Overlap occurs; merge by updating the end time
            merged.back()[1] = max(merged.back()[1], interval[1]);
        }
    }
    return merged;
}
```

## 8. Dry Run
**Input:** `intervals = [[1, 3], [2, 6], [8, 10], [15, 18]]`

- **Step 1: Sort:** Already sorted.
- **Step 2: Iterate:**
  - `interval = [1, 3]`: `merged` is empty. Push `[1, 3]`. `merged = [[1, 3]]`.
  - `interval = [2, 6]`: `merged.back()[1] (3) >= interval[0] (2)`. Overlap!
    - Update `merged.back()[1] = max(3, 6) = 6`.
    - `merged = [[1, 6]]`.
  - `interval = [8, 10]`: `merged.back()[1] (6) < interval[0] (8)`. No overlap.
    - Push `[8, 10]`.
    - `merged = [[1, 6], [8, 10]]`.
  - `interval = [15, 18]`: `merged.back()[1] (10) < interval[0] (15)`. No overlap.
    - Push `[15, 18]`.
    - `merged = [[1, 6], [8, 10], [15, 18]]`.
- **Result:** `[[1, 6], [8, 10], [15, 18]]`

## 9. Complexity
- **Time:** $O(N \log N)$ due to sorting the intervals. The linear scan takes $O(N)$ time.
- **Space:** $O(\log N)$ or $O(N)$ for sorting stack space.

## 10. Edge Cases
- **Fully overlapping intervals:** (e.g., `[[1, 4], [2, 3]]`). `merged.back()[1]` becomes `max(4, 3) = 4`. Correctly returns `[[1, 4]]`.
- **Touching boundaries:** (e.g., `[[1, 2], [2, 3]]`). Since `merged.back()[1] (2) >= interval[0] (2)`, they are merged. Correctly returns `[[1, 3]]`.
- **Single interval:** Returns the interval itself.

## 11. Follow-up Questions
- **How would you insert a new interval and merge? (Insert Interval)**
  - Instead of re-sorting, we can do it in $O(N)$ time. Add all intervals starting before the new interval to the result. Merge the new interval with the last interval if they overlap. Then merge the remaining intervals.
- **What if the intervals are in a read-only stream and cannot fit in memory?**
  - We can use an Interval Tree to dynamically insert and query overlapping intervals.

## 12. Interview Explanation
"First, I would clarify if the intervals are sorted. If not, we must sort them. Then I'd explain the brute force approach where we construct an overlap graph and find connected components, which takes $O(N^2)$ time. To optimize this, I would sort the intervals by start time. When sorted, any overlapping intervals will be adjacent. We can then iterate through the intervals, checking if the current interval overlaps with the last merged one. If it does, we update the end time; if not, we push it as a new interval. My C++ code implements this logic. This runs in $O(N \log N)$ time due to sorting and uses $O(1)$ auxiliary space."

---

# 9. Container With Most Water (LeetCode 11)

## 1. Pattern
**Two Pointers (Greedy)**

## 2. What is the interviewer asking?
- You are given an array of non-negative integers `height` representing the heights of vertical lines.
- Find two lines that, together with the x-axis, form a container that stores the maximum amount of water.
- Return the maximum area of water.

## 3. Intuition
The area of a container is bounded by the shorter line and the distance between them:
$$\text{Area} = \min(\text{height}[left], \text{height}[right]) \times (right - left)$$
To maximize the area, we can start with the widest possible container by putting pointers at the two ends (`left = 0` and `right = n-1`).
To find a larger area, we must move our pointers inward. Since width decreases with each step, we must find a taller boundary to compensate. Therefore, we should greedily discard the shorter of the two boundaries and move its pointer inward.

## 4. Brute Force
### Approach:
1. Use nested loops to calculate the area for all possible pairs of lines.
2. The outer loop selects the left boundary `i`.
3. The inner loop selects the right boundary `j` from `i+1` to `n-1`.
4. Calculate `area = min(height[i], height[j]) * (j - i)` and track the maximum.

### Complexity:
- **Time:** $O(N^2)$
- **Space:** $O(1)$

## 5. Optimized Approach
### Approach:
1. Initialize `left = 0` and `right = height.size() - 1`.
2. Initialize `maxWater = 0`.
3. While `left < right`:
   - Calculate the area: `currentWater = min(height[left], height[right]) * (right - left)`.
   - Update `maxWater = max(maxWater, currentWater)`.
   - Compare `height[left]` and `height[right]`:
     - If `height[left] < height[right]`, increment `left`.
     - Else, decrement `right`.
4. Return `maxWater`.

## 6. Why does it work?
If we keep the shorter line (say, `height[left]`) and move the taller line (`right`) inward, the width decreases. The new height can be at most `height[left]`. Thus, the area can only decrease or stay the same. The only way to potentially find a larger area is to move the pointer at the shorter boundary inward in search of a taller line.

## 7. C++ Code
```cpp
#include <vector>
#include <algorithm>

using namespace std;

int maxArea(vector<int>& height) {
    int left = 0, right = height.size() - 1, maxWater = 0;
    while (left < right) {
        // Calculate area and update maximum water
        maxWater = max(maxWater, min(height[left], height[right]) * (right - left));
        
        // Move the pointer of the shorter line inward
        if (height[left] < height[right]) {
            ++left;
        } else {
            --right;
        }
    }
    return maxWater;
}
```

## 8. Dry Run
**Input:** `height = [1, 8, 6, 2, 5, 4, 8, 3, 7]`

- **Initialization:** `left = 0`, `right = 8`, `maxWater = 0`
- **Step 1:**
  - `height[0] = 1`, `height[8] = 7`.
  - `currentWater = min(1, 7) * (8 - 0) = 8`. `maxWater = max(0, 8) = 8`.
  - Since `1 < 7`, move `left++` -> `left = 1`.
- **Step 2:**
  - `height[1] = 8`, `height[8] = 7`.
  - `currentWater = min(8, 7) * (8 - 1) = 49`. `maxWater = max(8, 49) = 49`.
  - Since `8 >= 7`, move `right--` -> `right = 7`.
- **Step 3:**
  - `height[1] = 8`, `height[7] = 3`.
  - `currentWater = min(8, 3) * (7 - 1) = 18`. `maxWater = max(49, 18) = 49`.
  - Since `8 >= 3`, move `right--` -> `right = 6`.
- **Step 4:**
  - `height[1] = 8`, `height[6] = 8`.
  - `currentWater = min(8, 8) * (6 - 1) = 40`. `maxWater = max(49, 40) = 49`.
  - Since `8 >= 8`, move `right--` -> `right = 5`.
- (The process continues until `left == right` at index 1).
- **Result:** `49`

## 9. Complexity
- **Time:** $O(N)$ because the distance between the pointers decreases by 1 in each step, taking at most $N$ iterations.
- **Space:** $O(1)$ auxiliary space.

## 10. Edge Cases
- **Array of minimum length (2):** (e.g., `[2, 3]`). Correctly computes area as `min(2, 3) * 1 = 2`.
- **Increasing/Decreasing heights:** (e.g., `[1, 2, 3, 4, 5]`). Handled correctly as the shorter pointer is moved inward.

## 11. Follow-up Questions
- **How does this compare to Trapping Rain Water?**
  - Trapping Rain Water calculates the total volume of water that can be trapped between all bars. This problem, however, finds the single largest container formed by two bars.

## 12. Interview Explanation
"First, I would clarify if we can tilt the container (no, we cannot). I'd explain the brute force approach where we check every pair of lines, taking $O(N^2)$ time. To optimize this, I would use a two-pointer approach starting at the outermost walls. The width is at its maximum here. To find a larger area, we must look for taller walls. Since the area is limited by the shorter wall, moving the pointer of the taller wall will never help. Thus, we should greedily move the pointer pointing to the shorter wall inward. My C++ code implements this logic in a single loop. This runs in $O(N)$ time and $O(1)$ space."

---

# 10. Rotate Image (LeetCode 48)

## 1. Pattern
**Matrix Transpose + Row Reversal**

## 2. What is the interviewer asking?
- You are given an $N \times N$ 2D matrix representing an image.
- Rotate the image by 90 degrees clockwise **in-place**.
- **Constraint:** You cannot allocate another 2D matrix for the rotation.

## 3. Intuition
A 90-degree clockwise rotation is equivalent to two matrix operations performed in sequence:
1. **Transpose the matrix:** Swap elements across the main diagonal (`matrix[i][j]` with `matrix[j][i]`).
2. **Reverse each row:** Flip each row horizontally.

Both operations can be done in-place, which satisfies the space constraints.

## 4. Brute Force
### Approach:
1. Allocate a new $N \times N$ matrix `temp`.
2. Copy each element of the original matrix to its new rotated position in `temp`:
   `temp[j][n - 1 - i] = matrix[i][j]`
3. Copy `temp` back to the original matrix.

### Complexity:
- **Time:** $O(N^2)$
- **Space:** $O(N^2)$ to store the copy.

## 5. Optimized Approach
### Approach:
1. **Transpose the matrix:**
   - Run outer loop `i` from `0` to `n-1`.
   - Run inner loop `j` from `i` to `n-1` (only swap elements above the diagonal to avoid swapping them back).
   - Swap `matrix[i][j]` and `matrix[j][i]`.
2. **Reverse each row:**
   - Loop through each row in the matrix.
   - Reverse the row in-place (e.g., using `reverse(row.begin(), row.end())`).

## 6. Why does it work?
Transposing a matrix swaps rows with columns, so row `i` becomes column `i`. Reversing each row then flips the column order, so column `i` is moved to column `n - 1 - i`. This matches the transformation formula `matrix[i][j] -> matrix[j][n - 1 - i]`.

## 7. C++ Code
```cpp
#include <vector>
#include <algorithm>

using namespace std;

void rotate(vector<vector<int>>& matrix) {
    int n = matrix.size();
    
    // Step 1: Transpose the matrix
    for (int i = 0; i < n; ++i) {
        for (int j = i; j < n; ++j) {
            swap(matrix[i][j], matrix[j][i]);
        }
    }
    
    // Step 2: Reverse each row
    for (auto& row : matrix) {
        reverse(row.begin(), row.end());
    }
}
```

## 8. Dry Run
**Input:** `matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]`

- **Step 1: Transpose:**
  - `i = 0`:
    - `j = 0`: swap `matrix[0][0]` with `matrix[0][0]` (no change).
    - `j = 1`: swap `matrix[0][1]` (2) with `matrix[1][0]` (4).
    - `j = 2`: swap `matrix[0][2]` (3) with `matrix[2][0]` (7).
  - `i = 1`:
    - `j = 1`: swap `matrix[1][1]` (5) with `matrix[1][1]` (no change).
    - `j = 2`: swap `matrix[1][2]` (6) with `matrix[2][1]` (8).
  - `i = 2`:
    - `j = 2`: swap `matrix[2][2]` (no change).
  - **Matrix after Transpose:** `[[1, 4, 7], [2, 5, 8], [3, 6, 9]]`
- **Step 2: Reverse Rows:**
  - Reverse Row 0: `[1, 4, 7]` -> `[7, 4, 1]`
  - Reverse Row 1: `[2, 5, 8]` -> `[8, 5, 2]`
  - Reverse Row 2: `[3, 6, 9]` -> `[9, 6, 3]`
  - **Matrix after Reversal:** `[[7, 4, 1], [8, 5, 2], [9, 6, 3]]`
- **Result:** `[[7, 4, 1], [8, 5, 2], [9, 6, 3]]`

## 9. Complexity
- **Time:** $O(N^2)$ since we visit every element of the $N \times N$ matrix twice (once during transpose, once during row reversal).
- **Space:** $O(1)$ auxiliary space because all swaps are done in-place.

## 10. Edge Cases
- **$1 \times 1$ matrix:** (e.g., `[[5]]`). Returns `[[5]]` directly.
- **Even vs. Odd matrix size ($N$):** The algorithm works for both shapes without modification.

## 11. Follow-up Questions
- **How would you rotate the image counter-clockwise by 90 degrees?**
  - Transpose the matrix first, then reverse the order of the columns (i.e., reverse the rows vertically by swapping row `i` with row `n-1-i`).
- **Can we rotate the matrix in a single pass without transposing first?**
  - Yes, by rotating groups of four elements in cycles. For example, moving elements at `(i, j)`, `(j, n-1-i)`, `(n-1-i, n-1-j)`, and `(n-1-j, i)` in a circular swap. This requires more complex index arithmetic.

## 12. Interview Explanation
"First, I would clarify if we can allocate another matrix (we cannot, it must be in-place). I'd explain the brute force approach where we copy to a temporary matrix and map coordinates, which takes $O(N^2)$ space. To optimize this, I would use the matrix property that a 90-degree clockwise rotation is equal to a transpose followed by reversing each row. My C++ code first transposes the matrix by swapping `matrix[i][j]` with `matrix[j][i]` for all $j \ge i$. Then it reverses each row using `std::reverse`. This gives us $O(N^2)$ time complexity and $O(1)$ auxiliary space."
