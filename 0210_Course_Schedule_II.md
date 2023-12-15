# 210. Course Schedule II
###### `medium` `depth-first search` `graph` `topological sort`

There are a total of numCourses courses you have to take, labeled from 0 to numCourses - 1. You are given an array prerequisites where prerequisites[i] = [ai, bi] indicates that you must take course bi first if you want to take course ai.

For example, the pair [0, 1], indicates that to take course 0 you have to first take course 1.
Return the ordering of courses you should take to finish all courses. If there are many valid answers, return any of them. If it is impossible to finish all courses, return an empty array.

 

Example 1:
> Input: numCourses = 2, prerequisites = [[1,0]] <br>
Output: [0,1] <br>
Explanation: There are a total of 2 courses to take. To take course 1 you should have finished course 0. So the correct course order is [0,1].

> Example 2:
Input: numCourses = 4, prerequisites = [[1,0],[2,0],[3,1],[3,2]] <br>
Output: [0,2,1,3] <br>
Explanation: There are a total of 4 courses to take. To take course 3 you should have finished both courses 1 and 2. Both courses 1 and 2 should be taken after you finished course 0.
So one correct course order is [0,1,2,3]. Another correct ordering is [0,2,1,3].

> Example 3:
Input: numCourses = 1, prerequisites = [] <br>
Output: [0]

---

## C++

直接建 graph 然後走 DFS。並且增加在每個 vertex 中紀錄還有幾個先修還未被修過，若有則代表目前該 vertex(course) 還無法被走的條件。

```cpp
class Solution {
public:
    typedef struct vertex {
        int nPre;
        vector<int> follows;
    } Vertex;

    void dfs(vector<int>& result, vector<Vertex>& vertices, int course) {
        result.push_back(course);
        for (const auto& f : vertices[course].follows) {
            vertices[f].nPre--;
            if (vertices[f].nPre == 0) {
                dfs(result, vertices, f);
            }
        }
        return;
    }

    vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
        vector<Vertex> vertices(numCourses + 1);
        for (size_t i = 0; i <= numCourses; i++) {
            vertices[i].nPre = 0;
            vertices[i].follows = {};
        }

        for (const auto& p : prerequisites) {
            vertices[p[0]].nPre++;
            vertices[p[1]].follows.push_back(p[0]);
        }

        for (size_t i = 0; i < numCourses; i++) {
            if (vertices[i].nPre == 0) {
                vertices[numCourses].follows.push_back(i);
                vertices[i].nPre++;
            }
        }

        vector<int> result;
        dfs(result, vertices, numCourses);

        for (size_t i = 0; i < numCourses; i++) {
            if (vertices[i].nPre != 0)
                return {};
        }
        result.erase(begin(result));
        return result;
    }
};
```