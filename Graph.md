# Section 9: Graphs

---

# 1. Clone Graph (LeetCode 133)

## 1. Pattern
Breadth-First Search (BFS) / Depth-First Search (DFS) with Hash Map.

## 2. What is the interviewer asking?
- You are given a reference to a node in a connected, undirected graph.
- Create and return a **deep copy** (clone) of the graph.
- Each node contains an integer value (`val`) and a list of its neighbors (`vector<Node*> neighbors`).
- A deep copy means that you must instantiate entirely new `Node` objects in memory. You cannot reuse existing node pointers from the input graph, and the new graph's structure must perfectly mirror the original.

## 3. Intuition
To copy the graph, we must traverse all of its vertices and edges. However, because undirected graphs can have cycles (e.g., Node A points to B, and B points to A), a simple recursive traversal without tracking visited nodes will result in infinite loops and stack overflow. 
To prevent this, we need a way to map each original node to its cloned copy. We can use a hash map `unordered_map<Node*, Node*>` where the key is the original node pointer and the value is the new cloned node pointer. Whenever we visit a neighbor:
- If the neighbor is already in the map, we simply retrieve its cloned pointer and link it.
- If the neighbor is not in the map, we clone it, add it to the map, and push it to the queue/stack to process its neighbors later.

## 4. Brute Force
### Approach:
Without a hash map or visited set, we might try to copy recursively, but with cycles we would loop infinitely. Alternatively, a brute force approach could first run a traversal (like BFS) to find all unique nodes in the graph, create new cloned instances of them without edges, and store them in an array or map indexed by their values (assuming unique values, though the problem does not strictly guarantee unique values). After creating all vertices, we run a second traversal to copy and assign the neighbor edges. This requires multiple passes and is structurally redundant.

### Time:
$O(V + E)$ where $V$ is the number of vertices and $E$ is the number of edges.

### Space:
$O(V)$ to store the array of cloned nodes.

## 5. Optimized Approach
We can perform an iterative BFS traversal using a queue and a single hash map.
1. If the input node is `nullptr`, return `nullptr`.
2. Initialize a queue `q` and an `unordered_map<Node*, Node*> visited`.
3. Clone the starting node: `visited[node] = new Node(node->val)`.
4. Push the original starting node to `q`.
5. While `q` is not empty:
   - Pop the current node `curr` from the front of the queue.
   - For each `neighbor` of `curr`:
     - If `neighbor` has not been cloned yet (i.e., it is not in the `visited` map):
       - Create its clone: `visited[neighbor] = new Node(neighbor->val)`.
       - Push the original `neighbor` to `q` to process its neighbors.
     - Connect the cloned current node to the cloned neighbor: `visited[curr]->neighbors.push_back(visited[neighbor])`.
6. Return `visited[node]`, which is the clone of the starting node.

## 6. Why does it work?
BFS guarantees that we visit every reachable vertex and edge in the connected graph. The hash map `visited` serves a dual purpose:
- It acts as a "visited set" to ensure that no node is enqueued or cloned more than once, preventing infinite loops.
- It maps original pointers to cloned pointers, ensuring that we can retrieve the correct cloned node reference when constructing the adjacency lists of the clones.

## 7. C++ Code
```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> neighbors;
    Node() {
        val = 0;
        neighbors = vector<Node*>();
    }
    Node(int _val) {
        val = _val;
        neighbors = vector<Node*>();
    }
    Node(int _val, vector<Node*> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
};
*/

Node* cloneGraph(Node* node) {
    if (!node) return nullptr;
    
    // Hash map to map original nodes to their corresponding cloned nodes
    unordered_map<Node*, Node*> visited;
    queue<Node*> q;
    
    // Start with the initial node
    q.push(node);
    // Create the clone of the initial node
    visited[node] = new Node(node->val);
    
    while (!q.empty()) {
        Node* curr = q.front();
        q.pop();
        
        // Iterate through all neighbors of the current original node
        for (Node* neighbor : curr->neighbors) {
            // If the neighbor has not been cloned/visited yet
            if (!visited.count(neighbor)) {
                // Clone the neighbor
                visited[neighbor] = new Node(neighbor->val);
                // Queue the original neighbor to process its connections later
                q.push(neighbor);
            }
            // Add the clone of the neighbor to the clone of the current node
            visited[curr]->neighbors.push_back(visited[neighbor]);
        }
    }
    
    return visited[node];
}
```

## 8. Dry Run
### Input:
An undirected cyclic graph with 4 nodes:
- `1` connected to `2` and `4`
- `2` connected to `1` and `3`
- `3` connected to `2` and `4`
- `4` connected to `1` and `3`

Input points to original Node `1`.

- **Initial State**:
  - `q = [Node(1)]`
  - `visited = { Node(1): Clone(1) }`

- **Step 1**:
  - Pop `curr = Node(1)`. `q = []`.
  - Neighbor `Node(2)`:
    - Not in `visited`.
    - Clone it: `visited[Node(2)] = Clone(2)`.
    - Push: `q = [Node(2)]`.
    - Link: `Clone(1)->neighbors` becomes `[Clone(2)]`.
  - Neighbor `Node(4)`:
    - Not in `visited`.
    - Clone it: `visited[Node(4)] = Clone(4)`.
    - Push: `q = [Node(2), Node(4)]`.
    - Link: `Clone(1)->neighbors` becomes `[Clone(2), Clone(4)]`.

- **Step 2**:
  - Pop `curr = Node(2)`. `q = [Node(4)]`.
  - Neighbor `Node(1)`:
    - Already in `visited`.
    - Link: `Clone(2)->neighbors` becomes `[Clone(1)]`.
  - Neighbor `Node(3)`:
    - Not in `visited`.
    - Clone it: `visited[Node(3)] = Clone(3)`.
    - Push: `q = [Node(4), Node(3)]`.
    - Link: `Clone(2)->neighbors` becomes `[Clone(1), Clone(3)]`.

