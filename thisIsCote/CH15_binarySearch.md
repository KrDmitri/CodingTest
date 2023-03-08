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

### 정렬된 배열에서 특정 수의 개수 구하기 해설 답안 코드
```
