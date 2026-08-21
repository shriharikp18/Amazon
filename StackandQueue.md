# Section 4: Stacks and Queues

---

# 1. Implement Queue Using Stacks (LeetCode 232)

## 1. Pattern
**Pattern:** Two Stacks (Amortized Analysis / Lazy Transfer)

## 2. What is the interviewer asking?
- Implement a First-In-First-Out (FIFO) queue using only two Last-In-First-Out (LIFO) stacks.
- The queue must support standard queue operations: `push(x)` (push to back), `pop()` (remove from front), `peek()` (get front element), and `empty()` (return true if empty).
- You must use only the standard stack operations (i.e., `push`, `pop`, `top/peek`, `size`, and `empty`).

## 3. Intuition
A stack reverses the order of elements (LIFO). If we push elements into a stack and then pop them all into another stack, the order of elements in the second stack is reversed again. A double reversal restores the original FIFO order.
To make this efficient, we perform a **lazy transfer**:
- We keep two stacks: `s1` (for incoming elements) and `s2` (for outgoing elements).
- We push every new element directly to `s1` in $O(1)$ time.
- We only transfer elements from `s1` to `s2` when `s2` is empty and we need to perform a `pop()` or `peek()` operation. This means we don't move elements back and forth repeatedly, achieving an amortized time complexity of $O(1)$ per operation.

## 4. Brute Force
### Approach:
Move all elements from the first stack `s1` to the second stack `s2` during every `push` operation to place the new element at the bottom, then move everything back to `s1`. This ensures `s1` always has the queue order.
1. Push:
   - While `s1` is not empty, pop from `s1` and push to `s2`.
   - Push the new element `x` to `s1`.
   - While `s2` is not empty, pop from `s2` and push to `s1`.
2. Pop / Peek:
   - Pop/peek directly from the top of `s1`.

### Time Complexity:
- **Push:** $O(N)$
- **Pop/Peek:** $O(1)$
- **Empty:** $O(1)$

### Space Complexity:
- **Space:** $O(N)$ to hold the elements in the stacks.

## 5. Optimized Approach
Keep two stacks, `s1` and `s2`, and use a lazy transfer strategy:
1. **`push(x)`:**
   - Always push `x` to `s1`. This takes $O(1)$ time.
2. **`peek()`:**
   - If `s2` is empty, pop all elements from `s1` one-by-one and push them into `s2`.
   - Return the top element of `s2`.
3. **`pop()`:**
   - Call `peek()` to ensure `s2` is not empty and elements are prepared.
   - Pop and return the top element of `s2`.
4. **`empty()`:**
   - The queue is empty if and only if both `s1` and `s2` are empty.

## 6. Why does it work?
When elements are moved from `s1` to `s2`, their order is reversed. Since they were pushed to `s1` in LIFO order, pushing them to `s2` makes the top of `s2` represent the oldest element (FIFO).
By transferring only when `s2` is empty, we ensure:
1. We preserve the chronological order: elements in `s2` are older than any elements in `s1`.
2. Each element is pushed to `s1` once, popped from `s1` once, pushed to `s2` once, and popped from `s2` once. The total operations per element is exactly 4, yielding an amortized cost of $O(1)$ per operation.

## 7. C++ Code
```cpp
#include <stack>

class MyQueue {
private:
    std::stack<int> s1; // Input stack for pushes
    std::stack<int> s2; // Output stack for pops and peeks

public:
    // Initialize the queue.
    MyQueue() {}
    
    // Push element x to the back of queue.
    void push(int x) { 
        s1.push(x); 
    }
    
    // Removes the element from in front of queue and returns that element.
    int pop() { 
        peek(); // Ensure s2 has elements
        int topElement = s2.top(); 
        s2.pop(); 
        return topElement; 
    }
    
    // Get the front element.
    int peek() {
        if (s2.empty()) {
            while (!s1.empty()) { 
                s2.push(s1.top()); 
                s1.pop(); 
            }
        }
        return s2.top();
    }
    
    // Returns whether the queue is empty.
    bool empty() { 
        return s1.empty() && s2.empty(); 
    }
};
```

## 8. Dry Run
### Input:
Operations: `push(1)`, `push(2)`, `peek()`, `pop()`, `empty()`

### Execution Trace:
1. **`push(1)`**
   - `s1.push(1)` -> `s1 = [1]` (top is 1)
   - `s2 = []`
2. **`push(2)`**
   - `s1.push(2)` -> `s1 = [1, 2]` (top is 2)
   - `s2 = []`
3. **`peek()`**
   - `s2` is empty. Transfer elements:
     - Pop `2` from `s1`, push to `s2`. `s1 = [1]`, `s2 = [2]`.
     - Pop `1` from `s1`, push to `s2`. `s1 = []`, `s2 = [2, 1]` (top is 1).
   - Return `s2.top()`, which is `1`.
4. **`pop()`**
   - Calls `peek()`. `s2` is not empty (`s2 = [2, 1]`), returns `1`.
   - `s2.top()` is `1`. `s2.pop()` -> `s2 = [2]`.
   - Returns `1`.
5. **`empty()`**
   - Check if `s1.empty() && s2.empty()`.
   - `s1` is empty (true), but `s2` has `[2]` (false).
   - Returns `false`.

## 9. Complexity
- **Time Complexity:**
  - `push`: $O(1)$
  - `pop`: Amortized $O(1)$
  - `peek`: Amortized $O(1)$
  - `empty`: $O(1)$
- **Space Complexity:** $O(N)$ where $N$ is the number of elements pushed, required to store the elements in stacks.

## 10. Edge Cases
- **Sequential Pushes and Pops:** e.g., `push` -> `pop` -> `push` -> `pop`. The lazy transfer correctly handles this because `s2` will be empty and trigger transfers only when needed.
- **Empty Checks:** Attempting to check `empty()` when no elements have been pushed, or after all elements are popped.
- **Consecutive `peeks`:** Multiple calls to `peek()` consecutive to each other when `s2` is already filled will skip the loop and return in $O(1)$ time.

