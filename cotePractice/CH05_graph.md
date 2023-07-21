# 그래프 알고리즘
### Two Dots
```
def nextDirection(direction):
    return (direction + 1) % 4

def searchCycle(start, now, direction):
    x, y = now[0], now[1]
    nextX = x + dx[direction]
    nextY = y + dy[direction]
    if nextX >= 0 and nextX < n and nextY >= 0 and nextY < m:
        next = [nextX, nextY]
        if board[start[0]][start[1]] == board[nextX][nextY]:
            if next == start:
                return True
            flag = searchCycle(start, next, direction)
            if flag:
                return True

    if direction != 3:
        direction = nextDirection(direction)
        nextX = x + dx[direction]
        nextY = y + dy[direction]
        if nextX >= 0 and nextX < n and nextY >= 0 and nextY < m:
            next = [nextX, nextY]
            if board[start[0]][start[1]] == board[nextX][nextY]:
                if next == start:
                    return True
                flag = searchCycle(start, next, direction)
                if flag:
                    return True
    # 아래 코드 맞나?
    return False


n, m = map(int, input().split())
board = []
for _ in range(n):
    board.append(input())

dx = [1, 0, -1, 0]
dy = [0, 1, 0, -1]
flag = 0

for i in range(n):
    for j in range(m):
        flag = searchCycle([i, j], [i, j], 0)
        if flag:
            break
    if flag:
        break

if flag:
    print('Yes')
else:
    print('No')
```
-> 위 답은 틀린 답인데, 문제를 잘못 이해했다. 나는 문제에서 말하는 싸이클이 꼭 직사각형이어야하는 줄 알았는데 그렇지 않았다..😤

### Two Dots (두번째 시도)
```
def searchCycle(start, now, history):
    x = now[0]
    y = now[1]
    flag = False

    for i in range(4):
        nx = x + dx[i]
        ny = y + dy[i]
        next = [nx, ny]
        if next == start and len(history) >= 4:
            flag = True
            break
        if nx >= 0 and nx < n and ny >= 0 and ny < m and [nx, ny] not in history and board[start[0]][start[1]] == board[nx][ny]:
            history.append(next)
            flag = searchCycle(start, next, history)
            if flag:
                break
            history.remove(next)
    if flag:
        return True
    else:
        return False

n, m = map(int, input().split())
board = []
dx = [1, 0, -1, 0]
dy = [0, 1, 0, -1]

for _ in range(n):
    board.append(input())

for i in range(n):
    for j in range(m):
        flag = searchCycle([i, j], [i, j], [[i, j]])
        if flag:
            break
    if flag:
        break

if flag:
    print('Yes')
else:
    print('No')
```
-> 알고보니 더 쉬운 문제였다

### 서울 지하철 2호선
```
import collections

def findSpanningTree(start, now, history):
    global spanningTree
    if len(history) == 0:
        for i in range(n):
            if graph[start][i] == 1:
                history.append(i)
                next = i
                findSpanningTree(start, next, history)
                history.remove(i)
    else:
        for i in range(n):
            if len(history) >= 3 and graph[now][start] == 1 and set(history) not in spanningTree:
                spanningTree.append(set(history))
            if i not in history and graph[now][i] == 1:
                history.append(i)
                next = i
                findSpanningTree(start, next, history)
                history.remove(i)

def distToSpanningTree(elem, visited):
    global spanningTree
    q = collections.deque([elem])
    while q:
        now = q.popleft()
        nowIndex = now[0]
        nowDist = now[1]
        for i in range(n):
            if graph[nowIndex][i] == 1 and i not in visited:
                if i in spanningTree:
                    return nowDist + 1
                q.append([i, nowDist + 1])
                visited.append(i)

n = int(input())

graph = [[0] * n for _ in range(n)]
spanningTree = []

for _ in range(n):
    x, y = map(int, input().split())
    graph[x - 1][y - 1] = 1
    graph[y - 1][x - 1] = 1

for i in range(n):
    findSpanningTree(i, i, [i])
    # 순환이 두 개 이상일 경우 아래 코드는 무효할듯?
    if len(spanningTree) != 0:
        break

spanningTree = list(spanningTree.pop())

# print(spanningTree)

for i in range(n):
    if i in spanningTree:
        print(0, end=' ')
    else:
        print(distToSpanningTree([i, 0], [i]), end=' ')
```
-> 싸이클 찾는 것은 dfs, 최소 거리 구하는 것은 bfs를 사용하는 방법으로 다른 사람들의 모범답안과 비슷한데 내 코드는 시간 초과가 난다

