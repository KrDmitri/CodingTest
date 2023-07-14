# 브루트포스 - 비트마스크
### 부분수열의 합(처음 내 풀이)
```
import itertools

n = int(input())
s = list(map(int, input().split()))
s.sort()

for i in range(1, 100001):
    tempNumList = []
    tempSumList = []
    for elem in s:
        if elem <= i:
            tempNumList.append(elem)
    for j in range(1, len(tempNumList) + 1):
        candidates = list(itertools.combinations(tempNumList, j))
        for candidate in candidates:
            tempSumList.append(sum(candidate))
    if i not in tempSumList:
        print(i)
        break
```

### 부분수열의 합(답안 참고 풀이)
```
n = int(input())
s = list(map(int, input().split()))
s.sort()

answer = 0
flag = 0

for elem in s:
    if elem - answer > 1:
        print(answer + 1)
        flag = 1
        break
    answer += elem

if flag == 0:
    print(answer + 1)
```
-> 올해 초에도 풀었던 문제인데, 당시 해당 개념에 대해 완벽한 이해를 하지 못해서 본 문제를 푸는데 시간이 좀 걸렸다

### 가르침
```
import itertools
import copy

n, k = map(int, input().split())
words = []
maxNum = 0

for _ in range(n):
    words.append(input())

if k < 5:
    print(0)
else:
    teachingAlpha = ['a', 'n', 't', 'i', 'c']
    needToTeach = []
    k -= 5
    tempWords = copy.deepcopy(words)
    for i in range(len(tempWords)):
        tempWords[i] = tempWords[i][4:len(tempWords[i]) - 4]

    for word in tempWords:
        for i in range(len(word)):
            if word[i] not in teachingAlpha and word[i] not in needToTeach:
                needToTeach.append(word[i])

    if k > len(needToTeach):
        candidates = list(itertools.combinations(needToTeach, len(needToTeach)))
    else:
        candidates = list(itertools.combinations(needToTeach, k))

    for candidate in candidates:
        cnt = 0
        totalNum = 0
        for word in tempWords:
            for i in range(len(word)):
                if word[i] not in teachingAlpha and word[i] not in candidate:
                    cnt += 1
                    break
        totalNum = n - cnt
        maxNum = max(maxNum, totalNum)

    print(maxNum)
```

