## 브루트 포스 - 재귀
### 로또(itertools 모듈 사용)
```
import itertools
def lotto():
    while True:
        tempList = list(map(int, input().split()))
        if tempList[0] == 0:
            break
        numList = tempList[1:]
        allCases = itertools.combinations(numList, 6)

        for case in allCases:
            case = list(case)
            case.sort()
            for elem in case:
                print(elem, end=' ')
            print()
        print()
```

### 로또(재귀함수 사용)
```
import copy
def lotto():
    def combination(domain, numSize, sinceList):
        newSince = copy.deepcopy(sinceList)
        if len(domain) == numSize:
            for elem in domain:
                newSince.append(elem)
            finalList.append(newSince)
            return
        elif len(domain) < numSize:
            return

        if len(domain) == 0:
            return
        newSince.append(domain[0])
        combination(domain[1:], numSize - 1, newSince)
        combination(domain[1:], numSize, sinceList)
        

    while True:
        tempList = list(map(int, input().split()))
        if tempList[0] == 0:
            break
        finalList = []
        numList = tempList[1:]
        combination(numList, 6, [])

        for candidate in finalList:
            for elem in candidate:
                print(elem, end=' ')
            print()
        print()
```
-> 이제껏 itertools 모듈의 함수를 사용해, 수의 조합을 어떻게 만드는건지 잘 몰랐는데, 이번에 재귀함수를 이용해서 문제해결을 해서 정확한 원리를 알게되었다!

### 부분수열의 합
```
import itertools
def sumOfSubnet():
    n, s = map(int, input().split())
    numList = list(map(int, input().split()))
    cnt = 0

    for i in range(1, n + 1):
        allCases = itertools.combinations(numList, i)
        for case in allCases:
            if sum(case) == s:
                cnt += 1

    print(cnt)
```

### 부분수열의 합 2
```
def sumOfSubnet2():
    n = int(input())
    s = list(map(int, input().split()))
    s.sort()

    answer = 0
    flag = 0

    for i in range(n):
        if s[i] - answer > 1:
            answer += 1
            flag = 1
            break
        answer += s[i]

    if flag == 0:
        answer += 1

    print(answer)
```

### 연산자 끼워넣기(2) 1번째 시도
```
import itertools

def insertOperands():
    def operate(a, b, operand):
        if operand == 0:
            return a + b
        elif operand == 1:
            return a - b
        elif operand == 2:
            return a * b
        else:
            return a // b

    n = int(input())
    numList = list(map(int, input().split()))
    numOperands = list(map(int, input().split()))   # +, -, *, /
    operandsList = []
    min = int(1e9)
    max = 0

    for i in range(4):
        for j in range(numOperands[i]):
            operandsList.append(i)

    candidates = itertools.permutations(operandsList, n - 1)

    for eachCase in candidates:
        tempAnswer = numList[0]
        for i in range(1, n):
            tempAnswer = operate(tempAnswer, numList[i], eachCase[i - 1])
        if min > tempAnswer:
            min = tempAnswer
        if max < tempAnswer:
            max = tempAnswer

    print(max)
    print(min)
```
-> 예제는 모두 통과했는데, 백준 사이트 제출하면 시간 초과가 난다. 본 문제의 갈래와 같이 재귀적인 용법으로 문제 해결을 시도해보려 한다.