### 서울 지하철 2호선 (2번째 시도)
```
import collections

def findSpanningTree(start, now, cnt):
    global isCycle, visited
    if cnt >= 2 and graph[now][start] == 1:
        if start == 3:
            print('', end='')
        isCycle[start] = True
        return

    for i in range(n):
        if graph[now][i] == 1 and not visited[i]:
            visited[i] = True
            findSpanningTree(start, i, cnt + 1)
            visited[i] = False


def distToSpanningTree():
    global spanningTree, distance
    q = collections.deque()
    for node in spanningTree:
        q.append([node, 0])

    while q:
        now = q.popleft()
        nowNode = now[0]
        nowDist = now[1]
        for i in range(n):
            if graph[nowNode][i] == 1 and i not in spanningTree and distance[i] == -1:
                distance[i] = nowDist + 1
                q.append([i, nowDist + 1])

n = int(input())

graph = [[0] * n for _ in range(n)]
isCycle = [False] * n

for _ in range(n):
    x, y = map(int, input().split())
    graph[x - 1][y - 1] = 1
    graph[y - 1][x - 1] = 1

for i in range(n):
    visited = [False] * n
    visited[i] = True
    findSpanningTree(i, i, 0)

# 위에서 isCyle 구하고, 기반으로 cycleTree 리스트 만들어주기
spanningTree = []
for i in range(len(isCycle)):
    if isCycle[i]:
        spanningTree.append(i)

distance = [-1] * n

for elem in spanningTree:
    distance[elem] = 0

distToSpanningTree()

for elem in distance:
    print(elem, end=' ')
```
-> 다른 모범 답안을 참고하여 푼 코드인데 이 코드도 시간 초과가 난다. 모범 답안 코드도 메모리 초과가 나던데 우선 원리는 이해했으므로 나중에 다시 풀어봐야겠다.

### 서울 지하철 2호선 (모범 답안)
https://lastwinter.tistory.com/45
-> 이게 머선 풀이고??

### 육각보드
```
import copy
import sys
sys.setrecursionlimit(2000)

def isPossible(board, blankList, colorList):
    if len(blankList) == 0:
        return True
    else:
        tempBlankList = copy.deepcopy(blankList)
        colorCandidates = copy.deepcopy(colorList)
        now = tempBlankList.pop(0)
        x, y = now[0], now[1]
        for i in range(6):
            nx = x + dx[i]
            ny = y + dy[i]
            if nx >= 0 and nx < n and ny >= 0 and ny < n:
                if board[nx][ny] in colorCandidates:
                    colorCandidates.remove(board[nx][ny])
        if len(colorCandidates) == 0:
            return False
        else:
            for elem in colorCandidates:
                board[x][y] = elem
                flag = isPossible(board, tempBlankList, colorList)
                if flag:
                    return True
                board[x][y] = 'X'



n = int(input())
board = []
blankList = []
colorList = []
dx = [-1, 0, 1, 1, 0, -1]
dy = [0, -1, -1, 0, 1, 1]
answer = int(1e9)

for i in range(n):
    temp = input()
    tempList = []
    for j in range(n):
        if temp[j] == 'X':
            blankList.append([i, j])
        tempList.append(temp[j])
    board.append(tempList)

if len(blankList) == 0:
    print(0)
else:
    flag = False
    for i in range(1, 3):
        colorList = []
        for i in range(1, i + 1):
            colorList.append(i)
        flag = isPossible(board, blankList, colorList)
        if flag:
            break
    if flag:
        print(len(colorList))
    else:
        print(3)
```
-> 처음엔 3개의 색으로 평면에 인접하지 않고 칠할 수 있다는 사실을 잊고 풀다 생각나서 위와 같은 코드를 짰다. 그런데 이것마저 시간 초과가 나는데, 제한 시간 2초인데 이게 시간초과가 나다니 조금 의아했다.

