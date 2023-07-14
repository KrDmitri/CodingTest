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

