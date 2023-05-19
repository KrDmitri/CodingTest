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