### 육각보드 모범답안
```
import sys
sys.setrecursionlimit(10**9)

n = int(input())

board = []

for _ in range(n):
    board.append(list(input()))

dx = [-1,-1,0,0,1,1]
dy = [0,1,-1,1,-1,0]

ans = 0

def dfs(x, y):
    global ans
    ans = max(ans, 1)

    # 다음 방향
    for k in range(6):
        nx, ny = x+ dx[k], y+ dy[k]

        if 0 <= nx < n and 0 <= ny <n and board[nx][ny] == 'X':
            if  visited[nx][ny] == 0:
                visited[nx][ny] = -visited[x][y]
                dfs(nx, ny)
                ans = max(ans, 2)
            else:
                if visited[nx][ny] == visited[x][y]:
                    ans = max(ans, 3)
                    return

visited = [[0]*n for _ in range(n)]

for x in range(n):
    for y in range(n):
        if board[x][y] == 'X' and visited[x][y] == 0:
            visited[x][y] = 1
            dfs(x, y)

print(ans)
```
-> 위 코드는 숫자로 색을 표현한 나의 방식과 다르게 +1, -1 두가지로 해결했다. 또한 재귀 함수 내 코드 수행이 나의 코드보다 짧은 것을 알 수 있다. 앞으로는 copy.deepcopy() 함수를 안쓰는 방향으로 코딩을 해봐야겠다.

### BFS 스페셜 저지
```
import collections

def checkRight(q, idx):
    q = collections.deque(q)
    while q:
        now = q.popleft()
        tempList = []
        for i in range(n):
            if ([now, i] in route or [i, now] in route) and not visited[i]:
                tempList.append(i)
        if len(tempList) == 0:
            continue
        for elem in answers[idx:idx + len(tempList)]:
            if elem not in tempList:
                return 0
        for elem in answers[idx:idx + len(tempList)]:
            q.append(elem)
            visited[elem] = True
        idx += len(tempList)
        if idx >= n:
            return 1
    return 1

n = int(input())
route = []

for _ in range(n - 1):
    x, y = map(int, input().split())
    route.append([x - 1, y - 1])

answers = list(map(int, input().split()))
for i in range(len(answers)):
    answers[i] -= 1

visited = [True]
for _ in range(n - 1):
    visited.append(False)

idx = 1

flag = checkRight([0], idx)

print(flag)
```
-> 이것보다 빠르게 풀 수 있는 방법을 생각하기 어렵다

### BFS 스페셜 저지(모범 답안)
```
import sys
from collections import deque
N = int(sys.stdin.readline().rstrip())
graph=[[] for _ in range(N+1)]
for _ in range(N-1):
    a,b= map(int,sys.stdin.readline().rstrip().split())
    graph[a].append(b)
    graph[b].append(a)
for i in range(1,N+1):
    graph[i].sort()
ans=list(map(int,sys.stdin.readline().split()))
stk=[]
flag=False
visited = [False]*(N+1)
def bfs(n):
    global flag
    q =deque()
    visited[n]=True
    q.append(n)
    start=1
    tmp=[]
    while q:
        stk=[]
        for i in graph[q.popleft()]:
            if not visited[i]:
                visited[i]=True
                stk.append(i)
        li=ans[start:start+len(stk)]
        tmp=list(li)
        start=start+len(stk)
        li.sort()
        if li!=stk:
            flag=True
            break
        else:
            for i in tmp:
                q.append(i)
if ans[0]==1:       
    bfs(1)
    print(1) if not flag else print(0)
else:
    print(0)
```
-> 내 코드랑 전체적인 아이디어는 동일하다, 어디서 시간 차이를 가져왔는지 분석해야겠다.(내 코드는 한 노드와 연결된 다른 노드들을 확인할 때 모든 노드를 확인하지만, 모범 답안 코드에서는 연결된 노드들만 가져와서 방문된 노드인지만 확인하기에 노드의 개수가 100,000 개인 본 문제에서 큰 시간 차이를 만드는 것 같다.)

