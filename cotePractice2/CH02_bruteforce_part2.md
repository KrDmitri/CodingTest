# 브루트 포스 part 2
### 게리맨더링 2
```
import sys
INF = int(1e9)

def countPopDiff(x, y, d1, d2):
    global ans
    sectors = [[0] * (N + 1) for _ in range(N + 1)]
    # 5번 경계 입력
    for i in range(d1 + 1):
        sectors[x + i][y - i] = 5
        sectors[x + d2 + i][y + d2 - i] = 5
    for i in range(d2 + 1):
        sectors[x + i][y + i] = 5
        sectors[x + d1 + i][y - d1 + i] = 5
    for i in range(1, N + 1):
        index = []
        for j in range(1, N + 1):
            if sectors[i][j] == 5:
                index.append(j)
        if len(index) == 2:
            for j in range(index[0], index[1]):
                sectors[i][j] = 5

    for i in range(1, N + 1):
        for j in range(1, N + 1):
            if 1 <= i < x + d1 and 1 <= j <= y and sectors[i][j] == 0:
                sectors[i][j] = 1
            elif 1 <= i <= x + d2 and y < j <= N and sectors[i][j] == 0:
                sectors[i][j] = 2
            elif x + d1 <= i <= N and 1 <= j < y - d1 + d2 and sectors[i][j] == 0:
                sectors[i][j] = 3
            elif x + d2 < i <= N and y - d1 + d2 <= j <= N and sectors[i][j] == 0:
                sectors[i][j] = 4

    populations = [0] * 5
    for i in range(1, N + 1):
        for j in range(1, N + 1):
            populations[sectors[i][j] - 1] += graph[i - 1][j - 1]

    return max(populations) - min(populations)


N = int(sys.stdin.readline().rstrip())
graph = []
for _ in range(N):
    graph.append(list(map(int, sys.stdin.readline().rstrip().split())))

nums = []
for i in range(1, N + 1):
    nums.append(i)

ans = INF
for i in range(1, N):
    for j in range(1, N):
        for x in range(1, N - i - j + 1):
            for y in range(1 + i, N - j + 1):
                if 1 <= x and x < x + i + j and x + i + j <= N and 1 <= y - i and y - i < y and y < y + j and y + j <= N:
                    ans = min(ans, countPopDiff(x, y, i, j))

print(ans)
```
-> 어렵지 않은 문제였는데 각 변수들의 scope를 잘못 설정하였다

### 파이프 옮기기1(DFS 풀이)
```
import sys

def checkType(head, tail):
    a = tail[0] - head[0]
    b = tail[1] - head[1]
    if a == 1 and b == 1:
        t = 2
    elif a == 1 and b == 0:
        t = 1
    else:
        t = 0
    return t

def dfs(head, tail):
    global ans
    t = checkType(head, tail)
    hx, hy = head
    tx, ty = tail
    if tx == N - 1 and ty == N - 1:
        ans += 1
        return

    # 이동
    nhx = hx + dx[t]
    nhy = hy + dy[t]
    ntx = tx + dx[t]
    nty = ty + dy[t]

    # 회전
    for i in range(len(rx[t])):
        flag = 0
        nntx = ntx + rx[t][i]
        nnty = nty + ry[t][i]
        if nntx < 0 or nntx >= N or nnty < 0 or nnty >= N or graph[nntx][nnty] == 1:
            continue
        nt = checkType([nhx, nhy], [nntx, nnty])
        if nt == 2:
            for j, k in ([[-1, 0], [0, -1]]):
                if nntx + j < 0 or nntx + j >= N or nnty + k < 0 or nnty + k >= N or graph[nntx + j][nnty + k] == 1:
                    flag = 1
                    break
        if flag:
            continue
        dfs([nhx, nhy], [nntx, nnty])



N = int(sys.stdin.readline().rstrip())
graph = []
for _ in range(N):
    graph.append(list(map(int, sys.stdin.readline().rstrip().split())))

head = [0, 0]
tail = [0, 1]

dx = [0, 1, 1]
dy = [1, 0, 1]
rx = [[0, 1], [0, 0], [0, -1, 0]]
ry = [[0, 0], [0, 1], [0, 0, -1]]

ans = 0
dfs(head, tail)

print(ans)
```
-> dfs로 풀었는데 시간초과가 나서 위 문제는 dp로 풀 수 있을거 같다
