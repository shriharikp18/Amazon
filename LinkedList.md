# Section 3: Linked Lists

This section covers essential Linked List problems commonly asked in Amazon technical interviews. For each problem, we explore the core pattern, interview explanation, step-by-step intuition, code, dry runs, complexity, edge cases, and follow-ups.

---

# 1. Reverse Linked List (LeetCode 206)

## 1. Pattern
Two Pointers / Iterative Reversal.

## 2. What is the interviewer asking?
The interviewer wants us to reverse a singly linked list so that the head becomes the tail, the tail becomes the head, and all intermediate links point in the opposite direction. They are looking to see if we can manipulate pointers carefully without losing references to the list nodes.

## 3. Intuition
To reverse the list, as we traverse, we must change the direction of each node's `next` pointer to point to its predecessor instead of its successor. Since doing this directly would sever the connection to the rest of the list, we must store the next node (`nextNode`) before breaking the link, and maintain a pointer to the previous node (`prev`).

## 4. Brute Force
Approach:
1. Traverse the linked list and push the values of all nodes into a stack or vector.
2. Traverse the list a second time (or create a new list) and copy the values from the stack/vector in reverse order into the nodes.

Time: O(N)
Space: O(N)

## 5. Optimized Approach
1. Initialize a pointer `prev` to `nullptr` and a pointer `head` (representing the current node).
2. Loop through the list while `head` is not `nullptr`.
3. In each iteration:
   - Store the next node: `ListNode* nextNode = head->next;`
   - Reverse the pointer of the current node: `head->next = prev;`
   - Move `prev` forward to the current node: `prev = head;`
   - Move `head` forward to `nextNode`: `head = nextNode;`
4. When the loop terminates, `prev` will be pointing to the new head of the reversed list. Return `prev`.

## 6. Why does it work?
It works because at every node, we locally reverse the arrow (pointing it to `prev`). By maintaining the `nextNode` reference before overwriting `head->next`, we ensure we never lose the pointer to the rest of the list. Since we do this for every single node in a linear fashion, the entire list is reversed correctly, and `prev` eventually stops at the original tail, which is the new head.

## 7. C++ Code
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
ListNode* reverseList(ListNode* head) {
    ListNode* prev = nullptr;
    while (head) {
        // Save the next node before overwriting the next pointer
        ListNode* nextNode = head->next;
        // Reverse the current node's link
        head->next = prev;
        // Move prev to the current node
        prev = head;
        // Move head to the next node
        head = nextNode;
    }
    return prev;
}
```

## 8. Dry Run
Input: `head = [1, 2, 3]`

Initially: `head` points to Node(1), `prev = nullptr`.

- **Step 1 (First Iteration)**:
  - `nextNode = head->next` -> points to Node(2)
  - `head->next = prev` -> Node(1)->next = nullptr
  - `prev = head` -> points to Node(1)
  - `head = nextNode` -> points to Node(2)
  - *State*: List so far: `Node(1) -> nullptr`. `prev` points to Node(1). `head` points to Node(2).

- **Step 2 (Second Iteration)**:
  - `nextNode = head->next` -> points to Node(3)
  - `head->next = prev` -> Node(2)->next = Node(1)
  - `prev = head` -> points to Node(2)
  - `head = nextNode` -> points to Node(3)
  - *State*: List so far: `Node(2) -> Node(1) -> nullptr`. `prev` points to Node(2). `head` points to Node(3).

- **Step 3 (Third Iteration)**:
  - `nextNode = head->next` -> nullptr
  - `head->next = prev` -> Node(3)->next = Node(2)
  - `prev = head` -> points to Node(3)
  - `head = nextNode` -> nullptr
  - *State*: List: `Node(3) -> Node(2) -> Node(1) -> nullptr`. `prev` points to Node(3). `head` is nullptr.

- **Step 4**:
  - Loop terminates since `head` is nullptr. Returns `prev` (Node(3)).

## 9. Complexity
Time: O(N) where N is the number of nodes in the list. We traverse the list exactly once.
Space: O(1) auxiliary space as we only use a few pointers.

## 10. Edge Cases
- **Empty list (`head == nullptr`)**: The loop doesn't execute, and `prev` (which is `nullptr`) is returned. This is correct.
- **Single node list (`[1]`)**: The loop executes once, `nextNode` becomes `nullptr`, `head->next` points to `nullptr`, `prev` becomes Node(1), `head` becomes `nullptr`. Returns Node(1), which is correct.
- **List with two nodes**: Reverses correctly as verified by dry run.

## 11. Follow-up Questions
- Can you reverse it recursively?
- Can you reverse only a subsegment of the list (e.g., LeetCode 92)?
- How would you reverse a doubly linked list?

## 12. Interview Explanation
"First, I would clarify the constraints of the problem, such as whether the list can be empty or if we need to do it in-place. Then I'd explain the brute force approach where we could copy all the values to an array, reverse the array, and write them back, which takes O(N) space. To optimize this, I would use an iterative, in-place approach using two pointers, `prev` and `head`. As we traverse the list, we temporarily store the next node so we don't lose the rest of the list, set the current node's `next` pointer to `prev`, and then advance `prev` and `head` forward. My C++ code would use a simple `while` loop that runs in O(N) time and O(1) auxiliary space."

---

# 2. Merge Two Sorted Lists (LeetCode 21)

## 1. Pattern
Two Pointers / Recursion.

## 2. What is the interviewer asking?
We are given the heads of two sorted linked lists, `list1` and `list2`. The goal is to merge the two lists into one single sorted list. The list should be made by splicing together the nodes of the first two lists, meaning we should reuse the existing nodes rather than creating new ones.

## 3. Intuition
Since both lists are already sorted, we can compare the heads of both lists. The node with the smaller value should be the next node in our merged list. We can solve this recursively: if `list1->val < list2->val`, then `list1` is our merged head, and its `next` pointer should point to the result of merging the rest of `list1` with `list2`.

## 4. Brute Force
Approach:
1. Create a dummy head node.
2. Compare elements of `list1` and `list2` using an iterative loop.
3. Append the smaller node to the current pointer of the merged list.
4. Advance the pointer of the list from which the node was taken, as well as the merged list pointer.
5. If one list is exhausted, append the remaining part of the other list.

Time: O(N + M)
Space: O(1) (for iterative), O(N + M) (for recursion stack).

## 5. Optimized Approach
1. **Base Cases**: If `list1` is empty, return `list2`. If `list2` is empty, return `list1`.
2. **Comparison**:
   - If `list1->val < list2->val`, then `list1` should come first. Recursively call `mergeTwoLists(list1->next, list2)` to get the merged list for the remainder, and assign this result to `list1->next`. Return `list1`.
   - Otherwise, `list2` should come first. Recursively call `mergeTwoLists(list1, list2->next)` and assign this result to `list2->next`. Return `list2`.

## 6. Why does it work?
The recursive approach works because of the optimal substructure property of the problem. Once we determine the smaller of the two head nodes, the problem of merging the rest of the lists is identical to the original problem, just with one list size reduced by 1. The base cases handle the termination correctly when we run out of nodes in either list.

## 7. C++ Code
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
    if (!list1) return list2;
    if (!list2) return list1;
    
    if (list1->val < list2->val) {
        list1->next = mergeTwoLists(list1->next, list2);
        return list1;
    } else {
        list2->next = mergeTwoLists(list1, list2->next);
        return list2;
    }
}
```