### 연산자 끼워넣기(2) 2번째 시도 - 성공
```
import copy

def calculate(a, b, operand):
    if operand == 0:
        return a + b
    elif operand == 1:
        return a - b
    elif operand == 2:
        return a * b
    else:
        if a < 0:
            a = abs(a)
            tempNum = a // b
            return (-1) * tempNum
        return a // b

def operate(numbers, operand, operands):
    if len(numbers) == 2:
        candidates.append(calculate(numbers[0], numbers[1], operand))
        return
    else:
        tempNum = calculate(numbers[0], numbers[1], operand)
        numbers.pop(0)
        numbers.pop(0)
        numbers.insert(0, tempNum)
        if operands[0] > 0:
            tempNumbers = copy.deepcopy(numbers)
            tempOperands = copy.deepcopy(operands)
            tempOperands[0] -= 1
            operate(tempNumbers, 0, tempOperands)
        if operands[1] > 0:
            tempNumbers = copy.deepcopy(numbers)
            tempOperands = copy.deepcopy(operands)
            tempOperands[1] -= 1
            operate(tempNumbers, 1, tempOperands)
        if operands[2] > 0:
            tempNumbers = copy.deepcopy(numbers)
            tempOperands = copy.deepcopy(operands)
            tempOperands[2] -= 1
            operate(tempNumbers, 2, tempOperands)
        if operands[3] > 0:
            tempNumbers = copy.deepcopy(numbers)
            tempOperands = copy.deepcopy(operands)
            tempOperands[3] -= 1
            operate(tempNumbers, 3, tempOperands)

n = int(input())
numList = list(map(int, input().split()))
operandsList = list(map(int, input().split()))

candidates = []

if operandsList[0] > 0:
    tempNumList = copy.deepcopy(numList)
    operandsList[0] -= 1
    operate(tempNumList, 0, operandsList)
    operandsList[0] += 1
if operandsList[1] > 0:
    tempNumList = copy.deepcopy(numList)
    operandsList[1] -= 1
    operate(tempNumList, 1, operandsList)
    operandsList[1] += 1
if operandsList[2] > 0:
    tempNumList = copy.deepcopy(numList)
    operandsList[2] -= 1
    operate(tempNumList, 2, operandsList)
    operandsList[2] += 1
if operandsList[3] > 0:
    tempNumList = copy.deepcopy(numList)
    operandsList[3] -= 1
    operate(tempNumList, 3, operandsList)
    operandsList[3] += 1

print(max(candidates))
print(min(candidates))
```
-> 풀이를 문제 공간을 dfs로 순회한 것과 같이 재귀적인 용법으로 하여서 시간 초과 문제를 해결하였다. 다만 재귀적 용법을 사용할 때에는 항상 그렇듯 deepcopy()함수를 사용하여 특정 리스트의 값이 변하지 않도록 해주어야 한다.

### 테트로미노(dfs 풀이)
```
import copy
import itertools
def checkArea(xpos, ypos, histroy, sum):
    global maxNum
    sum += paperMap[xpos][ypos]
    histroy.append([xpos, ypos])
    if len(histroy) == 4:
        maxNum = max(maxNum, sum)
    else:
        for i in range(4):
            nxpos = xpos + dx[i]
            nypos = ypos + dy[i]
            if nxpos >= 0 and nxpos < n and nypos >= 0 and nypos < m and [nxpos, nypos] not in histroy:
                newHistory = copy.deepcopy(histroy)
                newSum = copy.deepcopy(sum)
                checkArea(nxpos, nypos, newHistory, newSum)


def checkTuArea(xpos, ypos):
    global maxNum
    directionList = [0, 1, 2, 3]
    directions = itertools.combinations(directionList, 3)
    for eachCase in directions:
        sum = paperMap[xpos][ypos]
        for i in eachCase:
            nxpos = xpos + dx[i]
            nypos = ypos + dy[i]
            if nxpos >= 0 and nxpos < n and nypos >= 0 and nypos < m:
                sum += paperMap[nxpos][nypos]
        maxNum = max(maxNum, sum)


n, m = map(int, input().split())
paperMap = []
for _ in range(n):
    tempList = list(map(int, input().split()))
    paperMap.append(tempList)
maxNum = 0
dx = [-1, 0, 1, 0]
dy = [0, -1, 0, 1]

for i in range(n):
    for j in range(m):
        checkArea(i, j, [], 0)

for i in range(n):
    for j in range(m):
        checkTuArea(i, j)

print(maxNum)
```
-> 예제 코드는 전부 맞았는데 시간 초과가 났다. 아무래도 최대 경우인 500 * 500 매트릭스에서는 연산량이 많아서 나는거 같다. 방금 난 생각으로는 다익스트라 알고리즘처럼 인접한 곳의 정보를 모두 더 해 인접 노드 중 가장 높은 값만 추가하여 풀이하는 방법으로 해결할 수 있을거 같다.