### BFS 스페셜 저지(코드 수정 버전)
```
import collections
import sys

def checkRight(q, idx):
    q = collections.deque(q)
    while q:
        now = q.popleft()
        tempList = []
        for elem in graph[now]:
            if not visited[elem]:
                tempList.append(elem)
        if len(tempList) == 0:
            continue
        for elem in answers[idx:idx + len(tempList)]:
            if elem not in tempList:
                return 0
        for elem in answers[idx:idx + len(tempList)]:
            q.append(elem)
            visited[elem] = True
        idx += len(tempList)
        if idx >= n:
            return 1
    return 1

n = int(sys.stdin.readline())
graph = [[] for _ in range(n)]

for _ in range(n - 1):
    x, y = map(int, sys.stdin.readline().split())
    graph[x - 1].append(y - 1)
    graph[y - 1].append(x - 1)

for i in range(n):
    graph[i].sort()

answers = list(map(int, sys.stdin.readline().split()))
for i in range(len(answers)):
    answers[i] -= 1

visited = [True]
for _ in range(n - 1):
    visited.append(False)

idx = 1

flag = checkRight([0], idx)

print(flag)
```
-> 내가 짠 코드가 계속 시간 초과 나서 정답 판정을 받도록 수정을 해보았다. 수정된 부분은 각 노드에서 간선에 대한 정보를 찾는 부분인데, 기존 코드는 한 노드와 다른 모든 노드에 대한 간선 정보를 확인했는데, 이번 코드에서는 연결된 노드의 정보만 확인해서 시간을 줄여 정답 판정을 받을 수 있었다.

### DFS 스페셜 저지
```
import collections
import sys

def isElementIn(elem, tempList):
    start = 0
    end = len(tempList) - 1
    mid = (start + end) // 2
    while end >= start:
        if elem > tempList[mid]:
            start = mid + 1
            mid = (start + end) // 2
        elif elem < tempList[mid]:
            end = mid - 1
            mid = (start + end) // 2
        else:
            return True
    return False

n = int(sys.stdin.readline())
graph = [[] for _ in range(n + 1)]
for _ in range(n - 1):
    x, y = map(int, sys.stdin.readline().split())
    graph[x].append(y)
    graph[y].append(x)

answer = list(map(int, sys.stdin.readline().split()))
visited = [False] * (n + 1)

visited[1] = True

stack = [1]
idx = 1

while stack:
    if idx == n - 1:
        print(1)
        break
    tempList = []
    for elem in graph[stack[-1]]:
        if not visited[elem]:
            tempList.append(elem)
    if len(tempList) > 0:
        tempAnswer = answer[idx]
        if len(tempList) == 1:
            if tempAnswer != tempList[0]:
                print(0)
                break
            else:
                stack.append(tempAnswer)
                visited[tempAnswer] = True
                idx += 1
        else:
            tempList.sort()
            if not isElementIn(tempAnswer, tempList):
                print(0)
                break
            else:
                stack.append(tempAnswer)
                visited[tempAnswer] = True
                idx += 1
    else:
        stack.pop()
```
-> 나는 재귀함수를 사용하는 것보다 스택 자료구조를 사용하는 것이 수행 시간을 절약할 수 있을 것이라고 생각했다. 하지만 backtracking 과정에서는 재귀 용법이 더 빠르게 풀린다는 것을 확인할 수 있었다.

