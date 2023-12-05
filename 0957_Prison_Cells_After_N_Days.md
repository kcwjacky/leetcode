# 957. Prison Cells After N Days
###### `medium` `array` `math`

There are 8 prison cells in a row and each cell is either occupied or vacant.

Each day, whether the cell is occupied or vacant changes according to the following rules:

If a cell has two adjacent neighbors that are both occupied or both vacant, then the cell becomes occupied.
Otherwise, it becomes vacant.
Note that because the prison is a row, the first and the last cells in the row can't have two adjacent neighbors.

You are given an integer array cells where cells[i] == 1 if the ith cell is occupied and cells[i] == 0 if the ith cell is vacant, and you are given an integer n.

Return the state of the prison after n days (i.e., n such changes described above).

 

Example 1:

> Input: cells = [0,1,0,1,1,0,0,1], n = 7<br>
Output: [0,0,1,1,0,0,0,0]<br>
Explanation: The following table summarizes the state of the prison on each day:<br>
Day 0: [0, 1, 0, 1, 1, 0, 0, 1]<br>
Day 1: [0, 1, 1, 0, 0, 0, 0, 0]<br>
Day 2: [0, 0, 0, 0, 1, 1, 1, 0]<br>
Day 3: [0, 1, 1, 0, 0, 1, 0, 0]<br>
Day 4: [0, 0, 0, 0, 0, 1, 0, 0]<br>
Day 5: [0, 1, 1, 1, 0, 1, 0, 0]<br>
Day 6: [0, 0, 1, 0, 1, 1, 0, 0]<br>
Day 7: [0, 0, 1, 1, 0, 0, 0, 0]<br>

> Example 2:
Input: cells = [1,0,0,1,0,0,1,0], n = 1000000000<br>
Output: [0,0,1,1,1,1,1,0]<br>

---

It seems that the cycling phenomenon can be proven mathematically after operating at most fourteen times. However, a simple solution for this problem is to use an array to record each unrepeated result. 

## CPP

```cpp
class Solution {
public:
    vector<int> prisonAfterNDays(vector<int>& cells, int n) {
        vector<vector<int>> seen;
        for (size_t j = 0; j < n; j++) {
            vector<int> next(cells.size(), 0);
            for (size_t i = 1; i < 7; i++)
                next[i] = (cells[i - 1] == cells[i + 1]);
            if (count(begin(seen), end(seen), next) > 0) {
            //if (seen.size() and equal(seen[0].begin(), seen[0].end(), next.begin())) {
                break; 
            } else {
                seen.push_back(next);
            }
            cells = move(next);
        }
        return seen[(n - 1) % seen.size()];
    }
};
```

The space complexity of the above version can be optimized by using uint8_t to record each operating result instead of int[8]. I only implemented the optimized version in C since Leetcode detects "Memory limit exceeded."

## C

```cpp
uint8_t trans(int *array) {
    uint8_t res = 0;
    for (size_t i = 0; i < 8; i++) {
        res = (res << 1) | array[i];
    }
    return res;
} 

int* prisonAfterNDays(int* cells, int cellsSize, int n, int* returnSize) {
    uint8_t *seen = NULL;
    int seenSize = 0;

    for (size_t j = 0; j < n; j++) {
        int next[8] = {0};
        for (size_t i = 1; i < 7; i++) {
            next[i] = (cells[i-1] == cells[i+1]);
        }

        if (seenSize == 0 || trans(next) != seen[0]) {
            seenSize += 1;
            seen = realloc(seen, seenSize * sizeof(uint8_t));
            seen[seenSize-1] = trans(next);
            for (size_t i = 0; i < 8; i++) {
                cells[i] = next[i];
            }
        } else {
            break;
        }
    }
    int *res = calloc(8, sizeof(int));
    *returnSize = 8;
    uint8_t num = seen[(n - 1) % seenSize];
    for (size_t i = 0; i < 8; i++) {
        res[7-i] = num & 1;
        num = num >> 1;
    }
    return res;
}
```
