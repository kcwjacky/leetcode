# 2092. Find All People With Secret
###### `hard` `union find` 

You are given an integer n indicating there are n people numbered from 0 to n - 1. You are also given a 0-indexed 2D integer array meetings where meetings[i] = [xi, yi, timei] indicates that person xi and person yi have a meeting at timei. A person may attend multiple meetings at the same time. Finally, you are given an integer firstPerson.

Person 0 has a secret and initially shares the secret with a person firstPerson at time 0. This secret is then shared every time a meeting takes place with a person that has the secret. More formally, for every meeting, if a person xi has the secret at timei, then they will share the secret with person yi, and vice versa.

The secrets are shared instantaneously. That is, a person may receive the secret and share it with people in other meetings within the same time frame.

Return a list of all the people that have the secret after all the meetings have taken place. You may return the answer in any order.


Example 1:
> Input: n = 6, meetings = [[1,2,5],[2,3,8],[1,5,10]], firstPerson = 1 <br>
Output: [0,1,2,3,5] <br>
Explanation: <br>
At time 0, person 0 shares the secret with person 1. <br>
At time 5, person 1 shares the secret with person 2. <br>
At time 8, person 2 shares the secret with person 3. <br>
At time 10, person 1 shares the secret with person 5. <br>​​​​
Thus, people 0, 1, 2, 3, and 5 know the secret after all the meetings. <br>

Example 2:
> Input: n = 4, meetings = [[3,1,3],[1,2,2],[0,3,3]], firstPerson = 3 <br>
Output: [0,1,3] <br>
Explanation: <br>
At time 0, person 0 shares the secret with person 3. <br>
At time 2, neither person 1 nor person 2 know the secret. <br>
At time 3, person 3 shares the secret with person 0 and person 1. <br>
Thus, people 0, 1, and 3 know the secret after all the meetings. <br>

Example 3:
> Input: n = 5, meetings = [[3,4,2],[1,2,1],[2,3,1]], firstPerson = 1 <br>
Output: [0,1,2,3,4] <br>
Explanation: <br>
At time 0, person 0 shares the secret with person 1. <br>
At time 1, person 1 shares the secret with person 2, and person 2 shares the secret with person 3. <br>
Note that person 2 can share the secret at the same time as receiving it. <br>
At time 2, person 3 shares the secret with person 4. <br>
Thus, people 0, 1, 2, 3, and 4 know the secret after all the meetings. <br>

---

```cpp
class Solution {
public:
    int findSet(vector<int>& parent, const int& v) {
        if (parent[v] == v) 
            return v;
        int pAfterCompression = findSet(parent, parent[v]);
        parent[v] = pAfterCompression;
        return parent[v];
    }

    bool unionSet(vector<int>& parent, vector<int>& rank, const int& v1, const int& v2) {
        int p1 = findSet(parent, v1);
        int p2 = findSet(parent, v2);
        if (p1 == p2) return false;

        if (rank[p1] > rank[p2]) {
            parent[p2] = p1;
            rank[p1] += rank[p2];
        } else {
            parent[p1] = p2;
            rank[p2] += rank[p1]; 
        }
        return true;
    }

    vector<int> findAllPeople(int n, vector<vector<int>>& meetings, int firstPerson) {
        vector<int> rank(n, 1), parent(n);
        for (int i = 0; i < n; ++i)
            parent[i] = i;

        // 將 0 & firstPerson 分一群
        unionSet(parent, rank, 0, firstPerson);

        // 為簡單後續程式碼，先用時間分 meetings
        map<int, vector<pair<int, int>>> timeMeetings;
        for (const auto& m : meetings) 
            timeMeetings[m[2]].push_back({m[0], m[1]});

        // 將每個時段所有參與者分群，知道秘密者的 parent 會與 0 的 parent 相同
        // 反之不知道者，需要 reset 該 DSU 狀態
        for (const auto& [time, meetings_] : timeMeetings) {
            unordered_set<int> attend;
            for (const auto& m : meetings_) {
                unionSet(parent, rank, m.first, m.second);
                attend.insert(m.first);
                attend.insert(m.second);
            }

            for (const int& p : attend) {
                if (findSet(parent, p) != findSet(parent, 0)) {
                    parent[p] = p;
                    rank[p] = 1;
                }
            }
        }
        
        // 與 0 的 parent 相同為最後答案
        vector<int> res;
        for (int i = 0; i < n; ++i)
            if (findSet(parent, i) == findSet(parent, 0))
                res.push_back(i);
        return res;
    }
};
```

