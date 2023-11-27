# 49. Group Anagrams
###### `medium` `sorting` `array`

Given an array of strings strs, group the anagrams together. You can return the answer in any order.

An Anagram is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

 

Example 1:
> Input: strs = ["eat","tea","tan","ate","nat","bat"]
Output: [["bat"],["nat","tan"],["ate","eat","tea"]]

Example 2:
> Input: strs = [""]
Output: [[""]]

Example 3:
> Input: strs = ["a"]
Output: [["a"]]

---

## C:

* 對每個單字中的字母做排序，可使相同字母異序詞（anagrams）變得一致
* 再針對上述結果對每一個詞做排序，可使 anagrams 聚集在一起

```cpp
typedef struct Pair {
    char* ori;
    char* sorted;
} Pair;

int charAscending(const void* a, const void* b) {
    return *(const char*)a - *(const char*)b;
}

int pairAscending(const void* a, const void* b) {
    const Pair* pa = (const Pair*)a;
    const Pair* pb = (const Pair*)b;
    return strcmp(pa->sorted, pb->sorted);
}

char*** groupAnagrams(char** strs, int strsSize, int* returnSize, int** returnColumnSizes) {
    Pair* pairs = malloc(strsSize*sizeof(Pair));
    for (size_t i = 0; i < strsSize; i++) {
        char* sorted = strdup(strs[i]);
        qsort(sorted, strlen(sorted), 1, charAscending);
        pairs[i].ori = strs[i];
        pairs[i].sorted = sorted;
    }

    qsort(pairs, strsSize, sizeof(Pair), pairAscending);

    char*** res = NULL;
    int* cols = NULL;
    *returnSize = 0;

    for (size_t i = 0; i < strsSize; i++) {
        if (i == 0 || strcmp(pairs[i-1].sorted, pairs[i].sorted)) {
            int newSize = *returnSize + 1;
            
            res = realloc(res, newSize*sizeof(char**));
            res[*returnSize] = malloc(sizeof(char*));
            res[*returnSize][0] = pairs[i].ori;

            cols = realloc(cols, newSize*sizeof(int));
            cols[newSize-1] = 1;

            *returnSize = newSize;
        } else {
            int newColSize = cols[*returnSize-1] + 1;
            res[*returnSize-1] = realloc(res[*returnSize-1], newColSize*sizeof(char*));
            res[*returnSize-1][newColSize-1] = pairs[i].ori;
            cols[*returnSize-1] = newColSize;
        }
    }
    *returnColumnSizes = cols;
    return res;
}

```

## C++:
* C++/Python 可以用 hashmap，簡單很多
```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> tb;
        for (auto const &str: strs) {
            string sorted = str;
            sort(sorted.begin(), sorted.end());
            tb[sorted].push_back(str);
        }
        
        vector<vector<string>> res;
        for (auto const& pair : tb) {
            res.push_back(pair.second);
        }
        return res;
    }
};
```

## Python:
```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        tb = {}
        for word in strs:
            sorted_str = "".join(sorted(word))
            if sorted_str not in tb:
                tb[sorted_str] = [word, ]
            else:
                tb[sorted_str].append(word)
        return [[ori for ori in tb[sorted_str]] for sorted_str in tb]
     
```