## 8. Dry Run
Input: `list1 = [1, 3]`, `list2 = [2, 4]`

- **Step 1**: Call `mergeTwoLists([1, 3], [2, 4])`
  - Compare `1 < 2` (True).
  - `list1->next = mergeTwoLists([3], [2, 4])`
- **Step 2**: Call `mergeTwoLists([3], [2, 4])`
  - Compare `3 < 2` (False).
  - `list2->next = mergeTwoLists([3], [4])`
- **Step 3**: Call `mergeTwoLists([3], [4])`
  - Compare `3 < 4` (True).
  - `list1->next = mergeTwoLists(nullptr, [4])`
- **Step 4**: Call `mergeTwoLists(nullptr, [4])`
  - `list1` is null, returns `list2` ([4]).
- **Step 5 (Backtracking Call 3)**:
  - `list1->next` = [4], returns Node(3) -> [4].
- **Step 6 (Backtracking Call 2)**:
  - `list2->next` = Node(3) -> [4], returns Node(2) -> [3, 4].
- **Step 7 (Backtracking Call 1)**:
  - `list1->next` = Node(2) -> [3, 4], returns Node(1) -> [2, 3, 4].
- *Final returned list*: `[1, 2, 3, 4]`.

## 9. Complexity
Time: O(N + M) where N and M are the sizes of `list1` and `list2` respectively, since we visit each node in both lists once.
Space: O(N + M) recursive stack space. If we want O(1) space, we must use the iterative approach.

## 10. Edge Cases
- **Both lists empty**: Returns `nullptr`.
- **One list empty**: Returns the other list directly.
- **Lists of unequal length**: The recursion naturally stops when the shorter list is exhausted and returns the rest of the longer list.

## 11. Follow-up Questions
- How would you merge K sorted lists?
- Can you implement this iteratively to achieve O(1) auxiliary space?
- What happens if the lists contain duplicate values?

## 12. Interview Explanation
"First, I would clarify if we should do this in-place or if we can allocate new nodes. Assuming we do it in-place to save memory, I'd explain the recursive approach: at each step, we compare the heads of both lists. The node with the smaller value becomes the current head of the merged list, and we recursively merge the remaining nodes. My recursive C++ code is simple and clean, running in O(N + M) time and using O(N + M) recursive stack space. If O(1) space is required, we can convert it to an iterative approach using a dummy node."

---

