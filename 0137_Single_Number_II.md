# 137. Single Number II
###### `medium` `array` `bit manipulation` 

Given an integer array nums where every element appears three times except for one, which appears exactly once. Find the single element and return it.

You must implement a solution with a linear runtime complexity and use only constant extra space.

Example 1:
> Input: nums = [2,2,3,2]
Output: 3

Example 2:
> Input: nums = [0,1,0,1,0,1,99]
Output: 99

---

## C++

###  intuitive solution

排序後個數 (C 語言) 或是 hash table (C++) 解決，以下寫排序版本

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        if (nums.size() == 1) return nums[0];
        sort(begin(nums), end(nums));
        if (nums[0] != nums[1])  
            return nums[0];
        if (nums[nums.size()-1] != nums[nums.size()-2])  
            return nums[nums.size()-1];
        bool repeated = true;
        int pre = nums[0];
        for (const auto& num : nums) {
            if (pre == num) {
                repeated = true;
            } else {
                if (!repeated) {
                    return pre;
                }
                pre = num;
                repeated = false;
            }
        }
        return 0;
    }
};
```

### remainder of modulo 3
題目給的限制為非 single number 的 numbers 各自都會出現三次。因此將所有 nums 都 XOR 起來的過程中，每一個 bit 為 0 或 1 必定是 `3n` 或是 `3n + 1`，所以我們只要紀錄每一個 bit 出現 1 的次數並且對其取 3 的餘數，就可以得知道 single number 在該 bit 的值為 1 或 0。  

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        vector<int> ones(32);
        for (const auto& n : nums) {
            uint32_t mask = 1;
            for (size_t i = 0; i < 32; i++) {
                if (n & mask)
                    ones[31 - i] = (ones[31 - i] + 1) % 3; 
                mask <<= 1;
            }
        }
        int res = 0;
        for (const auto &one : ones) {
            res <<= 1;
            res += one;
        }
        return res;
    }
};
```

上面版本的加速版本。所有位元一起做，並且使用 `a`, `b` 表示 2 個 bit 用來記錄目前次數 0 ~ 3，搭配上 n 整理出真值表的關係即可得解。詳細可參考[連結](https://www.youtube.com/watch?v=yd1JBRIlFeA) 

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int a = 0, b = 0;
        for (const auto& n : nums) {
            int new_a = (~a & b & n) | (a & ~b & ~n);
            b = (~a & ~b & n) | (~a & b & ~n);
            a = new_a;
        }
        return b;
    }
};
```

