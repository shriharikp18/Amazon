# Section 6: Trees

# 1. Maximum Depth of Binary Tree (LeetCode 104)

## 1. Pattern
Tree Depth-First Search (DFS) / Recursion / Post-order Traversal.

## 2. What is the interviewer asking?
The interviewer wants to find the length of the longest path from the root node of a binary tree down to the farthest leaf node. The height (or depth) is calculated as the number of nodes along this path. If the tree is empty (contains zero nodes), its depth is 0.

## 3. Intuition
The maximum depth of a binary tree is determined by the maximum depth of its subtrees. Specifically, the depth at any node is $1 + \max(\text{depth of left child}, \text{depth of right child})$. This defines a recursive relationship where the solution for the parent node is computed using the results of its children (bottom-up/post-order processing).

## 4. Brute Force
Approach:
We can solve this iteratively by performing a Breadth-First Search (BFS) / Level Order Traversal. We use a queue to traverse the tree level by level. In each iteration of the level-by-level traversal, we increment our depth counter, dequeue all nodes at the current level, and enqueue their children for the next level. The traversal continues until the queue is empty, at which point the depth counter represents the maximum depth.

Time: O(N) where N is the total number of nodes in the binary tree, as we visit each node exactly once.
Space: O(N) because, in the worst case of a fully balanced binary tree, the last level contains approximately N/2 leaf nodes, which must be stored in the queue.

## 5. Optimized Approach
Approach:
Use a recursive Depth-First Search (DFS) post-order traversal to compute the height of the tree.
1. If the current node (`root`) is `nullptr` (base case), return `0` because an empty tree has a depth of 0.
2. Recursively calculate the maximum depth of the left subtree by calling `maxDepth(root->left)`.
3. Recursively calculate the maximum depth of the right subtree by calling `maxDepth(root->right)`.
4. The depth of the current node is the maximum of the left and right subtree depths plus 1 (to account for the current node itself). Return `1 + max(leftDepth, rightDepth)`.

## 6. Why does it work?
The recursive approach works because it computes the depth of the tree from the bottom up. By definition, a leaf node's children are null, and the base case returns 0 for them. The leaf node itself then correctly evaluates to $1 + \max(0, 0) = 1$. This value is passed up to its parent, which performs the same calculation. By taking the maximum height between the left and right subtrees at each node, we guarantee that we are tracking the longest path to a leaf, and adding 1 accounts for the step from child to parent.

## 7. C++ Code
```cpp
#include <algorithm>
using namespace std;

// Definition for a binary tree node.
// struct TreeNode {
//     int val;
//     TreeNode *left;
//     TreeNode *right;
//     TreeNode() : val(0), left(nullptr), right(nullptr) {}
//     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
//     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
// };

class Solution {
public:
    int maxDepth(TreeNode* root) {
        // Base case: If the current node is null, the depth is 0
        if (!root) {
            return 0;
        }
        
        // Recursively compute the depth of the left and right subtrees
        int leftHeight = maxDepth(root->left);
        int rightHeight = maxDepth(root->right);
        
        // The maximum depth of the current node is 1 plus the maximum of its children's depths
        return 1 + max(leftHeight, rightHeight);
    }
};
```

## 8. Dry Run
Input: root = [3, 9, 20, null, null, 15, 7]
Tree Structure:
      3
     / \
    9  20
      /  \
     15   7

Step-by-step:
1. `maxDepth(node(3))` is called. Since `node(3)` is not null:
   - It calls `maxDepth(node(9))` (left child).
2. `maxDepth(node(9))` is called. Since `node(9)` is not null:
   - It calls `maxDepth(nullptr)` (left child of 9) -> returns 0.
   - It calls `maxDepth(nullptr)` (right child of 9) -> returns 0.
   - Computes `1 + max(0, 0) = 1`. Returns 1.
3. `maxDepth(node(3))` now calls `maxDepth(node(20))` (right child).
4. `maxDepth(node(20))` is called. Since `node(20)` is not null:
   - It calls `maxDepth(node(15))` (left child).
5. `maxDepth(node(15))` is called. Since `node(15)` is not null:
   - It calls `maxDepth(nullptr)` (left child of 15) -> returns 0.
   - It calls `maxDepth(nullptr)` (right child of 15) -> returns 0.
   - Computes `1 + max(0, 0) = 1`. Returns 1.
6. `maxDepth(node(20))` now calls `maxDepth(node(7))` (right child).
7. `maxDepth(node(7))` is called. Since `node(7)` is not null:
   - It calls `maxDepth(nullptr)` (left child of 7) -> returns 0.
   - It calls `maxDepth(nullptr)` (right child of 7) -> returns 0.
   - Computes `1 + max(0, 0) = 1`. Returns 1.
8. `maxDepth(node(20))` finishes: computes `1 + max(1, 1) = 2`. Returns 2.
9. `maxDepth(node(3))` finishes: computes `1 + max(1, 2) = 3`. Returns 3.
Final Result: 3

## 9. Complexity
Time: O(N) where N is the number of nodes in the binary tree. Every node is visited exactly once.
Space: O(H) where H is the height of the tree. This is the maximum memory allocated on the stack for recursion. In the worst case (skewed tree), H = O(N). In the best case (balanced tree), H = O(log N).

## 10. Edge Cases
- **Empty Tree (`root == nullptr`)**: The base case handles this and correctly returns 0.
- **Single Node**: The recursion returns 0 for both children and evaluates to `1 + 0 = 1`.
- **Left-Skewed or Right-Skewed Tree**: All nodes lie on a single path (like a linked list). The algorithm correctly processes this with O(N) call stack usage.

## 11. Follow-up Questions
- **How would you solve this iteratively?**
  Using BFS with a queue (as described in the brute force section) or iterative DFS using a stack of pairs `std::stack<std::pair<TreeNode*, int>>` containing the node and its current depth.
- **What happens if the tree has millions of nodes and causes stack overflow?**
  We should use the iterative BFS method to avoid system call stack overflow, as it stores nodes in heap memory (via standard library collections) rather than call stack memory.
- **How is this different from finding the minimum depth?**
  In minimum depth, if a node has only one child, we cannot simply return `1 + min(left, right)` because that would select the empty child (depth 0) instead of traversing down the active path to a leaf node.