# 3. Remove Nth Node From End of List (LeetCode 19)

## 1. Pattern
Two Pointers (Fast & Slow Pointer / Delay Pointer).

## 2. What is the interviewer asking?
We need to remove the n-th node from the end of a singly linked list and return its head. The challenge is to do this in a single pass of the list, without knowing its total length beforehand.

## 3. Intuition
If we have two pointers, `slow` and `fast`, we can create a gap of `n` nodes between them. If we advance `fast` by `n` steps first, and then advance both `slow` and `fast` together at the same speed, when `fast` reaches the end of the list (i.e., `nullptr`), `slow` will be pointing exactly `n` nodes behind, which is the node just before the one we want to delete.

## 4. Brute Force
Approach:
1. Make a first pass through the list to count the total number of nodes, say `L`.
2. Calculate the position from the start: the node to remove is the `(L - n + 1)`-th node.
3. Make a second pass from the head, stopping at the `(L - n)`-th node, and adjust its `next` pointer to skip the next node.

Time: O(N)
Space: O(1)

## 5. Optimized Approach
1. Use a `dummy` node pointing to the head. This handles cases where we have to delete the head node.
2. Initialize two pointers `slow` and `fast` to point to the `dummy` node.
3. Move the `fast` pointer `n + 1` steps forward, establishing a gap of size `n` between `slow` and `fast`.
4. Move both `slow` and `fast` pointers forward one node at a time until `fast` becomes `nullptr`.
5. At this point, `slow` points to the node *before* the target node.
6. Update `slow->next` to `slow->next->next` to delete the target node.
7. Return `dummy.next` as the new head.

## 6. Why does it work?
By placing `fast` ahead of `slow` by `n + 1` steps, the distance between them is `n + 1` nodes. When `fast` traverses past the last node (i.e. reaches `nullptr`), `slow` is exactly `n + 1` nodes away from the end, meaning `slow->next` is the n-th node from the end. Thus, updating `slow->next` to `slow->next->next` successfully deletes the target node.

## 7. C++ Code
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
ListNode* removeNthFromEnd(ListNode* head, int n) {
    ListNode dummy(0, head);
    ListNode* slow = &dummy;
    ListNode* fast = &dummy;
    
    // Advance fast pointer so there is a gap of n nodes between slow and fast
    for (int i = 0; i <= n; ++i) {
        fast = fast->next;
    }
    
    // Move fast to the end, maintaining the gap
    while (fast) {
        slow = slow->next;
        fast = fast->next;
    }
    
    // slow is now pointing to the node preceding the one to delete
    slow->next = slow->next->next;
    
    return dummy.next;
}
```

## 8. Dry Run
Input: `head = [1, 2, 3, 4, 5]`, `n = 2`

- `dummy` has value 0, `dummy.next` points to Node(1).
- `slow` = `&dummy`, `fast` = `&dummy`.
- **Step 1 (For-loop to advance fast by 3 steps since n=2)**:
  - `i = 0`: `fast` = Node(1)
  - `i = 1`: `fast` = Node(2)
  - `i = 2`: `fast` = Node(3)
- **Step 2 (While-loop while fast is not nullptr)**:
  - *Iteration 1*: `fast` = Node(4), `slow` = Node(1)
  - *Iteration 2*: `fast` = Node(5), `slow` = Node(2)
  - *Iteration 3*: `fast` = nullptr, `slow` = Node(3)
- **Step 3 (Deletion)**:
  - `slow->next` = `slow->next->next` -> Node(3)->next = Node(5) (Node 4 is removed).
- **Step 4**:
  - Return `dummy.next` -> Node(1) -> `[1, 2, 3, 5]`.

## 9. Complexity
Time: O(N) where N is the length of the linked list. The list is traversed exactly once.
Space: O(1) auxiliary space as we only use three pointers.

## 10. Edge Cases
- **List has only one node (`[1]`, `n=1`)**: `fast` becomes `nullptr` after the loop. `slow` stays at `dummy`. `slow->next` is updated to `nullptr`. Returns `nullptr`.
- **Deleting the head node (`[1, 2]`, `n=2`)**: `fast` moves to Node(2) then `nullptr`. `slow` stays at `dummy`. `slow->next` becomes Node(2). Returns Node(2).
- **`n` is equal to the length of the list**: Handled correctly by the dummy node logic.

## 11. Follow-up Questions
- Can we free the memory of the deleted node to prevent memory leaks?
- What happens if `n` is greater than the length of the list?
- How would you solve this if you could only traverse from right to left?

## 12. Interview Explanation
"First, I would clarify if `n` is guaranteed to be valid. Then I'd explain the brute force approach where we make a first pass to find the length and a second pass to remove the node. To optimize this to a single pass, I would use two pointers: a `fast` and a `slow` pointer, initialized to a `dummy` node. I will first move the `fast` pointer `n + 1` steps ahead, then move both pointers together. Once `fast` reaches the end, `slow` will point directly to the node before the one to be removed. My C++ code uses a dummy node to easily handle the case where the head itself needs to be removed."

---

# 4. Linked List Cycle (LeetCode 141)

## 1. Pattern
Two Pointers / Floyd's Cycle-Finding Algorithm (Tortoise and Hare).

## 2. What is the interviewer asking?
We need to determine if a linked list contains a cycle (a loop where a node's `next` pointer points back to a previous node in the list).

## 3. Intuition
Think of two runners on a circular track. If one runs faster than the other, they will eventually meet. Similarly, if we have two pointers moving through the linked list—a slow pointer moving 1 step at a time, and a fast pointer moving 2 steps at a time—they will eventually point to the same node if a cycle exists. If there is no cycle, the fast pointer will reach the end (`nullptr`) first.

## 4. Brute Force
Approach:
1. Use a hash set to store the memory addresses of the nodes we visit as we traverse.
2. For each node, check if its address is already in the hash set.
3. If it is, there is a cycle. If we reach the end of the list without any duplicate address, there is no cycle.

Time: O(N)
Space: O(N)

## 5. Optimized Approach
1. Initialize two pointers `slow` and `fast` to the head of the list.
2. Loop as long as `fast` and `fast->next` are not `nullptr`.
3. In each iteration:
   - Move `slow` pointer one step: `slow = slow->next;`
   - Move `fast` pointer two steps: `fast = fast->next->next;`
   - Check if `slow == fast`. If they meet, return `true` (cycle detected).
4. If the loop terminates because `fast` or `fast->next` becomes null, return `false` (no cycle).

## 6. Why does it work?
If there is a cycle, once both pointers enter the loop, the distance between them decreases by 1 node at each step (since `fast` moves 2 steps while `slow` moves 1). Because the distance decreases by 1 in each iteration, the distance must eventually become 0, meaning they will inevitably meet. If there is no cycle, the fast pointer will reach the end of the list in O(N) time.

## 7. C++ Code
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
bool hasCycle(ListNode* head) {
    ListNode* slow = head;
    ListNode* fast = head;
    
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        
        if (slow == fast) return true;
    }
    return false;
}
```

