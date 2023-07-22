# BFS 알고리즘
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

### 연구소
```
import sys
import copy
from itertools import combinations
from collections import deque

def countBlanks(tempLab):
    num = 0
    for i in range(n):
        for j in range(m):
            if tempLab[i][j] == 0:
                num += 1
    return num

def spreadVirus(tempLab, virusList):
    q = deque(virusList)
    visited = [[False] * m for _ in range(n)]
    visited[q[0][0]][q[0][1]] = True
    while q:
        now = q.popleft()
        x, y = now[0], now[1]
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]
            if nx >= 0 and nx < n and ny >= 0 and ny < m and not visited[nx][ny] and tempLab[nx][ny] != 1:
                q.append([nx, ny])
                tempLab[nx][ny] = 2
                visited[nx][ny] = True
    blanks = countBlanks(tempLab)
    return blanks


n, m = map(int, sys.stdin.readline().split())
lab = []
virusPos = []
blanks = []
dx = [-1, 0, 1, 0]
dy = [0, -1, 0, 1]

for i in range(n):
    tempList = list(map(int, sys.stdin.readline().split()))
    for j in range(m):
        if tempList[j] == 2:
            virusPos.append([i, j])
        if tempList[j] == 0:
            blanks.append([i, j])
    lab.append(tempList)

candidates = list(combinations(blanks, 3))

answer = 0
for candidate in candidates:
    tempLab = copy.deepcopy(lab)
    for elem in candidate:
        tempLab[elem[0]][elem[1]] = 1
    blanks = spreadVirus(tempLab, virusPos)
    answer = max(answer, blanks)

print(answer)
```
-> 이 문제는 5달 전에도 풀었었는데 코드 길이를 반으로 줄이고 실행 시간도 3배 이상 단축했다.

### 돌 그룹
```
import sys
from collections import deque

def operate(x, y):
    if x < y:
        temp = x
        x += temp
        y -= temp
    else:
        temp = y
        y += temp
        x -= temp
    return x, y

inputList = list(map(int, sys.stdin.readline().split()))
if sum(inputList) % 3 != 0:
    print(0)
else:
    inputList.sort()
    visited = [[False] * 1501 for _ in range(1501)]
    visited[inputList[0]][inputList[2]] = True
    cases = [[0, 1, 2], [1, 2, 0], [2, 0, 1]]
    answer = sum(inputList) // 3

    q = deque()
    q.append(inputList)
    if inputList[0] == inputList[1] == inputList[2]:
        print(1)
        exit(0)

    while q:
        now = q.popleft()
        for case in cases:
            x, y = now[case[0]], now[case[1]]
            z = now[case[2]]
            if x == y:
                continue
            x, y = operate(x, y)
            if x == y and y == z:
                print(1)
                exit(0)
            tempList = [x, y, z]
            tempList.sort()
            if not visited[tempList[0]][tempList[2]]:
                q.append(tempList)
                visited[tempList[0]][tempList[2]] = True

    print(0)
```
-> 처음엔 위와 같이 if not visited[x][y]: 로 조건을 짜지 않고 in 메서드를 활용하여 코드를 짰는데 시간 초과가 났었다. in 메서드를 사용하면서 매번 완전 탐색을 하면서 시간이 오래걸린 것 같다.
