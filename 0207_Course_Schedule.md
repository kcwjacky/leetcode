# 207. Course Schedule
###### `medium` `depth-first search` `graph` `topological sort`

There are a total of numCourses courses you have to take, labeled from 0 to numCourses - 1. You are given an array prerequisites where prerequisites[i] = [ai, bi] indicates that you must take course bi first if you want to take course ai.

For example, the pair [0, 1], indicates that to take course 0 you have to first take course 1.
Return true if you can finish all courses. Otherwise, return false.

Example 1:
> Input: numCourses = 2, prerequisites = [[1,0]] <br>
Output: true <br>
Explanation: There are a total of 2 courses to take. 
To take course 1 you should have finished course 0. So it is possible.

Example 2:
> Input: numCourses = 2, prerequisites = [[1,0],[0,1]] <br>
Output: false <br>
Explanation: There are a total of 2 courses to take. 
To take course 1 you should have finished course 0, and to take course 0 you should also have finished course 1. So it is impossible.

---

## C++

先對每一個課程所需的幾堂先修課程做依賴關係的紀錄。之後 while-loop 的找出當前可以修的課程，設定為修過並且把列該課程為先修的課程的依賴數做減一。直到最後所有的課程的先修依賴性皆為零表示所有課程有辦法被修完。

class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        vector<int> deps(numCourses, 0);
        unordered_map<int, vector<int>> dependeds(numCourses);
        for (const auto& p : prerequisites) {
            deps[p[0]]++;
            dependeds[p[1]].push_back(p[0]);
        }
        
        bool changed = true;
        while (changed) {
            changed = false;
            for (size_t course = 0; course < numCourses; course++) {
                if (deps[course] == 0) {
                    for (const auto& d : dependeds[course]) {
                        deps[d]--;
                    }
                    deps[course] = -1;
                    changed = true;
                }
            }
        }
        for (const auto& d : deps) {
            if (d > 0) return false;
        }
        return true;
    }
};


## C

相較於 C++ 紀錄依賴性的解法，C 解法選擇直接用 graph 來解。先對所有先修關係畫出 graph，然後走 DFS 若有迴圈代表無法找出一個可行修課順序。

```cpp
typedef enum Color {white, grey, black} Color;
typedef struct vertex {
    Color color;
    size_t followsSize;
    struct vertex **follows;
} Vertex;

bool dfs(Vertex *v) {
    if (v->color == grey) return false;
    if (v->color == black) return true;

    v->color = grey;
    for (size_t i = 0; i < v->followsSize; i++) {
        if (!dfs(v->follows[i])) {
            return false;
        }
    }
    v->color = black;
    return true;
}

bool canFinish(int numCourses, int** prerequisites, int prerequisitesSize, int* prerequisitesColSize) {
    Vertex *vertices = (Vertex *)malloc(numCourses * sizeof(Vertex));
    Vertex freshman = {white, numCourses, (Vertex **)malloc(numCourses * sizeof(Vertex *))};
    
    for (size_t i = 0; i < numCourses; i++) {
        vertices[i].color = white;
        vertices[i].followsSize = 0;
        vertices[i].follows = NULL;
        freshman.follows[i] = &vertices[i];
    }

    for (size_t i = 0; i < prerequisitesSize; i++) {
        Vertex *pre = &vertices[prerequisites[i][1]];
        Vertex *follow = &vertices[prerequisites[i][0]];

        pre->followsSize++;
        pre->follows = (Vertex **)realloc(pre->follows, pre->followsSize * sizeof(Vertex *));
        pre->follows[pre->followsSize - 1] = follow;
    }

    bool res = dfs(&freshman);
    for (size_t i = 0; i < numCourses; i++) 
        free(vertices[i].follows);
    free(vertices);
    return res;
} 
```