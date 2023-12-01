# 46. Permutations
###### `medium` `backtracking`

Given an array nums of distinct integers, return all the possible permutations. You can return the answer in any order.

Example 1:
> Input: nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]

Example 2:
> Input: nums = [0,1]
Output: [[0,1],[1,0]]

Example 3:
> Input: nums = [1]
Output: [[1]]

---

Using backtracking to generate all possible permutations by recursively considering both "take" and "not take" the element that has not been taken. Once every element has been taken, the exploration for one permutation is finished (base case).

## C

```cpp
void genPermutations(int ***result, int *nums, int numsSize, int *stack, int stackSize, int *returnSize, int** returnColumnSizes) {
    if (stackSize == numsSize) {
        int *permutation = (int *)malloc(numsSize * sizeof(int));
        for (size_t i = 0; i < numsSize; i++) {
            permutation[i] = stack[i];
        }
        (*returnSize)++;
        *result = realloc(*result, *returnSize * sizeof(int *));
        (*result)[*returnSize - 1] = permutation;
        
        *returnColumnSizes = realloc(*returnColumnSizes, *returnSize * sizeof(int));
        (*returnColumnSizes)[*returnSize - 1] = numsSize;
        return;
    }

    for (size_t i = 0; i < numsSize; i++) {
        bool found = false;
        for (size_t j = 0; j < stackSize; j++) {
            if (nums[i] == stack[j]) {
                found = true;
                break;
            }
        }
        if (!found) {
            stackSize++;
            stack[stackSize - 1] = nums[i];
            genPermutations(result, nums, numsSize, stack, stackSize, returnSize, returnColumnSizes);
            stackSize--;
        }
    }
}

int** permute(int* nums, int numsSize, int* returnSize, int** returnColumnSizes) {
    int **result = NULL;
    *returnSize = 0;
    *returnColumnSizes = NULL;
    int *stack = (int *)malloc(numsSize * sizeof(int));
    genPermutations(&result, nums, numsSize, stack, 0, returnSize, returnColumnSizes);
    return result;
}
```

## CPP

Replace the array with a set to speed up the process of generating a non-repeated element in a stack. O(N) $\rightarrow$ O(logN)

```cpp
class Solution {
public:
    typedef struct Pair {
        vector<int> path;
        set<int> intSet;
    } Pair;

    void permute(vector<vector<int>>& result, vector<int>& nums, Pair& pair) {
        if (pair.path.size() == nums.size()) {
            result.push_back(pair.path);
            return;
        }

        for (const auto& n : nums) {
            if (find(begin(pair.intSet), end(pair.intSet), n) != end(pair.intSet))
                continue;
            pair.path.push_back(n);
            pair.intSet.insert(n);
            permute(result, nums, pair);
            pair.path.pop_back();
            pair.intSet.erase(n);
        }
    }

    vector<vector<int>> permute(vector<int>& nums) {
        sort(begin(nums), end(nums));
        vector<vector<int>> result;
        Pair pair;
        permute(result, nums, pair);
        return result;
    }
};
```
