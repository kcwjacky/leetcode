# 684. Redundant Connection
###### `medium` `union find` `graph`

In this problem, a tree is an undirected graph that is connected and has no cycles.

You are given a graph that started as a tree with n nodes labeled from 1 to n, with one additional edge added. The added edge has two different vertices chosen from 1 to n, and was not an edge that already existed. The graph is represented as an array edges of length n where edges[i] = [ai, bi] indicates that there is an edge between nodes ai and bi in the graph.

Return an edge that can be removed so that the resulting graph is a tree of n nodes. If there are multiple answers, return the answer that occurs last in the input.


Example 1:
> Input: edges = [[1,2],[1,3],[2,3]]
Output: [2,3]

Example 2:
> Input: edges = [[1,2],[2,3],[3,4],[1,4],[1,5]]
Output: [1,4]

---

```cpp
class Solution {
public:
    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
        vector<int> parent(edges.size() + 1);
        vector<int> weight(edges.size() + 1, 1);
        for (int i = 0; i < edges.size(); ++i)
            parent[i] = i;

        for (const auto& edge : edges) {
            if (!unionByWeight(parent, weight, edge[0], edge[1]))
                return edge;
        }
        return {};
    }

    int findSet(vector<int>& parent, int v) {
        if (parent[v] == v)
            return v;
        int pAfterCompression = findSet(parent, parent[v]);
        parent[v] = pAfterCompression;
        return parent[v];
    }

    bool unionByWeight(vector<int>& parent, vector<int>& weight, int v1, int v2) {
        int parentOfV1 = findSet(parent, v1);
        int parentOfV2 = findSet(parent, v2);
        if (parentOfV1 == parentOfV2) 
            return false;
        
        if (weight[v1] >= weight[v2]) {
            parent[parentOfV2] = parentOfV1;
            weight[parentOfV1] += weight[parentOfV2];
        } else {
            parent[parentOfV1] = parentOfV2;
            weight[parentOfV2] += weight[parentOfV1];
        }
        return true;
    }
};
```

可以在 union 或是 find 的時候做壓縮，壓縮樹高後，未來在 find parent 時效率會比較好。以下為 find 時壓縮的範例：

```cpp
int findSet(vector<int>& parent, int v) {
    if (parent[v] == v)
        return v;
    int pAfterCompression = findSet(parent, parent[v]);
    parent[v] = pAfterCompression;
    return parent[v];
}
```
