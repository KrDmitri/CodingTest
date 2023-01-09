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

