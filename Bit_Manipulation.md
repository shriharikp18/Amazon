# Section 10: Bit Manipulation

This section covers essential bit manipulation questions commonly asked during Amazon interviews. Bit manipulation is crucial for optimizing space complexity and performing low-level operations efficiently.

---

# 1. Single Number (LeetCode 136)

## 1. Pattern
Bit Manipulation (XOR Bitwise Operator)

## 2. What is the interviewer asking?
Given a non-empty array of integers `nums`, every element appears twice except for one. We need to find and return that single element. 
The key constraint is that we must implement a solution with a linear runtime complexity ($O(N)$) and use only constant extra space ($O(1)$).

## 3. Intuition
The core challenge is finding the unique element without using extra memory (like a hash set or map) and doing it in a single pass. 
The properties of the bitwise XOR (`^`) operator make it perfect for this:
1. **Identity**: $x \oplus 0 = x$ (XORing any number with 0 remains unchanged)
2. **Self-Inverse**: $x \oplus x = 0$ (XORing a number with itself cancels out to 0)
3. **Commutative & Associative**: The order of operations does not matter ($a \oplus b \oplus a = a \oplus a \oplus b = 0 \oplus b = b$)

If we XOR all numbers in the array together, every duplicate pair will cancel out to 0. The remaining non-zero value will be the single number.

## 4. Brute Force
### Approach:
1. Use a hash map to keep track of the frequency of each number in the array.
2. Iterate through the array and populate the counts.
3. Iterate through the hash map and return the element that has a frequency of 1.

### Complexity:
- **Time**: $O(N)$ to iterate through the array and hash map.
- **Space**: $O(N)$ to store frequencies in the hash map.

---

## 5. Optimized Approach
### Approach:
1. Initialize a variable `result = 0`.
2. Iterate through each element `num` in the array `nums`.
3. Perform the XOR operation between `result` and `num`: `result ^= num`.
4. After traversing the entire array, return `result`.

---

## 6. Why does it work?
Because the XOR operation is associative and commutative, we can group duplicate elements together regardless of their original index positions:
$$\text{result} = (n_1 \oplus n_1) \oplus (n_2 \oplus n_2) \dots \oplus \text{single\_number}$$
Since $x \oplus x = 0$:
$$\text{result} = 0 \oplus 0 \dots \oplus \text{single\_number}$$
$$\text{result} = \text{single\_number}$$
This ensures that regardless of the input order, all duplicate elements cancel out perfectly, and the single unique element remains.

---

## 7. C++ Code
```cpp
#include <vector>

class Solution {
public:
    int singleNumber(std::vector<int>& nums) {
        int result = 0;
        for (int num : nums) {
            result ^= num;
        }
        return result;
    }
};
```

---

## 8. Dry Run
Input: `nums = [4, 1, 2, 1, 2]`

### Initialization:
- `result = 0`

### Step 1:
- `num = 4`
- `result = 0 ^ 4 = 4` (Binary: `000 ^ 100 = 100`)

### Step 2:
- `num = 1`
- `result = 4 ^ 1 = 5` (Binary: `100 ^ 001 = 101`)

### Step 3:
- `num = 2`
- `result = 5 ^ 2 = 7` (Binary: `101 ^ 010 = 111`)

### Step 4:
- `num = 1`
- `result = 7 ^ 1 = 6` (Binary: `111 ^ 001 = 110`)

### Step 5:
- `num = 2`
- `result = 6 ^ 2 = 4` (Binary: `110 ^ 010 = 100`)

### Return:
- `result = 4`

---

## 9. Complexity
- **Time Complexity**: $O(N)$ where $N$ is the number of elements in the array. We visit each element exactly once.
- **Space Complexity**: $O(1)$ auxiliary space. Only a single integer variable `result` is used.

---

