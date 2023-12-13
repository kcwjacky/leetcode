# 190. Reverse Bits
###### `Divide and Conquer` `Bit Manipulation` `easy`

Reverse bits of a given 32 bits unsigned integer.

Example 1:
> Input: n = 00000010100101000001111010011100 <br>
Output:    964176192 (00111001011110000010100101000000) <br>
Explanation: The input binary string 00000010100101000001111010011100 represents the unsigned integer 43261596, so return 964176192 which its binary representation is 00111001011110000010100101000000. <br>

Example 2:
> Input: n = 11111111111111111111111111111101 <br>
Output:   3221225471 (10111111111111111111111111111111) <br>
Explanation: The input binary string 11111111111111111111111111111101 represents the unsigned integer 4294967293, so return 3221225471 which its binary representation is 10111111111111111111111111111111. <br>

--- 

The first code applies a straightforward loop to individually reverse each bit from LSB to MSB, while the second code utilizes a divide-and-conquer approach with multiple swaps. It starts with exchanging the left and right 16 bits, followed by 8-bit exchanges within each of these halves. This process continues for 4, 2, and 1-bit swaps until all 32 bits are reversed.

## C & CPP

```cpp
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        uint32_t res = 0;
        for (size_t i = 0; i < 32; i++) {
            res <<= 1;
            res |= n & 1;
            n >>= 1;
        }
        return res;
    }
};
```

```cpp
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        n = ((n & 0b00000000000000001111111111111111) << 16) | ((n & 0b11111111111111110000000000000000) >> 16);
        n = ((n & 0b00000000111111110000000011111111) << 8) | ((n & 0b11111111000000001111111100000000) >> 8);
        n = ((n & 0b00001111000011110000111100001111) << 4) | ((n & 0b11110000111100001111000011110000) >> 4);
        n = ((n & 0b00110011001100110011001100110011) << 2) | ((n & 0b11001100110011001100110011001100) >> 2);
        n = ((n & 0b01010101010101010101010101010101) << 1) | ((n & 0b10101010101010101010101010101010) >> 1);
        return n;

    }
};
```