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
