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

### 카드 놓기(오답)
```
import sys
import copy

def isTop(color):
    top = []
    bot = []
    topX = -1
    botX = -1
    for i in range(N):
        for j in range(M):
            if graph[i][j] == color:
                # 맨 위 인덱스 있는 경우
                if topX == -1:
                    topX = i
                    top.append([i, j])
                else:
                    if i == topX:
                        top.append([i, j])
                    else:
                        if i != botX:
                            botX = i
                            bot = []
                        if i == botX:
                            bot.append([i, j])
    if len(bot) == 0:
        for i, j in top:
            tempGraph[i][j] = -1
        return True
    leftY = min(top[0][1], bot[0][1])
    rightY = max(top[-1][1], bot[-1][1])

    for i in range(topX, botX + 1):
        for j in range(leftY, rightY + 1):
            if graph[i][j] not in [color, -1]:
                return False
    for i in range(topX, botX + 1):
        for j in range(leftY, rightY + 1):
            tempGraph[i][j] = -1
    return True


N, M = map(int, sys.stdin.readline().rstrip().split())
graph = []
colorSet = set()
for _ in range(N):
    str = sys.stdin.readline().rstrip()
    temp = []
    for elem in str:
        if elem == '.':
            temp.append(-1)
        else:
            colorSet.add(elem)
            temp.append(elem)
    graph.append(temp)

colorCheck = set()
checked = []

while colorCheck != colorSet:
    temp = []
    tempGraph = copy.deepcopy(graph)
    flag = True
    visited = set()
    for i in range(N):
        for j in range(M):
            if graph[i][j] != -1 and graph[i][j] not in colorCheck and graph[i][j] not in visited:
                color = graph[i][j]
                visited.add(color)
                if isTop(color):
                    flag = False
                    colorCheck.add(color)
                    temp.append(color)
    temp.sort(reverse=True)
    for elem in temp:
        checked.append(elem)
    graph = tempGraph
    if flag:
        print(-1)
        exit(0)

for i in range(len(checked) - 1, -1, -1):
    print(checked[i], end='')
print()
```
-> 나는 이 문제가 조금 복잡한 단순 구현 문제라고 생각했다. 그래서 맨 위에 올라간 카드부터 없애는 방식으로 문제 풀이를 시도했는데 오답 판정을 받았다. 힌트를 보니 위상 정렬을 사용하라고 나와 있는데, 카드를 놓는 순서가 다른 카드에 종속된다는 점을 간과한것 같다.

### 카드 놓기
```
import sys
from heapq import heappop, heappush

def checkParent(color):
    min_x, min_y = 51, 51
    max_x, max_y = -1, -1

    for i in range(N):
        for j in range(M):
            if graph[i][j] == color:
                min_x = min(min_x, i)
                min_y = min(min_y, j)
                max_x = max(max_x, i)
                max_y = max(max_y, j)

    leftY = min_y
    rightY = max_y
    topX = min_x
    botX = max_x

    # 구역 내에 부모 색깔이 있는지 찾는 루프
    for i in range(topX, botX + 1):
        for j in range(leftY, rightY + 1):
            if graph[i][j] == -1:
                print(-1)
                exit(0)
            if graph[i][j] != color and color not in parent[graph[i][j]]:
                children[color].append(graph[i][j])
                parent[graph[i][j]].append(color)


N, M = map(int, sys.stdin.readline().rstrip().split())
graph = []
colorSet = set()
for _ in range(N):
    str = sys.stdin.readline().rstrip()
    temp = []
    for elem in str:
        if elem == '.':
            temp.append(-1)
        else:
            colorSet.add(elem)
            temp.append(elem)
    graph.append(temp)

colorCheck = set()
checked = []
parent = {}
children = {}
for elem in colorSet:
    parent[elem] = []
    children[elem] = []


# 종속 관계 확인하기
for i in range(N):
    for j in range(M):
        if graph[i][j] != -1 and graph[i][j] not in checked:
            color = graph[i][j]
            checkParent(color)
            checked.append(color)

buffer = []
parentNum = {}
for elem in parent:
    parentNum[elem] = len(parent[elem])

for elem in parent:
    if parentNum[elem] == 0:
        heappush(buffer, elem)

answer = []
while colorSet != colorCheck:
    if len(buffer) == 0:
        print(-1)
        exit(0)
    temp = []
    elem = heappop(buffer)
    if parentNum[elem] != 0:
        print(-1)
        exit(0)
    temp.append(elem)
    for child in children[elem]:
        parentNum[child] -= 1
        if parentNum[child] == 0:
            heappush(buffer, child)
    for i in range(len(temp) - 1, -1, -1):
        answer.append(temp[i])
        colorCheck.add(temp[i])

for elem in answer:
    print(elem, end='')
print()
```