## 11. Follow-up Questions
- **Can we implement a queue using only ONE stack?**
  Yes, using the call stack (recursion). We pop elements to save them in recursion stack frames, push the element to the bottom, and then re-push the popped elements as recursion unwinds. However, this still uses $O(N)$ memory implicitly on the call stack.
- **How would you make this design thread-safe?**
  Use mutex locks inside each method, or a read-write lock, ensuring that concurrent threads calling `push()` and `pop()` do not cause race conditions during stack access and transfers.

## 12. Interview Explanation
"First, I would clarify the constraints of the problem, ensuring that we only use standard stack operations. A brute force approach would transfer all elements from one stack to another on every push to maintain the queue order in a single stack, resulting in an $O(N)$ push time. To optimize this, I would use a lazy transfer strategy with two stacks: one for input and one for output. We push new elements onto the input stack in $O(1)$ time. Only when the output stack is empty do we pop all elements from the input stack and push them into the output stack, reversing their order to FIFO. This achieves an amortized time complexity of $O(1)$ for all operations, as each element is moved between stacks a constant number of times."

---

# 2. Implement Stack Using Queues (LeetCode 225)

## 1. Pattern
**Pattern:** Two Queues (Queue Rotation)

## 2. What is the interviewer asking?
- Implement a Last-In-First-Out (LIFO) stack using only FIFO queues.
- The stack must support standard operations: `push(x)` (push to top), `pop()` (remove from top), `top()` (get top element), and `empty()` (return true if empty).
- You must use only the standard queue operations (i.e., `push to back`, `peek/pop from front`, `size`, and `is empty`).

## 3. Intuition
A queue operates on a First-In-First-Out basis. To make it behave like a stack (where the most recently added element is removed first), we must ensure that the newest element is always positioned at the front of the queue.
When we push a new element `x`:
1. We enqueue it into an auxiliary queue `q2`.
2. We then dequeue all elements from the primary queue `q1` and enqueue them to `q2`. This puts all older elements behind the new element `x`.
3. We swap the names of `q1` and `q2` so that `q1` remains our primary queue containing the elements in stack order (newest at the front).

## 4. Brute Force
We can achieve stack simulation by rearranging elements either on `push` or on `pop`. The two-queue swap during `push` is one direct approach. If we optimize `pop` instead:
- `push` just enqueues to `q1` in $O(1)$.
- `pop` dequeues $N-1$ elements from `q1` to `q2`, keeps the last element (to return it), and then swaps `q1` and `q2`. This makes `pop` $O(N)$ and `push` $O(1)$.

## 5. Optimized Approach
Here, we present the version where the **push** operation is modified to keep elements in stack order inside `q1`:
1. **`push(x)`:**
   - Enqueue `x` to `q2`.
   - Dequeue all elements from `q1` and enqueue them into `q2`.
   - Swap `q1` and `q2`. Now `q1` has the elements in LIFO order.
2. **`pop()`:**
   - Dequeue the front element of `q1` and return it.
3. **`top()`:**
   - Return the front element of `q1`.
4. **`empty()`:**
   - Return true if `q1` is empty.

## 6. Why does it work?
By pushing `x` into an empty `q2` first, `x` sits at the front. When we transfer elements from `q1` to `q2`, we are appending the existing elements behind `x` in their original order. Consequently, the last element pushed is now at the front of `q1` and will be popped first, satisfying the LIFO property of a stack.

## 7. C++ Code
```cpp
#include <queue>
#include <algorithm>

class MyStack {
private:
    std::queue<int> q1; // Primary queue storing elements in LIFO order
    std::queue<int> q2; // Auxiliary queue used during push

public:
    // Initialize the stack.
    MyStack() {}
    
    // Push element x onto stack.
    void push(int x) {
        q2.push(x);
        while (!q1.empty()) {
            q2.push(q1.front());
            q1.pop();
        }
        std::swap(q1, q2);
    }
    
    // Removes the element on top of the stack and returns it.
    int pop() {
        int topElement = q1.front();
        q1.pop();
        return topElement;
    }
    
    // Get the top element.
    int top() {
        return q1.front();
    }
    
    // Returns whether the stack is empty.
    bool empty() {
        return q1.empty();
    }
};
```

## 8. Dry Run
### Input:
Operations: `push(1)`, `push(2)`, `top()`, `pop()`, `empty()`

### Execution Trace:
1. **`push(1)`**
   - `q2.push(1)` -> `q2 = [1]`
   - `q1` is empty, loop doesn't run.
   - `swap(q1, q2)` -> `q1 = [1]`, `q2 = []`
2. **`push(2)`**
   - `q2.push(2)` -> `q2 = [2]`
   - `q1` is not empty. Dequeue `1` from `q1` and push to `q2`.
     - `q2 = [2, 1]`, `q1 = []`
   - `swap(q1, q2)` -> `q1 = [2, 1]` (front is 2), `q2 = []`
3. **`top()`**
   - Return `q1.front()`, which is `2`.
4. **`pop()`**
   - `topElement = q1.front()` (2).
   - `q1.pop()` -> `q1 = [1]`.
   - Return `2`.
5. **`empty()`**
   - Return `q1.empty()` -> `false` (contains `1`).

## 9. Complexity
- **Time Complexity:**
  - `push`: $O(N)$
  - `pop`: $O(1)$
  - `top`: $O(1)$
  - `empty`: $O(1)$
- **Space Complexity:** $O(N)$ to hold the elements in the queues.

## 10. Edge Cases
- **Stack size of 1:** `push(1)` -> `pop()` -> `empty()`. Swaps work correctly.
- **Popping until empty:** Ensure that after all elements are popped, `q1` is empty and subsequent `empty()` queries return true.

## 11. Follow-up Questions
- **Can we implement this using only ONE queue?**
  Yes. During `push(x)`, enqueue `x` to the queue, and then rotate the queue by dequeuing and enqueuing the existing elements `size - 1` times. This eliminates the second queue entirely.
  ```cpp
  void push(int x) {
      q.push(x);
      int sz = q.size();
      for (int i = 0; i < sz - 1; ++i) {
          q.push(q.front());
          q.pop();
      }
  }
  ```
