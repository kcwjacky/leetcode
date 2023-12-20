# 260. Single Number III
###### `medium` `array` `bit manipulation` 

Given an integer array nums, in which exactly two elements appear only once and all the other elements appear exactly twice. Find the two elements that appear only once. You can return the answer in any order.

You must write an algorithm that runs in linear runtime complexity and uses only constant extra space.

Example 1:
> Input: nums = [1,2,1,3,2,5] <br>
Output: [3,5] <br>
Explanation:  [5, 3] is also a valid answer.

Example 2:
> Input: nums = [-1,0] <br>
Output: [-1,0]

Example 3:
> Input: nums = [0,1] <br>
Output: [1,0]

---

## C++

有兩個 number (令為 `n1` & `n2`) 只會出現一次，其他會出現兩次。所以全部數字 XOR 在一起後的值代表了 `n1` 與 `n2` 的差異值 (令為 `r`)。為 `1` 的 bit 表示該 bit 兩數相異，而為 `0` 的 bit 表示兩數在該 bit 的值相同。因此我們選擇最右邊的相異 bit 來做分類 (技巧：`mask = r & ~(r - 1);`)，可以將所有 nums 中該 bit 為 `1` 的數都 XOR 在一起得 `r1`，即為其中一個該 bit 為 `1` 的 single number，而 `r1 ^ r` 可得另一個 single number。


```cpp
class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        uint32_t r = 0;
        for (const uint32_t& n : nums)
            r ^= n;
        uint32_t mask = r & ~(r - 1);
        int r1 = 0;
        for (const uint32_t& n : nums) {
            if (n & mask)
                r1 ^= n;
        }
        return {static_cast<int>(r1), static_cast<int>(r1^r)};
    }
};
```