## 12. Interview Explanation
"First, I would clarify the constraints of the problem, such as whether we could have an empty tree or if there are recursion stack limitations. Then, I'd explain the brute-force/alternative approach using a level-order traversal (BFS) with a queue, which calculates the depth by counting the levels of the tree. To optimize both code length and readability, I would use a recursive DFS approach. The core idea is that the height of any node is one plus the maximum height of its left and right subtrees. My C++ code would check for the base case where a node is null (returning 0), make recursive calls to obtain the heights of the subtrees, and return 1 plus their maximum. This runs in O(N) time and uses O(H) space."

---

# 2. Same Tree (LeetCode 100)

## 1. Pattern
Tree Depth-First Search (DFS) / Recursion.

## 2. What is the interviewer asking?
Given two binary trees `p` and `q`, write a function to check if they are identical. Two binary trees are considered identical if they are structurally identical and the nodes have the same values at each position.

## 3. Intuition
For two trees to be identical, their current root nodes must have the same value, and their left subtrees must be identical, and their right subtrees must be identical. If we verify this recursively for every node pair, we can determine equivalence.

## 4. Brute Force
Approach:
We can serialize both trees into string representations (using a traversal like preorder, including a special character like `#` for null nodes) and then compare if the two serialized strings are identical.
For example, a tree with root 1 and left child 2 would be serialized as `1,2,#,#,#`.
Time: O(N + M) where N and M are the number of nodes in the two trees.
Space: O(N + M) to store the serialized string copies.

## 5. Optimized Approach
Approach:
A direct, in-place recursive DFS comparing nodes side-by-side.
1. If both nodes `p` and `q` are null (`nullptr`), return `true` (base case: empty trees are identical).
2. If only one of the nodes is null, or if their values do not match (`p->val != q->val`), return `false` (base case: mismatch in structure or value).
3. Recursively check if the left subtrees match: `isSameTree(p->left, q->left)`.
4. Recursively check if the right subtrees match: `isSameTree(p->right, q->right)`.
5. Return the logical AND (`&&`) of both recursive checks.

## 6. Why does it work?
This recursive check is structurally complete and minimal. It enforces three conditions at every level:
- Value equality: `p->val == q->val`
- Left child equality: `isSameTree(p->left, q->left)`
- Right child equality: `isSameTree(p->right, q->right)`
By returning `false` early on any structural mismatch (one node null, other not) or value mismatch, we short-circuit the recursion and immediately propagate the failure. If the recursion successfully reaches the null leaf boundaries for all paths, the trees are confirmed identical.

## 7. C++ Code
```cpp
// Definition for a binary tree node.
// struct TreeNode {
//     int val;
//     TreeNode *left;
//     TreeNode *right;
//     TreeNode() : val(0), left(nullptr), right(nullptr) {}
//     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
//     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
// };

class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        // Base case: If both nodes are null, they are identical
        if (!p && !q) {
            return true;
        }
        
        // If only one node is null, or their values differ, they are not identical
        if (!p || !q || p->val != q->val) {
            return false;
        }
        
        // Recursively check the left and right subtrees
        return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
    }
};
```

## 8. Dry Run
Input: p = [1, 2, 3], q = [1, 2, 3]
Tree p:      Tree q:
    1            1
   / \          / \
  2   3        2   3

Step-by-step:
1. `isSameTree(p, q)` is called where `p` is node(1) and `q` is node(1):
   - Neither is null, and their values match (1 == 1).
   - Calls `isSameTree(p->left, q->left)` -> `isSameTree(node(2), node(2))`.
2. `isSameTree(node(2), node(2))` is called:
   - Neither is null, and their values match (2 == 2).
   - Calls `isSameTree(nullptr, nullptr)` (left children) -> returns `true`.
   - Calls `isSameTree(nullptr, nullptr)` (right children) -> returns `true`.
   - Since both return true, `isSameTree(node(2), node(2))` returns `true && true = true`.
3. Back to root, calls `isSameTree(p->right, q->right)` -> `isSameTree(node(3), node(3))`.
4. `isSameTree(node(3), node(3))` is called:
   - Neither is null, and their values match (3 == 3).
   - Calls `isSameTree(nullptr, nullptr)` (left children) -> returns `true`.
   - Calls `isSameTree(nullptr, nullptr)` (right children) -> returns `true`.
   - Since both return true, `isSameTree(node(3), node(3))` returns `true`.
5. Back to root, returns `true && true = true`.
Final Result: true

## 9. Complexity
Time: O(N) where N is the total number of nodes in the smaller of the two trees. We visit each node pair at most once.
Space: O(H) where H is the height of the taller tree, representing the recursion stack. In a skewed tree, this is O(N); in a balanced tree, O(log N).

## 10. Edge Cases
- **Both Trees Empty (`p == nullptr && q == nullptr`)**: First condition returns `true`.
- **One Tree Empty, One Non-Empty**: The second condition checks `!p || !q` and returns `false`.
- **Different Structures**: If `p` has a left child but `q` does not, the recursion compares the left child of `p` with `nullptr` and returns `false`.

## 11. Follow-up Questions
- **How would you solve this iteratively?**
  Use a queue or stack, pushing pairs of nodes `[p, q]`. Pop pairs, compare them, and if valid, push their left children `[p->left, q->left]` and right children `[p->right, q->right]`.
- **Can we optimize this for very large trees where nodes might match but are deep?**
  Yes, we can perform checks on subtree properties (like node count or hash values) before performing full recursion, or traverse nodes in parallel using multi-threading for large-scale structural validation.
- **What is the relation between this and checking if a tree is symmetric?**
  Symmetric tree is a variation of the same tree where we check if a tree is a mirror image of itself. Instead of comparing `p->left` with `q->left`, we compare `p->left` with `q->right`.

## 12. Interview Explanation
"First, I would clarify if the nodes contain values that require special comparison logic. Then, I'd explain the brute force approach of serializing both trees and comparing their strings. To optimize, I would use recursive DFS, which performs checks in place without extra string memory. Two trees are the same if their current root values are equal and their left and right subtrees are identical. My C++ code handles this recursively: checking if both nodes are null (returning true), if only one is null or values differ (returning false), and otherwise returning the conjunction of recursive calls on their left and right children."

---

# 3. Symmetric Tree (LeetCode 101)