## 8. Dry Run
Input: `head = [3, 2, 0, -4]`, cycle at index 1 (node with value 2)

Initially: `slow` = Node(3), `fast` = Node(3).

- **Step 1 (First Iteration)**:
  - `slow` = Node(2)
  - `fast` = Node(0) (skips from 3 -> 2 -> 0)
  - `slow != fast`

- **Step 2 (Second Iteration)**:
  - `slow` = Node(0)
  - `fast` = Node(2) (skips from 0 -> -4 -> 2, since -4 points back to 2)
  - `slow != fast`

- **Step 3 (Third Iteration)**:
  - `slow` = Node(-4)
  - `fast` = Node(-4) (skips from 2 -> 0 -> -4)
  - `slow == fast` -> Returns `true`.

## 9. Complexity
Time: O(N) where N is the number of nodes. If there is no cycle, `fast` reaches the end in N/2 steps. If there is a cycle, the pointers will meet within the first full traversal of the cycle.
Space: O(1) auxiliary space as we only use two pointers.

## 10. Edge Cases
- **Empty list (`head == nullptr`)**: `fast` is null, loop doesn't run, returns `false`.
- **Single node with no cycle (`[1]`)**: `fast->next` is null, loop doesn't run, returns `false`.
- **Single node pointing to itself (`[1]` -> loop to itself)**: `slow` and `fast` will meet on first iteration, returns `true`.

## 11. Follow-up Questions
- How do you find the starting node of the cycle?
- Can we solve this by modifying node values?
- How does Floyd's algorithm behave in a list with a cycle of size 1?

## 12. Interview Explanation
"First, I would clarify if we can modify the list structure or if we should treat it as read-only. Then I'd explain the brute force approach where we can use a hash set to track visited nodes, taking O(N) space. To optimize the space to O(1), I would use Floyd's Cycle-Finding Algorithm. We initialize two pointers at the head: one moves at single speed, and the other at double speed. If there is a cycle, they will eventually meet. If the fast pointer reaches the end, we know there is no cycle. My C++ code implements this logic in O(N) time and O(1) space."

---

# 5. Add Two Numbers (LeetCode 2)

## 1. Pattern
Math / Elementary Addition.

## 2. What is the interviewer asking?
We are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order, and each of their nodes contains a single digit. We need to add the two numbers and return the sum as a linked list in the same reverse format.

## 3. Intuition
We can perform digit-by-digit addition starting from the head (which represents the least significant digit). This is exactly how we do manual addition: add the digits from both lists, add any carry from the previous step, calculate the new carry for the next step, and create a new node with the current digit (sum % 10).

## 4. Brute Force
Approach:
1. Convert both linked lists to actual integers by traversing them and reconstructing the numbers.
2. Add the two integers.
3. Convert the sum back to a linked list.
*Critique*: This fails for large inputs because integers can easily overflow standard data types (like `long long`).