### 테트로미노(다익스트라 스타일 풀이)
```
# 아래 함수에 candidates 리스트를 추가하여 문제 더 빠르게 해결 가능?
def checkArea(xpos, ypos, histroy, sum, candidates):
    global maxNum
    sum += paperMap[xpos][ypos]
    histroy.append([xpos, ypos])
    # 여기 현재 추가된 노드 기준 상하좌우에 있는 노드를 candidate로 추가해줘야함
    for i in range(4):
        nxpos = xpos + dx[i]
        nypos = ypos + dy[i]
        if nxpos >= 0 and nxpos < n and nypos >= 0 and nypos < m and [nxpos, nypos] not in histroy:
            candidates.append([nxpos, nypos, paperMap[nxpos][nypos]])

    if len(histroy) == 4:
        maxNum = max(maxNum, sum)
    else:
        candidates.sort(key=lambda x:x[2], reverse=True)
        chosenNode = candidates.pop(0)
        checkArea(chosenNode[0], chosenNode[1], histroy, sum, candidates)


n, m = map(int, input().split())
paperMap = []
for _ in range(n):
    tempList = list(map(int, input().split()))
    paperMap.append(tempList)

maxNum = 0
dx = [-1, 0, 1, 0]
dy = [0, -1, 0, 1]

for i in range(n):
    for j in range(m):
        checkArea(i, j, [], 0, [])

print(maxNum)
```
-> 기존 알고리즘에 비해 속도도 확실히 빠르지만 85%에서 오답나옴.. 지금 생각해보니 아주 큰 값을 가진 어떤 노드의 인접 노드들이 전부 0이라면 내 코드가 틀릴 수 있을것 같음

### 테트로미노(Branch-and-Bound 풀이)
```
import copy
import itertools
def pruning(sum, history):
    global maxNum
    if len(history) == 1:
        sum += valList[0] + valList[1] + valList[2]
    elif len(history) == 2:
        sum += valList[0] + valList[1] + valList[2]
    elif len(history) == 3:
        sum += valList[0] + valList[1] + valList[2]
    else:
        return False

    if sum < maxNum:
        return True


def checkArea(xpos, ypos, history, sum):
    global maxNum
    sum += paperMap[xpos][ypos]
    history.append([xpos, ypos])
    # 대충 가지치기 코드..?
    if pruning(sum, history):
        return
    if len(history) == 4:
        maxNum = max(maxNum, sum)
        return
    else:
        for i in range(4):
            nxpos = xpos + dx[i]
            nypos = ypos + dy[i]
            if nxpos >= 0 and nxpos < n and nypos >= 0 and nypos < m and [nxpos, nypos] not in history:
                newHistory = copy.deepcopy(history)
                newSum = copy.deepcopy(sum)
                checkArea(nxpos, nypos, newHistory, newSum)


def checkTuArea(xpos, ypos):
    global maxNum
    directionList = [0, 1, 2, 3]
    directions = itertools.combinations(directionList, 3)
    for eachCase in directions:
        sum = paperMap[xpos][ypos]
        for i in eachCase:
            nxpos = xpos + dx[i]
            nypos = ypos + dy[i]
            if nxpos >= 0 and nxpos < n and nypos >= 0 and nypos < m:
                sum += paperMap[nxpos][nypos]
        maxNum = max(maxNum, sum)


n, m = map(int, input().split())
paperMap = []
valList = []
for _ in range(n):
    tempList = list(map(int, input().split()))
    for elem in tempList:
        valList.append(elem)
    paperMap.append(tempList)

valList.sort(reverse=True)
maxNum = 0
dx = [-1, 0, 1, 0]
dy = [0, -1, 0, 1]

for i in range(n):
    for j in range(m):
        checkArea(i, j, [], 0)

for i in range(n):
    for j in range(m):
        checkTuArea(i, j)

print(maxNum)
```
-> 위의 dfs 풀이에서 대충 pruning function을 추가해줬더니 시간 초과 없이 정답 판정을 받았다. 얼마나 시간이 절약되겠어 했는데 이게 되네;;;

