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

### 가르침
```
import itertools
import copy

n, k = map(int, input().split())
words = []
maxNum = 0

for _ in range(n):
    words.append(input())

if k < 5:
    print(0)
else:
    teachingAlpha = ['a', 'n', 't', 'i', 'c']
    needToTeach = []
    k -= 5
    tempWords = copy.deepcopy(words)
    for i in range(len(tempWords)):
        tempWords[i] = tempWords[i][4:len(tempWords[i]) - 4]

    for word in tempWords:
        for i in range(len(word)):
            if word[i] not in teachingAlpha and word[i] not in needToTeach:
                needToTeach.append(word[i])

    if k > len(needToTeach):
        candidates = list(itertools.combinations(needToTeach, len(needToTeach)))
    else:
        candidates = list(itertools.combinations(needToTeach, k))

    for candidate in candidates:
        cnt = 0
        totalNum = 0
        for word in tempWords:
            for i in range(len(word)):
                if word[i] not in teachingAlpha and word[i] not in candidate:
                    cnt += 1
                    break
        totalNum = n - cnt
        maxNum = max(maxNum, totalNum)

    print(maxNum)
```
