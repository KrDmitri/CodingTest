# 브루트포스 - 기타
### 수들의 합 2
```
n, m = map(int, input().split())
numList = list(map(int, input().split()))

answer = 0

for i in range(n):
    tempSum = 0
    for j in range(i, n):
        tempSum += numList[j]
        if tempSum == m:
            answer += 1
            break
        elif tempSum > m:
            break

print(answer)
```

### 부분합
```
n, s = map(int, input().split())
numList = list(map(int, input().split()))

bottom = 0
top = n

if sum(numList) < s:
    print(0)
else:
    while bottom < top:
        mid = (bottom + top) // 2
        flag = 0
        for i in range(n - mid + 1):
            tempSum = sum(numList[i:i + mid])
            if tempSum >= s:
                top = mid
                flag = 1
                break
        if flag == 0:
            bottom = mid + 1
        if bottom == top:
            print(bottom)
            break
```
-> 이분탐색을 활용한 풀이인데 시간 초과가 난다

### 부분합(두번째 풀이)
```
n, s = map(int, input().split())
numList = list(map(int, input().split()))

start, end = 0, 0
tempSum = numList[0]
answer = int(1e9)

while True:
    if tempSum < s:
        end += 1
        if end == n:
            break
        tempSum += numList[end]
    else:
        answer = min(answer, end - start + 1)
        tempSum -= numList[start]
        start += 1

if answer == int(1e9):
    print(0)
else:
    print(answer)
```
-> 해당 문제는 '두 포인터' 카테고리의 문제였다. 두 포인터 문제는 처음 풀어서 모범 답안 코드를 먼저 본 후 위의 코드를 개인적으로 작성해보았다.

### 소수의 연속합
```
def isPrime(num):
    for i in range(2, num):
        if num % i == 0:
            return False
    return True

def primeSum(first, last):
    tempSum = 0
    for i in range(first, last + 1):
        if isPrime(i):
            tempSum += i
    return tempSum

def nextPrime(num):
    num += 1
    while True:
        flag = 1
        for i in range(2, num):
            if num % i == 0:
                num += 1
                flag = 0
                break
        if flag:
            return num

n = int(input())

start = 2
end = 2
result = 0
tempSum = primeSum(start, end)

while True:
    if tempSum < n:
        end = nextPrime(end)
        if end > n:
            break
        tempSum = primeSum(start, end)
    elif tempSum > n:
        start = nextPrime(start)
        tempSum = primeSum(start, end)
    else:
        result += 1
        start = nextPrime(start)
        tempSum = primeSum(start, end)

print(result)
```
-> 이전 문제의 두 포인터 개념을 활용하여 풀었는데 시간 초과가 난다

### 소수의 연속합(2차 풀이)
```
def isPrime(num):
    tempNum = int(num ** (1/2))
    for i in range(2, tempNum + 1):
        if num % i == 0:
            return False
    return True

def primeSum(first, last, numList):
    firstIndex = numList.index(first)
    lastIndex = numList.index(last)
    tempSum = sum(numList[firstIndex:lastIndex + 1])
    return tempSum

def nextPrime(num):
    num += 1
    while True:
        flag = 1
        tempNum = int(num ** (1/2))
        for i in range(2, tempNum + 1):
            if num % i == 0:
                num += 1
                flag = 0
                break
        if flag:
            return num

def prevPrime(num):
    if num == 2 or num == 0:
        return None
    num -= 1
    while True:
        flag = 1
        try:
            tempNum = int(num ** (1/2))
        except TypeError:
            print('', end='')
        for i in range(2, tempNum + 1):
            if num % i == 0:
                num -= 1
                flag = 0
                break
        if flag:
            return num

n = int(input())

result = 0

if n == 1:
    print(0)
# elif n == 2:
#     print(1)
# elif n == 3:
#     print(1)
else:
    if isPrime(n):
        result += 1

    start = prevPrime(n // 2 + 1)
    end = nextPrime(n // 2)
    primeList = []
    primeList.append(start)
    primeList.append(end)
    tempSum = primeSum(start, end, primeList)

    while True:
        if tempSum < n:
            start = prevPrime(start)
            primeList.insert(0, start)
            if start == None:
                break
            tempSum = primeSum(start, end, primeList)
        elif tempSum > n:
            end = primeList[primeList.index(end) - 1]
            tempSum = primeSum(start, end, primeList)
        else:
            result += 1
            start = prevPrime(start)
            primeList.insert(0, start)
            if start == None:
                break
            tempSum = primeSum(start, end, primeList)

    print(result)
```
-> 소수 판별 시간 줄이기, 탐색 범위 반으로 줄이기, 약간의 다이나믹 프로그래밍 기법 등을 이용해서 시간을 줄여 정답 판정을 받았다. 모범 답안 코드를 봐야할 것 같다.