### 두 동전
```
import copy
def checkCoinNum(coinPos):
    coinNum = 2
    for i in range(2):
        if coinPos[i][0] == -1 and coinPos[i][1] == -1:
            coinNum -= 1
    return coinNum

def moveCoins(coinPos, coinMap, dir, tryTime, coinPosHistory):
    global minTry
    tryTime += 1
    if tryTime > minTry:
        return
    if tryTime > 10:
        return
    flag = 0
    for i in range(2):
        xpos = coinPos[i][0]
        ypos = coinPos[i][1]
        nxpos = xpos + dx[dir]
        nypos = ypos + dy[dir]
        # 코인이 밖으로 나가는지 벽에 부딪히는지 확인하기
        if nxpos < 0 or nxpos >= n or nypos < 0 or nypos >= m:
            coinPos[i][0], coinPos[i][1] = -1, -1
        elif coinMap[nxpos][nypos] == '#':
            flag += 1
        else:
            coinPos[i][0], coinPos[i][1] = nxpos, nypos

    if coinPos in coinPosHistory:
        return
    else:
        coinPosHistory.append(coinPos)

    if flag == 2:
        return
    coinNum = checkCoinNum(coinPos)
    if coinNum == 2:
        if tryTime == 10:
            return
        for i in range(4):
            newCoinPos = copy.deepcopy(coinPos)
            newTryTime = tryTime
            newCoinPosHistory = copy.deepcopy(coinPosHistory)
            moveCoins(newCoinPos, coinMap, i, newTryTime, newCoinPosHistory)
    elif coinNum == 1:
        minTry = min(minTry, tryTime)
    elif coinNum == 0:
        return


n, m = map(int, input().split())
coinMap = []
coinPos = []
dx = [-1, 0, 1, 0]
dy = [0, -1, 0, 1]
for i in range(n):
    temp = input()
    for j in range(m):
        if temp[j] == 'o':
            coinPos.append([i, j])
    coinMap.append(temp)

minTry = int(1e9)

for i in range(4):
    newCoinPos = copy.deepcopy(coinPos)
    history = copy.deepcopy(coinPos)
    moveCoins(newCoinPos, coinMap, i, 0, [history])

if minTry == int(1e9):
    minTry = -1

print(minTry)
```
-> 재귀적 용법으로 푸는 부르트 포스는 시간 초과가 나지 않으려면 pruning을 아주 꼼꼼히 해줘야한다...

### 에너지 모으기
```
import copy
# 이것도 pruning 생각해야하나 남은거 다 곱해도 못넘으면 리턴하는 식으로
def energeyCollect(beadList, index, energySum):
    global maxEnergy
    energySum += beadList[index - 1] * beadList[index + 1]
    beadList.pop(index)
    maxEnergy = max(maxEnergy, energySum)
    if len(beadList) == 2:
        return
    else:
        for i in range(1, len(beadList) -1):
            newBeadList = copy.deepcopy(beadList)
            newEnergySum = energySum
            energeyCollect(newBeadList, i, newEnergySum)


n = int(input())
w = list(map(int, input().split()))
maxEnergy = 0

for i in range(1, len(w) - 1):
    beadList = copy.deepcopy(w)
    energySum = 0
    energeyCollect(beadList, i, energySum)

print(maxEnergy)
```
-> EZ

### N-Queens (1차 시도)
```
# N-Queen 문제, N개의 칸에서 N개의 퀸이 서로 공격할 수 없는 위치에 놓도록 하는 경우의 수 구하기
import copy
def checkRight(oneQueen, chessMap, row):
    xpos = oneQueen[0]
    ypos = oneQueen[1]
    for i in range(2):
        nxpos = xpos + dx[i]
        nypos = ypos + dy[i]
        flag = True
        while nxpos >= 0 and nxpos <= row and nypos >=0 and nypos < n:
            if chessMap[nxpos][nypos] == 1:
                flag = False
                break
            nxpos += dx[i]
            nypos += dy[i]
        if flag == False:
            break
    if flag == False:
        return False
    else:
        return True


def appendNewQueen(row, col, chessMap, queenPos, visitedCol):
    global numCases
    # pruning 1 : 선택됐던 컬럼 다시 선택하면 return
    if row != 0:
        if col in visitedCol or col == visitedCol[-1] -1 or col == visitedCol[-1] + 1:
            return

    # 우선 바꿔봄
    chessMap[row][col] = 1
    visitedCol.append(col)
    queenPos.append([row, col])

    # 이전 행에 놓였던 여왕들 위치를 보고 계속해도 되는지 확인
    if row > 1:
        for i in range(row):
            oneQueen = queenPos[i]
            flag = checkRight(oneQueen, chessMap, row)
            if flag == False:
                return

    # 계속해도 괜찮으면 아래 코드 실행
    if len(queenPos) == n:
        numCases += 1
        return

    newRow = row + 1
    for i in range(n):
        newChessMap = copy.deepcopy(chessMap)
        newQueenPos = copy.deepcopy(queenPos)
        newVisitedCol = copy.deepcopy(visitedCol)
        col = i
        appendNewQueen(newRow, col, newChessMap, newQueenPos, newVisitedCol)


n = int(input())
chessMap = [[0] * n for _ in range(n)]
queenPos = []
numCases = 0
caseSum = 0
dx = [1, 1]
dy = [-1, 1]

for i in range(n // 2):
    newChessMap = copy.deepcopy(chessMap)
    newQueenPos = copy.deepcopy(queenPos)
    row = 0
    col = i
    visitedCol = []
    appendNewQueen(row, col, newChessMap, newQueenPos, visitedCol)

caseSum += numCases * 2

if n % 2 == 1:
    row = 0
    col = n // 2
    numCases = 0
    appendNewQueen(row, col, chessMap, queenPos, [])
    caseSum += numCases

print(caseSum)
```
-> 아... 또 시간초과나네...

