# 브루트포스 - 순열
### 부등호
```
import copy
k = int(input())
signList = list(input().split())
answerList = []
numList = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

def checkRight(sign, x, y):
    if sign == '>':
        if x > y:
            return True
        else:
            return False
    else:
        if x < y:
            return True
        else:
            return False

def stringListToInt(tempList):
    bstr = ''
    for elem in tempList:
        bstr += str(elem)
    return int(bstr)


def insertNumber(numLists, tempList, index):
    if index == 0:
        for num in numLists:
            newNumList = copy.deepcopy(numLists)
            newNumList.remove(num)
            tempList.append(num)
            insertNumber(newNumList, tempList, index + 1)
            tempList.remove(num)
    else:
        sign = signList[index - 1]
        for num in numLists:
            if not checkRight(sign, tempList[-1], num):
                continue
            else:
                newNumList = copy.deepcopy(numLists)
                newNumList.remove(num)
                tempList.append(num)
                if index == k:       # answer에 추가하는 코드
                    answerList.append(stringListToInt(tempList))
                else:
                    insertNumber(newNumList, tempList, index + 1)
                tempList.remove(num)

insertNumber(numList, [], 0)
maxNum = max(answerList)
minNum = min(answerList)

if len(str(maxNum)) > len(str(minNum)):
    print(maxNum)
    print('0', end='')
    print(minNum)
else:
    print(maxNum)
    print(minNum)
```
-> 이제 실버 1 문제는 easy

### 단어 수학
```
n = int(input())
alphaNumList = []
numList = []

for _ in range(n):
    alphaNumList.append(input())

alphabetList = []
for alphaNum in alphaNumList:
    for elem in alphaNum:
        if elem not in alphabetList:
            alphabetList.append(elem)

# 사용할 수 구하는 코드
alphabetList.sort()
for i in range(len(alphabetList)):
    numList.append(9 - i)

# 위치 list 구하기
posList = [0] * len(alphabetList)

for i in range(len(alphabetList)):
    for j in range(len(alphaNumList)):
        idx = 1
        for k in range(len(alphaNumList[j]) - 1, -1, - 1):
            if alphabetList[i] == alphaNumList[j][k] and idx > posList[i]:
                posList[i] = idx
            idx += 1

# 빈도 list 구하기
freqList = [[0] * 9 for _ in range(len(alphabetList))]
for i in range(len(alphabetList)):
    for j in range(len(alphaNumList)):
        idx = 1
        for k in range(len(alphaNumList[j]) - 1, -1, -1):
            if alphabetList[i] == alphaNumList[j][k]:
                freqList[i][idx] += 1
            idx += 1

figureList = [0] * len(alphabetList)

for i in range(max(posList), 0, -1):
    for j in range(len(alphabetList)):
        if freqList[j][i] != 0:
            figureList[j] += freqList[j][i] * (10 ** (i - 1))

figureList.sort(reverse=True)

idx = 0
result = 0
while idx < len(figureList):
    tempFigure = figureList[idx]
    tempList = []
    for elem in figureList:
        if elem == tempFigure:
            tempList.append(elem)
    usingNum = []
    for i in range(len(tempList)):
        usingNum = numList.pop(0)
        result += usingNum * tempFigure
    idx += len(tempList)

print(result)
```
-> 처음 문제 해결하려고 할 때 본 문제의 토픽인 브루트포스 방식으로 접근했는데 그 경우 10P10 즉 연산량이 100억일 경우도 생긴다. 그래서 브루트포스가 아닌 각 위치의 계수들을 확인하는 방식으로 문제를 해결했다.

### 연산자 끼워넣기
```
maxNum = (-1) * int(1e9)
minNum = int(1e9)

def calculate(a, b, sign):
    if sign == '+':
        return a + b
    elif sign == '-':
        return a - b
    elif sign == '*':
        return a * b
    else:
        if a < 0:
            a = (- 1) * a
            return (-1) * (a // b)
        else:
            return a // b


def calculateList(numList, candidate):
    result = numList[0]
    for i in range(len(candidate)):
        result = calculate(result, numList[i + 1], candidate[i])
    return result


def makePermutation(signNumList, tempList):
    global maxNum, minNum
    if signNumList[0] > 0:
        signNumList[0] -= 1
        tempList.append('+')
        makePermutation(signNumList, tempList)
        tempList.pop(-1)
        signNumList[0] += 1
    if signNumList[1] > 0:
        signNumList[1] -= 1
        tempList.append('-')
        makePermutation(signNumList, tempList)
        tempList.pop(-1)
        signNumList[1] += 1
    if signNumList[2] > 0:
        signNumList[2] -= 1
        tempList.append('*')
        makePermutation(signNumList, tempList)
        tempList.pop(-1)
        signNumList[2] += 1
    if signNumList[3] > 0:
        signNumList[3] -= 1
        tempList.append('/')
        makePermutation(signNumList, tempList)
        tempList.pop(-1)
        signNumList[3] += 1
    if signNumList[0] == 0 and signNumList[1] == 0 and signNumList[2] == 0 and signNumList[3] == 0:
        result = calculateList(aList, tempList)

        maxNum = max(result, maxNum)
        minNum = min(result, minNum)

n = int(input())
aList = list(map(int, input().split()))
signNumList = list(map(int, input().split()))

makePermutation(signNumList, [])

print(maxNum)
print(minNum)
```
-> 초기 max 값과 min 값 설정을 잘 해주자

### 스타트와 링크
```
import itertools
import copy

def countDiff(start, link):
    startStat = 0
    linkStat = 0
    for elem in start:
        for partner in start:
            startStat += s[elem][partner]
    for elem in link:
        for partner in link:
            linkStat += s[elem][partner]

    return abs(startStat - linkStat)

n = int(input())
s = []
for _ in range(n):
    s.append(list(map(int, input().split())))
atheletes = []
for i in range(n):
    atheletes.append(i)
candidates = list(itertools.combinations(atheletes, n // 2))

minNum = int(1e9)

for candidate in candidates:
    start = list(copy.deepcopy(candidate))
    link = list(set(atheletes) - set(candidate))
    diff = countDiff(start, link)
    minNum = min(minNum, diff)

print(minNum)
```
