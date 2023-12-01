# 78. Subsets
###### `medium` `backtracking`

Given an integer array nums of unique elements, return all possible 
subsets (the power set).

The solution set must not contain duplicate subsets. Return the solution in any order.

Example 1:
> Input: nums = [1,2,3]
Output: [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]

Example 2:
> Input: nums = [0]
Output: [[],[0]]

---

Using backtracking to generate all possible subsets by recursively considering both "with" and "without" each input element. Once every element has been considered, the exploration for one subset is finished (base case).

## CPP

```cpp
class Solution {
public:
    void genSubsets(vector<vector<int>>& result, vector<int>& nums, vector<int>& elements, int depth) {
        if (depth == nums.size()) {
            result.push_back(vector<int>(elements));
            return;
        }

        genSubsets(result, nums, elements, depth + 1);
        elements.push_back(nums[depth]);
        genSubsets(result, nums, elements, depth + 1);
        elements.pop_back();
    }

    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> result;
        vector<int> elements;
        genSubsets(result, nums, elements, 0);
        return result;
    }
};
```

## C

```cpp
void genSubsets(int ***result, int *nums, int numsSize, int* returnSize, int** returnColumnSizes, int *elements, int elementsSize, int depth) {
    if (depth == numsSize) {
        int *subset = (int *)malloc(elementsSize * sizeof(int));
        for (size_t i = 0; i < elementsSize; i++)
            subset[i] = elements[i];
        
        (*returnSize)++;
        *result = (int **)realloc(*result, *returnSize * sizeof(int *));
        (*result)[*returnSize - 1] = subset;

        *returnColumnSizes = (int *)realloc(*returnColumnSizes, *returnSize * sizeof(int));
        (*returnColumnSizes)[*returnSize - 1] = elementsSize;
        return;
    }

    genSubsets(result, nums, numsSize, returnSize, returnColumnSizes, elements, elementsSize, depth + 1);
    elements[elementsSize] = nums[depth];
    genSubsets(result, nums, numsSize, returnSize, returnColumnSizes, elements, elementsSize + 1, depth + 1);

}
int** subsets(int* nums, int numsSize, int* returnSize, int** returnColumnSizes) {
    int **result = NULL;
    *returnSize = 0;
    *returnColumnSizes = NULL;

    int *elements = (int *)malloc(numsSize * sizeof(int));
    genSubsets(&result, nums, numsSize, returnSize, returnColumnSizes, elements, 0, 0);
    free(elements);
    return result;
}

```