### N-Queens (2차 시도)
```
# N-Queen 문제, N개의 칸에서 N개의 퀸이 서로 공격할 수 없는 위치에 놓도록 하는 경우의 수 구하기
import copy
def checkRight(oneQueen, chessMap, fromRow, row, candidates):
    xpos = oneQueen[0]
    ypos = oneQueen[1]
    for i in range(2):
        nxpos = xpos + dx[i] * (row - fromRow)
        nypos = ypos + dy[i] * (row - fromRow)
        flag = True
        if nypos in candidates:
            delElem = nypos
            flag = False
            break
    if flag == False:
        return False, delElem
    else:
        return True, None


def appendNewQueen(row, col, chessMap, queenPos, visitedCol):
    global numCases

    chessMap[row][col] = 1
    visitedCol.append(col)
    queenPos.append([row, col])

    if chessMap[0][3] == 1 and chessMap[1][1] == 1 and chessMap[2][4] == 1:
        print('', end='')

    # 계속해도 괜찮으면 아래 코드 실행
    if len(queenPos) == n:
        numCases += 1
        # for line in chessMap:
        #     print(line)
        # print()
        return

    ####
    candidates = []
    for i in range(n):
        candidates.append(i)
    candidates.remove(col)
    for elem in visitedCol:
        if elem in candidates:
            candidates.remove(elem)
    if col - 1 in candidates:
        candidates.remove(col - 1)
    if col + 1 in candidates:
        candidates.remove(col + 1)

    if row > 0:
        for elem in range(row):
            oneQueen = queenPos[elem]
            flag, delElem = checkRight(oneQueen, chessMap, elem, row + 1, candidates)
            if flag == False and delElem in candidates:
                candidates.remove(delElem)
        for elem in range(row):
            oneQueen = queenPos[elem]
            flag, delElem = checkRight(oneQueen, chessMap, elem, row + 1, candidates)
            if flag == False and delElem in candidates:
                candidates.remove(delElem)
    ####

    newRow = row + 1
    for i in candidates:
        newChessMap = copy.deepcopy(chessMap)
        newQueenPos = copy.deepcopy(queenPos)
        newVisitedCol = copy.deepcopy(visitedCol)
        col = i
        appendNewQueen(newRow, col, newChessMap, newQueenPos, newVisitedCol)


n = int(input())
chessMap = [[0] * n for _ in range(n)]
queenPos = []
numCases = 0
caseSum = 0
dx = [1, 1]
dy = [-1, 1]

for i in range(n // 2):
    newChessMap = copy.deepcopy(chessMap)
    newQueenPos = copy.deepcopy(queenPos)
    row = 0
    col = i
    visitedCol = []
    appendNewQueen(row, col, newChessMap, newQueenPos, visitedCol)

caseSum += numCases * 2

if n % 2 == 1:
    row = 0
    col = n // 2
    numCases = 0
    appendNewQueen(row, col, chessMap, queenPos, [])
    caseSum += numCases

print(caseSum)
```
-> candidates 리스트를 추가해서 계산 시간을 조금 줄여 n이 12까지는 가능하게 되었지만 14까지는 아직 무리쓰..ㅠ