- **What are the pros and cons of making `push` $O(1)$ vs `pop` $O(1)$?**
  If `push` is $O(1)$, then `pop` is $O(N)$. This is preferred if the application writes/pushes data frequently but reads/pops rarely. If reads/pops are frequent, making `pop` $O(1)$ at the expense of `push` is better.

## 12. Interview Explanation
"First, I would clarify if we are looking to optimize for push or pop operations. To make pop $O(1)$, I would use two queues. During a push, we enqueue the new element to an auxiliary queue, then dequeue all elements from our primary queue and append them to the auxiliary queue before swapping the two. This places the newest element at the front, achieving $O(1)$ pop and top times, and $O(N)$ push time. Alternatively, we can use a single queue and rotate the elements during push to achieve the same result with less space overhead."

---

# 3. Min Stack (LeetCode 155)

## 1. Pattern
**Pattern:** Auxiliary Stack (Tracking State)

## 2. What is the interviewer asking?
- Design a stack that supports standard stack operations (`push`, `pop`, `top`) and retrieving the minimum element in $O(1)$ time.
- All operations must run in constant time.

## 3. Intuition
A standard stack cannot retrieve the minimum in $O(1)$ time because finding the minimum requires scanning all elements ($O(N)$). 
Since stack operations only occur at the top (LIFO order), the history of minimums is tied directly to the stack's state. When we push a new element, the new minimum is the smaller of the current element and the minimum of the elements already in the stack. 
We can maintain a secondary stack, `minS`, which tracks the minimum value corresponding to each level of the main stack `s`.

## 4. Brute Force
### Approach:
Use a single stack. Whenever `getMin()` is called, iterate through the stack or copy it to search for the minimum element.
- **Time Complexity:** `getMin()` is $O(N)$, `push`/`pop`/`top` are $O(1)$.
- **Space Complexity:** $O(1)$ auxiliary space.

## 5. Optimized Approach
Maintain two stacks: `s` (to store all elements) and `minS` (to store the minimum elements).
1. **`push(val)`:**
   - Push `val` to `s`.
   - If `minS` is empty or `val` is less than or equal to the current top of `minS`, push `val` to `minS`.
2. **`pop()`:**
   - If the top of `s` is equal to the top of `minS`, pop from `minS`.
   - Pop from `s`.
3. **`top()`:**
   - Return the top of `s`.
4. **`getMin()`:**
   - Return the top of `minS`.

*Note: By using `<=` instead of `<` in `push()`, we correctly handle duplicate minimum elements.*

## 6. Why does it work?
The auxiliary stack `minS` stores a decreasing sequence of values. Because the stack operates in LIFO order, if a value `val` is pushed that is larger than the current minimum, the minimum of the stack does not change. When we eventually pop `val`, the minimum remains unchanged. 
If `val` is smaller than or equal to the current minimum, it becomes the new minimum. When we pop this value, the minimum reverts back to the previous minimum, which is now exposed at the top of `minS`.

## 7. C++ Code
```cpp
#include <stack>
#include <algorithm>

class MinStack {
private:
    std::stack<int> s;    // Main stack to store all elements
    std::stack<int> minS; // Stack to store the history of minimums

public:
    // Initialize the stack object.
    MinStack() {}
    
    // Push element val onto stack.
    void push(int val) {
        s.push(val);
        // Push to minS if it's empty or val is smaller/equal to current minimum
        if (minS.empty() || val <= minS.top()) {
            minS.push(val);
        }
    }
    
    // Removes the element on top of the stack.
    void pop() {
        if (!s.empty()) {
            if (s.top() == minS.top()) {
                minS.pop();
            }
            s.pop();
        }
    }
    
    // Get the top element of the stack.
    int top() {
        return s.top();
    }
    
    // Retrieve the minimum element in the stack.
    int getMin() {
        return minS.top();
    }
};
```

## 8. Dry Run
### Input:
Operations: `push(-2)`, `push(0)`, `push(-3)`, `getMin()`, `pop()`, `top()`, `getMin()`

### Execution Trace:
1. **`push(-2)`**
   - `s.push(-2)` -> `s = [-2]`
   - `minS` is empty -> `minS.push(-2)` -> `minS = [-2]`
2. **`push(0)`**
   - `s.push(0)` -> `s = [-2, 0]`
   - `0 <= minS.top()` (0 <= -2) is false -> `minS` remains `[-2]`
3. **`push(-3)`**
   - `s.push(-3)` -> `s = [-2, 0, -3]`
   - `-3 <= minS.top()` (-3 <= -2) is true -> `minS.push(-3)` -> `minS = [-2, -3]`
4. **`getMin()`**
   - Return `minS.top()` -> `-3`.
5. **`pop()`**
   - `s.top()` is `-3`, which equals `minS.top()` (`-3`).
   - Pop from `minS` -> `minS = [-2]`.
   - Pop from `s` -> `s = [-2, 0]`.
6. **`top()`**
   - Return `s.top()` -> `0`.
7. **`getMin()`**
   - Return `minS.top()` -> `-2`.

## 9. Complexity
- **Time Complexity:** $O(1)$ for all operations (`push`, `pop`, `top`, `getMin`).
- **Space Complexity:** $O(N)$ in the worst case (e.g., when elements are pushed in non-increasing order, causing all elements to be duplicated in `minS`).

## 10. Edge Cases
- **Duplicate Minimums:** Pushing `[2, 1, 1]`. The stack state will be `s = [2, 1, 1]` and `minS = [2, 1, 1]`. Popping the first `1` only pops one `1` from `minS`, leaving `minS = [2, 1]`, which correctly identifies `1` as the remaining minimum.
- **Negative Values:** Handled seamlessly by standard integer comparisons.

## 11. Follow-up Questions
- **Can we implement Min Stack using $O(1)$ auxiliary space?**
  Yes, by storing the difference between the pushed value and the current minimum. 
  - When pushing `val < min`: we push `2 * val - min` to the stack and update `min = val`. Since `2 * val - min` is guaranteed to be less than `val`, we can identify when a new minimum was set.
  - When popping, if the top value `y` is less than `min`, we restore the previous minimum: `min = 2 * min - y`.