Time: O(N)
Space: O(N)

## 5. Optimized Approach
1. Initialize a `dummy` node to build the result list.
2. Keep a `current` pointer to traverse and build the new list, starting at the `dummy` node.
3. Keep a variable `carry` initialized to 0.
4. Loop as long as `l1` is not null, `l2` is not null, or `carry` is not 0:
   - Set `sum` to `carry`.
   - If `l1` is not null, add `l1->val` to `sum` and advance `l1`.
   - If `l2` is not null, add `l2->val` to `sum` and advance `l2`.
   - Update `carry = sum / 10`.
   - Create a new node with value `sum % 10` and link it to `current->next`.
   - Move `current` to `current->next`.
5. Return `dummy.next`.

## 6. Why does it work?
It works because it models the mathematical process of long addition. By traversing both lists simultaneously from head to tail, we add digits of the same place value along with the carry. The condition `while (l1 || l2 || carry)` ensures that if one list is longer or if there is a remaining carry at the end, we still process it and append the correct final nodes.

## 7. C++ Code
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
    ListNode dummy;
    ListNode* current = &dummy;
    int carry = 0;
    while (l1 || l2 || carry) {
        int sum = carry;
        if (l1) { sum += l1->val; l1 = l1->next; }
        if (l2) { sum += l2->val; l2 = l2->next; }
        carry = sum / 10;
        current->next = new ListNode(sum % 10);
        current = current->next;
    }
    return dummy.next;
}
```

## 8. Dry Run
Input: `l1 = [2, 4, 3]`, `l2 = [5, 6, 4]`

- **Step 1 (First Iteration)**:
  - `sum = carry(0) + l1->val(2) + l2->val(5) = 7`.
  - `carry = 7 / 10 = 0`.
  - Create `ListNode(7)`. `current->next` points to Node(7).
  - Move pointers: `l1 = [4, 3]`, `l2 = [6, 4]`, `current` points to Node(7).

- **Step 2 (Second Iteration)**:
  - `sum = carry(0) + l1->val(4) + l2->val(6) = 10`.
  - `carry = 10 / 10 = 1`.
  - Create `ListNode(0)`. `current->next` points to Node(0).
  - Move pointers: `l1 = [3]`, `l2 = [4]`, `current` points to Node(0).

- **Step 3 (Third Iteration)**:
  - `sum = carry(1) + l1->val(3) + l2->val(4) = 8`.
  - `carry = 8 / 10 = 0`.
  - Create `ListNode(8)`. `current->next` points to Node(8).
  - Move pointers: `l1 = nullptr`, `l2 = nullptr`, `current` points to Node(8).

- **Step 4**:
  - Loop terminates. Return `dummy.next` -> `[7, 0, 8]`.

## 9. Complexity
Time: O(max(N, M)) where N and M are the lengths of `l1` and `l2` respectively. We traverse both lists at most once.
Space: O(max(N, M)) to store the newly created list representing the sum.

## 10. Edge Cases
- **Lists of different lengths (e.g. `[9, 9] + [1]`)**: Handled correctly because the loop continues and treats the shorter list's missing nodes as 0.
- **Final carry remaining (e.g. `[5] + [5]`)**: Sum is 10, carry is 1. After list traversal is complete, the loop runs one more time for the `carry` and appends Node(1).
- **Lists contain 0 (e.g., `[0] + [0]`)**: Loop runs once, creates Node(0), and returns it.

## 11. Follow-up Questions
- What if the digits are stored in non-reversed order?
- How would you handle large numbers in a real-world software system?
- Can we reuse the nodes of `l1` or `l2` to achieve O(1) auxiliary space?

## 12. Interview Explanation
"First, I would clarify if the inputs could contain negative numbers or if we need to consider integer overflow. I would explain that we can simulate digit-by-digit addition starting from the head, carrying over the tens digit. We can iterate through both lists using a loop, keep track of a `carry` variable, and build the resulting list node-by-node. My C++ code uses a `dummy` node to simplify list building and handles uneven list lengths and trailing carries seamlessly in O(max(N, M)) time."

---

# 6. Intersection of Two Linked Lists (LeetCode 160)

## 1. Pattern
Two Pointers / Distance Alignment.

## 2. What is the interviewer asking?
Given the heads of two singly linked lists `headA` and `headB`, return the node at which the two lists intersect. If the two linked lists have no intersection, return `nullptr`. The intersection must be based on reference equality, not just value.

## 3. Intuition
If the two lists have different lengths, they have different distances to the intersection point. If we could align their starts so they have the same distance to the intersection point, we could traverse them at the same speed and they would meet at the intersection. By switching pointers to the head of the *other* list when they reach the end, both pointers will eventually traverse exactly `Len(A) + Len(B)` nodes, aligning them perfectly.

## 4. Brute Force
Approach:
1. For each node in list A, traverse the entire list B to see if there is any matching node.
2. Alternatively, traverse list A and store all node pointers in a hash set. Then traverse list B and check if any node exists in the hash set.

Time: O(N * M) (for nested loops), or O(N + M) (for hash set)
Space: O(1) (nested loops), or O(N) (hash set)

## 5. Optimized Approach
1. Initialize two pointers `a` at `headA` and `b` at `headB`.
2. Traverse the lists. In each step:
   - If pointer `a` is not null, move it to `a->next`. Otherwise, redirect it to `headB`.
   - If pointer `b` is not null, move it to `b->next`. Otherwise, redirect it to `headA`.
3. If they intersect, they will meet at the intersection node. If they do not intersect, they will both reach `nullptr` at the same time and the loop will terminate (since `a == b == nullptr`).
4. Return pointer `a`.

## 6. Why does it work?
Let the length of list A before the intersection be $a$, list B before the intersection be $b$, and the common suffix length be $c$.
- Pointer `a` travels path $a + c$ then switches to `headB`, traveling $b$ to reach the intersection. Total traveled: $a + c + b$.
- Pointer `b` travels path $b + c$ then switches to `headA`, traveling $a$ to reach the intersection. Total traveled: $b + c + a$.
Since $a + c + b = b + c + a$, they will reach the intersection point at the exact same step, meeting there. If there is no intersection ($c = 0$), they will both reach `nullptr` after traveling $a + b$ steps.

## 7. C++ Code
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
ListNode* getIntersectionNode(ListNode* headA, ListNode* headB) {
    ListNode* a = headA;
    ListNode* b = headB;
    while (a != b) {
        a = a ? a->next : headB;
        b = b ? b->next : headA;
    }
    return a;
}
```

