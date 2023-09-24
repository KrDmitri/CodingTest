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

### 리모컨
```
import sys
from itertools import product
INF = int(1e9)

N = int(sys.stdin.readline().rstrip())
M = int(sys.stdin.readline().rstrip())
now = 100
buttons = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
if M > 0:
    brokenButtons = list(map(int, sys.stdin.readline().rstrip().split()))
    for elem in brokenButtons:
        buttons.remove(elem)

ans = INF

# 같은 자리 수 생성
candidates = list(product(buttons, repeat=len(str(N))))
diff = INF
tempNum = 0
for numList in candidates:
    if numList[0] == 0 and len(str(N)) != 1:
        continue
    tempNum = numList[0]
    for i in range(1, len(numList)):
        tempNum = tempNum * 10 + numList[i]
    newDiff = abs(N - tempNum)
    if newDiff < diff:
        diff = newDiff
    else:
        break
ans = min(ans, len(str(N)) + diff)

if len(buttons) > 0:
    # 하나 작은 자리 수 생성
    tempNum = max(buttons)
    for i in range(len(str(N)) - 2):
        tempNum = tempNum * 10 + buttons[-1]
    newDiff = abs(N - tempNum)
    ans = min(ans, len(str(tempNum)) + newDiff)

    # 하나 큰 자리 수 생성
    if buttons[0] == 0 and len(buttons) > 1:
        tempNum = buttons[1]
    else:
        tempNum = buttons[0]
    for i in range(len(str(N))):
        tempNum = tempNum * 10 + buttons[0]
    newDiff = abs(N - tempNum)
    ans = min(ans, len(str(tempNum)) + newDiff)

# 현재에서의 차이
newDiff = abs(N - now)
ans = min(ans, newDiff)

print(ans)
```

### DFS와 BFS
```
import sys
from collections import deque

def search_dfs(now):
    visited_dfs[now] = True
    print(now, end=' ')
    for elem in sorted(edges[now]):
        if not visited_dfs[elem]:
            search_dfs(elem)


N, M, V = map(int, sys.stdin.readline().rstrip().split())
edges = [[] for _ in range(N + 1)]

for _ in range(M):
    x, y = map(int, sys.stdin.readline().rstrip().split())
    edges[x].append(y)
    edges[y].append(x)

# dfs 출력
visited_dfs = [False] * (N + 1)
search_dfs(V)
print()

# bfs 출력
visited_bfs = [False] * (N + 1)
q = deque()
q.append(V)
visited_bfs[V] = True
while q:
    now = q.popleft()
    print(now, end=' ')
    for elem in sorted(edges[now]):
        if not visited_bfs[elem]:
            q.append(elem)
            visited_bfs[elem] = True
```