- **What if we store pairs `(value, min_so_far)` in a single stack?**
  This uses $O(N)$ space but simplifies code. However, it uses more memory on average than the two-stack approach since the two-stack approach only pushes to `minS` when a new minimum is found.

## 12. Interview Explanation
"To solve this problem in $O(1)$ time for all operations, I would maintain two stacks: a main stack to hold the elements and an auxiliary 'minimum stack' to track the running minimum. When pushing a value, if it is less than or equal to the top of our minimum stack, we push it onto the minimum stack as well. When popping, if the value matches the top of our minimum stack, we pop it from there too. This guarantees that the minimum stack's top always contains the smallest value in the stack. Alternatively, if we wanted to save memory, we could encode differences in a single stack to achieve $O(1)$ auxiliary space."

---

# 4. Daily Temperatures (LeetCode 739)

## 1. Pattern
**Pattern:** Monotonic Stack (Decreasing Stack)

## 2. What is the interviewer asking?
- Given an array of integers `temperatures` representing daily temperatures, return an array `res` such that `res[i]` is the number of days you have to wait after the `i`-th day to get a warmer temperature.
- If there is no future day for which this is possible, keep `res[i] = 0`.

## 3. Intuition
For each day, we want to find the first day in the future with a strictly higher temperature. 
Using a **monotonic decreasing stack**, we store indices of temperatures we have seen but haven't found a warmer day for yet. 
As we iterate through the array:
- If the current temperature is warmer than the temperature at the index stored at the top of the stack, we have found the next warmer day for that index.
- We pop the index from the stack, compute the index difference (`current_index - popped_index`), write it to the results, and repeat this check for the new stack top.
- We then push the current index onto the stack.

## 4. Brute Force
### Approach:
For each day `i`, run a nested loop `j` from `i + 1` to the end of the array. Find the first index `j` where `temperatures[j] > temperatures[i]`. Set `res[i] = j - i`.

```cpp
vector<int> dailyTemperatures(vector<int>& temperatures) {
    int n = temperatures.size();
    vector<int> res(n, 0);
    for (int i = 0; i < n; ++i) {
        for (int j = i + 1; j < n; ++j) {
            if (temperatures[j] > temperatures[i]) {
                res[i] = j - i;
                break;
            }
        }
    }
    return res;
}
```

### Time Complexity:
- **Time:** $O(N^2)$ in the worst case (e.g., decreasing temperatures `[100, 90, 80, 70]`).

### Space Complexity:
- **Space:** $O(1)$ auxiliary space (excluding the output array).

## 5. Optimized Approach
Use a monotonic stack storing indices of temperatures:
1. Initialize an array `res` of size `n` with all `0`s.
2. Create an empty stack `s`.
3. Loop through the array from left to right (index `i`):
   - While the stack is not empty and `temperatures[i] > temperatures[s.top()]`:
     - Pop the top index: `index = s.top()`.
     - Update `res[index] = i - index`.
   - Push current index `i` to the stack.
4. Return `res`.

## 6. Why does it work?
By maintaining a decreasing order of temperatures in the stack, we ensure that elements remain "unresolved" until a warmer temperature is encountered. Since we scan from left to right, the first temperature that is warmer than `temperatures[s.top()]` is guaranteed to be the *nearest* warmer day in the future for `s.top()`.

## 7. C++ Code
```cpp
#include <vector>
#include <stack>

std::vector<int> dailyTemperatures(std::vector<int>& temperatures) {
    std::stack<int> s; // Monotonic stack storing indices
    std::vector<int> res(temperatures.size(), 0);
    
    for (int i = 0; i < temperatures.size(); ++i) {
        // While current temperature is warmer than the temperature at the stack top index
        while (!s.empty() && temperatures[i] > temperatures[s.top()]) {
            int index = s.top(); 
            s.pop();
            res[index] = i - index; // Calculate distance
        }
        s.push(i); // Push current day's index
    }
    return res;
}
```

## 8. Dry Run
### Input:
`temperatures = [73, 74, 75, 71, 69, 72]`

### Execution Trace:
- **`i = 0` (temp = 73):** Stack is empty. `s.push(0)` -> `s = [0]`
- **`i = 1` (temp = 74):** `74 > temperatures[0]` (73).
  - Pop `0`. `res[0] = 1 - 0 = 1`.
  - Stack is empty. `s.push(1)` -> `s = [1]`
- **`i = 2` (temp = 75):** `75 > temperatures[1]` (74).
  - Pop `1`. `res[1] = 2 - 1 = 1`.
  - Stack is empty. `s.push(2)` -> `s = [2]`
- **`i = 3` (temp = 71):** `71 > 75` is false. `s.push(3)` -> `s = [2, 3]`
- **`i = 4` (temp = 69):** `69 > 71` is false. `s.push(4)` -> `s = [2, 3, 4]`
- **`i = 5` (temp = 72):**
  - `72 > temperatures[4]` (69). Pop `4`. `res[4] = 5 - 4 = 1`. Stack: `[2, 3]`
  - `72 > temperatures[3]` (71). Pop `3`. `res[3] = 5 - 3 = 2`. Stack: `[2]`
  - `72 > temperatures[2]` (75) is false.
  - `s.push(5)` -> `s = [2, 5]`
- End of loop. Unresolved indices `2` and `5` remain in stack with their default values in `res` as `0`.
- Output: `[1, 1, 0, 2, 1, 0]`.

## 9. Complexity
- **Time Complexity:** $O(N)$ because every index is pushed onto the stack exactly once and popped at most once.
- **Space Complexity:** $O(N)$ for the stack in the worst case (e.g., strictly decreasing temperatures).

## 10. Edge Cases
- **Strictly Decreasing Temperatures:** e.g., `[80, 70, 60]`. All elements except the last stay in the stack, output is `[0, 0, 0]`.
- **Strictly Increasing Temperatures:** e.g., `[60, 70, 80]`. The stack is popped on every step, output is `[1, 1, 0]`.
- **Duplicate Temperatures:** e.g., `[70, 70, 80]`. The condition `temperatures[i] > temperatures[s.top()]` is strictly greater, so duplicate elements won't pop each other.

