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
elif n == 2:
    print(1)
elif n == 3:
    print(1)
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

### 소수의 연속합(모범 답안)
```
import math

N = int(input())

a = [False, False] + [True] * (N-1)
prime_num = []

for i in range(2, N+1):
    if a[i]:
        prime_num.append(i)
        for j in range(2*i, N+1, i):
            a[j] = False

answer = 0
start = 0
end = 0
while end <= len(prime_num):
    temp_sum = sum(prime_num[start:end])
    if temp_sum == N:
        answer += 1
        end += 1
    elif temp_sum < N:
        end += 1
    else:
        start += 1

print(answer)
```
-> 위의 풀이는 '에라토스테네스의 체'라는 알고리즘을 통해 소수인지에 대한 정보 리스트 a를 구해놓고 풀어서 시간 및 코드 길이를 단축하였다. 투 포인터 알고리즘을 이용하는 풀이는 나의 코드와 비슷하다. 출처: https://donghak-dev.tistory.com/158

### 부분수열의 합 2
```
import itertools

n, s = map(int, input().split())
numList = list(map(int, input().split()))

if n == 1:
    if numList[0] == s:
        print(1)
    else:
        print(0)
else:
    leftN = n // 2
    rightN = n - leftN

    leftSubset = []
    rightSubset = []

    for i in range(leftN + 1):
        tempSubset = itertools.combinations(numList[:leftN], i)
        for elem in tempSubset:
            leftSubset.append(sum(list(elem)))

    for i in range(rightN + 1):
        tempSubset = itertools.combinations(numList[leftN:], i)
        for elem in tempSubset:
            rightSubset.append(sum(list(elem)))

    leftSubset.sort()
    rightSubset.sort(reverse=True)
    # print(leftSubset)
    # print(rightSubset)

    li = 0
    ri = 0
    result = 0
    leftN = len(leftSubset)
    rightN = len(rightSubset)

    # for elem in leftSubset:
    #     if elem == s:
    #         result += 1
    # for elem in rightSubset:
    #     if elem == s:
    #         result += 1

    while li < leftN and ri < rightN:
        if leftSubset[li] + rightSubset[ri] < s:
            li += 1
        elif leftSubset[li] + rightSubset[ri] > s:
            ri += 1
        else:
            li += 1
            ri += 1
            tl = 1
            tr = 1
            while li < leftN and leftSubset[li] == leftSubset[li - 1]:
                li += 1
                tl += 1
            while ri < rightN and rightSubset[ri] == rightSubset[ri - 1]:
                ri += 1
                tr += 1
            result += tl * tr

    if s == 0:
        result -= 1
    print(result)
```
-> 위 문제를 이전에 풀었던 문제들과 비슷하다고 생각하여 투 포인터 알고리즘으로 해결을 시도하였지만 위 문제는 순서가 섞일 수 있고 음수도 포함되어 있어서 투 포인터로 풀기 적합하지 않았다. 고민 후에 이분 탐색으로 푸는 모범 답안 코드를 참고하여 내 방식대로 조금 바꿔서 풀었다. 모범답안에서는 combinations 함수를 쓴 부분을 비트마스킹으로 구해줬는데, 해당 부분의 로직에 대해 추후 공부를 해봐야겠다.
