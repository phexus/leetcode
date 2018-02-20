# Clone Graph

Tags: Depth-first Search, Breath-first Search, Graph

## Problem

Clone an undirected graph. Each node in the graph contains a `label` and a list of its `neighbors`.

## Solution 1 - BFS

BFS 的做法还是套模板，只不过需要一个 map 来记录新老结点的对应关系，同时也避免了重复添加

```cpp
/**
 * Definition for undirected graph.
 * struct UndirectedGraphNode {
 *     int label;
 *     vector<UndirectedGraphNode *> neighbors;
 *     UndirectedGraphNode(int x) : label(x) {};
 * };
 */
class Solution {
public:
    UndirectedGraphNode *cloneGraph(UndirectedGraphNode *node) {
        if (!node) return NULL;
        unordered_map<UndirectedGraphNode*, UndirectedGraphNode*> map;
        UndirectedGraphNode* copy = new UndirectedGraphNode(node -> label);
        map[node] = copy;
        queue<UndirectedGraphNode*> que;
        que.push(node);
        
        while (!que.empty()) {
            UndirectedGraphNode* curr = que.front();
            que.pop();
            for (UndirectedGraphNode* neighbor : curr -> neighbors) {
                if (map.count(neighbor) == 0) {
                    UndirectedGraphNode* copyNeighbor = new UndirectedGraphNode(neighbor -> label);
                    que.push(neighbor);
                    map[neighbor] = copyNeighbor;
                }
                
                map[curr] -> neighbors.push_back(map[neighbor]);
            }
        }
        
        return copy;
    }
};
```

## Solution 2 - DFS

这里用递归更加简洁，注意好对应关系。

```cpp
/**
 * Definition for undirected graph.
 * struct UndirectedGraphNode {
 *     int label;
 *     vector<UndirectedGraphNode *> neighbors;
 *     UndirectedGraphNode(int x) : label(x) {};
 * };
 */
class Solution {
public:
    unordered_map<UndirectedGraphNode*, UndirectedGraphNode*> map;
    
    UndirectedGraphNode *cloneGraph(UndirectedGraphNode *node) {
        if (!node) return NULL;
        
        if (map.count(node) == 0) {
            map[node] = new UndirectedGraphNode(node -> label);
            for (UndirectedGraphNode* neighbor : node -> neighbors) map[node] -> neighbors.push_back(cloneGraph(neighbor));
        }
        
        return map[node];
    }
};
```