## 11. Follow-up Questions
- **Can we solve this from right to left?**
  Yes, by iterating backwards from the end of the array. The stack will store the values of elements to the right.
- **Can we optimize space if we modify the input array?**
  No, because we still need to keep track of unresolved elements, which requires a stack structure.

## 12. Interview Explanation
"First, I would clarify the constraints of the problem and state that a brute force approach would require checking all elements ahead for each day, taking $O(N^2)$ time. To optimize this, I would use a monotonic decreasing stack to store the indices of the days. As we scan the temperatures from left to right, we compare the current temperature with the temperature at the index stored at the top of the stack. If the current temperature is warmer, we pop the index, calculate the difference in days, and store it in our result array. We repeat this until the current temperature is no longer warmer, and then push the current index. This ensures that every element is processed in $O(1)$ amortized time, leading to an overall $O(N)$ time complexity."

---

# 5. Evaluate Reverse Polish Notation (LeetCode 150)

## 1. Pattern
**Pattern:** Stack-based Expression Evaluation

## 2. What is the interviewer asking?
- Evaluate the value of an arithmetic expression written in Reverse Polish Notation (postfix notation).
- Valid operators are `+`, `-`, `*`, and `/`.
- Operands can be integers or other expressions.
- Division between two integers must truncate toward zero (standard integer division in C++).

## 3. Intuition
In Reverse Polish Notation (RPN), the operators follow their operands. For example, `3 4 +` means `3 + 4`. 
Because operands appear first and are processed in a LIFO manner when an operator is encountered, a stack is the ideal data structure:
- When we see a number, we push it onto the stack.
- When we see an operator, we pop the top two numbers from the stack, apply the operator, and push the result back onto the stack.
- The order of operands matters for division and subtraction: the first popped value is the right operand `b`, and the second popped value is the left operand `a`.

## 4. Brute Force
### Approach:
Search for the first operator in the expression, locate the two preceding numbers, replace these three tokens with their evaluation, and repeat the process.
- **Time Complexity:** $O(N^2)$ because we have to scan the tokens list multiple times to reduce them.

## 5. Optimized Approach
Using a stack:
1. Create a stack of integers `s`.
2. Iterate through each token in the list:
   - If the token is an operator (`+`, `-`, `*`, `/`):
     - Pop the top element as `b` (right operand).
     - Pop the next top element as `a` (left operand).
     - Perform the calculation `a operator b`.
     - Push the result back onto `s`.
   - Otherwise, the token is a number. Convert it to an integer using `stoi` and push it to `s`.
3. The remaining element in the stack is the final result. Return it.

## 6. Why does it work?
Postfix expressions do not require parentheses because the position of the operator determines the order of evaluation. The stack naturally keeps track of unresolved operands. Since the operator applies to the two most recently resolved values, the LIFO property of the stack correctly pairs operators with their respective operands.

## 7. C++ Code
```cpp
#include <vector>
#include <string>
#include <stack>
#include <string>

int evalRPN(std::vector<std::string>& tokens) {
    std::stack<int> s;
    for (const std::string& token : tokens) {
        if (token == "+" || token == "-" || token == "*" || token == "/") {
            int b = s.top(); s.pop(); // Right operand
            int a = s.top(); s.pop(); // Left operand
            
            if (token == "+") s.push(a + b);
            else if (token == "-") s.push(a - b);
            else if (token == "*") s.push(a * b);
            else s.push(a / b); // Truncates toward zero in C++
        } else {
            s.push(std::stoi(token));
        }
    }
    return s.top();
}
```

## 8. Dry Run
### Input:
`tokens = ["4", "13", "5", "/", "+"]`

### Execution Trace:
- **`token = "4"`:** Push `4` -> `s = [4]`
- **`token = "13"`:** Push `13` -> `s = [4, 13]`
- **`token = "5"`:** Push `5` -> `s = [4, 13, 5]`
- **`token = "/"`:**
  - Pop `b = 5`
  - Pop `a = 13`
  - Compute `a / b` -> `13 / 5 = 2`.
  - Push `2` -> `s = [4, 2]`
- **`token = "+"`:**
  - Pop `b = 2`
  - Pop `a = 4`
  - Compute `a + b` -> `4 + 2 = 6`.
  - Push `6` -> `s = [6]`
- Return `s.top()` which is `6`.

## 9. Complexity
- **Time Complexity:** $O(N)$ where $N$ is the number of tokens, since we process each token once.
- **Space Complexity:** $O(N)$ for the stack.

## 10. Edge Cases
- **Negative operands:** e.g., `["-2", "1", "+"]` -> `stoi("-2")` evaluates to `-2` correctly.
- **Single operand:** e.g., `["18"]` -> returns `18` correctly.
- **Division truncation:** e.g., `6 / -132` should result in `0`. Standard integer division in C++ truncates towards zero.

## 11. Follow-up Questions
- **How would you prevent integer overflow?**
  Use `long long` for stack values and typecasts during operations if the intermediate results can exceed the range of standard 32-bit integers.
- **How do you evaluate infix expressions?**
  Infix expressions require operator precedence and parenthesis handling. You would typically use the Shunting-Yard algorithm to convert them to RPN first, or evaluate directly using two stacks (one for operators, one for operands).

## 12. Interview Explanation
"First, I would clarify the input format and verify that division truncates towards zero. Evaluating postfix expressions is a classic application of a stack because operations occur on the most recently parsed operands. We iterate through the tokens: if we see an operand, we push it onto the stack. If we see an operator, we pop the top two operands—being careful to use the first popped value as the right operand and the second as the left operand—evaluate the expression, and push the result back. This runs in $O(N)$ time and uses $O(N)$ space."

---

# 6. Next Greater Element I (LeetCode 496)

## 1. Pattern
**Pattern:** Monotonic Stack + Hash Map

## 2. What is the interviewer asking?
- You are given two unique integer arrays `nums1` and `nums2`, where `nums1` is a subset of `nums2`.
- For each element in `nums1`, find the next greater element in `nums2`. The next greater element of a number `x` in `nums2` is the first element to its right that is larger than `x`. If it does not exist, return `-1`.