### 구슬탈출2
```
import copy

def moveUp(redPos, bluePos):
    redIsHole = 0
    if redPos[1] == bluePos[1]:
        if redPos[0] < bluePos[0]:
            while True:
                redPos[0] -= 1
                if ballMap[redPos[0]][redPos[1]] == '#':
                    redPos[0] += 1
                    break
                # 이 경우에 블루가 움직여도 레드만 구멍에 빠지는 경우도 고려해야 함
                elif ballMap[redPos[0]][redPos[1]] == 'O':
                    redPos = [-1, -1]
                    redIsHole = 1
                    break

            while True:
                bluePos[0] -= 1
                if redPos[0] == bluePos[0]:
                    bluePos[0] += 1
                    break
                elif ballMap[bluePos[0]][bluePos[1]] == 'O':
                    return -1, redPos, bluePos
                elif ballMap[bluePos[0]][bluePos[1]] == '#':
                    bluePos[0] += 1
                    if redIsHole:
                        return 1, redPos, bluePos
                    else:
                        break

        else:
            while True:
                bluePos[0] -= 1
                if ballMap[bluePos[0]][bluePos[1]] == 'O':
                    return -1, redPos, bluePos
                elif ballMap[bluePos[0]][bluePos[1]] == '#':
                    bluePos[0] += 1
                    break

            while True:
                redPos[0] -= 1
                if redPos[0] == bluePos[0]:
                    redPos[0] += 1
                    break
                elif ballMap[redPos[0]][redPos[1]] == '#':
                    redPos[0] += 1
                    break
                elif ballMap[redPos[0]][redPos[1]] == 'O':
                    return 1, redPos, bluePos
    else:
        while True:
            redPos[0] -= 1
            if ballMap[redPos[0]][redPos[1]] == '#':
                redPos[0] += 1
                break
            elif ballMap[redPos[0]][redPos[1]] == 'O':
                return 1, redPos, bluePos

        while True:
            bluePos[0] -= 1
            if ballMap[bluePos[0]][bluePos[1]] == '#':
                bluePos[0] += 1
                break
            elif ballMap[bluePos[0]][bluePos[1]] == 'O':
                return -1, redPos, bluePos
    return 0, redPos, bluePos

def moveDown(redPos, bluePos):
    redIsHole = 0
    if redPos[1] == bluePos[1]:
        if redPos[0] > bluePos[0]:
            while True:
                redPos[0] += 1
                if ballMap[redPos[0]][redPos[1]] == '#':
                    redPos[0] -= 1
                    break
                elif ballMap[redPos[0]][redPos[1]] == 'O':
                    redIsHole = 1
                    redPos = [-1, -1]
                    break

            while True:
                bluePos[0] += 1
                if redPos[0] == bluePos[0]:
                    bluePos[0] -= 1
                    break
                elif ballMap[bluePos[0]][bluePos[1]] == 'O':
                    return -1, redPos, bluePos
                elif ballMap[bluePos[0]][bluePos[1]] == '#':
                    bluePos[0] -= 1
                    if redIsHole:
                        return 1, redPos, bluePos
                    else:
                        break

        else:
            while True:
                bluePos[0] += 1
                if ballMap[bluePos[0]][bluePos[1]] == 'O':
                    return -1, redPos, bluePos
                elif ballMap[bluePos[0]][bluePos[1]] == '#':
                    bluePos[0] -= 1
                    break

            while True:
                redPos[0] += 1
                if redPos[0] == bluePos[0]:
                    redPos[0] -= 1
                    break
                elif ballMap[redPos[0]][redPos[1]] == '#':
                    redPos[0] -= 1
                    break
                elif ballMap[redPos[0]][redPos[1]] == 'O':
                    return 1, redPos, bluePos
    else:
        while True:
            redPos[0] += 1
            if ballMap[redPos[0]][redPos[1]] == '#':
                redPos[0] -= 1
                break
            elif ballMap[redPos[0]][redPos[1]] == 'O':
                return 1, redPos, bluePos

        while True:
            bluePos[0] += 1
            if ballMap[bluePos[0]][bluePos[1]] == '#':
                bluePos[0] -= 1
                break
            elif ballMap[bluePos[0]][bluePos[1]] == 'O':
                return -1, redPos, bluePos
    return 0, redPos, bluePos

def moveLeft(redPos, bluePos):
    redIsHole = 0
    if redPos[0] == bluePos[0]:
        if redPos[1] < bluePos[1]:
            while True:
                redPos[1] -= 1
                if ballMap[redPos[0]][redPos[1]] == '#':
                    redPos[1] += 1
                    break
                elif ballMap[redPos[0]][redPos[1]] == 'O':
                    redIsHole = 1
                    redPos = [-1, -1]
                    break

            while True:
                bluePos[1] -= 1
                if redPos[1] == bluePos[1]:
                    bluePos[1] += 1
                    break
                elif ballMap[bluePos[0]][bluePos[1]] == 'O':
                    return -1, redPos, bluePos
                elif ballMap[bluePos[0]][bluePos[1]] == '#':
                    bluePos[1] += 1
                    if redIsHole:
                        return 1, redPos, bluePos
                    else:
                        break

        else:
            while True:
                bluePos[1] -= 1
                if ballMap[bluePos[0]][bluePos[1]] == 'O':
                    return -1, redPos, bluePos
                elif ballMap[bluePos[0]][bluePos[1]] == '#':
                    bluePos[1] += 1
                    break

            while True:
                redPos[1] -= 1
                if redPos[1] == bluePos[1]:
                    redPos[1] += 1
                    break
                elif ballMap[redPos[0]][redPos[1]] == '#':
                    redPos[1] += 1
                    break
                elif ballMap[redPos[0]][redPos[1]] == 'O':
                    return 1, redPos, bluePos
    else:
        while True:
            redPos[1] -= 1
            if ballMap[redPos[0]][redPos[1]] == '#':
                redPos[1] += 1
                break
            elif ballMap[redPos[0]][redPos[1]] == 'O':
                return 1, redPos, bluePos

        while True:
            bluePos[1] -= 1
            if ballMap[bluePos[0]][bluePos[1]] == '#':
                bluePos[1] += 1
                break
            elif ballMap[bluePos[0]][bluePos[1]] == 'O':
                return -1, redPos, bluePos
    return 0, redPos, bluePos

def moveRight(redPos, bluePos):
    redIsHole = 0
    if redPos[0] == bluePos[0]:
        if redPos[1] > bluePos[1]:
            while True:
                redPos[1] += 1
                if ballMap[redPos[0]][redPos[1]] == '#':
                    redPos[1] -= 1
                    break
                elif ballMap[redPos[0]][redPos[1]] == 'O':
                    redIsHole = 1
                    redPos = [-1, -1]
                    break

            while True:
                bluePos[1] += 1
                if redPos[1] == bluePos[1]:
                    bluePos[1] -= 1
                    break
                elif ballMap[bluePos[0]][bluePos[1]] == 'O':
                    return -1, redPos, bluePos
                elif ballMap[bluePos[0]][bluePos[1]] == '#':
                    bluePos[1] -= 1
                    if redIsHole:
                        return 1, redPos, bluePos
                    else:
                        break

        else:
            while True:
                bluePos[1] += 1
                if ballMap[bluePos[0]][bluePos[1]] == 'O':
                    return -1, redPos, bluePos
                elif ballMap[bluePos[0]][bluePos[1]] == '#':
                    bluePos[1] -= 1
                    break

            while True:
                redPos[1] += 1
                if redPos[1] == bluePos[1]:
                    redPos[1] -= 1
                    break
                elif ballMap[redPos[0]][redPos[1]] == '#':
                    redPos[1] -= 1
                    break
                elif ballMap[redPos[0]][redPos[1]] == 'O':
                    return 1, redPos, bluePos
    else:
        while True:
            redPos[1] += 1
            if ballMap[redPos[0]][redPos[1]] == '#':
                redPos[1] -= 1
                break
            elif ballMap[redPos[0]][redPos[1]] == 'O':
                return 1, redPos, bluePos

        while True:
            bluePos[1] += 1
            if ballMap[bluePos[0]][bluePos[1]] == '#':
                bluePos[1] -= 1
                break
            elif ballMap[bluePos[0]][bluePos[1]] == 'O':
                return -1, redPos, bluePos
    return 0, redPos, bluePos


def moveMap(redPos, bluePos, tryTime, history):
    global minMove
    if tryTime > 10:
        return

    orgRed = copy.deepcopy(redPos)
    orgBlue = copy.deepcopy(bluePos)

    for i in range(4):
        if i == 0:
            flag, redPos, bluePos = moveUp(redPos, bluePos)
            history.append('up')
            if redPos == orgRed and bluePos == orgBlue:
                history.pop()
                continue
            if flag == 1:
                minMove = min(minMove, tryTime)
                history.pop()
                return
            elif flag == 0:
                moveMap(redPos, bluePos, tryTime + 1, history)
                redPos = copy.deepcopy(orgRed)
                bluePos = copy.deepcopy(orgBlue)
                history.pop()
                continue
            else:
                redPos = copy.deepcopy(orgRed)
                bluePos = copy.deepcopy(orgBlue)
                history.pop()
                continue
        elif i == 1:
            flag, redPos, bluePos = moveLeft(redPos, bluePos)
            history.append('left')
            if redPos == orgRed and bluePos == orgBlue:
                history.pop()
                continue
            if flag == 1:
                minMove = min(minMove, tryTime)
                if minMove == 4:
                    print('', end='')
                history.pop()
                return
            elif flag == 0:
                moveMap(redPos, bluePos, tryTime + 1, history)
                redPos = copy.deepcopy(orgRed)
                bluePos = copy.deepcopy(orgBlue)
                history.pop()
                continue
            else:
                redPos = copy.deepcopy(orgRed)
                bluePos = copy.deepcopy(orgBlue)
                history.pop()
                continue
        elif i == 2:
            flag, redPos, bluePos = moveDown(redPos, bluePos)
            history.append('down')
            if redPos == orgRed and bluePos == orgBlue:
                history.pop()
                continue
            if flag == 1:
                minMove = min(minMove, tryTime)
                if minMove == 4:
                    print('', end='')
                history.pop()
                return
            elif flag == 0:
                moveMap(redPos, bluePos, tryTime + 1, history)
                redPos = copy.deepcopy(orgRed)
                bluePos = copy.deepcopy(orgBlue)
                history.pop()
                continue
            else:
                redPos = copy.deepcopy(orgRed)
                bluePos = copy.deepcopy(orgBlue)
                history.pop()
                continue
        else:
            flag, redPos, bluePos = moveRight(redPos, bluePos)
            history.append('right')
            if redPos == orgRed and bluePos == orgBlue:
                history.pop()
                continue
            if flag == 1:
                minMove = min(minMove, tryTime)
                if minMove == 4:
                    print('', end='')
                history.pop()
                return
            elif flag == 0:
                moveMap(redPos, bluePos, tryTime + 1, history)
                redPos = copy.deepcopy(orgRed)
                bluePos = copy.deepcopy(orgBlue)
                history.pop()
                continue
            else:
                redPos = copy.deepcopy(orgRed)
                bluePos = copy.deepcopy(orgBlue)
                history.pop()
                continue


n, m = map(int, input().split())
holePos = [0, 0]
redPos = [0, 0]
bluePos = [0, 0]
ballMap = []
minMove = 11

for i in range(n):
    tempList = list(input())
    for j in range(m):
        if tempList[j] == 'O':
            holePos = [i, j]
        elif tempList[j] == 'R':
            redPos = [i, j]
            tempList[j] = '.'
        elif tempList[j] == 'B':
            bluePos = [i, j]
            tempList[j] = '.'
    ballMap.append(tempList)

moveMap(redPos, bluePos, 1, [])


if minMove > 10:
    print(-1)
else:
    print(minMove)
```
-> 정답 판정은 받았지만 분기문을 너무 많이 사용해서 코드가 복잡한것 같다