## 10. Edge Cases
- **Single element array**: `nums = [1]`. The loop runs once, `result = 0 ^ 1 = 1`. Returns `1` (Correct).
- **Negative numbers**: `nums = [-1, -2, -1]`. The XOR operation is bitwise and works perfectly on signed integer bit patterns (two's complement representation).
- **Large numbers**: Bitwise XOR is independent of the size of the integer values as long as they fit within standard integer limits.

---

## 11. Follow-up Questions
- **What if every element appears three times and one element appears once?**
  - *Answer*: We cannot use simple XOR. Instead, we can sum the bits at each position modulo 3, or use two bitmasks (`ones` and `twos`) to track state transitions.
- **What if two elements appear once and all others appear twice?**
  - *Answer*: XORing all elements will yield `X ^ Y` where `X` and `Y` are the two unique elements. We can then find the first set bit in `X ^ Y` and use it to partition the array into two groups, finding `X` in one group and `Y` in the other.
- **Can we solve this using sorting?**
  - *Answer*: Yes, sorting takes $O(N \log N)$ time and $O(1)$ space. We would check if `nums[i] != nums[i+1]` by moving in steps of 2.

---

## 12. Interview Explanation
"First, I would clarify the constraints of the problem, such as verifying if the array is non-empty and if it is guaranteed that all elements except one appear exactly twice. Then I'd explain the brute force approach where we count the occurrences of each element using a hash map, which takes $O(N)$ time and $O(N)$ space. To optimize this to $O(1)$ space, I would use the XOR bitwise operator. Since XORing a number with itself yields 0 and XORing with 0 leaves the number unchanged, XORing all elements in the array will cancel out all duplicates and leave only the single number. My C++ code implements this by iterating through the array, performing XOR on each element with a running accumulator, and returning the final result."

---
---

# 2. Reverse Bits (LeetCode 190)

## 1. Pattern
Bit Manipulation (Bit Shifting and Masking)

## 2. What is the interviewer asking?
Given a 32-bit unsigned integer `n`, reverse its binary digits and return the resulting 32-bit unsigned integer.

## 3. Intuition
To reverse the bits, we can build the result bit by bit. We extract the least significant bit (LSB) of the input `n` and append it to our `result`.
To append the bit to the `result`, we shift `result` left by 1 to make room at the LSB position, then OR it with the extracted bit. We then shift `n` right by 1 to process the next bit. We repeat this process exactly 32 times for a 32-bit integer.

## 4. Brute Force
### Approach:
1. Convert the 32-bit integer into its 32-character binary string representation (e.g. `00000010...`).
2. Reverse the string using standard string reversal techniques.
3. Convert the reversed binary string back into a 32-bit unsigned integer.

### Complexity:
- **Time**: $O(1)$ because the integer has a fixed size of 32 bits, but string conversion has a high constant factor overhead.
- **Space**: $O(1)$ auxiliary space (about 32 bytes for the string).

---

## 5. Optimized Approach
### Approach:
1. Initialize `result` to 0.
2. Run a loop 32 times (representing the 32 bits of the integer).
3. Shift `result` to the left by 1: `result << 1`. This makes room for the new bit.
4. Extract the LSB of `n` using bitwise AND: `n & 1`.
5. Combine `result` and the LSB using bitwise OR: `result = (result << 1) | (n & 1)`.
6. Shift `n` to the right by 1: `n >>= 1` to prepare for the next iteration.
7. Return `result` after 32 iterations.

---

## 6. Why does it work?
Bit shifting shifts the binary digits left or right:
- `result << 1` multiplies the current value by 2, shifting all bits to the left and leaving a `0` at the LSB.
- `n & 1` extracts the lowest bit of `n`.
- `result | (n & 1)` puts the extracted bit into the LSB of `result`.
By repeating this 32 times:
- The first bit extracted (original LSB of `n`) is shifted left 31 times by the end of the loop, ending up at the most significant bit (MSB) position.
- The last bit extracted (original MSB of `n`) is shifted left 0 times, ending up at the LSB position.
Thus, the entire sequence of 32 bits is successfully reversed.

---

## 7. C++ Code
```cpp
#include <cstdint>

class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        uint32_t result = 0;
        for (int i = 0; i < 32; ++i) {
            result = (result << 1) | (n & 1);
            n >>= 1;
        }
        return result;
    }
};
```

---

## 8. Dry Run
Input: `n = 43261596` (Binary: `00000010100101000001111010011100`)

### Step 1:
- `n & 1 = 0` (LSB of `n` is 0)
- `result = (0 << 1) | 0 = 0`
- `n >>= 1` -> `n` becomes `00000001010010100000111101001110`

### Step 2:
- `n & 1 = 0` (next LSB is 0)
- `result = (0 << 1) | 0 = 0`
- `n >>= 1` -> `n` becomes `00000000101001010000011110100111`

### Step 3:
- `n & 1 = 1` (next LSB is 1)
- `result = (0 << 1) | 1 = 1`
- `n >>= 1` -> `n` becomes `00000000010100101000001111010011`

### Step 4:
- `n & 1 = 1` (next LSB is 1)
- `result = (1 << 1) | 1 = 3` (Binary: `11`)
- `n >>= 1` -> `n` becomes `00000000001010010100000111101001`

...

### Step 32:
- The last bit (originally the MSB) is processed and placed into `result`.
- Output: `964176192` (Binary: `00111001011110000010100101000000`)

---

## 9. Complexity
- **Time Complexity**: $O(1)$ because the loop always runs exactly 32 times, regardless of the value of `n`.
- **Space Complexity**: $O(1)$ since no additional memory is allocated besides a few primitive variables.

---

## 10. Edge Cases
- **All bits are 0**: `n = 0`. The result will remain 0 (Correct).
- **All bits are 1**: `n = 4294967295` (max unsigned 32-bit int). The result will remain `4294967295` (Correct).
- **Alternating bit patterns**: `n = 2863311530` (Binary: `10101010...10`). The reversed result will be `1431655765` (Binary: `01010101...01`) (Correct).

---

## 11. Follow-up Questions
- **If this function is called many times, how would you optimize it?**
  - *Answer*: We can use a cache / lookup table. We can split the 32-bit integer into four 8-bit bytes. We precompute the reversed pattern for all possible 8-bit numbers (0 to 255) and store them in an array. For any input, we fetch the reversed byte values from the lookup table and recombine them in reverse order.
- **Can you perform the reversal using divide and conquer?**
  - *Answer*: Yes, we can swap adjacent bit chunks. First swap 16-bit blocks, then swap 8-bit blocks within those, then 4-bit blocks, then 2-bit blocks, and finally individual bits using masking and shifting.
- **What is the difference between arithmetic right shift (`>>`) and logical right shift (`>>>`)?**
  - *Answer*: Logical right shift fills the vacated MSB positions with 0s, which is default for unsigned types in C++. Arithmetic right shift preserves the sign bit (MSB) for signed integers.

---

## 12. Interview Explanation
"First, I would clarify if the input is always a standard 32-bit unsigned integer. Then I'd explain the brute force approach, which would be converting the number to a binary string, reversing the string, and parsing it back to an integer. To optimize this, I would use bitwise operations directly on the integer to avoid string allocation overhead. By running a loop 32 times, I can extract the least significant bit of the input using `n & 1`, shift our running result to the left to make room, and OR the extracted bit into it. Then, we shift `n` to the right by 1 to process the next bit. My C++ code executes this logic efficiently in $O(1)$ time and $O(1)$ space."

---
---

# 3. Number of 1 Bits (LeetCode 191)

## 1. Pattern
Bit Manipulation (Bit Masking / Bit Counting / Brian Kernighan's Algorithm)

## 2. What is the interviewer asking?
Given an unsigned 32-bit integer `n`, return the number of set bits (also known as '1' bits or the Hamming weight) that it contains.

## 3. Intuition
To count the set bits:
- **Standard approach**: We check the LSB of `n` using `n & 1`. If it is 1, we increment our counter. We then shift `n` right by 1. We repeat this until `n` becomes 0.
- **Optimized approach (Brian Kernighan's Algorithm)**: Instead of shifting 32 times, we can skip directly to the next set bit. The expression `n & (n - 1)` clears the lowest set bit of `n`. By performing this operation repeatedly until `n` becomes 0, the number of operations is exactly equal to the number of set bits.

## 4. Brute Force
### Approach:
1. Convert the integer to its binary string representation.
2. Iterate through the string character by character.
3. Count how many times the character `'1'` appears.

### Complexity:
- **Time**: $O(1)$ since the number of bits is fixed at 32, but converting to string adds overhead.
- **Space**: $O(1)$ to store the string of length 32.

---

## 5. Optimized Approach
### Approach 1 (Shifting all bits - matches prompt C++ code):
1. Initialize `count = 0`.
2. While `n` is not 0:
   - Check the LSB of `n` using `n & 1`. Add this value (either 0 or 1) to `count`.
   - Shift `n` to the right by 1 (`n >>= 1`).
3. Return `count`.

### Approach 2 (Brian Kernighan's Algorithm):
1. Initialize `count = 0`.
2. While `n` is not 0:
   - Perform `n = n & (n - 1)`. This clears the rightmost set bit.
   - Increment `count`.
3. Return `count`.

---

## 6. Why does it work?
- For **Approach 1**: Shifting `n` right discards the current LSB. Performing `n & 1` extracts the LSB. Thus, we inspect every single bit position, incrementing the count when a bit is 1.
- For **Approach 2 (Brian Kernighan's)**: Subtracting 1 from a number reverses all the bits to the right of the rightmost set bit, and flips the rightmost set bit itself from 1 to 0. 
  For example, if `n = 12` (binary `1100`), then `n - 1 = 11` (binary `1011`). 
  Performing bitwise AND: `1100 & 1011 = 1000` (binary `8`). 
  The lowest set bit at the 2nd position has been cleared. Because each step clears exactly one set bit, the loop runs exactly as many times as there are set bits.

---

## 7. C++ Code
```cpp
#include <cstdint>

class Solution {
public:
    // Approach 1: Simple shifting (matching prompt)
    int hammingWeight(uint32_t n) {
        int count = 0;
        while (n != 0) {
            count += n & 1;
            n >>= 1;
        }
        return count;
    }

    // Approach 2: Brian Kernighan's Algorithm (Optimized for sparse set bits)
    int hammingWeightOptimized(uint32_t n) {
        int count = 0;
        while (n != 0) {
            n &= (n - 1); // Clears the lowest set bit
            count++;
        }
        return count;
    }
};
```

---

## 8. Dry Run
Input: `n = 11` (Binary: `00000000000000000000000000001011`)

### Simple Shifting (Approach 1):
- **Iteration 1**: `count += 11 & 1` (1). `count = 1`. `n >>= 1` -> `n = 5` (`101` in binary).
- **Iteration 2**: `count += 5 & 1` (1). `count = 2`. `n >>= 1` -> `n = 2` (`10` in binary).
- **Iteration 3**: `count += 2 & 1` (0). `count = 2`. `n >>= 1` -> `n = 1` (`1` in binary).
- **Iteration 4**: `count += 1 & 1` (1). `count = 3`. `n >>= 1` -> `n = 0`.
- Loop terminates. Return `count = 3`.

### Brian Kernighan's (Approach 2):
- **Iteration 1**: `n = 11 & 10 = 1011 & 1010 = 1010` (value 10). `count = 1`.
- **Iteration 2**: `n = 10 & 9 = 1010 & 1001 = 1000` (value 8). `count = 2`.
- **Iteration 3**: `n = 8 & 7 = 1000 & 0111 = 0000` (value 0). `count = 3`.
- Loop terminates. Return `count = 3`.

---

## 9. Complexity
### Approach 1 (Shifting):
- **Time Complexity**: $O(1)$ since the number of bits in the input is fixed (at most 32 shifts).
- **Space Complexity**: $O(1)$ auxiliary space.

### Approach 2 (Brian Kernighan's):
- **Time Complexity**: $O(K)$ where $K$ is the number of set bits (Hamming weight). In the worst case (all bits set), it takes 32 iterations, but for numbers with very few set bits, it is much faster.
- **Space Complexity**: $O(1)$ auxiliary space.

---

## 10. Edge Cases
- **Zero set bits**: `n = 0`. The loop condition `n != 0` immediately fails, returns `count = 0` (Correct).
- **All set bits**: `n = 4294967295` (Binary: all 32 bits are 1). Returns `32` (Correct).
- **Power of 2**: `n = 1024` (Binary: only one set bit). The loop runs exactly once using Brian Kernighan's, returning `1` (Correct).

---

## 11. Follow-up Questions
- **Are there compiler built-ins or library functions that do this?**
  - *Answer*: Yes, in C++20 you can use `<bit>` library's `std::popcount(n)`. For GCC, there is a built-in function `__builtin_popcount(n)` which maps directly to hardware instructions (like `POPCNT` on x86 architecture).
- **How would you count bits if you have to call this function millions of times on different values?**
  - *Answer*: Use a precomputed Lookup Table (LUT). For example, split the 32-bit number into four 8-bit parts. Use an array of size 256 where `LUT[i]` contains the number of set bits in `i`. Then the result is `LUT[part1] + LUT[part2] + LUT[part3] + LUT[part4]`. This runs in $O(1)$ operations with virtually no branching.
- **Can we count bits using a divide and conquer technique without a loop?**
  - *Answer*: Yes, by masking and adding adjacent bits:
    ```cpp
    n = n - ((n >> 1) & 0x55555555);
    n = (n & 0x33333333) + ((n >> 2) & 0x33333333);
    n = (n + (n >> 4)) & 0x0F0F0F0F;
    n = n + (n >> 8);
    n = n + (n >> 16);
    return n & 0x0000003F;
    ```

---

## 12. Interview Explanation
"First, I would clarify if the input size is fixed at 32-bits. Then I'd explain the basic approach where we shift the bits of the integer one by one to the right, checking if the lowest bit is set using a bitwise AND with 1. To optimize this, I would use Brian Kernighan's algorithm. Instead of checking all 32 bits, we can directly clear the lowest set bit in each iteration using `n & (n - 1)`. The loop will then run exactly $K$ times, where $K$ is the number of set bits. My C++ code implementation covers the shifting technique, and I can also implement the Brian Kernighan's optimization to make the code faster."

---
---

# 4. Missing Number (LeetCode 268)

## 1. Pattern
Bit Manipulation (XOR) or Mathematics (Sum Formula)

## 2. What is the interviewer asking?
Given an array `nums` containing $n$ distinct numbers in the range $[0, n]$, find and return the only number in the range that is missing from the array.

## 3. Intuition
- **XOR Approach**: XORing a number with itself cancels it out ($A \oplus A = 0$). If we XOR all indices from $0$ to $n$ together, and then XOR that result with all numbers inside the array `nums`, all the numbers present in both sets will cancel out. The remaining value will be the missing number.
- **Math Approach**: The sum of all numbers from $0$ to $n$ is given by the formula $\frac{n(n + 1)}{2}$. By summing all the elements in `nums` and subtracting that sum from the expected total sum, we get the missing number.

## 4. Brute Force
### Approach:
1. Sort the input array `nums`.
2. Iterate through the array and check if the element at index `i` is equal to `i`.
3. If `nums[i] != i`, then `i` is the missing number.
4. If the loop completes successfully, then the missing number must be $n$.

### Complexity:
- **Time**: $O(N \log N)$ due to sorting.
- **Space**: $O(1)$ auxiliary space if sorted in place, otherwise $O(N)$.

---

## 5. Optimized Approach
### Approach (XOR - matching prompt C++ code):
1. Determine the size of the array: `n = nums.size()`.
2. Initialize `totalXOR = 0` and `arrayXOR = 0`.
3. Loop through all integers `i` from $0$ to $n$ (inclusive) and accumulate their XOR sum in `totalXOR`.
4. Loop through all elements `num` in the array `nums` and accumulate their XOR sum in `arrayXOR`.
5. Return the result of `totalXOR ^ arrayXOR`.

---

## 6. Why does it work?
Let the complete set of numbers be $C = \{0, 1, 2, \dots, n\}$ and the given array elements be $A = \{nums[0], nums[1], \dots, nums[n-1]\}$.
The missing number $m$ is the only element that belongs to $C$ but not $A$.
When we calculate:
$$\text{result} = (0 \oplus 1 \oplus \dots \oplus n) \oplus (nums[0] \oplus nums[1] \dots \oplus nums[n-1])$$
Every number that is present in both sets appears exactly twice in the expression. Since $X \oplus X = 0$, all these common numbers cancel out. The missing number $m$ appears only once (in the complete set), so it does not cancel out and remains as the final result:
$$\text{result} = m$$

---

## 7. C++ Code
```cpp
#include <vector>

class Solution {
public:
    int missingNumber(std::vector<int>& nums) {
        int n = nums.size();
        int totalXOR = 0, arrayXOR = 0;
        
        // XOR all numbers in range [0, n]
        for (int i = 0; i <= n; ++i) {
            totalXOR ^= i;
        }
        
        // XOR all numbers present in the array
        for (int num : nums) {
            arrayXOR ^= num;
        }
        
        return totalXOR ^ arrayXOR;
    }
};
```

---

## 8. Dry Run
Input: `nums = [3, 0, 1]`
Array size: `n = 3`

### Step 1 (Calculate totalXOR):
- `i = 0`: `totalXOR = 0`
- `i = 1`: `totalXOR = 0 ^ 1 = 1`
- `i = 2`: `totalXOR = 1 ^ 2 = 3` (Binary: `01 ^ 10 = 11`)
- `i = 3`: `totalXOR = 3 ^ 3 = 0` (Binary: `11 ^ 11 = 00`)

### Step 2 (Calculate arrayXOR):
- `num = 3`: `arrayXOR = 0 ^ 3 = 3`
- `num = 0`: `arrayXOR = 3 ^ 0 = 3`
- `num = 1`: `arrayXOR = 3 ^ 1 = 2`

### Step 3 (Calculate result):
- `totalXOR ^ arrayXOR = 0 ^ 2 = 2`

### Return:
- `2` (Correct, since `2` is missing from `[3, 0, 1]`)

---

## 9. Complexity
- **Time Complexity**: $O(N)$ where $N$ is the number of elements in the array. We loop from $0$ to $n$ and then through the array once.
- **Space Complexity**: $O(1)$ auxiliary space. Only two integer variables (`totalXOR` and `arrayXOR`) are used.

---

## 10. Edge Cases
- **Missing number is 0**: `nums = [1, 2, 3]`. `totalXOR = 0^1^2^3 = 0`. `arrayXOR = 1^2^3 = 0`. Result: `0 ^ 0 = 0` (Correct).
- **Missing number is n**: `nums = [0, 1, 2]`. `totalXOR = 0^1^2^3 = 0`. `arrayXOR = 0^1^2 = 3`. Result: `0 ^ 3 = 3` (Correct).
- **Array of size 1**: `nums = [0]`. `n = 1`. `totalXOR = 0^1 = 1`. `arrayXOR = 0`. Result: `1 ^ 0 = 1` (Correct).

---

## 11. Follow-up Questions
- **Can you solve this using the sum formula?**
  - *Answer*: Yes. We calculate the sum of numbers from $0$ to $n$ as $S = \frac{n(n + 1)}{2}$ and the sum of the array. The missing number is $S - \text{arraySum}$.
- **Why would the XOR approach be preferred over the sum approach?**
  - *Answer*: The sum formula can lead to arithmetic overflow if $n$ is very large (e.g., if $n \approx 10^5$, $n(n+1)/2$ fits in standard integers, but for extremely large sizes it might exceed standard 32-bit integer limits). XOR never overflows since it is a bitwise operation.
- **How would you find the missing number if the array elements could be modified?**
  - *Answer*: We can use the array indices to flag visited numbers. For each number $v = |nums[i]|$, if $v < n$, we set $nums[v] = -nums[v]$. The index that remains positive is the missing number.

---

## 12. Interview Explanation
"First, I would clarify if the numbers in the array are guaranteed to be unique and in the range $[0, n]$. Then, I'd explain the brute-force approach, which is sorting the array and checking if the element at index `i` matches `i`. This would take $O(N \log N)$ time. To optimize this to $O(N)$ time and $O(1)$ space, we can use the XOR bitwise operator. Since a number XORed with itself is 0, if we XOR all integers from 0 to $n$ and then XOR all elements in the array, every number present in the array will cancel out, leaving only the missing number. My C++ code does exactly this using two simple loops and returns the combined XOR result."