## 3. Intuition
Because `nums1` is a subset of `nums2`, we can precalculate the next greater element (NGE) for *every* element in `nums2`. 
We use a monotonic decreasing stack to find the NGEs of elements in `nums2`:
- We iterate through `nums2`.
- While the current element `num` is larger than the top of our stack, `num` is the next greater element for the element at the top of the stack.
- We map `s.top()` to `num` in a hash map, pop the stack, and continue.
- We push `num` to the stack.
- Finally, we query the hash map for each element in `nums1`. If an element is not in the map, its next greater element is `-1`.

## 4. Brute Force
### Approach:
For each element `x` in `nums1`, find its position in `nums2`. Then, scan to the right of that position in `nums2` until a larger element is found. If no larger element is found, return `-1`.
- **Time Complexity:** $O(M \times N)$ where $M$ is the size of `nums1` and $N$ is the size of `nums2`.
- **Space Complexity:** $O(1)$ auxiliary space.

## 5. Optimized Approach
1. Initialize an empty stack `s` and a hash map `map` (key: element, value: its next greater element).
2. Traverse `nums2` from left to right:
   - While the stack is not empty and `s.top() < num`:
     - Record in the hash map: `map[s.top()] = num`.
     - Pop from `s`.
   - Push `num` to `s`.
3. Create a result array `res`.
4. For each element `num` in `nums1`:
   - Check if `num` is a key in `map`. If yes, append `map[num]` to `res`; otherwise, append `-1`.
5. Return `res`.

## 6. Why does it work?
The monotonic stack contains elements from `nums2` for which we haven't found a next greater element yet. Elements in the stack are strictly decreasing. The moment we see a number `num` that is larger than the stack's top, it must be the *first* larger element to the right of `s.top()`. The uniqueness of the elements allows us to map the values directly.

## 7. C++ Code
```cpp
#include <vector>
#include <stack>
#include <unordered_map>

std::vector<int> nextGreaterElement(std::vector<int>& nums1, std::vector<int>& nums2) {
    std::unordered_map<int, int> map; // Maps element -> next greater element
    std::stack<int> s;                // Monotonic decreasing stack
    
    // Find next greater elements for all numbers in nums2
    for (int num : nums2) {
        while (!s.empty() && s.top() < num) {
            map[s.top()] = num;
            s.pop();
        }
        s.push(num);
    }
    
    // Build the results for nums1
    std::vector<int> res;
    for (int num : nums1) {
        if (map.count(num)) {
            res.push_back(map[num]);
        } else {
            res.push_back(-1);
        }
    }
    return res;
}
```

## 8. Dry Run
### Input:
`nums1 = [4, 1, 2]`, `nums2 = [1, 3, 4, 2]`

### Execution Trace:
1. **Find NGEs in `nums2`:**
   - `num = 1`: Stack is empty. `s.push(1)` -> `s = [1]`
   - `num = 3`: `3 > 1` (top). Map `1 -> 3`. Pop `1`. Stack empty. `s.push(3)` -> `s = [3]`. `map = {1: 3}`
   - `num = 4`: `4 > 3` (top). Map `3 -> 4`. Pop `3`. Stack empty. `s.push(4)` -> `s = [4]`. `map = {1: 3, 3: 4}`
   - `num = 2`: `2 > 4` is false. `s.push(2)` -> `s = [4, 2]`.
2. **Build result for `nums1`:**
   - `nums1[0] = 4`: Not in `map` -> push `-1`. `res = [-1]`
   - `nums1[1] = 1`: `map[1] = 3` -> push `3`. `res = [-1, 3]`
   - `nums1[2] = 2`: Not in `map` -> push `-1`. `res = [-1, 3, -1]`
3. Return `res = [-1, 3, -1]`.

## 9. Complexity
- **Time Complexity:** $O(M + N)$ where $M$ is the size of `nums1` and $N$ is the size of `nums2`. We traverse `nums2` once (pushing/popping each element at most once) taking $O(N)$ time, and then scan `nums1` taking $O(M)$ time.
- **Space Complexity:** $O(N)$ to store the hash map and the stack.

## 10. Edge Cases
- **Decreasing Elements in `nums2`:** e.g., `nums2 = [4, 3, 2, 1]`. No elements will have a next greater element, map remains empty, return `-1` for all queries.
- **No elements in `nums1`:** Returns empty array.
- **`nums1` matches `nums2`:** Works identically.

## 11. Follow-up Questions
- **What if the elements in `nums2` are not unique?**
  If elements are not unique, a value-to-value map will fail. We would have to store indices in the stack and record next greater elements by mapping indices or storing them in an array matching the size of `nums2`.
- **How would you find the next *smaller* element?**
  Use a monotonic *increasing* stack. Pop elements when the current element is smaller than the top of the stack.

## 12. Interview Explanation
"First, I would clarify that the elements in both arrays are unique. A brute force approach of searching for each element in `nums2` and then scanning right would take $O(M \times N)$ time. We can optimize this by finding the next greater element for all items in `nums2` first using a monotonic decreasing stack. We iterate through `nums2` and maintain elements in decreasing order in the stack. When we see a larger element, it serves as the next greater element for the popped stack elements, which we save in a hash map. Finally, we lookup elements of `nums1` in this map in $O(1)$ time, yielding an overall $O(M + N)$ time complexity."

---

# 7. Next Greater Element II (LeetCode 503)

## 1. Pattern
**Pattern:** Monotonic Stack + Circular Array (Modulo Arithmetic)

## 2. What is the interviewer asking?
- Given a circular integer array `nums` (i.e., the next element of the last element is the first element of the array), return the Next Greater Number for every element.
- If it does not exist, return `-1`.

