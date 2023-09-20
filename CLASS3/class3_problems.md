# solvec.ac CLASS 3 문제 풀이
### 피보나치
```
import sys

T = int(sys.stdin.readline().rstrip())

dp = [[0, 0] for _ in range(41)]
dp[0] = [1, 0]
dp[1] = [0, 1]
for i in range(2, 41):
    dp[i][0] = dp[i - 1][0] + dp[i - 2][0]
    dp[i][1] = dp[i - 1][1] + dp[i - 2][1]

for _ in range(T):
    N = int(sys.stdin.readline().rstrip())

    print(dp[N][0], dp[N][1])
```