## 1. Pattern
Tree Depth-First Search (DFS) / Recursion / Mirror Traversal.

## 2. What is the interviewer asking?
Given the root of a binary tree, check whether it is a mirror image of itself (i.e., symmetric around its center).

## 3. Intuition
A binary tree is symmetric if its left subtree is a mirror image of its right subtree. Two subtrees are mirror images of each other if:
1. Their roots have the same value.
2. The left child of the left subtree is identical to the right child of the right subtree.
3. The right child of the left subtree is identical to the left child of the right subtree.

## 4. Brute Force
Approach:
We can copy the entire binary tree and invert the copy (swap the left and right children of every node). Then, we use the `isSameTree` function to compare the original tree with the inverted copy.
Time: O(N) to copy, O(N) to invert, and O(N) to compare. Total time: O(N).
Space: O(N) to store the copy of the tree in memory.

## 5. Optimized Approach
Approach:
Avoid copying the tree by checking for symmetry in place using a recursive helper function `isMirror(TreeNode* t1, TreeNode* t2)`.
1. The main function `isSymmetric(root)` returns `isMirror(root, root)` or `isMirror(root->left, root->right)`.
2. Inside `isMirror(t1, t2)`:
   - If both `t1` and `t2` are `nullptr`, return `true` (base case: both paths reached null end).
   - If only one of them is null, or if their values differ (`t1->val != t2->val`), return `false`.
   - Recursively verify outer children: `isMirror(t1->left, t2->right)`.
   - Recursively verify inner children: `isMirror(t1->right, t2->left)`.
   - Return the logical AND (`&&`) of both checks.

## 6. Why does it work?
Folding a tree in half means that the left subtree's left boundary must match the right subtree's right boundary, and the left subtree's right boundary must match the right subtree's left boundary. The recursive helper `isMirror` verifies this alignment precisely by cross-comparing `t1->left` with `t2->right` and `t1->right` with `t2->left` while verifying value equality at each step.

## 7. C++ Code
```cpp
// Definition for a binary tree node.
// struct TreeNode {
//     int val;
//     TreeNode *left;
//     TreeNode *right;
//     TreeNode() : val(0), left(nullptr), right(nullptr) {}
//     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
//     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
// };

class Solution {
private:
    // Helper function to check if two subtrees are mirror images of each other
    bool isMirror(TreeNode* t1, TreeNode* t2) {
        // Base case: If both nodes are null, they are mirrors
        if (!t1 && !t2) {
            return true;
        }
        
        // If only one node is null, or if their values differ, they cannot be mirrors
        if (!t1 || !t2 || t1->val != t2->val) {
            return false;
        }
        
        // Check outer subtrees (left of t1, right of t2) AND inner subtrees (right of t1, left of t2)
        return isMirror(t1->left, t2->right) && isMirror(t1->right, t2->left);
    }

public:
    bool isSymmetric(TreeNode* root) {
        // An empty tree is symmetric
        if (!root) {
            return true;
        }
        
        // Check if the left and right subtrees are mirror images
        return isMirror(root->left, root->right);
    }
};
```

## 8. Dry Run
Input: root = [1, 2, 2, 3, 4, 4, 3]
Tree Structure:
        1
       / \
      2   2
     / \ / \
    3  4 4  3

Step-by-step:
1. `isSymmetric(node(1))` is called. `root` is not null.
   - Calls `isMirror(node(2_left), node(2_right))` (left and right children of root).
2. `isMirror(node(2_left), node(2_right))` is called:
   - Neither node is null, and their values are equal (2 == 2).
   - Calls `isMirror(node(2_left)->left, node(2_right)->right)` -> `isMirror(node(3_left), node(3_right))`.
3. `isMirror(node(3_left), node(3_right))` is called:
   - Neither node is null, and their values are equal (3 == 3).
   - Calls `isMirror(nullptr, nullptr)` (left of 3_left, right of 3_right) -> returns `true`.
   - Calls `isMirror(nullptr, nullptr)` (right of 3_left, left of 3_right) -> returns `true`.
   - Returns `true && true = true`.
4. `isMirror(node(2_left), node(2_right))` now calls the second check:
   - `isMirror(node(2_left)->right, node(2_right)->left)` -> `isMirror(node(4_left), node(4_right))`.
5. `isMirror(node(4_left), node(4_right))` is called:
   - Neither node is null, and their values are equal (4 == 4).
   - Calls `isMirror(nullptr, nullptr)` -> returns `true`.
   - Calls `isMirror(nullptr, nullptr)` -> returns `true`.
   - Returns `true && true = true`.
6. `isMirror(node(2_left), node(2_right))` returns `true && true = true`.
7. `isSymmetric` returns `true`.
Final Result: true

## 9. Complexity
Time: O(N) where N is the number of nodes in the tree, because we traverse each node once.
Space: O(H) where H is the height of the tree, representing the call stack depth. Worst-case is O(N) for a skewed tree; average-case is O(log N) for a balanced tree.

## 10. Edge Cases
- **Empty Tree**: Handled at the beginning of `isSymmetric`, returning `true`.
- **Single Node**: The children are both null. `isMirror(nullptr, nullptr)` returns `true`.
- **Asymmetric Structure, Same Values**: e.g., root has left child 2 (no grandchildren) and right child 2 with a left grandchild. Structure mismatch is caught when `isMirror` receives a valid node and a `nullptr`, returning `false`.

## 11. Follow-up Questions
- **How would you solve this iteratively?**
  Use a queue (similar to BFS). Push the left child and right child of the root first. In each iteration, pop two nodes from the queue, say `n1` and `n2`. If both are null, continue. If one is null or values differ, return false. Otherwise, push their children in mirror order: `n1->left`, `n2->right`, and `n1->right`, `n2->left`.
- **Can you check if two different trees are mirrors of each other?**
  Yes, the helper `isMirror(tree1, tree2)` is already designed to compare two separate tree roots for mirror symmetry.
- **Does checking symmetry have a practical application?**
  Yes, it is useful in computer vision and graphic applications where structural symmetry in patterns or 3D meshes (represented as hierarchical trees) needs to be analyzed.