## 8. Dry Run
Input:
List A: `1 -> 2 -> 3 -> 4`
List B: `5 -> 3 -> 4`
Intersection starts at Node(3).

- **Step 1**: `a` = Node(1), `b` = Node(5)
- **Step 2**: `a` = Node(2), `b` = Node(3)
- **Step 3**: `a` = Node(3), `b` = Node(4)
- **Step 4**: `a` = Node(4), `b` = nullptr
- **Step 5**: `a` = nullptr, `b` = Node(1) (redirected to `headA`)
- **Step 6**: `a` = Node(5) (redirected to `headB`), `b` = Node(2)
- **Step 7**: `a` = Node(3), `b` = Node(3)
- **Step 8**: Loop terminates since `a == b` (both point to Node(3)). Return Node(3).

## 9. Complexity
Time: O(N + M) where N and M are the lengths of the two lists. In the worst case, each pointer travels at most `N + M` nodes.
Space: O(1) auxiliary space as we only use two pointers.

## 10. Edge Cases
- **No intersection**: Pointers will both become `nullptr` and terminate the loop. Returns `nullptr`.
- **One or both lists empty**: Handled correctly as the loop condition `a != b` terminates immediately if both are null, or after 1 step.
- **Intersection is at the very first node**: The loop will terminate immediately on `a == b` if lengths are equal, or after redirecting if lengths differ.

## 11. Follow-up Questions
- Can we solve this by calculating the lengths of both lists first?
- Can we solve this if the lists have cycles?
- Does this algorithm modify the structure of the lists?

## 12. Interview Explanation
"First, I would clarify if we can modify the lists or if we must preserve their original structure. I'd mention the hash set approach which takes O(N) space. To optimize to O(1) space, I would use the two-pointer approach: we traverse both lists. When a pointer reaches the end, we redirect it to the head of the other list. This aligns the start positions by equalizing the total distance traveled. If there is an intersection, they will meet at that node; otherwise, they will meet at `nullptr`. My C++ code implements this logic cleanly in O(N + M) time and O(1) space."

---

# 7. Palindrome Linked List (LeetCode 234)

## 1. Pattern
Fast & Slow Pointer / Two Pointers / Linked List Reversal.

## 2. What is the interviewer asking?
We are given the head of a singly linked list. We need to determine if the list is a palindrome (reads the same forward and backward). The challenge is to achieve this in O(N) time and O(1) space.

## 3. Intuition
To check if a linked list is a palindrome in O(1) space, we can find the middle of the list, reverse the second half of the list, and then compare the first half with the reversed second half. If they match, the list is a palindrome. We can find the middle and reverse the first half on-the-fly using fast and slow pointers.

## 4. Brute Force
Approach:
1. Traverse the list and copy all values into an array/vector.
2. Use two pointers (start and end) on the array to check if the array is a palindrome.

Time: O(N)
Space: O(N)

## 5. Optimized Approach
1. Use a slow and fast pointer approach. Initialize `slow` and `fast` to `head`, and `prev` to `nullptr`.
2. Loop while `fast` and `fast->next` are not null:
   - Move `fast` by 2 steps: `fast = fast->next->next;`
   - Reverse the current `slow` node's link:
     ```cpp
     ListNode* temp = slow->next;
     slow->next = prev;
     prev = slow;
     slow = temp;
     ```