### N-Queens(3차 시도)
```
# N-Queen 문제, N개의 칸에서 N개의 퀸이 서로 공격할 수 없는 위치에 놓도록 하는 경우의 수 구하기
import copy

# 이전 함수는 이때까지 놓은 여왕 말을 기준으로 후보 좌표에 새로운 말을 놓아도 되는지 확인했다면, 이번에는 후보 좌표를 기준으로 확인
def checkRight(row, candidates, chessMap):
    delList = []

    for col in candidates:
        for i in range(2):
            nxpos = row + dx[i]
            nypos = col + dy[i]
            flag = True
            while nxpos >= 0 and nxpos < row and nypos >=0 and nypos < n:
                if chessMap[nxpos][nypos] == 1:
                    delList.append(col)
                    flag = False
                    break
                nxpos = nxpos + dx[i]
                nypos = nypos + dy[i]
            if flag == False:
                break

    return delList


def appendNewQueen(row, col, chessMap, queenPos, visitedCol):
    global numCases

    chessMap[row][col] = 1
    visitedCol.append(col)
    queenPos.append([row, col])

    if chessMap[0][3] == 1 and chessMap[1][1] == 1 and chessMap[2][4] == 1:
        print('', end='')

    # 계속해도 괜찮으면 아래 코드 실행
    if len(queenPos) == n:
        numCases += 1
        # for line in chessMap:
        #     print(line)
        # print()
        return

    ####
    candidates = []
    for i in range(n):
        candidates.append(i)
    candidates.remove(col)
    for elem in visitedCol:
        if elem in candidates:
            candidates.remove(elem)
    if col - 1 in candidates:
        candidates.remove(col - 1)
    if col + 1 in candidates:
        candidates.remove(col + 1)

    if row > 0:
        delList = checkRight(row + 1, candidates, chessMap)
        for elem in delList:
            candidates.remove(elem)
    ####

    newRow = row + 1
    for i in candidates:
        newChessMap = copy.deepcopy(chessMap)
        newQueenPos = copy.deepcopy(queenPos)
        newVisitedCol = copy.deepcopy(visitedCol)
        col = i
        appendNewQueen(newRow, col, newChessMap, newQueenPos, newVisitedCol)


n = int(input())
chessMap = [[0] * n for _ in range(n)]
queenPos = []
numCases = 0
caseSum = 0
dx = [-1, -1]
dy = [-1, 1]

for i in range(n // 2):
    newChessMap = copy.deepcopy(chessMap)
    newQueenPos = copy.deepcopy(queenPos)
    row = 0
    col = i
    visitedCol = []
    appendNewQueen(row, col, newChessMap, newQueenPos, visitedCol)

caseSum += numCases * 2

if n % 2 == 1:
    row = 0
    col = n // 2
    numCases = 0
    appendNewQueen(row, col, chessMap, queenPos, [])
    caseSum += numCases

print(caseSum)
```
-> n이 13까지는 계산이 되는데 14는 역시 무리쓰...😤

### N-Queens(4차 시도)
```
# N-Queen 문제, N개의 칸에서 N개의 퀸이 서로 공격할 수 없는 위치에 놓도록 하는 경우의 수 구하기
import copy

def appendNewQueen(row, col, chessMap, queenPos, visitedCol):
    global numCases

    chessMap[row][col] = 1
    visitedCol.append(col)
    queenPos.append([row, col])

    if len(queenPos) == n:
        numCases += 1
        return

    candidates = []
    for i in range(n):
        candidates.append(i)
    candidates.remove(col)
    for elem in visitedCol:
        if elem in candidates:
            candidates.remove(elem)

    if row > 0:
        for i in range(len(queenPos)):
            posDif = len(queenPos) - i
            baseCol = queenPos[i][1]
            if baseCol - posDif in candidates:
                candidates.remove(baseCol - posDif)
            if baseCol + posDif in candidates:
                candidates.remove(baseCol + posDif)

    newRow = row + 1
    for i in candidates:
        newChessMap = copy.deepcopy(chessMap)
        newQueenPos = copy.deepcopy(queenPos)
        newVisitedCol = copy.deepcopy(visitedCol)
        col = i
        appendNewQueen(newRow, col, newChessMap, newQueenPos, newVisitedCol)

n = int(input())
chessMap = [[0] * n for _ in range(n)]
queenPos = []
numCases = 0
caseSum = 0

for i in range(n // 2):
    newChessMap = copy.deepcopy(chessMap)
    newQueenPos = copy.deepcopy(queenPos)
    row = 0
    col = i
    visitedCol = []
    appendNewQueen(row, col, newChessMap, newQueenPos, visitedCol)

caseSum += numCases * 2

if n % 2 == 1:
    row = 0
    col = n // 2
    numCases = 0
    appendNewQueen(row, col, chessMap, queenPos, [])
    caseSum += numCases

print(caseSum)
```
### N-Queens 해설 답안 코드
```
n = int(input())
ans = 0
row = [0] * n


def is_promising(x):
    for i in range(x):
        if row[x] == row[i] or abs(row[x] - row[i]) == abs(x - i):
            return False

    return True


def n_queens(x):
    global ans
    if x == n:
        ans += 1
        return

    else:
        for i in range(n):
            # [x, i]에 퀸을 놓겠다.
            row[x] = i
            if is_promising(x):
                n_queens(x + 1)


n_queens(0)
print(ans)
```