## 3. Intuition
To handle a circular array, we conceptually double the array by repeating it once (e.g., `[1, 2, 1]` becomes `[1, 2, 1, 1, 2, 1]`). 
Instead of allocating a new array of double size, we simulate the circular structure using the modulo operator `% n` over an iteration range from `0` to `2*n - 1`.
We maintain a monotonic decreasing stack of indices:
- As we iterate, if `nums[i % n]` is greater than `nums[s.top()]`, it means `nums[i % n]` is the next greater element for index `s.top()`.
- We pop the index, record `res[popped_index] = nums[i % n]`, and continue checking.
- We only push indices to the stack when `i < n`, because we only need to resolve the next greater elements for the original indices.

## 4. Brute Force
### Approach:
For each element `nums[i]`, iterate through the next `n - 1` elements using the index `(i + j) % n` to search for the first element strictly larger than `nums[i]`.
- **Time Complexity:** $O(N^2)$
- **Space Complexity:** $O(1)$ auxiliary space.

## 5. Optimized Approach
1. Get the size `n` of the array and initialize a result vector `res` of size `n` with `-1`.
2. Create an empty stack `s` to hold indices.
3. Loop `i` from `0` to `2*n - 1`:
   - While `s` is not empty and `nums[s.top()] < nums[i % n]`:
     - Set `res[s.top()] = nums[i % n]`.
     - Pop from `s`.
   - If `i < n`, push index `i` onto `s`.
4. Return `res`.

## 6. Why does it work?
By traversing the array twice, we allow elements near the end of the array to wrap around and compare themselves with elements at the beginning. The monotonic stack stores indices of elements whose next greater element is not yet found. When we find a larger element `nums[i % n]`, it acts as the next greater element for the index on top of the stack.

## 7. C++ Code
```cpp
#include <vector>
#include <stack>

std::vector<int> nextGreaterElements(std::vector<int>& nums) {
    int n = nums.size();
    std::vector<int> res(n, -1);
    std::stack<int> s; // Monotonic stack of indices
    
    // Simulate circular array by iterating 2 * n times
    for (int i = 0; i < 2 * n; ++i) {
        while (!s.empty() && nums[s.top()] < nums[i % n]) {
            res[s.top()] = nums[i % n];
            s.pop();
        }
        // Only push elements during the first pass
        if (i < n) {
            s.push(i);
        }
    }
    return res;
}
```

## 8. Dry Run
### Input:
`nums = [1, 2, 1]`, `n = 3`

### Execution Trace:
- **`i = 0` (val = 1):** Stack is empty. `i < 3` -> push index `0`. `s = [0]`
- **`i = 1` (val = 2):**
  - `nums[s.top()]` (`nums[0] = 1`) < `nums[1]` (2).
  - Pop `0`. `res[0] = 2`. Stack is empty.
  - `i < 3` -> push index `1`. `s = [1]`
- **`i = 2` (val = 1):**
  - `nums[1]` (2) < `1` is false.
  - `i < 3` -> push index `2`. `s = [1, 2]`
- **`i = 3` (val = 1):** (Wrap around begins)
  - `nums[2]` (1) < `nums[0]` (1) is false.
  - `i >= 3` -> do not push index. Stack: `[1, 2]`
- **`i = 4` (val = 2):**
  - `nums[2]` (1) < `nums[1]` (2). Pop `2`. `res[2] = 2`. Stack: `[1]`
  - `nums[1]` (2) < `2` is false.
  - `i >= 3` -> do not push. Stack: `[1]`
- **`i = 5` (val = 1):**
  - `nums[1]` (2) < `1` is false.
  - `i >= 3` -> do not push.
- End of loop. Index `1` remains in stack. `res[1]` remains `-1`.
- Output: `[2, -1, 2]`.

## 9. Complexity
- **Time Complexity:** $O(N)$ because the loop runs $2N$ times, and each index is pushed to the stack at most once and popped at most once.
- **Space Complexity:** $O(N)$ for the stack.

## 10. Edge Cases
- **Array with All Identical Elements:** e.g., `[3, 3, 3]`. Stack is never popped, return `[-1, -1, -1]`.
- **Strictly Decreasing Array:** e.g., `[5, 4, 3]`. Wrapping around allows elements to find their NGE (except the maximum element). Output is `[-1, 5, 5]`.
- **Single Element:** `[1]`. Returns `[-1]`.

## 11. Follow-up Questions
- **Can we optimize this by scanning from right to left?**
  Yes, we can iterate from `2*n - 1` down to `0`. We push elements onto a monotonic stack and search it for the NGE.
- **What if we did not use the `% n` operator?**
  We would have to copy the array twice (making a new array of size `2N`), which would double the memory footprint. Using `% n` is an in-place optimization.

## 12. Interview Explanation
"First, I would clarify how circular indexing works—specifically, that the last element's next neighbor is the first element. A brute force approach checks all elements circularly for each element in $O(N^2)$ time. To optimize, I would simulate a circular array of size $2N$ using modulo indexing (`i % n`) and run a monotonic decreasing stack over it. We only push indices during the first pass (where `i < n`). On the second pass, we allow elements at the end of the array to find their next greater element from the beginning of the array. This maintains an overall time complexity of $O(N)$ and space complexity of $O(N)$."

---

# 8. Circular Queue (LeetCode 622)

## 1. Pattern
**Pattern:** Fixed-size Array Representation / Ring Buffer

## 2. What is the interviewer asking?
- Design a circular queue (ring buffer) with a fixed capacity `k`.
- Operations to support:
  - `enQueue(value)`: Insert an element. Return true if successful.
  - `deQueue()`: Delete an element. Return true if successful.
  - `Front()`: Get the front item. If queue is empty, return `-1`.
  - `Rear()`: Get the last item. If queue is empty, return `-1`.
  - `isEmpty()`: Return true if the queue is empty.
  - `isFull()`: Return true if the queue is full.

## 3. Intuition
A circular queue solves the problem of memory waste in a normal array-based queue when elements are dequeued. By linking the end back to the beginning, we can reuse vacant spots.
To implement this:
- We use a fixed-size vector `q` of capacity `size`.
- We maintain two pointers: `head` (index of front element) and `tail` (index of rear element).
- Initially, `head = -1` and `tail = -1` (empty queue).
- **Enqueue:** If not full, advance `tail` circularly: `tail = (tail + 1) % size`, write the value, and if it's the first element, set `head = 0`.
- **Dequeue:** If not empty, if `head == tail` (only 1 element left), reset both to `-1`. Otherwise, advance `head` circularly: `head = (head + 1) % size`.