- **Step 3**:
  - Pop `curr = Node(4)`. `q = [Node(3)]`.
  - Neighbor `Node(1)`:
    - Already in `visited`.
    - Link: `Clone(4)->neighbors` becomes `[Clone(1)]`.
  - Neighbor `Node(3)`:
    - Already in `visited`.
    - Link: `Clone(4)->neighbors` becomes `[Clone(1), Clone(3)]`.

- **Step 4**:
  - Pop `curr = Node(3)`. `q = []`.
  - Neighbor `Node(2)`:
    - Already in `visited`.
    - Link: `Clone(3)->neighbors` becomes `[Clone(2)]`.
  - Neighbor `Node(4)`:
    - Already in `visited`.
    - Link: `Clone(3)->neighbors` becomes `[Clone(2), Clone(4)]`.

- **End**:
  - `q` is empty. Loop terminates.
  - Return `Clone(1)`.

## 9. Complexity
- **Time**: $O(V + E)$ where $V$ is the number of vertices and $E$ is the number of edges. We visit each vertex once and traverse each edge.
- **Space**: $O(V)$ auxiliary space to store the `visited` map and the BFS queue.

## 10. Edge Cases
- **Null Graph (`node = nullptr`)**: Handled by the initial check, returns `nullptr`.
- **Single Node Graph (no neighbors)**: The queue processes the single node, its neighbors loop is skipped, and it returns the single cloned node.
- **Self-Loops**: If Node 1 points to itself, since Node 1 is already in the map, the code safely retrieves its own clone and adds it to its neighbors without entering an infinite loop.
- **Multiple Connected Components**: The problem states that the input is a node from a *connected* graph, so we only need to clone the component containing that node.

## 11. Follow-up Questions
- **How would you implement this using DFS?**
  - *Answer*: You can use a recursive helper: `Node* clone(Node* node, unordered_map<Node*, Node*>& visited)`. If `visited` contains `node`, return `visited[node]`. Otherwise, create `Clone(node->val)`, store it in the map, recursively call `clone` on all its neighbors, and push the results into the cloned node's neighbors list.
- **What if the graph contains millions of nodes and cannot fit in memory?**
  - *Answer*: We would partition the graph and clone it in chunks. A distributed system (e.g., using Pregel or MapReduce) can process sections of the graph across multiple machines. The mapping table could be stored in a distributed key-value store (like Redis or DynamoDB).
- **Can we optimize space if we know node values are unique and range from 1 to N?**
  - *Answer*: Yes, we can replace the `unordered_map` with an array/vector of size $N + 1$ to store the cloned node pointers, avoiding hash map overhead and achieving $O(1)$ constant time lookup.

## 12. Interview Explanation
"First, I would clarify the constraints of the problem, such as the range of node values and whether the graph is connected. Then, I'd explain that a naive recursive copy without tracking visited nodes would fail because cycles in undirected graphs lead to infinite recursion. To optimize this, I would use BFS traversal with a hash map. The hash map maps original nodes to their clones, tracking which nodes are already visited and allowing us to retrieve clone references. In C++, I would start by cloning the input node and pushing it to a queue. Then, while the queue is not empty, I'd pop the current node, iterate through its neighbors, clone any unvisited neighbors, push them to the queue, and append their clones to the current clone's neighbor list. This approach runs in $O(V + E)$ time and uses $O(V)$ space."

---

# 2. Number of Islands (LeetCode 200)

## 1. Pattern
Depth-First Search (DFS) / Grid Traversal.

## 2. What is the interviewer asking?
- You are given an $m \times n$ 2D binary grid representing a map of land (`'1'`) and water (`'0'`).
- Return the total number of islands.
- An island is surrounded by water and is formed by connecting adjacent land cells horizontally or vertically (4 directions). 
- The boundaries of the grid are assumed to be surrounded by water.

## 3. Intuition
Think of the grid as a graph where each `'1'` is a node, and edges exist between adjacent `'1'`s. An island represents a **connected component** in this graph.
By scanning the grid cell by cell:
- When we find a `'1'`, we increment our island count.
- To avoid counting any other parts of this same island again, we must perform a traversal (DFS or BFS) starting from this cell to visit all land cells connected to it.
- During the traversal, we can "sink" the island by changing all connected `'1'`s to `'0'`s. This acts as a visited marker in-place.

## 4. Brute Force
### Approach:
Without modifying the grid in-place or using a simple visited table, we would have to keep track of islands as list of coordinate sets. For each land cell `'1'`, we search through all coordinate sets to check if it's adjacent to any existing island. If it connects two separate sets, we must merge those sets. This lookup and merge strategy is highly inefficient.

### Time:
$O((M \times N)^2)$ due to coordinate set checks and merges.

### Space:
$O(M \times N)$ to store the sets of coordinates.

## 5. Optimized Approach
We use an in-place DFS to find and sink connected land cells.
1. Initialize `count = 0`.
2. Iterate through each cell `(i, j)` in the grid:
   - If `grid[i][j] == '1'`:
     - Increment `count`.
     - Trigger `dfs(grid, i, j)` to sink the entire island.
3. In `dfs(grid, i, j)`:
   - Check base cases: if `i` or `j` is out of bounds, or if `grid[i][j] == '0'`, return.
   - Mark the current cell as visited: `grid[i][j] = '0'`.
   - Recursively call `dfs` on the four adjacent directions: `(i + 1, j)`, `(i - 1, j)`, `(i, j + 1)`, and `(i, j - 1)`.
4. Return `count`.

## 6. Why does it work?
Launching a DFS on a land cell `'1'` explores all adjacent land cells recursively until it hits water (`'0'`) or the grid boundary. Because we change visited land cells to `'0'`, we guarantee that the DFS will terminate and that we will never visit any cell of this island again. Thus, the number of DFS calls initiated from the main loop is exactly equal to the number of connected components (islands).

