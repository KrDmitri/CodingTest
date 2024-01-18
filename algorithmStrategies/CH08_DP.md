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

### 와일드카드(WILDCARD)
```
import sys

def match(pattern, file, p, f):
    global dp
    if dp[p][f] != -1:
        return dp[p][f]

    while p < len(pattern) and f < len(file) and (pattern[p] == file[f] or pattern[p] == '?'):
        p += 1
        f += 1

    if p == len(pattern):
        if len(file) == f:
            return 1
        else:
            return 0
        
    if pattern[p] == '*':
        skip = 0
        while f + skip <= len(file):
            if match(pattern, file, p + 1, f + skip):
                return 1
            skip += 1
            
    return 0

dp = [[-1] * 101 for _ in range(101)]

T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    pattern = sys.stdin.readline().rstrip()
    numFiles = int(sys.stdin.readline().rstrip())
    files = [sys.stdin.readline().rstrip() for _ in range(numFiles)]
    files.sort()
    for i in range(101):
        for j in range(101):
            dp[i][j] = -1

    for file in files:
        if match(pattern, file, 0, 0) == 1:
            print(file)
```

### 삼각형 위의 최대 경로(TRIANGLEPATH)
```
import sys
import copy

T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    n = int(sys.stdin.readline().rstrip())
    graph = [[0] * n for _ in range(n)]
    for i in range(n):
        temp = list(map(int, sys.stdin.readline().rstrip().split()))
        for j in range(len(temp)):
            graph[i][j] = temp[j]

    dp = copy.deepcopy(graph)
    for i in range(n - 1):
        for j in range(n - 1):
            dp[i + 1][j] = max(dp[i][j] + graph[i + 1][j], dp[i + 1][j])
            dp[i + 1][j + 1] = max(dp[i][j] + graph[i + 1][j + 1], dp[i + 1][j + 1])

    print(max(dp[n - 1]))
```

### 최대 증가 부분 수열(LIS)
```
import sys

def lis(idx):
    global cache
    if cache[idx] != -1:
        return cache[idx]
    ret = 1
    for i in range(idx + 1, len(nums)):
        if nums[i] > nums[idx]:
            cache[i] = lis(i)
            ret = max(ret, 1 + cache[i])
    return ret


T = int(sys.stdin.readline().rstrip())
cache = [-1] * 500

for _ in range(T):
    N = int(sys.stdin.readline().rstrip())
    nums = list(map(int, sys.stdin.readline().rstrip().split()))
    for i in range(500):
        cache[i] = -1
    ans = 0
    for i in range(len(nums)):
        ans = max(ans, lis(i))
    print(ans)
```
### 합친 LIS(JLIS)
```
import sys
NEGINF = (-1) * int(1e9)

def jlis(a, b):
    global dp
    if dp[a + 1][b + 1] != -1:
        return dp[a + 1][b + 1]
    dp[a + 1][b + 1] = 2
    nowA = NEGINF if a == -1 else aList[a]
    nowB = NEGINF if b == -1 else bList[b]
    maxElem = max(nowA, nowB)

    for i in range(a + 1, n):
        if aList[i] > maxElem:
            dp[a + 1][b + 1] = max(dp[a + 1][b + 1], jlis(i, b) + 1)
    for i in range(b + 1, m):
        if bList[i] > maxElem:
            dp[a + 1][b + 1] = max(dp[a + 1][b + 1], jlis(a, i) + 1)
    return dp[a + 1][b + 1]


T = int(sys.stdin.readline().rstrip())
dp = [[-1] * 101 for _ in range(101)]

for _ in range(T):
    n, m = map(int, sys.stdin.readline().rstrip().split())
    aList = list(map(int, sys.stdin.readline().rstrip().split()))
    bList = list(map(int, sys.stdin.readline().rstrip().split()))
    for i in range(101):
        for j in range(101):
            dp[i][j] = -1

    ans = jlis(-1, -1)
    print(ans - 2)
```
