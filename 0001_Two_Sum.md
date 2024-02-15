# 1. Two Sum
###### `easy` `sorting` `array` `hash table`

Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

You can return the answer in any order.

 

Example 1:
> Input: nums = [2,7,11,15], target = 9
Output: [0,1]
Explanation: Because nums[0] + nums[1] == 9, we return [0, 1].

Example 2:
> Input: nums = [3,2,4], target = 6 
Output: [1,2]

Example 3:
> Input: nums = [3,3], target = 6
Output: [0,1]

---

## Python

使用 hashmap

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        tb = {}
        for i, n in enumerate(nums):
            if target - n in tb:
                return [tb[target-n], i]
            else:
                tb[n] = i

```

## C

* 沒有 hashmap 可用
* 改用 BST，將所有 nums 做排序，取 base 由小到大去找解
* base, left, ..., mid, ..., right
  * complement = target - base
  * 當位於 mid 的值大於 complement 時，代表 right 太大
  * 當位於 mid 的值小於 complement 時，代表 left 太大
  * 當位於 mid 的值等於 complement，表示找到解
  * 當 left > right 時，代表此 base 無解
  * 換下一個 base

```cpp
typedef struct pair {
  int val;
  int index;
} Pair;

int cmp(const void* a, const void* b) {
  return ((const Pair*)a)->val - ((const Pair*)b)->val;
}

int* twoSum(int* nums, int numsSize, int target, int* returnSize) {
  Pair* pairs = malloc(numsSize * sizeof(Pair));
  for (size_t i = 0; i < numsSize; i++) {
    pairs[i].val = nums[i];
    pairs[i].index = i; 
  }

  qsort(pairs, numsSize, sizeof(Pair), cmp);

  int* res = malloc(2 * sizeof(int));
  for (size_t i = 0; i < numsSize; i++) {
    int complement = target - pairs[i].val;
    size_t left = i + 1;
    size_t right = numsSize - 1;

    while (left <= right) {
      int mid = left + (right - left) / 2;
      if (pairs[mid].val > complement) 
        right--;
      else if (pairs[mid].val < complement) 
        left++;
      else {
        *returnSize = 2;
        res[0] = pairs[i].index;
        res[1] = pairs[mid].index;
        return res;
      }
    }
  }
  return NULL;
}
```

## CPP

### 使用 hashmap

```cpp
class Solution {
public:
  vector<int> twoSum(vector<int>& nums, int target) {
    unordered_map<int, int> tb;
    for (int i = 0; i < nums.size(); i++) {
      if (tb.count(target - nums[i]) != 0)
        return {tb[target-nums[i]], i};
      else
        tb[nums[i]] = i;
    }
    return {};
  }
};

```

### 使用 BST

```cpp
class Solution {
public:
  vector<int> twoSum(vector<int>& nums, int target) {
    vector<pair<int, int>> pairs;
    for (int i = 0; i < nums.size(); i++) {
      pairs.push_back(pair<int, int>(nums[i], i));
    }

    sort(begin(pairs), end(pairs), 
    [](pair<int, int> pa, pair<int, int> pb) {return pa.first < pb.first;});

    for (int i = 0; i < nums.size(); i++) {
      int base = pairs[i].first;
      int complement = target - base;
      int left = i + 1;
      int right = nums.size() - 1;

      while (left <= right) {
        int mid = left + (right - left);
        if (pairs[mid].first > complement)
          right--;
        else if (pairs[mid].first < complement)
          left++;
        else {
          return {pairs[i].second, pairs[mid].second};
        }
      }
    }
    return {};
  }
};
```