## 12. Interview Explanation
"First, I would clarify if an empty tree or a single node is symmetric by definition. Then, I'd explain the brute force approach where we clone the tree, reverse all its child pointers, and check if it is identical to the original tree. To optimize, I would perform an in-place mirror traversal. The main idea is that a tree is symmetric if its left subtree is a mirror image of its right subtree. I'll write a helper function `isMirror` that compares two nodes. They mirror each other if their values are equal, and the outer subtrees are mirrors of each other, and the inner subtrees are mirrors of each other. My C++ code implements this bottom-up check recursively."

---

# 4. Binary Tree Preorder Traversal (LeetCode 144)

## 1. Pattern
Tree Depth-First Search (DFS) / Iterative Traversal / Stack.

## 2. What is the interviewer asking?
Given the root of a binary tree, return the preorder traversal of its nodes' values. Preorder traversal visits nodes in the order: Root -> Left -> Right.

## 3. Intuition
To traverse the tree without using the system call stack (which recursive solutions use), we must use an explicit stack data structure. Preorder processes the root first. When we pop a node, we visit it and then push its children. Since a stack is LIFO (Last-In, First-Out) and we want to visit the Left child before the Right child, we must push the Right child onto the stack first, followed by the Left child.

## 4. Brute Force
Approach:
The simplest approach is recursive DFS:
1. Define a helper function `preorder(root, result)`.
2. If `root` is null, return.
3. Append `root->val` to `result`.
4. Call `preorder(root->left, result)`.
5. Call `preorder(root->right, result)`.
This is simple, but relies on the system recursion stack.
Time: O(N) to visit all nodes.
Space: O(H) representing the recursion call stack height.

## 5. Optimized Approach
Approach:
Iterative preorder traversal using a standard stack container (`std::stack`).
1. Initialize a vector `result` and a stack of node pointers `st`.
2. If `root` is not null, push it onto the stack.
3. While the stack is not empty:
   - Pop the top node `curr` from the stack.
   - Add `curr->val` to the `result` vector.
   - If `curr->right` is not null, push it onto the stack.
   - If `curr->left` is not null, push it onto the stack.
4. Return `result`.

## 6. Why does it work?
The iterative stack mimics the activation records of recursion. By pushing the right child first and the left child second, the left child sits on top of the stack. Therefore, in the very next loop iteration, the left child is popped and processed. This ensures that the entire left subtree is fully traversed before we back out and process the right subtree, maintaining the exact Root -> Left -> Right order.

## 7. C++ Code
```cpp
#include <vector>
#include <stack>
using namespace std;

// Definition for a binary tree node.
// struct TreeNode {
//     int val;
//     TreeNode *left;
//     TreeNode *right;
//     TreeNode() : val(0), left(nullptr), right(nullptr) {}
//     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
//     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
// };

class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        
        // Push the root node if it exists
        if (root) {
            st.push(root);
        }
        
        // Process nodes until stack is empty
        while (!st.empty()) {
            TreeNode* curr = st.top();
            st.pop();
            
            // Visit current node (Root)
            result.push_back(curr->val);
            
            // Push right child first (processed later)
            if (curr->right) {
                st.push(curr->right);
            }
            
            // Push left child last (processed next)
            if (curr->left) {
                st.push(curr->left);
            }
        }
        
        return result;
    }
};
```

## 8. Dry Run
Input: root = [1, null, 2, 3]
Tree Structure:
  1
   \
    2
   /
  3

Step-by-step:
1. `result = []`, `st = []`. Root is 1, so push 1. Stack: `[1]`.
2. **Iteration 1**:
   - Pop `curr = 1`. Stack: `[]`.
   - Append `1` to `result`. Result: `[1]`.
   - `curr->right` (node 2) is not null, push 2. Stack: `[2]`.
   - `curr->left` (null), nothing to push.
3. **Iteration 2**:
   - Pop `curr = 2`. Stack: `[]`.
   - Append `2` to `result`. Result: `[1, 2]`.
   - `curr->right` (null), nothing to push.
   - `curr->left` (node 3) is not null, push 3. Stack: `[3]`.
4. **Iteration 3**:
   - Pop `curr = 3`. Stack: `[]`.
   - Append `3` to `result`. Result: `[1, 2, 3]`.
   - Both children of 3 are null, nothing is pushed.
5. Loop terminates since `st` is empty.
Final Result: `[1, 2, 3]`

## 9. Complexity
Time: O(N) where N is the number of nodes in the binary tree. We visit each node exactly once.
Space: O(H) where H is the height of the tree. The stack stores at most the height of the tree in the worst case. For a skewed tree, it's O(N); for a balanced tree, O(log N).

## 10. Edge Cases
- **Empty Tree (`root == nullptr`)**: Handled correctly. The stack remains empty, loop doesn't execute, and an empty vector is returned.
- **Single Node**: Node is pushed, popped, visited, and since it has no children, the loop finishes.
- **Perfect Binary Tree**: The stack size will grow and shrink, peaking at the tree height `H`.

## 11. Follow-up Questions
- **How can we traverse preorder in O(1) auxiliary space?**
  Using Morris Preorder Traversal. It uses thread connections from predecessor nodes back to current nodes to navigate the tree without a stack.
- **When is Preorder traversal preferred over Inorder?**
  Preorder is preferred when cloning a tree or serializing a tree structure. When rebuilding a tree, we must create parent nodes before children.
- **How would you modify this to solve LeetCode 226 (Invert Binary Tree)?**
  Instead of pushing values to a vector, we pop each node, swap its left and right child pointers, and push the children to the stack to repeat the process.

## 12. Interview Explanation
"First, I would clarify if an iterative solution is required, as the recursive solution is trivial. I'd mention that the recursive solution takes implicit call-stack space. To implement the iterative solution, I would use an explicit stack. The core idea is that we pop a node, visit it, and then push its children. Because stacks are LIFO, we push the right child first and the left child second. This guarantees that the left child is popped next, maintaining the Preorder sequence. My C++ code uses a standard `std::stack` to run this DFS iteratively in O(N) time and O(H) space."

---

# 5. Binary Tree Inorder Traversal (LeetCode 94)

## 1. Pattern
Tree Depth-First Search (DFS) / Iterative Traversal / Stack.

## 2. What is the interviewer asking?
Given the root of a binary tree, return the inorder traversal of its nodes' values. Inorder traversal visits nodes in the order: Left -> Root -> Right.

