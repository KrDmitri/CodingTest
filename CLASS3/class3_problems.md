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

### 유기농 배추
```
import sys
from collections import deque

def searchCabbage(i, j):
    q = deque([])
    visited[i][j] = True
    q.append([i, j])
    while q:
        x, y = q.popleft()
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            if nx < 0 or nx >= N or ny < 0 or ny >= M or visited[nx][ny] or graph[nx][ny] == 0:
                continue
            visited[nx][ny] = True
            q.append([nx, ny])


dx = [-1, 0, 1, 0]
dy = [0, 1, 0, -1]
T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    M, N, K = map(int, sys.stdin.readline().rstrip().split())
    graph = [[0] * M for _ in range(N)]
    for _ in range(K):
        x, y = map(int, sys.stdin.readline().rstrip().split())
        graph[y][x] = 1

    visited = [[False] * M for _ in range(N)]

    ans = 0
    for i in range(N):
        for j in range(M):
            if graph[i][j] == 1 and not visited[i][j]:
                searchCabbage(i, j)
                ans += 1
    print(ans)
```

### Z
```
import sys

def whichPos(x, y):
    if x == 0 and y == 0:
        return 0
    elif x == 0 and y == 1:
        return 1
    elif x == 1 and y == 0:
        return 2
    else:
        return 3

def dfs(x, y, depth, temp):
    # 1. 4개의 칸 중 몇번째인지 찾는다
    px = x // (2 ** (depth - 1))
    py = y // (2 ** (depth - 1))
    pos = whichPos(px, py)
    # 2. 칸의 순서를 보고 temp 업데이트 후 다음 depth 로 넘어간다
    temp += pos * (2 ** (2 * (depth - 1)))
    # 3. 다음 depth에서 쓸 x, y 구하기
    if px == 1:
        x -= 2 ** (depth - 1)
    if py == 1:
        y -= 2 ** (depth - 1)
    # 3. depth가 1일때 답을 구할 수 있다.
    if depth == 1:
        return temp
    return dfs(x, y, depth - 1, temp)


N, r, c = map(int, sys.stdin.readline().rstrip().split())
ans = dfs(r, c, N, 0)
print(ans)
```
