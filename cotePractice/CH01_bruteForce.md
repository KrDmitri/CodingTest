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

### 두 
