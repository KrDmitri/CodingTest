# 그래프 알고리즘
### 줄 세우기
```
import sys
from collections import deque

N, M = map(int, sys.stdin.readline().rstrip().split())

infos = [[] for _ in range(N + 1)]
inNumbers = [0] * (N + 1)

for _ in range(M):
    x, y = map(int, sys.stdin.readline().rstrip().split())
    infos[x].append(y)
    inNumbers[y] += 1

q = deque()

for i in range(1, N + 1):
    if inNumbers[i] == 0:
        q.append(i)

while q:
    now = q.popleft()
    print(now, end=' ')
    for elem in infos[now]:
        inNumbers[elem] -= 1
        if inNumbers[elem] == 0:
            q.append(elem)
```
