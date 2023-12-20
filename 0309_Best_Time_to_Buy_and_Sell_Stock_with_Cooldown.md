# 309. Best Time to Buy and Sell Stock with Cooldown
###### `medium` `dynamic programming`

You are given an array prices where prices[i] is the price of a given stock on the ith day.

Find the maximum profit you can achieve. You may complete as many transactions as you like (i.e., buy one and sell one share of the stock multiple times) with the following restrictions:

After you sell your stock, you cannot buy stock on the next day (i.e., cooldown one day).
Note: You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again).

Example 1:
> Input: prices = [1,2,3,0,2] <br>
Output: 3 <br>
Explanation: transactions = [buy, sell, cooldown, buy, sell]

Example 2:
> Input: prices = [1] <br>
Output: 0

---

## C++

### recursion

TLE

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        return maxProfit(prices, prices.size() - 1);
    }

    int maxProfit(vector<int>& prices, int i) {
        if (i == 0) return 0;
        if (i == 1) return max(0, prices[1] - prices[0]);

        int buyOnfirstDay = max(0, prices[i] - prices[0]);
        int buyOnSecondDay = max(0, prices[i] - prices[1]);
        int m = max(buyOnfirstDay, buyOnSecondDay);
        int onDayBefore = maxProfit(prices, i - 1);
        m = max(m, onDayBefore);
        for (size_t j = 2; j < i; j++) {
            m = max(m, prices[i] - prices[j] + maxProfit(prices, j - 2));
        }
        return m;
    }
};
```

### DP

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        unordered_map<int, int> cache;
        for (int i = 0; i < prices.size(); i++) 
            cache[i] = -1;
        return maxProfit(prices, cache, prices.size() - 1);
    }

    int maxProfit(vector<int>& prices, unordered_map<int, int>& cache, int i) {
        if (cache[i] != -1)
            return cache[i];

        if (i == 0) return 0;
        if (i == 1) return max(0, prices[1] - prices[0]);

        int buyOnfirstDay = max(0, prices[i] - prices[0]); 
        int buyOnSecondDay = max(0, prices[i] - prices[1]); 
        int m = max(buyOnfirstDay, buyOnSecondDay);
        int oneDayBefore = maxProfit(prices, cache, i - 1);
        m = max(m , oneDayBefore);
        for (size_t j = 2; j < i; j++) {
            m = max(m, prices[i] - prices[j] + maxProfit(prices, cache, j - 2));
        }
        cache[i] = m;
        return cache[i];
    }
};
```

### DP in bottom up method

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        vector<int> cache = {0};
        if (prices.size() == 1)
            return cache[0];
        cache.push_back(max(0, prices[1] - prices[0]));
        if (prices.size() == 1) 
            return cache[1];

        for (size_t i = 2; i < prices.size(); i++) {
            int buyOnfirstDay = max(0, prices[i] - prices[0]); 
            int buyOnSecondDay = max(0, prices[i] - prices[1]); 
            int m = max(buyOnfirstDay, buyOnSecondDay);
            m = max(m, cache[i-1]);
            for (size_t j = 2; j < i; j++) {
                m = max(m, cache[j-2] + prices[i] - prices[j]);
            }
            cache.push_back(m);
        }
        return cache[prices.size() - 1];
    }
};
```

以下列舉出 bottom up 求 cache 所需的變數，方便對照上面的 for-loop。

```
cache[2] = max(
    // 0, 1, 2
    buyOnfirstDay,
    buyOnSecondDay,
    cache[1], 
);
cache[3] = max(
    // 0, 1, 2, 3
    buyOnfirstDay,
    buyOnSecondDay,
    cache[0] + prices[3] - prices[2],
    cache[2], 
);
cache[4] = max(
    // 0, 1, 2, 3, 4
    buyOnfirstDay,
    buyOnSecondDay,
    cache[0] + prices[4] - prices[2],
    cache[1] + prices[4] - prices[3],
    cache[3], 
);
```