## 4. Brute Force
### Approach:
Use a dynamic array. During `deQueue()`, remove the first element and shift all remaining elements to the left by one position.
- **Time Complexity:** `deQueue()` is $O(N)$ because of elements shifting.
- **Space Complexity:** $O(1)$ auxiliary space beyond queue storage.

## 5. Optimized Approach
Using modular arithmetic with fixed pointers:
- **`MyCircularQueue(k)`:** Initialize `q` of size `k`, `head = -1`, `tail = -1`, `size = k`.
- **`isEmpty()`:** Return `head == -1`.
- **`isFull()`:** Return `(tail + 1) % size == head`.
- **`enQueue(value)`:**
  - If `isFull()`, return `false`.
  - If `isEmpty()`, set `head = 0`.
  - Update `tail = (tail + 1) % size`.
  - Store `q[tail] = value`.
  - Return `true`.
- **`deQueue()`:**
  - If `isEmpty()`, return `false`.
  - If `head == tail` (only one element), set `head = -1`, `tail = -1`.
  - Otherwise, update `head = (head + 1) % size`.
  - Return `true`.
- **`Front()`:** Return `isEmpty() ? -1 : q[head]`.
- **`Rear()`:** Return `isEmpty() ? -1 : q[tail]`.

## 6. Why does it work?
By wrapping the indices using `(index + 1) % size`, the pointers naturally circle back to `0` when they exceed `size - 1`. The condition `(tail + 1) % size == head` accurately flags that there is no space between the tail and the head, and resetting pointers to `-1` handles the empty state clean of edge cases.

## 7. C++ Code
```cpp
#include <vector>

class MyCircularQueue {
private:
    std::vector<int> q;
    int head;
    int tail;
    int size;

public:
    // Initialize the queue with size k.
    MyCircularQueue(int k) : q(k), head(-1), tail(-1), size(k) {}
    
    // Insert an element into the circular queue. Return true if successful.
    bool enQueue(int value) {
        if (isFull()) return false;
        if (isEmpty()) head = 0;
        
        tail = (tail + 1) % size;
        q[tail] = value;
        return true;
    }
    
    // Delete an element from the circular queue. Return true if successful.
    bool deQueue() {
        if (isEmpty()) return false;
        if (head == tail) { 
            // Queue becomes empty after dequeuing the last element
            head = -1; 
            tail = -1; 
        } else {
            head = (head + 1) % size;
        }
        return true;
    }
    
    // Get the front item from the queue.
    int Front() { 
        return isEmpty() ? -1 : q[head]; 
    }
    
    // Get the last item from the queue.
    int Rear() { 
        return isEmpty() ? -1 : q[tail]; 
    }
    
    // Checks whether the circular queue is empty or not.
    bool isEmpty() { 
        return head == -1; 
    }
    
    // Checks whether the circular queue is full or not.
    bool isFull() { 
        return (tail + 1) % size == head; 
    }
};
```

## 8. Dry Run
### Input:
`MyCircularQueue(3)`, `enQueue(1)`, `enQueue(2)`, `enQueue(3)`, `enQueue(4)` (fails), `Rear()`, `isFull()`, `deQueue()`, `enQueue(4)`, `Rear()`

### Execution Trace:
1. **Initialize:** `q` capacity 3. `head = -1, tail = -1`.
2. **`enQueue(1)`:**
   - Not full. `isEmpty()` is true -> `head = 0`.
   - `tail = (-1 + 1) % 3 = 0`. `q[0] = 1`. Returns `true`.
3. **`enQueue(2)`:**
   - Not full. `tail = (0 + 1) % 3 = 1`. `q[1] = 2`. Returns `true`.
4. **`enQueue(3)`:**
   - Not full. `tail = (1 + 1) % 3 = 2`. `q[2] = 3`. Returns `true`.
5. **`enQueue(4)`:**
   - `isFull()` check: `(2 + 1) % 3 == 0` -> `0 == 0` (true). Returns `false`.
6. **`Rear()`:**
   - Returns `q[tail]` (`q[2]`) -> `3`.
7. **`isFull()`:**
   - Returns `true`.
8. **`deQueue()`:**
   - Not empty. `head != tail` (`0 != 2`).
   - `head = (0 + 1) % 3 = 1`. Returns `true`.
9. **`enQueue(4)`:**
   - Not full (head is 1, tail is 2).
   - `tail = (2 + 1) % 3 = 0`. `q[0] = 4`. Returns `true`.
10. **`Rear()`:**
    - Returns `q[tail]` (`q[0]`) -> `4`.

## 9. Complexity
- **Time Complexity:** $O(1)$ for all operations.
- **Space Complexity:** $O(K)$ where $K$ is the capacity of the circular queue.

## 10. Edge Cases
- **Queue of size 1:** `enQueue` -> `deQueue` -> pointer resets to `-1`.
- **Calling Front/Rear on Empty Queue:** Correctly returns `-1`.
- **Alternating wrap-around operations:** Pointers wrap circularly without index out of bounds.

## 11. Follow-up Questions
- **How would you make this thread-safe?**
  Use lock mechanisms (e.g., standard mutexes) around mutating operations like `enQueue` and `deQueue`. Alternatively, design a lock-free circular queue using atomic pointer updates.
- **Can we implement a circular queue using a Linked List?**
  Yes. We can use a circular singly linked list where the tail node points back to the head node. This avoids fixing a static size limit but adds dynamic memory allocation overhead.

## 12. Interview Explanation
"First, I would clarify if the queue needs to have a fixed capacity and if dynamic resizing is allowed. A standard queue implemented with dynamic array shifts elements on dequeue, which takes $O(N)$ time. To optimize to $O(1)$ time for all operations, I would implement a ring buffer using a fixed-size vector and two pointers, `head` and `tail`. By using modulo arithmetic to wrap indices around, we reuse empty spaces left by dequeued items. We handle empty and full cases by tracking when the tail wraps around to just behind the head, or when the pointers reset to `-1`."
