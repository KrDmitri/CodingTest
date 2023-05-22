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