## 3. Intuition
To traverse a tree in inorder (Left-Root-Right) iteratively, we must delay visiting any parent node until its entire left subtree has been traversed. Thus, we should traverse down the left pointers as far as possible, pushing each node onto our stack. When we cannot go left any further, we pop the top node from our stack, visit it, and then traverse its right child.

## 4. Brute Force
Approach:
Recursive Inorder DFS:
1. Define a helper `inorder(root, result)`.
2. If `root` is null, return.
3. Call `inorder(root->left, result)`.
4. Append `root->val` to `result`.
5. Call `inorder(root->right, result)`.
This uses the runtime call stack of size O(H).
Time: O(N)
Space: O(H)

## 5. Optimized Approach
Approach:
Iterative inorder traversal using an explicit stack of node pointers.
1. Initialize a vector `result`, a stack `st`, and a pointer `curr = root`.
2. Set up a loop that runs while `curr` is not null or the stack is not empty:
   - Within this loop, use an inner loop to push `curr` and move to `curr->left` until `curr` becomes null. (Go as deep left as possible).
   - Pop the top node from the stack and assign it to `curr`.
   - Visit the node by adding `curr->val` to `result`.
   - Move to the right child: `curr = curr->right`.
3. Return `result`.

## 6. Why does it work?
The stack stores the parent nodes while we search for the leftmost node. By going left until `curr` is null, we locate the leftmost unvisited node. Popping it from the stack visits it (Root). By setting `curr = curr->right` next, we move to its right subtree. If that subtree is null, we pop the next ancestor on the next iteration. This ensures the correct Left -> Root -> Right sequence.

## 7. C++ Code
```cpp
#include <vector>
#include <stack>
using namespace std;

// Definition for a binary tree node.
// struct TreeNode {
//     int val;
//     TreeNode *left;
//     TreeNode *right;
//     TreeNode() : val(0), left(nullptr), right(nullptr) {}
//     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
//     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
// };

class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        TreeNode* curr = root;
        
        // Loop runs as long as we have nodes to traverse or ancestors in the stack
        while (curr || !st.empty()) {
            // Traverse to the leftmost node of the current subtree
            while (curr) {
                st.push(curr);
                curr = curr->left;
            }
            
            // Pop the top node (deepest unvisited left node or parent)
            curr = st.top();
            st.pop();
            
            // Visit node (Root)
            result.push_back(curr->val);
            
            // Move to the right subtree (Right)
            curr = curr->right;
        }
        
        return result;
    }
};
```

## 8. Dry Run
Input: root = [1, null, 2, 3]
Tree Structure:
  1
   \
    2
   /
  3

Step-by-step:
1. `result = []`, `st = []`, `curr = node(1)`.
2. **Iteration 1**:
   - `curr` (1) is not null. Inner loop pushes 1 to `st` and sets `curr = curr->left` (null). Stack: `[1]`.
   - Pop `curr = 1`. Stack: `[]`.
   - Append `1` to `result`. Result: `[1]`.
   - Set `curr = curr->right` -> node(2).
3. **Iteration 2**:
   - `curr` (2) is not null. Inner loop:
     - Push 2. Stack: `[2]`. `curr = 2->left` (node 3).
     - Push 3. Stack: `[2, 3]`. `curr = 3->left` (null).
   - Pop `curr = 3`. Stack: `[2]`.
   - Append `3` to `result`. Result: `[1, 3]`.
   - Set `curr = curr->right` -> null.
4. **Iteration 3**:
   - `curr` is null, but `st` is not empty.
   - Pop `curr = 2`. Stack: `[]`.
   - Append `2` to `result`. Result: `[1, 3, 2]`.
   - Set `curr = curr->right` -> null.
5. Loop terminates as `curr` is null and `st` is empty.
Final Result: `[1, 3, 2]`

## 9. Complexity
Time: O(N) where N is the number of nodes. Each node is pushed onto the stack and popped from the stack exactly once.
Space: O(H) where H is the height of the tree. The stack stores at most the height of the tree in the worst case (skewed tree: O(N); balanced tree: O(log N)).

## 10. Edge Cases
- **Empty Tree (`root == nullptr`)**: Outer loop condition is false, returns empty vector immediately.
- **Right-Skewed Tree**: Inner loop only pushes 1 node at a time before popping, visiting, and moving right.
- **Left-Skewed Tree**: All nodes are pushed to the stack first, then popped in reverse order, which is the correct sorted sequence.

## 11. Follow-up Questions
- **What is the significance of Inorder traversal on a Binary Search Tree (BST)?**
  It traverses the keys of the BST in sorted ascending order.
- **How would you find the inorder successor of a node in a BST?**
  If the node has a right child, the successor is the leftmost node in its right subtree. Otherwise, it is the lowest ancestor that has this node in its left subtree.
- **Can we perform Inorder traversal in O(1) extra space?**
  Yes, using Morris Inorder Traversal, which links leaf nodes to their inorder successors temporarily.

## 12. Interview Explanation
"First, I would clarify if the tree is a standard binary tree or a Binary Search Tree. Then, I'd explain that the recursive inorder traversal is simple but consumes stack space. To write it iteratively, we can use an explicit stack. We traverse down to the leftmost leaf, pushing all parent nodes to the stack along the way. When we hit a null, we pop the top element, visit it, and move to its right child, repeating the process. My C++ code uses this standard nested loop structure to process Left, then Root, then Right."

---

# 6. Binary Tree Postorder Traversal (LeetCode 145)

## 1. Pattern
Tree Depth-First Search (DFS) / Iterative Traversal / Stack / Last Visited Node Tracker.

## 2. What is the interviewer asking?
Given the root of a binary tree, return the postorder traversal of its nodes' values. Postorder traversal visits nodes in the order: Left -> Right -> Root.

## 3. Intuition
Postorder traversal is the most challenging traversal to implement iteratively with a single stack. This is because we cannot visit the root node when we backtrack from its left child; we must visit its right child first. We need a way to distinguish whether we are returning from the left subtree or the right subtree. We can solve this by tracking the `lastVisited` node. If a node's right child is null, or it has already been visited (meaning `node->right == lastVisited`), we can safely visit the node and pop it.

