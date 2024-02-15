# 461. Hamming Distance
###### `easy` `bit manipulation`

The Hamming distance between two integers is the number of positions at which the corresponding bits are different.

Given two integers x and y, return the Hamming distance between them.

Example 1: 
> Input: x = 1, y = 4 <br>
Output: 2 <br>
Explanation: <br>
1   (0 0 0 1) <br>
4   (0 1 0 0) <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;↑&nbsp;&nbsp;&nbsp;↑<br>
The above arrows point to positions where the corresponding bits are different.

Example 2:
> Input: x = 3, y = 1 <br>
Output: 1 

---

## C

The expression `x & (x - 1)` is used to set the lowest significant bit of the binary representation of x to 0. 
For example, let `x` equals to `6`, `1010 & 1001 = 1000`.

```cpp
int hammingDistance(int x, int y) {
    int d = x ^ y;
    int res = 0;
    while (d) {
        res++;
        d &= d - 1;
    }
    return res;
}
```