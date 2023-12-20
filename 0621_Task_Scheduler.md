# 621. Task Scheduler
###### `medium` `array` `hash table` `heap` `priority queue` `sorting` `counting` `greedy`

Given a characters array tasks, representing the tasks a CPU needs to do, where each letter represents a different task. Tasks could be done in any order. Each task is done in one unit of time. For each unit of time, the CPU could complete either one task or just be idle.

However, there is a non-negative integer n that represents the cooldown period between two same tasks (the same letter in the array), that is that there must be at least n units of time between any two same tasks.

Return the least number of units of times that the CPU will take to finish all the given tasks.


Example 1:
> Input: tasks = ["A","A","A","B","B","B"], n = 2 <br>
Output: 8 <br>
Explanation:  <br>
A -> B -> idle -> A -> B -> idle -> A -> B <br>
There is at least 2 units of time between any two same tasks.

Example 2:
> Input: tasks = ["A","A","A","B","B","B"], n = 0 <br>
Output: 6 <br>
Explanation: On this case any permutation of size 6 would work since n = 0. <br>
["A","A","A","B","B","B"] <br>
["A","B","A","B","A","B"] <br>
["B","B","B","A","A","A"] <br>
... <br>
And so on.

Example 3:
> Input: tasks = ["A","A","A","A","A","A","B","C","D","E","F","G"], n = 2 <br>
Output: 16 <br>
Explanation:  <br> 
One possible solution is <br>
A -> B -> C -> A -> D -> E -> A -> F -> G -> A -> idle -> idle -> A -> idle -> idle -> A <br>

---

## intuitive solution

直覺用 timer 的概念，幫每一個 task 計時。C 語言若無 hash table 則可直接用 array 直接紀錄 26 個字母 (task) 的 出現次數與 timer。

```cpp
class Solution {
public:
    int leastInterval(vector<char>& tasks, int n) {
        int res = 0;
        unordered_map<char, int> d, t;
        for (const auto& task : tasks) {
            d[task]++;
            t[task] = 0;
        }

        while (d.size() > 0) {
            char m = '\0';
            for (const auto& [k, v] : d) {
                if (t[k] == 0 && (m == '\0' || d[m] < d[k])) 
                    m = k;
            }
            if (m != '\0') {
                d[m]--;
                if (d[m] == 0) {
                    d.erase(m);
                    t.erase(m);
                } else
                    t[m] = n;
            }
            for (auto& [k, v] : d) {
                if (k != m && t[k] > 0) {
                    --t[k];
                }
            }
            res++;
        }
        return res;
    }
};
```

## optimizing timer mechanism to record next executable time

```cpp
class Solution {
public:
    int leastInterval(vector<char>& tasks, int n) {
        int current = 0;
        unordered_map<char, int> d, t;
        for (const auto& task : tasks) {
            d[task]++;
            t[task] = current;
        }

        while (d.size() > 0) {
            char m = '\0';
            for (const auto& [k, v] : d) {
                if (t[k] <= current && (m == '\0' || d[m] < d[k])) 
                    m = k;
            }
            if (m != '\0') {
                d[m]--;
                if (d[m] == 0) {
                    d.erase(m);
                    t.erase(m);
                } else
                    t[m] += (n + 1);
            }
            current++;
        }
        return current;
    }
};
```

### optimizing linear search for most tasks with heap (priority queue)

```cpp
class Solution {
public:
    int leastInterval(vector<char>& tasks, int n) {
        int current = 0;
        unordered_map<char, int> d, t;
        for (const auto& task : tasks) {
            d[task]++;
            t[task] = current;
        }
        vector<pair<char, int>> pairs;
        for (const auto& [k, v] : d) 
            pairs.push_back(pair<char, int>(k, v));

        make_heap(pairs.begin(), pairs.end(), 
            [](const pair<char, int>& a, const pair<char, int>& b) {return a.second >= b.second;});

        while (pairs.size() > 0) {
            sort_heap(pairs.begin(), pairs.end());
            for (size_t i = 0; i < pairs.size(); i++) {
                if (t[pairs[i].first] <= current) {
                    t[pairs[i].first] += (n + 1);
                    pairs[i].second--;
                    if (pairs[i].second == 0) {
                        pairs.erase(begin(pairs) + i);
                    }
                    break;
                }
            }
            current++;
        }
        return current;
    }
};
```