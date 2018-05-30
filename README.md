# Interview

## Bit operations

1. Clear out least significant set bit: `x & (x-1)`.
```P
(0110) & (0101) = (0100)
```
2. Isolate least significant set bit: `x & ~(x-1)`.
```P
(0110) & ~(0101) = (0110) & (1010) = (0010)
```
3. XOR operation are associative and commutative. Parity of a 64 bit number `O(log N)`:
```P
x = 42384729387
x ^= x >> 32  # Effectively does (First 32 bits) ^ (Last 32 bits)
x ^= x >> 16  # Effectively does (First 16 bits) ^ (Last 16 bits) of previous result
x ^= x >> 8  # So on
x ^= x >> 4
x ^= x >> 2
x ^= x >> 1
x &= 0x1
```
