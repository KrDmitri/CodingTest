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

### 작업
```
import sys
from heapq import heappop, heappush

N = int(sys.stdin.readline().rstrip())

taskList = [[]]
childList = [[] for _ in range(N + 1)]
parentNum = [0] * (N + 1)
parentNum[0] = -1
finished = 0

buffer = []
for i in range(N):
    temp = list(map(int, sys.stdin.readline().rstrip().split()))
    taskList.append(temp)
    parentNum[i + 1] = temp[1]
    if temp[1] == 0:
        heappush(buffer, [temp[0], i + 1])    # 시간, 작업 번호
    for j in range(2, len(temp)):
        childList[temp[j]].append(i + 1)

now = 0
# change item : parentNum
while finished != N:
    time, workNum = heappop(buffer)
    now += time
    for i in range(len(buffer)):
        buffer[i][0] -= time
    for elem in childList[workNum]:
        parentNum[elem] -= 1
        if parentNum[elem] == 0:
            heappush(buffer, [taskList[elem][0], elem])
    finished += 1

print(now)
```