### 스도쿠
```
import copy
def checkHorizontal(xIndex, tempMap):
    candidates = [1, 2, 3, 4, 5, 6, 7, 8, 9]
    for i in range(9):
        if tempMap[xIndex][i] in candidates:
            candidates.remove(tempMap[xIndex][i])
    return candidates

def checkVertical(yIndex, tempMap):
    candidates = [1, 2, 3, 4, 5, 6, 7, 8, 9]
    for i in range(9):
        if tempMap[i][yIndex] in candidates:
            candidates.remove(tempMap[i][yIndex])
    return candidates

# //2 해서 나오는 값으로 어떤 칸인지 확인 가능?
def checkSquare(xIndex, yIndex, tempMap):
    candidates = [1, 2, 3, 4, 5, 6, 7, 8, 9]
    xSquare = xIndex // 3
    ySquare = yIndex // 3
    for i in range(xSquare * 3, xSquare * 3 + 3):
        for j in range(ySquare * 3, ySquare * 3 + 3):
            if tempMap[i][j] in candidates:
                candidates.remove(tempMap[i][j])
    return candidates

def checkAllRight(xIndex, yIndex, tempMap):
    candidates = [1, 2, 3, 4, 5, 6, 7, 8, 9]
    for i in range(9):
        if tempMap[xIndex][i] in candidates:
            candidates.remove(tempMap[xIndex][i])
        elif tempMap[xIndex][i] == 0:
            continue
        else:
            return False

    candidates = [1, 2, 3, 4, 5, 6, 7, 8, 9]
    for i in range(9):
        if tempMap[i][yIndex] in candidates:
            candidates.remove(tempMap[i][yIndex])
        elif tempMap[i][yIndex] == 0:
            continue
        else:
            return False

    candidates = [1, 2, 3, 4, 5, 6, 7, 8, 9]
    xSquare = xIndex // 3
    ySquare = yIndex // 3
    for i in range(xSquare * 3, xSquare * 3 + 3):
        for j in range(ySquare * 3, ySquare * 3 + 3):
            if tempMap[i][j] in candidates:
                candidates.remove(tempMap[i][j])
            elif tempMap[i][j] == 0:
                continue
            else:
                return False

    return True

def solveSudoku(bList, cList, pMap):
    blank = bList.pop(0)
    candidates = cList.pop(0)
    xpos = blank[0]
    ypos = blank[1]
    for elem in candidates:
        tempMap = copy.deepcopy(pMap)
        tempMap[xpos][ypos] = elem
        if checkAllRight(xpos, ypos, tempMap):
            if len(bList) == 0:
                for i in range(9):
                    for j in range(9):
                        print(tempMap[i][j], end=' ')
                    print()
                return True
            else:
                tempBList = copy.deepcopy(bList)
                tempCList = copy.deepcopy(cList)
                flag = solveSudoku(tempBList, tempCList, tempMap)
                if flag == True:
                    return True
        else:
            continue

puzzleMap = []
blankList = []
candidatesList = []
for i in range(9):
    tempList = list(map(int, input().split()))
    for j in range(9):
        if tempList[j] == 0:
            blankList.append([i, j])
    puzzleMap.append(tempList)

# 각 칸의 candidates 구하기
for i in range(len(blankList)):
    tempBlank = blankList[i]
    tempHList = checkHorizontal(tempBlank[0], puzzleMap)
    tempVList = checkVertical(tempBlank[1], puzzleMap)
    tempSList = checkSquare(tempBlank[0], tempBlank[1], puzzleMap)

    hSet = set(tempHList)
    vSet = set(tempVList)
    sSet = set(tempSList)

    tempCandidate = list(hSet & vSet & sSet)

    candidatesList.append(tempCandidate)

solveSudoku(blankList, candidatesList, puzzleMap)
```
-> 아오오오 시간초과...ㅜㅜ
