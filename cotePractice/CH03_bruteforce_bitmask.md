# 브루트포스 - 비트마스크
### 부분수열의 합(처음 내 풀이)
```
import itertools

n = int(input())
s = list(map(int, input().split()))
s.sort()

for i in range(1, 100001):
    tempNumList = []
    tempSumList = []
    for elem in s:
        if elem <= i:
            tempNumList.append(elem)
    for j in range(1, len(tempNumList) + 1):
        candidates = list(itertools.combinations(tempNumList, j))
        for candidate in candidates:
            tempSumList.append(sum(candidate))
    if i not in tempSumList:
        print(i)
        break
```

### 부분수열의 합(답안 참고 풀이)
```
n = int(input())
s = list(map(int, input().split()))
s.sort()

answer = 0
flag = 0

for elem in s:
    if elem - answer > 1:
        print(answer + 1)
        flag = 1
        break
    answer += elem

if flag == 0:
    print(answer + 1)
```
-> 올해 초에도 풀었던 문제인데, 당시 해당 개념에 대해 완벽한 이해를 하지 못해서 본 문제를 푸는데 시간이 좀 걸렸다