## 4. Brute Force
Approach:
An easier alternative is to perform a modified preorder traversal (Root -> Right -> Left) and then reverse the final result to get (Left -> Right -> Root).
1. Perform preorder-like traversal: push root, and when popping, push left first and right second.
2. Reverse the resulting vector.
This requires an extra reversal step.
Time: O(N) for traversal + O(N) for reversal.
Space: O(H) stack space.

## 5. Optimized Approach
Approach:
One-pass iterative postorder traversal using a single stack and tracking the `lastVisited` node.
1. Initialize a vector `result`, a stack `st`, a pointer `curr = root`, and `lastVisited = nullptr`.
2. Loop while `curr` is not null or the stack is not empty:
   - If `curr` is not null, push `curr` to the stack and move to its left child: `curr = curr->left`.
   - If `curr` is null, inspect the top node of the stack: `node = st.top()`.
     - If `node->right` exists and `node->right != lastVisited`, then we haven't visited the right subtree yet. Set `curr = node->right` to traverse it.
     - Otherwise, both subtrees are processed. Pop `node`, append `node->val` to `result`, update `lastVisited = node`, and set `curr = nullptr` (so we process the next stack element in the next iteration).

## 6. Why does it work?
The stack holds ancestors in bottom-up order. By setting `curr = node->right` when the right child is unvisited, we defer the parent's visitation. When the right child is eventually processed, it becomes `lastVisited`. On the next iteration, the condition `node->right != lastVisited` is false. The code falls into the `else` block, popping and visiting the parent (Root). This ensures we process Left, then Right, then Root.

## 7. C++ Code
```cpp
#include <vector>
#include <stack>
using namespace std;

// Definition for a binary tree node.
// struct TreeNode {
//     int val;
//     TreeNode *left;
//     TreeNode *right;
//     TreeNode() : val(0), left(nullptr), right(nullptr) {}
//     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
//     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
// };

class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        TreeNode* lastVisited = nullptr;
        TreeNode* curr = root;
        
        while (curr || !st.empty()) {
            if (curr) {
                // Go left as far as possible
                st.push(curr);
                curr = curr->left;
            } else {
                TreeNode* node = st.top();
                // If right child exists and is not visited yet, traverse it first
                if (node->right && node->right != lastVisited) {
                    curr = node->right;
                } else {
                    // Visit parent (Root) since left and right subtrees are done
                    result.push_back(node->val);
                    lastVisited = node;
                    st.pop();
                }
            }
        }
        
        return result;
    }
};
```

## 8. Dry Run
Input: root = [1, null, 2, 3]
Tree Structure:
  1
   \
    2
   /
  3

Step-by-step:
1. `result = []`, `st = []`, `lastVisited = nullptr`, `curr = node(1)`.
2. **Iteration 1**: `curr` is not null. Push 1. Stack: `[1]`. `curr = 1->left` (null).
3. **Iteration 2**: `curr` is null. `node = st.top()` -> 1.
   - `node->right` (node 2) exists and `node->right != lastVisited` (2 != null).
   - Set `curr = 2`.
4. **Iteration 3**: `curr` (2) is not null. Push 2. Stack: `[1, 2]`. `curr = 2->left` (node 3).
5. **Iteration 4**: `curr` (3) is not null. Push 3. Stack: `[1, 2, 3]`. `curr = 3->left` (null).
6. **Iteration 5**: `curr` is null. `node = st.top()` -> 3.
   - `node->right` (null).
   - Else: `result.push_back(3)`. Result: `[3]`. `lastVisited = 3`. Pop 3. Stack: `[1, 2]`. `curr = null`.
7. **Iteration 6**: `curr` is null. `node = st.top()` -> 2.
   - `node->right` (null).
   - Else: `result.push_back(2)`. Result: `[3, 2]`. `lastVisited = 2`. Pop 2. Stack: `[1]`. `curr = null`.
8. **Iteration 7**: `curr` is null. `node = st.top()` -> 1.
   - `node->right` (2) exists, but `node->right == lastVisited` (2 == 2).
   - Else: `result.push_back(1)`. Result: `[3, 2, 1]`. `lastVisited = 1`. Pop 1. Stack: `[]`. `curr = null`.
9. Loop terminates since `curr` is null and `st` is empty.
Final Result: `[3, 2, 1]`

## 9. Complexity
Time: O(N) where N is the number of nodes in the tree. Each node is pushed once, and visited (popped) once.
Space: O(H) where H is the height of the tree. The stack stores at most the height of the tree in the worst case.

## 10. Edge Cases
- **Empty Tree**: Loop does not run, returns empty vector.
- **Single Node**: Pushed, popped immediately (since children are null), value recorded.
- **Symmetric/Skewed structures**: Handled correctly. In a right-skewed tree, it descends right, visits the leaf, and then pops back up.

## 11. Follow-up Questions
- **Why is postorder traversal commonly used to delete a tree?**
  Because you must delete the children nodes before you delete the parent node. Deleting the parent first would make the children unreachable.
- **What is the double stack iterative approach for postorder?**
  Use a first stack to do a modified preorder traversal (Root -> Right -> Left) and push the popped elements onto a second stack. Popping from the second stack gives the postorder traversal (Left -> Right -> Root).
- **Can we implement Postorder in O(1) space?**
  Yes, using Morris Postorder Traversal, which is more complex and involves temporary reversing of right boundaries.

## 12. Interview Explanation
"First, I would clarify if we can use the two-stack or modified-preorder-reversal trick. If they want a single-stack, single-pass solution, I'd explain using a stack accompanied by a `lastVisited` tracker. The tracker tells us if we just returned from traversing the right child. If we did, or if the right child is null, we visit the current node and pop it. Otherwise, we traverse down the right child. My C++ code uses this tracking logic to process Left, then Right, then Root."

---

### Question 7: Binary Tree Level Order Traversal (LeetCode 102)
# 7. Binary Tree Level Order Traversal (LeetCode 102)

## 1. Pattern
Tree Breadth-First Search (BFS) / Queue / Level-by-Level Partitioning.

## 2. What is the interviewer asking?
Given the root of a binary tree, return the level order traversal of its nodes' values. (i.e., from left to right, level by level).

## 3. Intuition
Level order traversal is equivalent to Breadth-First Search (BFS). To group the nodes of each level together in separate vectors, we can snapshot the size of our queue at the beginning of each level's processing. The size tells us exactly how many nodes exist on the current level. We process that exact number of nodes, adding their children to the queue for the next level.

