# 1436. Destination City
###### `easy` `hash table` `sorting`

> 此問題的 C 語言解法有用到對字元陣列做排序

You are given the array paths, where paths[i] = [cityAi, cityBi] means there exists a direct path going from cityAi to cityBi. Return the destination city, that is, the city without any path outgoing to another city.

It is guaranteed that the graph of paths forms a line without any loop, therefore, there will be exactly one destination city.

Example 1:
> Input: paths = [["London","New York"],["New York","Lima"],["Lima","Sao Paulo"]] <br>
Output: "Sao Paulo"  <br>
Explanation: Starting at "London" city you will reach "Sao Paulo" city which is the destination city. Your trip consist of: "London" -> "New York" -> "Lima" -> "Sao Paulo".

Example 2:
> Input: paths = [["B","C"],["D","B"],["C","A"]] <br>
Output: "A" <br>
Explanation: All possible trips are:  <br>
"D" -> "B" -> "C" -> "A".  <br>
"B" -> "C" -> "A".  <br>
"C" -> "A".  <br>
"A".  <br>
Clearly the destination city is "A".

Example 3:
> Input: paths = [["A","Z"]] <br>
Output: "Z"

---

## C

We address this issue by utilizing sorting due to the insufficient support for hash tables and set structures in C lang. By utilizing two sorted arrays of strings, it is efficient to identify which city only appears in the destination cities array and not in the array of cities of departure.

```cpp
int cmpString(const void *a, const void *b) {
    return strcmp(*(const char**)a, *(const char**)b);
}

char* destCity(char*** paths, int pathsSize, int* pathsColSize) {
    char **arrivals = (char **)malloc(pathsSize * sizeof(char *));
    char **departures = (char **)malloc(pathsSize * sizeof(char *));

    for (size_t i = 0; i < pathsSize; i++) {
        departures[i] = paths[i][0];
        arrivals[i] = paths[i][1];
    }

    qsort(arrivals, pathsSize, sizeof(char *), cmpString);
    qsort(departures, pathsSize, sizeof(char *), cmpString);

    size_t idxA = 0, idxD = 0; 
    while (idxD < pathsSize) {
        if (strcmp(arrivals[idxA], departures[idxD]))
            idxD++;
        else {
            idxA++;
            idxD++;
        }
    }
    return arrivals[idxA];
}
```

## CPP

The intuitive implementation using a hash table in C++.

```cpp
class Solution {
public:
    string destCity(vector<vector<string>>& paths) {
        unordered_map<string, int> departures;
        for (const auto& path : paths) {
            departures[path[1]] = departures[path[1]];
            departures[path[0]]++;
        }
        for (const auto& [k, v] : departures) {
            if (v == 0)
                return k;
        }
        return "";
    }
};
```