3. If `fast` is not null, the list has an odd number of elements. We skip the middle node by moving `slow` one step forward (`slow = slow->next`).
4. Now, `prev` is the head of the reversed first half, and `slow` is the head of the second half.
5. Compare the nodes of both halves one by one. If any values differ, return `false`.
6. If they match completely, return `true`.

## 6. Why does it work?
By using the fast pointer to move twice as fast as the slow pointer, the slow pointer reaches the exact midpoint when the fast pointer reaches the end. By reversing the links of the first half as the slow pointer moves, we don't need any extra space. Finally, comparing the reversed first half with the second half determines if they are symmetric.

## 7. C++ Code
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
bool isPalindrome(ListNode* head) {
    ListNode* slow = head;
    ListNode* fast = head;
    ListNode* prev = nullptr;
    
    // Find middle and reverse the first half on-the-fly
    while (fast && fast->next) {
        fast = fast->next->next;
        ListNode* temp = slow->next;
        slow->next = prev;
        prev = slow;
        slow = temp;
    }
    
    // If fast is not null, the list has an odd number of elements; skip middle
    if (fast) slow = slow->next;
    
    // Compare the reversed first half (prev) and the second half (slow)
    while (slow) {
        if (slow->val != prev->val) return false;
        slow = slow->next;
        prev = prev->next;
    }
    return true;
}
```

## 8. Dry Run
Input: `head = [1, 2, 2, 1]`

- **Step 1 (First Iteration)**:
  - `fast` moves to Node(2) (index 2).
  - `temp = slow->next` -> Node(2) (index 1).
  - `slow->next = prev` -> Node(1)->next = nullptr.
  - `prev = slow` -> Node(1).
  - `slow = temp` -> Node(2) (index 1).

- **Step 2 (Second Iteration)**:
  - `fast` moves to nullptr (past end).
  - `temp = slow->next` -> Node(2) (index 2).
  - `slow->next = prev` -> Node(2)->next = Node(1).
  - `prev = slow` -> Node(2) (index 1).
  - `slow = temp` -> Node(2) (index 2).

- **Step 3**:
  - Loop ends because `fast` is nullptr.
  - `fast` is nullptr, so we do not skip.
  - Compare `slow` (`[2, 1]`) and `prev` (`[2, 1]`):
    - *Iteration 1*: `slow->val`(2) == `prev->val`(2). `slow` = Node(1), `prev` = Node(1).
    - *Iteration 2*: `slow->val`(1) == `prev->val`(1). `slow` = nullptr, `prev` = nullptr.
  - Returns `true`.

## 9. Complexity
Time: O(N) where N is the number of nodes. We traverse the list to find the middle and reverse the first half, and then compare.
Space: O(1) auxiliary space as the reversal is done in place.

## 10. Edge Cases
- **Single node list (`[1]`)**: `fast->next` is null, loop doesn't run. `fast` is not null, so `slow` becomes `nullptr`. Second loop doesn't run. Returns `true`.
- **Two node list (`[1, 2]`)**: Loop runs once, `prev` becomes Node(1), `slow` becomes Node(2). Compare Node(2) and Node(1) -> returns `false`.
- **Three node list (`[1, 2, 1]`)**: Loop runs once. `fast` is Node(1). `prev` is Node(1), `slow` is Node(2). `fast` is not null, so `slow` becomes Node(1). Compare Node(1) and Node(1) -> returns `true`.

## 11. Follow-up Questions
- Can we restore the original list structure before returning?
- Can this be done recursively?
- Why is modifying the list in-place sometimes discouraged in production?

## 12. Interview Explanation
"First, I would clarify if we can modify the list temporarily or if it must remain read-only. I'd explain the brute force approach where we copy nodes to an array, taking O(N) space. To achieve O(1) auxiliary space, I would find the middle of the list using slow and fast pointers. While finding the middle, I reverse the first half of the list on-the-fly. Once the middle is reached, I adjust the pointer for odd-length lists, and then compare the reversed first half with the second half node by node. My C++ code implements this cleanly in O(N) time and O(1) space."

---

# 8. Reverse Nodes in k-Group (LeetCode 25)

## 1. Pattern
Recursion / In-place Sub-list Reversal.

## 2. What is the interviewer asking?
We are given the head of a linked list and an integer `k`. We need to reverse the nodes of the list `k` at a time and return its modified head. If the number of nodes is not a multiple of `k` then left-out nodes, in the end, should remain as they are.

## 3. Intuition
This is a generalization of the reverse linked list problem. We can solve it recursively or iteratively. For the recursive approach: first, we check if there are at least `k` nodes remaining. If not, we don't reverse them and return the head. If there are at least `k` nodes, we reverse those `k` nodes. After reversing, the original head becomes the tail of this reversed group, and we recursively call `reverseKGroup` on the remaining list and connect the returned head to `head->next`.

## 4. Brute Force
Approach:
1. Traverse the list and store node pointers in a list/vector.
2. For every group of size `k`, reverse their values or pointers in the vector.
3. Reconstruct the list links based on the modified order.

Time: O(N)
Space: O(N)

## 5. Optimized Approach
1. First, check if there are at least `k` nodes remaining. We use a pointer `temp` starting at `head` and advance it `k` times. If `temp` becomes `nullptr` before `k` steps, return `head` directly.
2. If we have at least `k` nodes, we perform a standard linked list reversal for the next `k` nodes:
   - Keep pointers `prev = nullptr`, `current = head`, and `next = nullptr`.
   - In a loop running `k` times:
     ```cpp
     next = current->next;
     current->next = prev;
     prev = current;
     current = next;
     ```
3. After reversing `k` nodes, `head` (which was the first node of this group) is now the tail of the reversed group.
4. Set `head->next = reverseKGroup(current, k);` to link the tail of the current group to the head of the reversed remaining list.
5. Return `prev` (which is the new head of the reversed group).

## 6. Why does it work?
The recursive definition is extremely elegant. The base case ensures that if there are fewer than `k` nodes left, they are left untouched. For a group of `k` nodes, the local reversal reverses their pointers correctly. The recursive call then processes the rest of the list, returning the head of the next group, which is linked directly to the tail of our current group (`head->next`). This guarantees that all reversed groups are correctly chained together.

## 7. C++ Code
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
ListNode* reverseKGroup(ListNode* head, int k) {
    ListNode* temp = head;
    
    // Check if there are at least k nodes left in the list
    for (int i = 0; i < k; ++i) {
        if (!temp) return head; // Fewer than k nodes, keep them as is
        temp = temp->next;
    }
    
    // Reverse the first k nodes of the list
    ListNode* prev = nullptr;
    ListNode* current = head;
    ListNode* next = nullptr;
    
    for (int i = 0; i < k; ++i) {
        next = current->next;
        current->next = prev;
        prev = current;
        current = next;
    }
    
    // Recurse for the remaining nodes and connect the head of the reversed group
    // (which is now the tail) to the result of the recursion
    head->next = reverseKGroup(current, k);
    
    return prev;
}
```