## 7. C++ Code
```cpp
void dfs(vector<vector<char>>& grid, int i, int j) {
    // Base case: check boundary limits and if the cell is water ('0')
    if (i < 0 || j < 0 || i >= grid.size() || j >= grid[0].size() || grid[i][j] == '0') {
        return;
    }
    
    // Mark the current land cell as water ('0') to flag it as visited
    grid[i][j] = '0';
    
    // Traverse all four cardinal directions recursively
    dfs(grid, i + 1, j); // Down
    dfs(grid, i - 1, j); // Up
    dfs(grid, i, j + 1); // Right
    dfs(grid, i, j - 1); // Left
}

int numIslands(vector<vector<char>>& grid) {
    int count = 0;
    
    // Scan the entire grid cell by cell
    for (int i = 0; i < grid.size(); ++i) {
        for (int j = 0; j < grid[0].size(); ++j) {
            // When we encounter land, we've found a new island
            if (grid[i][j] == '1') {
                count++;
                dfs(grid, i, j); // Sink the island recursively
            }
        }
    }
    
    return count;
}
```

## 8. Dry Run
### Input:
```
[
  ['1', '1', '0'],
  ['1', '0', '0'],
  ['0', '0', '1']
]
```

- **Initial State**: `count = 0`.
- **Iteration i=0, j=0**:
  - `grid[0][0] == '1'`. Increment `count = 1`.
  - Call `dfs(grid, 0, 0)`:
    - Set `grid[0][0] = '0'`.
    - Call `dfs(grid, 1, 0)`:
      - Set `grid[1][0] = '0'`.
      - Neighbors `(2,0)`, `(0,0)`, `(1,1)`, `(1,-1)` all return.
    - Call `dfs(grid, -1, 0)` -> returns.
    - Call `dfs(grid, 0, 1)`:
      - Set `grid[0][1] = '0'`.
      - Neighbors `(1,1)`, `(-1,1)`, `(0,2)`, `(0,0)` all return.
    - Call `dfs(grid, 0, -1)` -> returns.
  - Grid state after DFS:
    ```
    [
      ['0', '0', '0'],
      ['0', '0', '0'],
      ['0', '0', '1']
    ]
    ```
- **Iteration i=2, j=2**:
  - `grid[2][2] == '1'`. Increment `count = 2`.
  - Call `dfs(grid, 2, 2)`:
    - Set `grid[2][2] = '0'`.
    - Neighbors `(3,2)`, `(1,2)`, `(2,3)`, `(2,1)` all return.
- **Return**: `count = 2`.

## 9. Complexity
- **Time**: $O(M \times N)$ where $M$ is the number of rows and $N$ is the number of columns. Every cell is visited at most a constant number of times.
- **Space**: $O(M \times N)$ in the worst case for the recursion stack (e.g., if the grid is filled entirely with land, the stack depth will reach $M \times N$).

## 10. Edge Cases
- **Grid with only Water (`'0'`)**: No DFS calls are made, returns `0`.
- **Grid with only Land (`'1'`)**: A single DFS call sinks the entire grid, returns `1`.
- **1x1 Grid**: Correctly processed.
- **Diagonal Land Cells**: Diagonal components (e.g., `(0,0)` and `(1,1)` without horizontal/vertical connectors) are not connected, and are correctly classified as separate islands.

## 11. Follow-up Questions
- **What if we are not allowed to modify the grid?**
  - *Answer*: We can use a 2D boolean array `visited` of size $M \times N$ initialized to `false` to keep track of visited cells. This keeps the space complexity at $O(M \times N)$ but preserves the input.
- **How would you solve this using BFS?**
  - *Answer*: Instead of DFS, use a queue. When you encounter `'1'`, increment count, set the cell to `'0'`, push the coordinates to the queue, and run a standard BFS queue loop, marking popped coordinates' neighbors as `'0'` and pushing them.
- **Can we solve this using Union-Find?**
  - *Answer*: Yes. Initialize a disjoint set where each land cell is parent to itself. Iterate through the grid, and for each `'1'`, union it with its valid down and right neighbors. The number of disjoint sets representing land cells at the end will be the number of islands.

## 12. Interview Explanation
"First, I would clarify if the grid can be mutated in-place and if we only consider the four cardinal directions. Then, I'd explain the brute force approach where we track lists of coordinate sets for each island, which adds lookup and merge overhead. To optimize this, I would use a DFS traversal. As I scan the grid, whenever I find a `'1'`, I increment my island count and trigger a DFS to 'sink' the island by changing all adjacent `'1'`s to `'0'`s. This ensures we visit each land cell once. My C++ code implements this recursively, checking boundary conditions before sinking and expanding. This approach runs in $O(M \times N)$ time complexity and uses $O(M \times N)$ space in the worst case."

---

# 3. Course Schedule (LeetCode 207)