### 구슬탈출2 모범 답안
```
N, M = map(int, input().split())
B = [list(input().rstrip()) for _ in range(N)]  # Board
dx = [-1, 1, 0, 0]  # x축 움직임
dy = [0, 0, -1, 1]  # y축 움직임
queue = []  # BFS : queue 활용
# Red(rx,ry)와 Blue(bx,by)의 탐사 여부 체크
visited = [[[[False] * M for _ in range(N)] for _ in range(M)] for _ in range(N)]

def pos_init():
    rx, ry, bx, by = 0, 0, 0, 0  # 초기화
    for i in range(N):
        for j in range(M):
            if B[i][j] == 'R':
                rx, ry = i, j
            elif B[i][j] == 'B':
                bx, by = i, j
    # 위치 정보와 depth(breadth 끝나면 +1)
    queue.append((rx, ry, bx, by, 1))
    visited[rx][ry][bx][by] = True

def move(x, y, dx, dy):
    cnt = 0  # 이동 칸 수
    # 다음이 벽이거나 현재가 구멍일 때까지
    while B[x+dx][y+dy] != '#' and B[x][y] != 'O':
        x += dx
        y += dy
        cnt += 1
    return x, y, cnt

def solve():
    pos_init()  # 시작 조건
    while queue:  # BFS : queue 기준
        rx, ry, bx, by, depth = queue.pop(0)
        if depth > 10:  # 실패 조건
            break
        for i in range(4):  # 4방향 이동 시도
            nrx, nry, rcnt = move(rx, ry, dx[i], dy[i])  # Red
            nbx, nby, bcnt = move(bx, by, dx[i], dy[i])  # Blue
            if B[nbx][nby] != 'O':  # 실패 조건이 아니면
                if B[nrx][nry] == 'O':  # 성공 조건
                    print(depth)
                    return
                if nrx == nbx and nry == nby:  # 겹쳤을 때
                    if rcnt > bcnt:  # 이동거리가 많은 것을
                        nrx -= dx[i]  # 한 칸 뒤로
                        nry -= dy[i]
                    else:
                        nbx -= dx[i]
                        nby -= dy[i]
                # breadth 탐색 후, 탐사 여부 체크
                if not visited[nrx][nry][nbx][nby]:
                    visited[nrx][nry][nbx][nby] = True
                    # 다음 depth의 breadth 탐색 위한 queue
                    queue.append((nrx, nry, nbx, nby, depth+1))
    print(-1)  # 실패 시

solve()
```
-> 나는 dfs를 활용하여 문제를 해결한 반면 위 풀이는 bfs를 활용하여 문제를 해결하였다. 그리고 나는 분기문을 여러개로 나누어 여러 경우의 수를 표현한 반면 위의 풀이는 4방향과 각 경우를 반목문과 더 적은 수의 분기문을 통해 표현하였다. 여기서 인상 깊은 점은 두 구슬의 x, y 좌표 4개의 데이터를 가지고 visited 리스트를 만든 점이다.