## 4. Brute Force
Approach:
We can use a recursive DFS where we keep track of the depth of the current node.
1. Define a helper `dfs(root, depth, result)`.
2. If `root` is null, return.
3. If `depth == result.size()`, append a new empty vector to `result` to represent this new level.
4. Append `root->val` to `result[depth]`.
5. Call `dfs(root->left, depth + 1, result)`.
6. Call `dfs(root->right, depth + 1, result)`.
While recursive, this is an elegant alternative, though it uses O(H) stack memory.
Time: O(N)
Space: O(N) for output + O(H) call stack.

## 5. Optimized Approach
Approach:
Iterative BFS using a queue to process level-by-level.
1. If the `root` is null, return an empty 2D vector.
2. Initialize a queue of node pointers `q`, and push `root`.
3. Loop while `q` is not empty:
   - Get the number of nodes at the current level: `size = q.size()`.
   - Create a vector `level` to store values of this level.
   - For `i = 0` to `size - 1`:
     - Pop the front node `curr` from `q`.
     - Add `curr->val` to `level`.
     - If `curr->left` exists, push it to `q`.
     - If `curr->right` exists, push it to `q`.
   - Add `level` to the final `result` vector.
4. Return `result`.

## 6. Why does it work?
A queue operates on a First-In-First-Out (FIFO) basis. Thus, nodes at level $L$ are enqueued and dequeued before nodes at level $L+1$. By evaluating `size = q.size()` before starting the inner loop, we ensure that we only pop nodes that belong to the current level. The children of these nodes are appended to the back of the queue, making them part of the next level's processing.

## 7. C++ Code
```cpp
#include <vector>
#include <queue>
using namespace std;

// Definition for a binary tree node.
// struct TreeNode {
//     int val;
//     TreeNode *left;
//     TreeNode *right;
//     TreeNode() : val(0), left(nullptr), right(nullptr) {}
//     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
//     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
// };

class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> result;
        if (!root) {
            return result;
        }
        
        queue<TreeNode*> q;
        q.push(root);
        
        while (!q.empty()) {
            int size = q.size(); // Count of elements at the current level
            vector<int> level;
            
            for (int i = 0; i < size; ++i) {
                TreeNode* curr = q.front();
                q.pop();
                
                level.push_back(curr->val);
                
                // Enqueue children for the next level
                if (curr->left) {
                    q.push(curr->left);
                }
                if (curr->right) {
                    q.push(curr->right);
                }
            }
            
            result.push_back(level);
        }
        
        return result;
    }
};
```

## 8. Dry Run
Input: root = [3, 9, 20, null, null, 15, 7]
Tree Structure:
      3
     / \
    9  20
      /  \
     15   7

Step-by-step:
1. `result = []`, `q = [3]`.
2. **Level 1**:
   - `size = 1`. `level = []`.
   - Pop `curr = 3`. `level = [3]`.
   - Push 9 and 20. `q = [9, 20]`.
   - `result = [[3]]`.
3. **Level 2**:
   - `size = 2`. `level = []`.
   - `i = 0`: Pop `curr = 9`. `level = [9]`. (No children). `q = [20]`.
   - `i = 1`: Pop `curr = 20`. `level = [9, 20]`. Push 15 and 7. `q = [15, 7]`.
   - `result = [[3], [9, 20]]`.
4. **Level 3**:
   - `size = 2`. `level = []`.
   - `i = 0`: Pop `curr = 15`. `level = [15]`. (No children). `q = [7]`.
   - `i = 1`: Pop `curr = 7`. `level = [15, 7]`. (No children). `q = []`.
   - `result = [[3], [9, 20], [15, 7]]`.
5. Queue is empty. Loop terminates.
Final Result: `[[3], [9, 20], [15, 7]]`

## 9. Complexity
Time: O(N) where N is the number of nodes in the tree. We visit each node exactly once.
Space: O(N) because the queue must hold all nodes of the widest level. In a full binary tree, the last level contains N/2 nodes, which is O(N).

## 10. Edge Cases
- **Empty Tree**: The guard `if (!root)` handles this and returns an empty 2D vector.
- **Skewed Tree**: The queue size remains 1 at each level, returning a 2D vector where each sublist has 1 element.
- **Root only**: Returns `[[root->val]]`.

## 11. Follow-up Questions
- **How would you print the nodes level-by-level bottom-up?**
  Run the exact same level order BFS, and reverse the final `result` vector at the end, or use `std::reverse`.
- **How would you find the average of values at each level?**
  Inside the inner loop, sum up the node values, and after the loop completes, divide the sum by `size` to get the average, storing it in a vector of doubles.
- **Can we solve this using DFS?**
  Yes, as described in the brute force section. DFS with a depth parameter can construct the levels because we can access any sublist `result[depth]` directly.

## 12. Interview Explanation
"First, I would clarify if the output needs to be grouped by level or flat. If it must be grouped, I'd explain using a queue-based Breadth-First Search. The key optimization is to calculate the queue size at the beginning of each level. This size dictates how many elements we dequeue for the current level, ensuring children pushed during this step do not bleed into the current level. My C++ code uses a queue and a nested loop to implement this logic, resulting in O(N) time and O(N) space."

---

# 8. Balanced Binary Tree (LeetCode 110)

## 1. Pattern
Tree Depth-First Search (DFS) / Recursion / Post-order Bottom-up Evaluation.

## 2. What is the interviewer asking?
Given a binary tree, determine if it is height-balanced. A height-balanced binary tree is defined as a binary tree in which the left and right subtrees of every node differ in height by no more than 1.

## 3. Intuition
To know if the entire tree is balanced, we must check every node. A node is balanced if:
1. Its left subtree is balanced.
2. Its right subtree is balanced.
3. The absolute difference between the height of the left subtree and the right subtree is at most 1.
If we use a top-down approach, calculating the height of the subtrees at each node, we will visit the same descendants repeatedly, causing O(N^2) time complexity. We can optimize this by checking the height and balance status bottom-up in a single post-order traversal. If any subtree is unbalanced, we bubble up a sentinel value (e.g. `-1`) to signal failure immediately.

