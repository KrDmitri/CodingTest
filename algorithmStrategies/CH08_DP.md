# CH08 동적 계획법
### 외발 뛰기(JUMPGAME) - 브루트포스 풀이
```
import sys

def dfs(x, y):
    if x == n - 1 and y == n - 1:
        return True

    num = graph[x][y]
    if x + num < n:
        flag = dfs(x + num, y)
        if flag:
            return True
    if y + num < n:
        flag = dfs(x, y + num)
        if flag:
            return True

T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    n = int(sys.stdin.readline().rstrip())
    graph = [list(map(int, sys.stdin.readline().rstrip().split())) for _ in range(n)]
    flag = False
    flag = dfs(0, 0)

    print('YES' if flag else 'NO')
```

### 외발 뛰기(JUMPGAME) - DP 풀이
```
import sys

T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    n = int(sys.stdin.readline().rstrip())
    graph = [list(map(int, sys.stdin.readline().rstrip().split())) for _ in range(n)]
    dp = [[False] * n for _ in range(n)]
    dp[0][0] = True

    for i in range(n):
        for j in range(n):
            if dp[i][j]:
                num = graph[i][j]
                if i + num < n:
                    dp[i + num][j] = True
                if j + num < n:
                    dp[i][j + num] = True

    print('YES' if dp[n - 1][n - 1] else 'NO')
```
