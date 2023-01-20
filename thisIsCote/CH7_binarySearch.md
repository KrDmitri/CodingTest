## Chapter 07 이진 탐색
### 부품 찾기 문제
```
def binarySearch(array, target, start, end):
    if start > end:
        print("no", end=' ')
        return
    mid = (start + end) // 2
    if array[mid] == target:
        print("yes", end=' ')
        return
    elif array[mid] > target:
        return binarySearch(array, target, start, mid - 1)
    else:
        return binarySearch(array, target, mid + 1, end)


def findItem():
    n = int(input())
    nList = list(map(int, input().split()))
    m = int(input())
    mList = list(map(int, input().split()))

    for i in range(len(nList)):
        minIndex = i
        for j in range(i+1, len(nList)):
            if nList[minIndex] > nList[j]:
                minIndex = j
        nList[i], nList[minIndex] = nList[minIndex], nList[i]

    for i in mList:
        binarySearch(nList, i, 0, len(nList))
```

### 부품 찾기 문제 해설 답안 1
```
def findItem2():
    def binary_search(array, target, start, end):
        while start <= end:
            mid = (start + end) // 2
            if array[mid] == target:
                return mid
            elif array[mid] > target:
                end = mid - 1
            else:
                start = mid + 1
        return None
    
    n = int(input())
    array = list(map(int, input().split()))
    array.sort()
    
    m = int(input())
    x = list(map(int, input().split()))
    
    for i in x:
        result = binary_search(array, i, 0, n - 1)
        if result != None:
            print('yes', end=' ')
        else:
            print('no', end=' ')
```

### 부품 찾기 문제 해설 답안 2(계수 정렬 활용)
```
def findItem3():
    n = int(input())
    array = [0] * 1000001
    
    for i in input().split():
        array[int(i)] = 1
    
    m = int(input())
    x = list(map(int, input().split()))
    
    for i in x:
        if array[i] == 1:
            print('yes', end=' ')
        else:
            print('no', end=' ')
```

### 부품 찾기 문제 해설 답안3(집합 자료형 이용)
```
def findItem4():
    n = int(input())
    array = set(map(int, input().split()))
    
    m = int(input())
    x = list(map(int, input().split()))
    
    for i in x:
        if i in array:
            print('yes', end=' ')
        else:
            print('no', end=' ')
```

### 떡볶이 떡 만들기 문제
```
def cutTteok():
    n, m = map(int, input().split())
    tteok = list(map(int, input().split()))
    tteok.sort()

    afterCut = [0] * n

    start = 0
    end = tteok[-1]

    while True:
        mid = (start + end) // 2

        for i in range(n):
            temp = tteok[i] - mid
            if temp < 0:
                afterCut[i] = 0
            else:
                afterCut[i] = temp

        if sum(afterCut) > m:
            start = mid + 1
        elif sum(afterCut) < m:
            end = mid - 1
        elif sum(afterCut) == m:
            print(mid)
            break
```
-> 이 문제는 처음 봤을 때 자른 후 떡의 길이를 조합해서 원하는 길이를 만들어야 하는 줄 알았는데, 문제를 다시보니 원하는 길이를 넘기만 하면 됐음. 그래서 떡의 길이를 가장 큰 길이부터 잘라가면서 원하는 길이를 넘는지 체크하는 형태로 풀 수 있는데 문제에서 높이가 10억보다 작거나 같은 양의 정수로 범위가 아주 크기 때문에 이진 탐색 기법을 활용하여 원하는 길이를 더 빠르게 구할 수 있다.
### 이진 탐색 활용 포인트
1. 입력 조건의 범위가 매우 방대 하다
2. 범위를 좁혀갈 수 있다

그런데 위의 내 코드는 딱 원하는 길이만큼 떨어져 나올때만 정상적으로 작동을 해서 아래의 해설 코드처럼 조건을 만족하는 값을 저장해나가면서 풀어야함
### 떡볶이 떡 만들기 해설 답안
```
def cutTteok2():
    n, m = list(map(int, input().split()))
    array = list(map(int, input().split()))
    
    start = 0
    end = max(array)
    
    result = 0
    while(start <= end):
        total = 0
        mid = (start + end) // 2
        for x in array:
            if x > mid:
                total += x - mid
        if total < m:
            end = mid - 1
        else:
            result = mid
            start = mid + 1
```

