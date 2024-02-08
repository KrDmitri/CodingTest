# CH04 BFS
### 0과 1
```
import sys
from collections import deque

T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    N = int(sys.stdin.readline().rstrip())

    q = deque([1])
    visited = [False] * (N + 1)
    visited[1] = True
    flag = True
    while q:
        now = q.popleft()
        if now % N == 0:
            print(now)
            flag = False
            break
        if len(str(now * 10)) <= 100 and not visited[(now * 10) % N]:
            visited[(now * 10) % N] = True
            q.append(now * 10)
        if len(str(now * 10 + 1)) <= 100 and not visited[(now * 10 + 1) % N]:
            visited[(now * 10 + 1) % N] = True
            q.append(now * 10 + 1)
    if flag:
        print('BRAK')
```

### 숨바꼭질 5
```
import sys
from collections import deque

N, K = map(int, sys.stdin.readline().rstrip().split())

visited = [[False] * 500001 for _ in range(2)]
visited[0][N] = True
nq = [N]
flag = 0
speed = 1

while K <= 500000:
    q = deque(nq)
    nq = []
    while q:
        now = q.popleft()
        if visited[flag][K]:
            print(speed - 1)
            exit(0)

        if now - 1 >= 0 and not visited[1 - flag][now - 1]:
            visited[1 - flag][now - 1] = True
            nq.append(now - 1)
        if now + 1 <= 500000 and not visited[1 - flag][now + 1]:
            visited[1 - flag][now + 1] = True
            nq.append(now + 1)
        if now * 2 <= 500000 and not visited[1 - flag][now * 2]:
            visited[1 - flag][now * 2] = True
            nq.append(now * 2)

    flag = 1 - flag
    K += speed
    speed += 1

print(-1)
```

### 직사각형 탈출
```
import sys
from collections import deque

N, M = map(int, sys.stdin.readline().rstrip().split())
graph = [list(map(int, sys.stdin.readline().rstrip().split())) for _ in range(N)]
H, W, sx, sy, fx, fy = map(int, sys.stdin.readline().rstrip().split())
sx, sy, fx, fy = sx - 1, sy - 1, fx - 1, fy - 1
dx = [0, 1, 0, -1]
dy = [1, 0, -1, 0]

visited = [[False] * M for _ in range(N)]
visited[sx][sy] = True
q = deque([[sx, sy, 0]])

while q:
    x, y, time = q.popleft()
    if x == fx and y == fy:
        print(time)
        exit(0)
    for k in range(4):
        nx = x + dx[k]
        ny = y + dy[k]
        if nx < 0 or nx >= N or ny < 0 or ny >= M or graph[nx][ny] == 1 or visited[nx][ny]:
            continue
        visited[nx][ny] = True

        # 수정 요구 구역
        flag = False
        if k % 2 == 0:
            if k == 0:
                nny = y + W
            else:
                nny = y - 1

            for i in range(x, x + H):
                if i < 0 or i >= N or nny < 0 or nny >= M or graph[i][nny] == 1:
                    flag = True
                    break
        else:
            if k == 1:
                nnx = x + H
            else:
                nnx = x - 1
            for j in range(y, y + W):
                if nnx < 0 or nnx >= N or j < 0 or j >= M or graph[nnx][j] == 1:
                    flag = True
                    break

        if not flag:
            q.append([nx, ny, time + 1])

print(-1)
```

### 배달
```
import sys
from collections import deque

row, col = map(int,sys.stdin.readline().split())
board = []
start = []
for i in range(row):
    board.append(list(sys.stdin.readline().strip()))
    if 'S' in board[i]:
        start.append((i,board[i].index('S'),-1,0))

dir = [[-1, 0], [1, 0], [0, -1], [0, 1]]

def bfs(start):
    q = deque(start)
    visit = [[[1 for _ in range(col)] for _ in range(row)] for _ in range(4)]
    cNum = 0
    find = deque()
    while q :
        curRow, curCol, direction, cnt = q.popleft()
        for i in range(4):
            if i == direction :
                continue
            nextRow = curRow + dir[i][0]
            nextCol = curCol + dir[i][1]
            if 0 <= nextRow < row and 0 <= nextCol < col and board[nextRow][nextCol] != '#' and visit[i][nextRow][nextCol]:
                if board[nextRow][nextCol] == 'C' :
                    if not find :
                        cNum +=1
                        if cNum == 2:
                            return cnt+1
                    elif find[0][0] != nextRow or find[0][1] != nextCol:
                        continue
                    find.append((nextRow,nextCol,i,cnt+1))
                else :
                    if find:
                        continue
                    visit[i][nextRow][nextCol] -=1
                    q.append((nextRow,nextCol,i,cnt+1))
        if not q and find:
            visit = [[[1 for _ in range(col)] for _ in range(row)] for _ in range(4)]
            board[find[0][0]][find[0][1]] = '.'
            while find:
                q.append(find.pop())
    return -1
print(bfs(start))
```
