## Chapter 12 구현 문제
### 럭키 스트레이트
```
def luckyStraight():
    n = input()

    leftNum = 0
    rightNum = 0

    for i in range(len(n)):
        if i < len(n) // 2:
            leftNum += int(n[i])
        else:
            rightNum += int(n[i])

    if leftNum == rightNum:
        print('LUCKY')
    else:
        print('READY')
```

### 문자열 재정렬
```
def sortString():
    S = input()
    charList = []
    numList = []

    for i in range(len(S)):
        if S[i].isalpha() == True:
            charList.append(S[i])
        else:
            numList.append(int(S[i]))

    charList.sort()

    for i in charList:
        print(i, end='')
    print(sum(numList))
```
