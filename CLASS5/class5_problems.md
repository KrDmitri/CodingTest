# solvec.ac CLASS 5 문제 풀이
### ACM Craft(dfs 풀이)
```
import sys

def dfs(this):
    tempParent = parents[this]
    if len(tempParent) > 0:
        taskTimes = []
        for elem in tempParent:
            taskTimes.append(dfs(elem))
        return times[this] + max(taskTimes)
    else:
        return times[this]

T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    N, K = map(int, sys.stdin.readline().rstrip().split())
    times = list(map(int, sys.stdin.readline().rstrip().split()))
    parents = [[] for _ in range(N)]
    childs = [[] for _ in range(N)]
    for _ in range(K):
        parent, child = map(int, sys.stdin.readline().rstrip().split())
        parents[child - 1].append(parent - 1)
        childs[parent - 1].append(child - 1)
    W = int(sys.stdin.readline().rstrip())
    isProcessed = [False] * N

    ans = dfs(W - 1)
    print(ans)
```
-> dfs로 어렵지 않게 답을 유도할 수 있었으나 시간 초과가 난다. 해당 문제는 위상 정렬로 풀어야한다고 하는데 각 노드의 값에 따라 처리를 해줘야 하므로 단순한 위상정렬로는 풀이가 힘들것 같다.

### ACM Craft(위상정렬 풀이)
```
import sys
from collections import deque

def isAllParentProcessed(child):
    for elem in parents[child]:
        if not isProcessed[elem]:
            return False
    return True

T = int(sys.stdin.readline().rstrip())

for _ in range(T):
    N, K = map(int, sys.stdin.readline().rstrip().split())
    times = list(map(int, sys.stdin.readline().rstrip().split()))
    parents = [[] for _ in range(N)]
    childs = [[] for _ in range(N)]
    for _ in range(K):
        parent, child = map(int, sys.stdin.readline().rstrip().split())
        parents[child - 1].append(parent - 1)
        childs[parent - 1].append(child - 1)
    W = int(sys.stdin.readline().rstrip())

    timeNeeds = [-1] * N
    isProcessed = [False] * N
    q = deque([])
    for elem in range(N):
        if len(parents[elem]) == 0:
            q.append(elem)
            timeNeeds[elem] = times[elem]
            isProcessed[elem] = True


    while True:
        if isAllParentProcessed(W - 1) and isProcessed[W - 1]:
            break

        now = q.popleft()
        if now == 4:
            print('', end='')
        isProcessed[now] = True
        for elem in childs[now]:
            tempChild = elem
            timeNeeds[elem] = max(timeNeeds[elem], timeNeeds[now] + times[elem])
            if not isProcessed[elem] and isAllParentProcessed(tempChild):
                q.append(elem)
    print(timeNeeds[W - 1])
```
-> 위상정렬 코드에서 조금만 더 생각하면 풀 수 있었는데, 처음에 너무 어렵게 생각한 것 같다.

### 최소 스패닝 트리
```
import sys
import heapq

def checkFinished():
    for elem in parent:
        if elem != 0:
            return False
    return True

# 서로소 함수 사용하기
def find(x):
    if parent[x] == x:
        return x
    else:
        return find(parent[x])

def union(x, y):
    xp = find(x)
    yp = find(y)
    if xp < yp:
        parent[yp] = xp
    else:
        parent[xp] = yp

V, E = map(int, sys.stdin.readline().rstrip().split())
roads = []
parent = []
for i in range(V):
    parent.append(i)

for _ in range(E):
    a, b, c = map(int, sys.stdin.readline().rstrip().split())
    heapq.heappush(roads, [c, a - 1, b - 1])

ans = 0

while roads:
    if checkFinished():
        break
    z, x, y = heapq.heappop(roads)
    px = find(x)
    py = find(y)
    if px == py:
        continue
    ans += z
    union(x, y)

print(ans)
```