## 8. Dry Run
Input: `head = [1, 2, 3, 4, 5]`, `k = 2`

- **Call 1: `reverseKGroup([1, 2, 3, 4, 5], 2)`**
  - `temp` checks for 2 nodes: Node(1) -> Node(2) -> Node(3). Yes, at least 2 nodes exist.
  - Reverse first 2 nodes:
    - `prev` becomes Node(2), `current` becomes Node(3).
    - Connection is reversed: Node(2)->next = Node(1)->next = nullptr.
  - `head` (Node(1))->next = `reverseKGroup(Node(3), 2)`.

- **Call 2: `reverseKGroup([3, 4, 5], 2)`**
  - `temp` checks for 2 nodes: Node(3) -> Node(4) -> Node(5). Yes, at least 2 nodes exist.
  - Reverse first 2 nodes:
    - `prev` becomes Node(4), `current` becomes Node(5).
    - Connection is reversed: Node(4)->next = Node(3)->next = nullptr.
  - `head` (Node(3))->next = `reverseKGroup(Node(5), 2)`.

- **Call 3: `reverseKGroup([5], 2)`**
  - `temp` checks for 2 nodes: Node(5) -> nullptr. Fewer than 2 nodes!
  - Returns `head` (Node(5)).

- **Backtracking Call 2**: Node(3)->next = Node(5). Returns Node(4) -> `[3, 5]`.
- **Backtracking Call 1**: Node(1)->next = Node(4). Returns Node(2) -> `[1, 4, 3, 5]`.
- *Final returned list*: `[2, 1, 4, 3, 5]`.

## 9. Complexity
Time: O(N) where N is the number of nodes in the list. Each node is visited at most twice (once for counting, once for reversing).
Space: O(N/k) recursive stack space due to the depth of recursion.

## 10. Edge Cases
- **`k = 1`**: The list remains unchanged because reversing groups of size 1 does nothing. Handled correctly.
- **`k` greater than length of list**: Returns `head` immediately.
- **Length of list is a multiple of `k`**: The final recursive call gets `nullptr` and returns `nullptr` immediately.

## 11. Follow-up Questions
- Can we implement this in O(1) space iteratively?
- What happens if the final incomplete group needs to be reversed too?
- Can we optimize the counting of `k` nodes?

## 12. Interview Explanation
"First, I would clarify what we should do if the last group has fewer than `k` nodes. I would explain a recursive approach: for each group, we check if there are at least `k` nodes left. If not, we return the head. If so, we reverse the first `k` nodes iteratively. The tail of this reversed group (which was the original head) is then linked to the result of the recursive call on the remaining list. My C++ code implements this elegant recursive pattern, running in O(N) time and using O(N/k) stack space."
