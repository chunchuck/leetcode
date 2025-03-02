# 754. Reach a Number

You are standing at position 0 on 
You are standing at position `0` on an infinite number line. There is a destination at position `target`.

You can make some number of moves numMoves so that:
On each move, 
- you can either go left or right.
- During the ith move (starting from `i` == 1 to `i` == numMoves), you take `i` steps in the chosen direction.

Given the integer `target`, return the minimum number of moves required (i.e., the minimum numMoves) to reach the destination.
```
Example 1:

Input: target = 2
Output: 3
Explanation:
On the 1st move, we step from 0 to 1 (1 step).
On the 2nd move, we step from 1 to -1 (2 steps).
On the 3rd move, we step from -1 to 2 (3 steps).

Example 2:

Input: target = 3
Output: 2
Explanation:
On the 1st move, we step from 0 to 1 (1 step).
On the 2nd move, we step from 1 to 3 (2 steps).
```

### Restating the problem
Find smallest N s.t. `1 +/- 2 +/- 3 +/- ... +/- N = target`

### Fact 1
We can pick a subset of numbers in `[1, n]` and sum them to make any integers from `1` to `T(n)`, where
`T(n) = 1 + 2 + 3 + ... + n = (n^2 + n) / 2`.
```
e.g.
1 = 1
2 = 2
3 = 3
4 = 1 + 3
5 = 2 + 3
6 = 1 + 2 + 3
```
_Proof_

Proof by induction, suppose this is true for `n = k`. There are exactly k+1 numbers between T(k) and T(k+1)

```T(k) + 1, T(k) + 2, ..., T(k) + k + 1```. 
- To make T(k) + 1, we take away k from T(k) and add k+1
- To make T(k) + 2, we take away k-1 from T(k) and add k+1
- ...
- To make T(k) + k, we take away 1 from T(k) and add k+1
- To make T(k) + k + 1, we add T(k) to k+1

Therefore Fact 1 is true for `n = k + 1`.
Fact 1 is true for `n = 1`, Fact 1 is true for `n >= 1` by induction.

### Fact 2
`1 +/- 2 +/- 3 +/- ... +/- N` can be:
- every even integer in `[-T(N), T(N)]` if `T(N)` is even.
- every odd integer in `[-T(N), T(N)]` if `T(N)` is odd.
```
e.g.
0, 2, 4, 6, -6, -4, -2 for N = 3
-3, -1, 1, 3 for N = 2
```
_Proof_

Consider T(N) - 2k, where 1 <= k <= T(N).
1. from Fact 1, let `k` be the sum of a subset of numbers in `[1, N]`, `k` is in `[1, T(N)]`
2. turn that subset of numbers into negative numbers in this sum `1 + 2 + ... + N`, which will equal T(N) - 2k.
```
e.g. N = 5
T(N) = 15
13  = 15 - 2 * 1
11  = 15 - 2 * 2   = 1 - 2 + 3 + 4 + 5
...
1   = 15 - 2 * 7   = 1 - 2 + 3 + 4 - 5
-1  = 15 - 2 * 8   = 1 + 2 - 3 + 4 - 5
...
-15 = 15 - 2 * 15  = -1 - 2 - 3 - 4 - 5
```
3. If T(N) is even, then T(N) - 2k is even.
4. If T(N) is odd, then T(N) - 2k is odd.

### When is T(n) even or odd?
- T(n) is even if n is `3 mod 4` or `0 mod 4`.
- T(n) is odd if n is `1 mod 4` or `2 mod 4`.
  
This can be easily verified.

### Original Problem
Find integer `n` such that `T(n) >= abs(target)`:
```
n >= ceil( sqrt(2 * target + 0.25) - 0.5 )
```
If `target` is even, find the smallest `a >= n` such that `T(a)` is even, and by Fact 2, numbers in the form `1 +/- 2 +/- ... +/- a` will include `target`.
Vice versa for if `target` is odd.

This should be a constant time solution.

### Code
```python
class Solution:
    def reachNumber(self, target: int) -> int:
        import math
        target = abs(target)
        n = math.ceil((target * 2 + 0.25) ** 0.5 - 0.5)

        if target % 2 == 0:
            return n + [0, 2, 1, 0][n % 4]

        else:
            return n + [1, 0, 0, 2][n % 4]
```
