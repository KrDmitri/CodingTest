## Chapter 15 이진 탐색 문제
### 정렬된 배열에서 특정 수의 개수 구하기
```
def countCertainNumber():
    n, x = map(int, input().split())
    numList = list(map(int, input().split()))

    min = 0
    max = len(numList) - 1
    numIndex = 0
    numCount = 0

    while min <= max:
        mid = (min + max) // 2
        if x == numList[mid]:
            numIndex = mid
            break
        elif x < numList[mid]:
            max = mid - 1
        else:
            min = mid + 1

    if min > max:
        print(-1)
    else:
        biggerIndex = numIndex
        smallerIndex = numIndex
        while numList[biggerIndex] == x:
            numCount += 1
            biggerIndex += 1
        while numList[smallerIndex] == x:
            numCount += 1
            smallerIndex -= 1
        numCount -= 1
        print(numCount)
```
-> 위의 방법은 첫 target을 찾는데는 O(logN) 만큼의 시간이 걸리지만 그 다음에 해당 숫자의 개수를 구하는 데에는 O(N)만큼의 시간이 걸리기 때문에 아래 답안 코드와 같이 target 수의 첫 index와 마지막 index를 이진 탐색으로 찾아서 개수를 구하는 방식으로 풀어야 한다.
### 정렬된 배열에서 특정 수의 개수 구하기 해설 답안 코드
```
def count_by_value(array, x):
    n = len(array)

    a = first(array, x, 0, n - 1)

    if a == None:
        return 0

    b = last(array, x, 0, n - 1)

    return b - a + 1

def first(array, target, start, end):
    if start > end:
        return None
    mid = (start + end) // 2
    if (mid == 0 or target > array[mid - 1]) and array[mid] == target:
        return mid
    elif array[mid] >= target:
        first(array, target, start, mid - 1)
    else:
        return first(array, target, mid + 1, end)

def last(array, target, start, end):
    if start > end:
        return None
    mid = (start + end) // 2
    if (mid == n - 1 or target < array[mid + 1]) and array[mid] == target:
        return mid
    elif array[mid] > target:
        last(array, target, start, mid - 1)
    else:
        last(array, target, mid + 1, end)

n, x =map(int, input().split())
array = list(map(int, input().split()))

count = count_by_value(array, x)

if count == 0:
    print(-1)
else:
    print(count)
```