### DFS 스페셜 저지(모범 답안)
```
import sys
from collections import deque
sys.setrecursionlimit(100000)


def dfs(graph, visited, cmp):
    tmp = cmp.popleft()
    if not cmp:
        print(1)
        exit(0)
    visited[tmp] = True
    for _ in range(len(graph[tmp])):
        if cmp[0] in graph[tmp] and not visited[cmp[0]]:
            dfs(graph, visited, cmp)
    return False


N = int(sys.stdin.readline())
visited = [False] * (N + 1)
graph = [[] for _ in range(N + 1)]

for i in range(1, N):
    x, y = map(int, sys.stdin.readline().split())
    graph[x].append(y)
    graph[y].append(x)

cmp = deque(map(int, sys.stdin.readline().split()))
if cmp[0] != 1:
    print(0)
    exit(0)
if not dfs(graph, visited, cmp):
    print(0)
```
-> 처음엔 "for _ in range(len(graph[tmp])):" 해당 코드가 왜 필요한지 몰랐는데, 이는 이어진 간선의 갯수만큼 현재 노드에서 다음 노드로 갈 수 있는 경우의 수가 주어지기 때문이라는 것을 이해했다.

### 뱀과 사다리 게임
```
import sys

INF = int(1e9)
def findNext(num, type):
    for elem in type:
        if elem[0] == num:
            return elem[1]

n, m = map(int, sys.stdin.readline().split())
ladder = []
ladderBlank = []
snake = []
snakeBlank = []
for _ in range(n):
    x, y = map(int, sys.stdin.readline().split())
    ladder.append([x, y])
    ladderBlank.append(x)
for _ in range(m):
    x, y = map(int, sys.stdin.readline().split())
    snake.append([x, y])
    snakeBlank.append(x)
history = [INF] * 101
q = [[1, 0]]
answer = []

while len(q) > 0:
    q.sort(key=lambda x:x[1])
    nowInfo = q.pop(0)
    nowNum = nowInfo[0]
    nowTry = nowInfo[1]
    if nowNum >= 94:
        answer.append(nowTry + 1)
        break
    for i in range(1, 7):
        next = nowNum + i
        if next in snakeBlank:
            nextBlank = findNext(next, snake)
            if history[nextBlank] > nowTry + 1:
                history[nextBlank] = nowTry + 1
                q.append([nextBlank, nowTry + 1])
        elif next in ladderBlank:
            nextBlank = findNext(next, ladder)
            if nextBlank == 100:
                answer.append(nowTry)
                break
            if history[nextBlank] > nowTry + 1:
                history[nextBlank] = nowTry + 1
                q.append([nextBlank, nowTry + 1])
        else:
            if history[next] > nowTry + 1:
                history[next] = nowTry + 1
                q.append([next, nowTry + 1])

print(min(answer))
```

### 데스 나이트
```
import sys
from collections import deque

n = int(sys.stdin.readline())
x1, y1, x2, y2 = map(int, sys.stdin.readline().split())
dx = [-2, 0, 2, 2, 0, -2]
dy = [-1, -2, -1, 1, 2, 1]
visited = [[False] * n for _ in range(n)]

q = deque()
q.append([x1, y1, 0])
visited[x1][y1] = True

while q:
    now = q.popleft()
    nowx, nowy, nowCnt = now[0], now[1], now[2]
    for i in range(6):
        nx = nowx + dx[i]
        ny = nowy + dy[i]
        if nx == x2 and ny == y2:
            print(nowCnt + 1)
            exit(0)
        if nx >= 0 and nx < n and ny >= 0 and ny < n and not visited[nx][ny]:
            q.append([nx, ny, nowCnt + 1])
            visited[nx][ny] = True

print(-1)
```