## 4. Brute Force
Approach:
Top-down check:
1. Write a helper function `getHeight(node)` that calculates the height of a tree in O(N).
2. In the main function `isBalanced(root)`:
   - If `root` is null, return true.
   - Calculate `leftHeight = getHeight(root->left)` and `rightHeight = getHeight(root->right)`.
   - If `abs(leftHeight - rightHeight) > 1`, return false.
   - Otherwise, return `isBalanced(root->left) && isBalanced(root->right)`.
Time: O(N^2) in the worst case (e.g. for a balanced tree, height is called for every single node, doing O(N) work per node).
Space: O(H) recursion stack.

## 5. Optimized Approach
Approach:
Bottom-up evaluation using a single post-order traversal:
1. Create a helper function `checkHeight(root)`.
2. If `root` is null, return `0` (base case: height of empty tree is 0).
3. Recursively find `leftHeight = checkHeight(root->left)`. If `leftHeight == -1`, return `-1` (unbalanced).
4. Recursively find `rightHeight = checkHeight(root->right)`. If `rightHeight == -1`, return `-1` (unbalanced).
5. If `abs(leftHeight - rightHeight) > 1`, return `-1` (current node is unbalanced).
6. Otherwise, return `1 + max(leftHeight, rightHeight)` (the actual height of the current node).
7. In the main function, return `checkHeight(root) != -1`.

## 6. Why does it work?
In a bottom-up traversal, we process the leaf nodes first. If a subtree is unbalanced, it returns `-1`. This `-1` acts as a fail-fast flag; if any child of a node returns `-1`, the node immediately passes `-1` up to its parent without performing unnecessary calculations. If the tree is balanced, the function successfully returns the height. Thus, we combine the height calculation and the balance check into a single, clean O(N) pass.

## 7. C++ Code
```cpp
#include <algorithm>
#include <cmath>
using namespace std;

// Definition for a binary tree node.
// struct TreeNode {
//     int val;
//     TreeNode *left;
//     TreeNode *right;
//     TreeNode() : val(0), left(nullptr), right(nullptr) {}
//     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
//     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
// };

class Solution {
private:
    // Helper function that returns the height of the tree if balanced,
    // or -1 if the subtree is unbalanced.
    int checkHeight(TreeNode* root) {
        if (!root) {
            return 0;
        }
        
        // Post-order: Check left subtree
        int leftHeight = checkHeight(root->left);
        if (leftHeight == -1) {
            return -1; // Propagate the unbalanced signal
        }
        
        // Post-order: Check right subtree
        int rightHeight = checkHeight(root->right);
        if (rightHeight == -1) {
            return -1; // Propagate the unbalanced signal
        }
        
        // Check if the current node is unbalanced
        if (abs(leftHeight - rightHeight) > 1) {
            return -1;
        }
        
        // Return height of current node
        return 1 + max(leftHeight, rightHeight);
    }

public:
    bool isBalanced(TreeNode* root) {
        // If the tree is balanced, checkHeight returns a non-negative value (height)
        return checkHeight(root) != -1;
    }
};
```

## 8. Dry Run
Input: root = [3, 9, 20, null, null, 15, 7]
Tree Structure:
      3
     / \
    9  20
      /  \
     15   7

Step-by-step:
1. `checkHeight(node(3))` is called:
   - Calls `checkHeight(node(9))` (left).
2. `checkHeight(node(9))` is called:
   - Left and right are null, both return 0.
   - `abs(0 - 0) <= 1`. Returns `1 + max(0, 0) = 1`.
3. `checkHeight(node(3))` now calls `checkHeight(node(20))` (right).
4. `checkHeight(node(20))` is called:
   - Calls `checkHeight(node(15))` (left) -> returns `1`.
   - Calls `checkHeight(node(7))` (right) -> returns `1`.
   - For node 20, `abs(1 - 1) = 0 <= 1`. Returns `1 + max(1, 1) = 2`.
5. Back to `checkHeight(node(3))`:
   - `leftHeight = 1` (from node 9).
   - `rightHeight = 2` (from node 20).
   - Difference: `abs(1 - 2) = 1 <= 1`.
   - Returns `1 + max(1, 2) = 3`.
6. `isBalanced` checks: `3 != -1` is true.
Final Result: true

## 9. Complexity
Time: O(N) where N is the number of nodes in the tree. We visit each node at most once in post-order.
Space: O(H) where H is the height of the tree. The recursion stack uses at most O(H) space. Worst-case is O(N) for a skewed tree; average-case is O(log N) for a balanced tree.

## 10. Edge Cases
- **Empty Tree (`root == nullptr`)**: Returns `0` from `checkHeight`, so `isBalanced` returns true.
- **Single Node**: Left and right subtrees return `0`. Absolute difference `0 <= 1`, returns height `1`. `isBalanced` returns true.
- **Highly Unbalanced Tree**: E.g., a left-skewed tree of height 3 with no right children. The root's `leftHeight` will be 2, and `rightHeight` will be 0. Since `abs(2 - 0) = 2 > 1`, it returns `-1`, which evaluates to false.

## 11. Follow-up Questions
- **How is a height-balanced tree different from a weight-balanced tree?**
  A height-balanced tree restricts the difference in subtree heights to $\le 1$. A weight-balanced tree restricts the difference in the number of nodes (or weights) in the left and right subtrees.
- **Can we write this without the -1 sentinel?**
  Yes, by returning a `std::pair<bool, int>` representing `{isBalanced, height}` from our recursive function. However, the sentinel approach is more concise and reduces object instantiation overhead.
- **How would you balance a binary search tree?**
  By performing rotations (left and right rotations) on unbalanced nodes during insertions/deletions, which is how self-balancing trees like AVL or Red-Black trees operate.

## 12. Interview Explanation
"First, I would clarify the definition of a balanced tree—specifically, that the height difference between the left and right subtrees must not exceed one at every node. Then, I'd explain that a top-down brute force approach requires O(N^2) time because it repeatedly calculates height for the same descendants. To optimize, I would use a bottom-up post-order DFS. By calculating the height from leaves upwards, we can determine the balance at each node. If any child is unbalanced, we bubble up a sentinel value of `-1` immediately, which short-circuits the remaining calculations. My C++ code implements this bottom-up check, running in O(N) time and O(H) space."