## 1. Pattern
Topological Sort (Kahn's Algorithm - BFS) / Cycle Detection in a Directed Graph.

## 2. What is the interviewer asking?
- There are `numCourses` courses, labeled from `0` to `numCourses - 1`.
- You are given a list of dependencies `prerequisites` where `prerequisites[i] = [a, b]` means you must take course `b` before course `a` (directed edge `b -> a`).
- Return `true` if you can complete all courses, and `false` if there is a cycle making it impossible.

## 3. Intuition
We can model this as a directed graph where courses are nodes and dependencies are directed edges: `b -> a` implies course `b` is a prerequisite for `a`.
A set of courses can be completed if and only if there are no circular dependencies (cycles) in the graph. In graph theory, a directed graph with no cycles is called a Directed Acyclic Graph (DAG) and can be ordered linearly (Topological Sort).
We can use Kahn's algorithm (BFS-based topological sort) which works by tracking the **in-degrees** (number of incoming edges) of each node. We start by taking courses with an in-degree of 0 (no prerequisites) and removing them, which reduces the in-degrees of their dependent courses.

## 4. Brute Force
### Approach:
For every course, we can perform a backtracking search to check if we can reach the starting course again (which indicates a cycle). If we run this search from every course without memoization, we will end up checking the same paths multiple times.

### Time:
$O(V \times (V + E))$ or $O(V!)$ in the worst-case dense graph.

### Space:
$O(V)$ for the recursion stack.

## 5. Optimized Approach
We implement Kahn's Algorithm (BFS-based topological sort):
1. Build an adjacency list `graph` representing the directed edges `prereq -> course` and compute the `inDegree` array of size `numCourses`.
2. Push all courses with `inDegree[i] == 0` (no prerequisites) to a queue `q`.
3. Initialize `count = 0`.
4. While `q` is not empty:
   - Pop a course `course` from the queue, and increment `count` by 1.
   - For each dependent course `neighbor` in `graph[course]`:
     - Decrement its in-degree: `inDegree[neighbor]--`.
     - If its in-degree becomes 0, push `neighbor` to `q`.
5. If `count == numCourses`, return `true`. Otherwise, return `false` (a cycle exists).

## 6. Why does it work?
In a DAG, there must be at least one node with in-degree 0. Removing it represents taking the course. Once taken, its neighbors' in-degrees are decremented.
If the graph contains a cycle, the nodes in the cycle will never have an in-degree of 0 because they depend on each other. Consequently, they will never enter the queue, and the final `count` of completed courses will be less than `numCourses`.

## 7. C++ Code
```cpp
bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
    // Adjacency list representation of the graph
    vector<vector<int>> graph(numCourses);
    // In-degree array to keep track of prerequisites count for each course
    vector<int> inDegree(numCourses, 0);
    
    // Build the graph: pre[1] must be taken before pre[0]
    for (auto& pre : prerequisites) {
        graph[pre[1]].push_back(pre[0]); // Edge: prereq -> course
        inDegree[pre[0]]++;
    }
    
    // Queue to hold courses that have no remaining prerequisites
    queue<int> q;
    for (int i = 0; i < numCourses; ++i) {
        if (inDegree[i] == 0) {
            q.push(i);
        }
    }
    
    int count = 0;
    while (!q.empty()) {
        int course = q.front();
        q.pop();
        count++; // Course completed
        
        // Decrement in-degree for all adjacent neighbors
        for (int neighbor : graph[course]) {
            if (--inDegree[neighbor] == 0) {
                q.push(neighbor); // Enqueue neighbor if all its prerequisites are met
            }
        }
    }
    
    // If we completed all courses, no cycle exists
    return count == numCourses;
}
```

## 8. Dry Run
### Input:
`numCourses = 4`, `prerequisites = [[1, 0], [2, 0], [3, 1], [3, 2]]`

- **Initialization**:
  - `graph`: `0 -> [1, 2]`, `1 -> [3]`, `2 -> [3]`, `3 -> []`
  - `inDegree = [0, 1, 1, 2]`
  - `q = [0]`
  - `count = 0`

- **Step 1**:
  - Pop `course = 0`. `q = []`. `count = 1`.
  - Neighbors of `0`: `1` and `2`.
    - Decrement `inDegree[1]` to `0`. Push `1`. `q = [1]`.
    - Decrement `inDegree[2]` to `0`. Push `2`. `q = [1, 2]`.

- **Step 2**:
  - Pop `course = 1`. `q = [2]`. `count = 2`.
  - Neighbors of `1`: `3`.
    - Decrement `inDegree[3]` to `1` (not 0, do not push).

- **Step 3**:
  - Pop `course = 2`. `q = []`. `count = 3`.
  - Neighbors of `2`: `3`.
    - Decrement `inDegree[3]` to `0`. Push `3`. `q = [3]`.

- **Step 4**:
  - Pop `course = 3`. `q = []`. `count = 4`.
  - Neighbors of `3`: None.

- **End**:
  - `q` is empty.
  - Return `count == numCourses` -> `4 == 4` -> `true`.

## 9. Complexity
- **Time**: $O(V + E)$ where $V = \text{numCourses}$ and $E = \text{prerequisites.size()}$. We visit each vertex and edge once.
- **Space**: $O(V + E)$ to store the adjacency list representation of the graph, and $O(V)$ for the `inDegree` array and the queue.

## 10. Edge Cases
- **No prerequisites (`prerequisites` is empty)**: All courses have in-degree 0 initially, all are queued, returns `true`.
- **Self-loops (e.g., `[[0, 0]]`)**: `inDegree[0]` is initialized to 1, never reaches 0, returns `false`.
- **Fully Disconnected Components**: Correctly handles disconnected nodes as they are pushed into the queue at the start.

## 11. Follow-up Questions
- **How do you return the actual course path order?**
  - *Answer*: Instead of just tracking `count`, push the popped course into a `vector<int> result` inside the BFS loop. If `result.size() == numCourses`, return `result`; else return an empty vector (LeetCode 210: Course Schedule II).
- **Can we implement cycle detection using DFS?**
  - *Answer*: Yes, use a 3-state visited array: 0 (unvisited), 1 (visiting), 2 (visited). If you encounter a neighbor that is in state 1 during DFS, a cycle is present.
- **What is the difference between Kahn's algorithm and DFS for topological sort?**
  - *Answer*: Kahn's algorithm is BFS-based and handles dependencies level-by-level, while DFS goes deep first and requires reversing the post-order visit list to get the topological order.

## 12. Interview Explanation
"First, I would clarify if the dependencies represent a cyclic graph and if the number of courses is valid. Then, I'd explain that the courses and prerequisites form a directed graph where edges flow from prerequisites to courses. Taking all courses is possible if and only if there are no cycles. To solve this, I would use Kahn's algorithm for topological sorting. I would build an adjacency list and calculate the in-degree for each course. Enqueuing all courses with in-degree 0, I'd then repeatedly pop courses from the queue, decrement the in-degree of their neighbor courses, and enqueue any neighbor whose in-degree drops to 0. If our count of popped courses equals the total courses, no cycle exists. This runs in $O(V + E)$ time and space."

---

# 4. Is Graph Bipartite? (LeetCode 785)

## 1. Pattern
Graph Coloring / Breadth-First Search (BFS).

## 2. What is the interviewer asking?
- You are given an undirected graph represented as a 2D adjacency list `graph`.
- Return `true` if the graph is bipartite, and `false` otherwise.
- A graph is bipartite if its vertices can be partitioned into two independent sets, say A and B, such that every edge in the graph connects a vertex in set A and a vertex in set B.

## 3. Intuition
A graph is bipartite if and only if it can be colored using two colors (e.g., `0` and `1`) such that no two adjacent vertices share the same color.
If we assign color `0` to a starting node, all of its neighbors *must* be colored `1` to satisfy the bipartite condition. Their neighbors must then be colored `0`, and so on.
If we traverse the graph (using BFS or DFS) and color nodes dynamically:
- If we encounter an uncolored neighbor, we color it with the opposite color.
- If we encounter a neighbor that is already colored and it has the same color as the current node, we have a color conflict, meaning the graph is not bipartite.

## 4. Brute Force
### Approach:
Generate all possible 2-partitions of the graph's vertices (there are $2^V$ combinations) and verify if any partition is valid (i.e., no edges exist between nodes in the same partition).

### Time:
$O(2^V \times (V + E))$

### Space:
$O(V)$ to store partitions.

## 5. Optimized Approach
We use BFS to color the graph nodes step-by-step.
1. Initialize a `color` array of size `n` with `-1` (representing uncolored).
2. Loop through all vertices from `0` to `n - 1` (to handle disconnected graphs).
3. If `color[i] != -1`, skip it as it is already colored.
4. Otherwise, start BFS:
   - Push `i` to a queue `q` and set `color[i] = 0`.
   - While `q` is not empty:
     - Pop `node` from the front.
     - For each `neighbor` of `node`:
       - If `color[neighbor] == -1`:
         - Assign `color[neighbor] = 1 - color[node]` (opposite color).
         - Push `neighbor` to `q`.
       - Else if `color[neighbor] == color[node]`:
         - Return `false` (conflict found).
5. If the entire graph is colored without conflicts, return `true`.

## 6. Why does it work?
A graph is bipartite if and only if it does not contain an odd-length cycle. Color alternation along paths (`0 -> 1 -> 0 -> 1`) naturally detects odd cycles. If we encounter a node that has already been colored the same color as the node we are visiting from, it implies the existence of an odd cycle, making it mathematically impossible to partition the graph into two independent sets.

## 7. C++ Code
```cpp
bool isBipartite(vector<vector<int>>& graph) {
    int n = graph.size();
    // Color array: -1 means uncolored, 0 and 1 represent the two colors
    vector<int> color(n, -1);
    
    // Loop through all nodes to handle disconnected components
    for (int i = 0; i < n; ++i) {
        // Skip if the node has already been colored
        if (color[i] != -1) continue;
        
        queue<int> q;
        q.push(i);
        color[i] = 0; // Assign color 0 to the starting node
        
        while (!q.empty()) {
            int node = q.front();
            q.pop();
            
            // Check all neighbors of the current node
            for (int neighbor : graph[node]) {
                // If neighbor is uncolored, assign it the opposite color
                if (color[neighbor] == -1) {
                    color[neighbor] = 1 - color[node];
                    q.push(neighbor);
                } 
                // If neighbor is colored with the same color, graph is not bipartite
                else if (color[neighbor] == color[node]) {
                    return false;
                }
            }
        }
    }
    
    return true;
}
```

## 8. Dry Run
### Input:
`graph = [[1, 3], [0, 2], [1, 3], [0, 2]]` (A cycle: `0 - 1 - 2 - 3 - 0`)

- **Initialization**:
  - `color = [-1, -1, -1, -1]`

- **Loop i = 0**:
  - `color[0] == -1`. Push `0` to `q`, set `color[0] = 0`.
  - `color = [0, -1, -1, -1]`. `q = [0]`.

- **BFS Step 1**:
  - Pop `node = 0`. `q = []`.
  - Neighbors of `0`: `1` and `3`.
    - `color[1] == -1` -> Set `color[1] = 1 - 0 = 1`. Push `1`.
    - `color[3] == -1` -> Set `color[3] = 1 - 0 = 1`. Push `3`.
  - `color = [0, 1, -1, 1]`. `q = [1, 3]`.

- **BFS Step 2**:
  - Pop `node = 1`. `q = [3]`.
  - Neighbors of `1`: `0` and `2`.
    - Neighbor `0`: `color[0] == 0` (opposite of `color[1] == 1`), valid.
    - Neighbor `2`: `color[2] == -1` -> Set `color[2] = 1 - 1 = 0`. Push `2`.
  - `color = [0, 1, 0, 1]`. `q = [3, 2]`.

- **BFS Step 3**:
  - Pop `node = 3`. `q = [2]`.
  - Neighbors of `3`: `0` and `2`.
    - Neighbor `0`: `color[0] == 0` (opposite of `color[3] == 1`), valid.
    - Neighbor `2`: `color[2] == 0` (opposite of `color[3] == 1`), valid.

- **BFS Step 4**:
  - Pop `node = 2`. `q = []`.
  - Neighbors of `2`: `1` and `3`.
    - Neighbors are already colored with `1` (opposite of `color[2] == 0`), valid.

- **End**:
  - Loops finish, returns `true`.

## 9. Complexity
- **Time**: $O(V + E)$ where $V$ is the number of vertices and $E$ is the number of edges. We visit every vertex and edge once.
- **Space**: $O(V)$ auxiliary space for the `color` array and the BFS queue.

## 10. Edge Cases
- **Disconnected Graph**: The outer loop checks all nodes, ensuring that every independent component is checked and colored.
- **Graph with No Edges**: Returns `true` since all nodes are colored without conflict.
- **Odd Cycle (e.g., Triangle 0-1, 1-2, 2-0)**: Node 0 colored 0, 1 colored 1, 2 colored 1. Checking edge between 1 and 2 detects `color[1] == color[2] == 1`, returning `false`.

## 11. Follow-up Questions
- **Can we implement this using DFS?**
  - *Answer*: Yes, we can write a recursive DFS helper: `bool dfs(vector<vector<int>>& graph, vector<int>& color, int node, int c)`. We color `node` with `c`, and for each neighbor, recursively call DFS with `1 - c`. If a neighbor is already colored, check if its color matches `c`.
- **How can this problem be solved using Disjoint Set Union (DSU)?**
  - *Answer*: For each node `u`, all its neighbors must belong to the opposite set. This means all neighbors of `u` must be in the same set as each other. So we can union all neighbors of `u` together. If we ever find that `u` is in the same set as any of its neighbors (i.e. `find(u) == find(neighbor)`), then the graph is not bipartite.
- **What is the significance of a bipartite graph in matching?**
  - *Answer*: It allows solving the Maximum Bipartite Matching problem using Ford-Fulkerson or Hopcroft-Karp algorithms, which are useful in resource allocation and stable marriage problems.

## 12. Interview Explanation
"First, I would clarify if the graph contains self-loops or disconnected components. Then, I'd explain that a bipartite graph can be partitioned into two independent sets, which is equivalent to 2-coloring the graph such that no adjacent nodes have the same color. To solve this, I would use BFS. I'll maintain a color array initialized to -1. For every uncolored node, I'd assign it color 0 and push it to a queue. While the queue is not empty, I'd pop the front node and iterate through its neighbors. If a neighbor is uncolored, I'd assign it the opposite color and push it to the queue. If it is already colored the same color as the current node, I'd immediately return false. This BFS checks all nodes and edges in $O(V + E)$ time and uses $O(V)$ space."

---

# 5. Rotting Oranges (LeetCode 994)

## 1. Pattern
Multi-source Breadth-First Search (BFS).

## 2. What is the interviewer asking?
- You are given an $m \times n$ grid containing:
  - `0`: Empty cell
  - `1`: Fresh orange
  - `2`: Rotten orange
- Every minute, any fresh orange that is 4-directionally adjacent to a rotten orange becomes rotten.
- Return the minimum number of minutes that must elapse until no cell has a fresh orange. If this is impossible, return `-1`.

## 3. Intuition
The rotting process of oranges spreads outwards layer by layer from all rotten oranges simultaneously. This represents a shortest path problem in an unweighted grid, which is best solved using Breadth-First Search (BFS).
Since there can be multiple rotten oranges initially, we must start the BFS with all initial rotten oranges in the queue. This is a **Multi-source BFS**.
We process the BFS queue level-by-level (where each level represents one minute) and keep track of the remaining fresh oranges.

## 4. Brute Force
### Approach:
In each minute, scan the entire grid to find all currently rotten oranges. For each rotten orange, mark its adjacent fresh neighbors as "about to rot". Update the grid at the end of the scan, increment the minutes counter, and repeat until no new fresh oranges rot.

### Time:
$O((M \times N)^2)$ because in the worst case, only one orange rots per grid scan.

### Space:
$O(M \times N)$ to store the temporary grid state.

## 5. Optimized Approach
We use a Multi-source BFS to simulate the spreading process.
1. Initialize a queue `q` to store coordinates of rotten oranges and `fresh = 0` to count fresh oranges.
2. Traverse the grid:
   - If `grid[i][j] == 2`, push `{i, j}` to `q`.
   - If `grid[i][j] == 1`, increment `fresh`.
3. Initialize `minutes = 0` and the direction offsets `directions = {{0,1}, {1,0}, {0,-1}, {-1,0}}`.
4. While `q` is not empty and `fresh > 0`:
   - Get the number of rotten oranges at the current level: `size = q.size()`.
   - For `i = 0` to `size - 1`:
     - Pop `{x, y}` from the queue.
     - For each `[dx, dy]` in `directions`:
       - Calculate neighbor coordinates `nx = x + dx`, `ny = y + dy`.
       - If `nx` and `ny` are within bounds and `grid[nx][ny] == 1`:
         - Set `grid[nx][ny] = 2` (rot the orange).
         - Push `{nx, ny}` to `q`.
         - Decrement `fresh` by 1.
   - Increment `minutes` after processing the level.
5. If `fresh == 0`, return `minutes`. Otherwise, return `-1`.

## 6. Why does it work?
Multi-source BFS enqueues all initial sources at distance 0. The queue ensures that we process all nodes at distance $d$ before processing nodes at distance $d + 1$. This correctly simulates the parallel, simultaneous spread of the rot. Since we count fresh oranges and decrement the count whenever one rots, we can reliably determine if any fresh oranges remain unreachable once the queue is empty.

## 7. C++ Code
```cpp
int orangesRotting(vector<vector<int>>& grid) {
    int m = grid.size(), n = grid[0].size();
    queue<pair<int, int>> q;
    int fresh = 0;
    
    // Step 1: Scan grid to push rotten oranges and count fresh oranges
    for (int i = 0; i < m; ++i) {
        for (int j = 0; j < n; ++j) {
            if (grid[i][j] == 2) {
                q.push({i, j});
            }
            if (grid[i][j] == 1) {
                fresh++;
            }
        }
    }
    
    int minutes = 0;
    vector<pair<int, int>> directions = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
    
    // Step 2: Multi-source BFS
    while (!q.empty() && fresh > 0) {
        int size = q.size();
        for (int i = 0; i < size; ++i) {
            auto [x, y] = q.front();
            q.pop();
            
            // Check all 4-directional neighbors
            for (auto [dx, dy] : directions) {
                int nx = x + dx, ny = y + dy;
                
                // If neighbor is within bounds and has a fresh orange
                if (nx >= 0 && nx < m && ny >= 0 && ny < n && grid[nx][ny] == 1) {
                    grid[nx][ny] = 2; // Rot the orange
                    q.push({nx, ny}); // Add new rotten orange to queue
                    fresh--;          // Decrement fresh count
                }
            }
        }
        minutes++; // Increment time elapsed
    }
    
    // If all fresh oranges are rotted, return minutes; else return -1
    return fresh == 0 ? minutes : -1;
}
```

## 8. Dry Run
### Input:
`grid = [[2, 1, 1], [1, 1, 0], [0, 1, 1]]`

- **Initial State**:
  - `q = [{0, 0}]`
  - `fresh = 6`
  - `minutes = 0`

- **Step 1 (minutes 0 -> 1)**:
  - `size = 1`. Pop `{0, 0}`.
  - Neighbors of `{0, 0}`:
    - `{0, 1}` is fresh: set `grid[0][1] = 2`, push `{0, 1}`, `fresh = 5`.
    - `{1, 0}` is fresh: set `grid[1][0] = 2`, push `{1, 0}`, `fresh = 4`.
  - `q = [{0, 1}, {1, 0}]`.
  - Increment `minutes` to 1.

- **Step 2 (minutes 1 -> 2)**:
  - `size = 2`.
  - Pop `{0, 1}`:
    - `{0, 2}` is fresh: set `grid[0][2] = 2`, push `{0, 2}`, `fresh = 3`.
    - `{1, 1}` is fresh: set `grid[1][1] = 2`, push `{1, 1}`, `fresh = 2`.
  - Pop `{1, 0}`:
    - Neighbors are already rotten or empty.
  - `q = [{0, 2}, {1, 1}]`.
  - Increment `minutes` to 2.

- **Step 3 (minutes 2 -> 3)**:
  - `size = 2`.
  - Pop `{0, 2}`: No fresh neighbors.
  - Pop `{1, 1}`:
    - `{2, 1}` is fresh: set `grid[2][1] = 2`, push `{2, 1}`, `fresh = 1`.
  - `q = [{2, 1}]`.
  - Increment `minutes` to 3.

- **Step 4 (minutes 3 -> 4)**:
  - `size = 1`.
  - Pop `{2, 1}`:
    - `{2, 2}` is fresh: set `grid[2][2] = 2`, push `{2, 2}`, `fresh = 0`.
  - `q = [{2, 2}]`.
  - Increment `minutes` to 4.

- **End**:
  - `fresh == 0` check halts the loop.
  - Return `minutes` -> 4.

## 9. Complexity
- **Time**: $O(M \times N)$ where $M$ is the number of rows and $N$ is the number of columns. Each cell is visited and processed at most once.
- **Space**: $O(M \times N)$ in the worst case for the BFS queue (e.g., if all cells contain rotten oranges initially).

## 10. Edge Cases
- **No Fresh Oranges initially**: `fresh = 0`. The BFS loop is skipped, returns `0`.
- **No Rotten Oranges initially**: The queue is empty, BFS loop is skipped, returns `-1` (if `fresh > 0`).
- **Isolated Fresh Oranges**: Fresh oranges surrounded by empty cells `0`. The BFS terminates with `fresh > 0`, and the function correctly returns `-1`.

## 11. Follow-up Questions
- **Why is DFS not suitable for this problem?**
  - *Answer*: DFS explores paths depth-first. To find the minimum time, we need the shortest path from *any* rotten orange to each fresh orange. If we used DFS, a fresh orange could be reached through a long path first, and finding the shortest time would require updating states and backtracking, leading to high time complexity. BFS naturally processes level-by-level, ensuring shortest paths in unweighted graphs.
- **How would you handle this if the rotting spreads to diagonal neighbors as well?**
  - *Answer*: We would expand our `directions` array to include 8 directions instead of 4: `{{0, 1}, {1, 0}, {0, -1}, {-1, 0}, {1, 1}, {1, -1}, {-1, 1}, {-1, -1}}`.
- **Can we optimize space if the grid is very large?**
  - *Answer*: Since we modify the grid in-place to store the state of rotten oranges, we only use the queue for BFS. Queue memory overhead is bounded by $O(M \times N)$ which is required for level traversal.

## 12. Interview Explanation
"First, I would clarify if the grid boundaries are closed and if the propagation happens diagonally. Then, I'd explain that this is a shortest path problem in an unweighted grid with multiple starting points. Thus, Multi-source BFS is the optimal approach. In my C++ code, I first iterate through the grid to enqueue all coordinates of initially rotten oranges and count the total number of fresh oranges. Then, I perform a level-by-level BFS traversal, where each level represents a minute. In each step, I pop the current level of rotten oranges, check their four neighbors, rot any fresh ones, decrement the fresh count, and enqueue the newly rotten ones. If we successfully rot all fresh oranges, we return the minutes elapsed; otherwise, we return -1. This approach runs in $O(M \times N)$ time and space."

---

# 6. Number of Connected Components in an Undirected Graph (LeetCode 323)

## 1. Pattern
Depth-First Search (DFS) / Union-Find (Disjoint Set Union).

## 2. What is the interviewer asking?
- You are given `n` nodes labeled from `0` to `n - 1`.
- You are given a list of undirected edges: `edges` where `edges[i] = [a, b]` means there is an edge between `a` and `b`.
- Return the number of connected components in the graph.

## 3. Intuition
A connected component is a subgraph where any two vertices are connected to each other by paths, and which is connected to no additional vertices in the supergraph.
We can count components by traversing the graph:
- We initialize a visited tracker.
- We loop through each node from `0` to `n - 1`. If a node is not visited, it marks the discovery of a new connected component.
- We then run a DFS/BFS traversal starting from that node to visit and mark all other nodes in the same component.
- The number of times we initiate a new traversal is the number of connected components.

## 4. Brute Force
### Approach:
For every pair of nodes, check if a path exists between them using a search algorithm (like BFS/DFS). Group nodes together based on reachability. This would require running path searches many times.

### Time:
$O(N \times (V + E))$

### Space:
$O(V + E)$

## 5. Optimized Approach
We represent the graph as an adjacency list and use a boolean `visited` array to run DFS.
1. Build an adjacency list `graph` of size `n` from the undirected `edges`.
2. Initialize a boolean vector `visited` of size `n` with `false`.
3. Initialize `count = 0`.
4. Loop through each node `i` from `0` to `n - 1`:
   - If `visited[i]` is `false`:
     - Increment `count`.
     - Call `dfs(graph, visited, i)`.
5. In the `dfs(graph, visited, node)` function:
   - Mark `node` as visited: `visited[node] = true`.
   - For each `neighbor` of `node`:
     - If `visited[neighbor]` is `false`, recursively call `dfs(graph, visited, neighbor)`.
6. Return `count`.

## 6. Why does it work?
In an undirected graph, connectivity is symmetric and transitive. Starting a DFS from an unvisited node `i` will recursively traverse and mark all nodes that are reachable from `i` via any sequence of edges. Since we only increment our component count when we encounter a node that has not been visited by any previous traversal, we guarantee that each connected component is counted exactly once.

## 7. C++ Code
```cpp
void dfs(vector<vector<int>>& graph, vector<bool>& visited, int node) {
    // Mark the current node as visited
    visited[node] = true;
    
    // Visit all adjacent neighbors recursively
    for (int neighbor : graph[node]) {
        if (!visited[neighbor]) {
            dfs(graph, visited, neighbor);
        }
    }
}

int countComponents(int n, vector<vector<int>>& edges) {
    // Step 1: Build the adjacency list for the undirected graph
    vector<vector<int>> graph(n);
    for (auto& edge : edges) {
        graph[edge[0]].push_back(edge[1]);
        graph[edge[1]].push_back(edge[0]); // Graph is undirected
    }
    
    // Step 2: Initialize visited tracker and component counter
    vector<bool> visited(n, false);
    int count = 0;
    
    // Step 3: Scan all nodes to discover components
    for (int i = 0; i < n; ++i) {
        if (!visited[i]) {
            count++; // Discover a new component
            dfs(graph, visited, i); // Mark all connected nodes in this component
        }
    }
    
    return count;
}
```

## 8. Dry Run
### Input:
`n = 5`, `edges = [[0, 1], [1, 2], [3, 4]]`

- **Initialization**:
  - `graph`: `0 -> [1]`, `1 -> [0, 2]`, `2 -> [1]`, `3 -> [4]`, `4 -> [3]`
  - `visited = [false, false, false, false, false]`
  - `count = 0`

- **Iteration i = 0**:
  - `visited[0]` is `false`.
  - Increment `count` to 1.
  - Call `dfs(graph, visited, 0)`:
    - Set `visited[0] = true`.
    - Neighbor `1` is unvisited. Call `dfs(graph, visited, 1)`:
      - Set `visited[1] = true`.
      - Neighbor `0` is visited.
      - Neighbor `2` is unvisited. Call `dfs(graph, visited, 2)`:
        - Set `visited[2] = true`.
        - Neighbor `1` is visited.
  - `visited = [true, true, true, false, false]`.

- **Iteration i = 1**:
  - `visited[1]` is `true`, skip.

- **Iteration i = 2**:
  - `visited[2]` is `true`, skip.

- **Iteration i = 3**:
  - `visited[3]` is `false`.
  - Increment `count` to 2.
  - Call `dfs(graph, visited, 3)`:
    - Set `visited[3] = true`.
    - Neighbor `4` is unvisited. Call `dfs(graph, visited, 4)`:
      - Set `visited[4] = true`.
      - Neighbor `3` is visited.
  - `visited = [true, true, true, true, true]`.

- **Iteration i = 4**:
  - `visited[4]` is `true`, skip.

- **End**:
  - Return `count` -> 2.

## 9. Complexity
- **Time**: $O(V + E)$ where $V = n$ and $E = \text{edges.size()}$. Building the graph takes $O(E)$ time, and the DFS traversal visits each vertex and edge once, taking $O(V + E)$ time.
- **Space**: $O(V + E)$ auxiliary space to store the adjacency list representation, plus $O(V)$ for the visited array and recursion stack.

## 10. Edge Cases
- **No Edges**: All nodes are isolated. Returns `n`.
- **Fully Connected Graph**: A single DFS traversal marks all nodes. Returns `1`.
- **n = 1**: Loop runs once, returns `1`.
- **Empty Graph (n = 0)**: Returns `0`.

## 11. Follow-up Questions
- **How can we solve this using Union-Find (Disjoint Set Union)?**
  - *Answer*: Start with `count = n`. Create a DSU structure where each node is its own parent. For each edge `[u, v]`, perform a union operation. If `find(u) != find(v)`, union them and decrement `count` by 1. Return the final `count`.
- **What is the complexity of the DSU approach?**
  - *Answer*: With path compression and union by rank, DSU runs in $O(V + E \cdot \alpha(V))$ time, where $\alpha$ is the Inverse Ackermann function (effectively constant, $\le 4$). DSU only requires $O(V)$ space (parent and rank arrays), which is more memory-efficient than storing the graph adjacency list.
- **When is DSU preferred over DFS/BFS for connected components?**
  - *Answer*: DSU is preferred in online scenarios where edges are added dynamically (stream-like) and we need to query the component count or connectivity of nodes in real-time.

## 12. Interview Explanation
"First, I would clarify if the nodes are labeled from 0 to n-1 and verify the maximum value of n. Then, I'd explain that we can count the connected components of an undirected graph by performing a DFS traversal. I would construct an adjacency list representation of the graph and initialize a visited array to false. I'll loop through each vertex from 0 to n-1. If a node has not been visited yet, it means we have found a new connected component. I'll increment our component counter and run DFS starting from that node to recursively mark all reachable nodes as visited. Alternatively, we could solve this using a Disjoint Set Union (DSU) structure where we start with n components and decrement the count whenever we union two disjoint nodes. The DFS approach runs in $O(V + E)$ time and space."
