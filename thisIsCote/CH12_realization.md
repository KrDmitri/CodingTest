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

### 문자열 압축
```
def compressString():
    s = input()
    stringLength = len(s)
    newString = ''

    # i는 몇 개씩 모으는지 나타냄
    for i in range(1, len(s) // 2 + 1):
        pos = 0
        cnt = 1
        while pos < len(s):
            temp = s[pos:pos+i]
            if temp == s[pos + i:pos + 2 * i]:
                pos += i
                cnt += 1
            else:
                if cnt == 1:
                    newString += temp[0]
                    pos += 1
                else:
                    newString += str(cnt) + temp
                    pos += i
                    if pos + i == len(s):
                        break
                cnt = 1

        stringLength = min(stringLength, len(newString))
        newString = ''

    print(stringLength)
```
-> 교재 입출력 예시 5 문제 있음(자르는 방식에 오해가 있었음)

### 문자열 압축 수정 코드
```
def compressString2():
    s = input()
    stringLength = len(s)
    newString = ''

    # i는 몇 개씩 모으는지 나타냄
    for i in range(1, len(s) // 2 + 1):
        pos = 0
        cnt = 1
        while pos < len(s):
            temp = s[pos:pos+i]
            if temp == s[pos + i:pos + 2 * i]:
                pos += i
                cnt += 1
            else:
                if cnt == 1:
                    newString += temp
                    pos += i
                else:
                    newString += str(cnt) + temp
                    pos += i
                    if pos + i == len(s):
                        break
                cnt = 1

        stringLength = min(stringLength, len(newString))
        newString = ''

    print(stringLength)
```
https://school.programmers.co.kr/learn/courses/30/lessons/60057
정답 판정

### 자물쇠와 열쇠(문제해결 실패)⚠️
```
import math
def unlock():
    ##### 리스트 입력 #####
    keyString = input()
    lockString = input()

    keyInt = []
    lockInt = []

    for i in keyString:
        if i.isdigit():
            keyInt.append(int(i))

    for i in lockString:
        if i.isdigit():
            lockInt.append(int(i))

    keyDimension = int(math.sqrt(len(keyInt)))
    lockDimension = int(math.sqrt(len(lockInt)))

    key = [[0] * keyDimension for _ in range(keyDimension)]
    lock = [[0] * lockDimension for _ in range(lockDimension)]

    for i in range(len(keyInt)):
        xPos = i // keyDimension
        yPos = i % keyDimension
        key[xPos][yPos] = keyInt[i]

    for i in range(len(lockInt)):
        xPos = i // lockDimension
        yPos = i % lockDimension
        lock[xPos][yPos] = lockInt[i]
    ##### 여기까지 입력받은 문자열 리스트화 완료 #####

    keyTooth = keyInt.count(1)
    lockHole = lockInt.count(0)

    # 가장 기본 조건 확인(키의 이빨 개수가 충분한지)
    if lockHole > keyTooth:
        print('false')
    else:
        lockLength = int(math.sqrt(lockHole))

        for length in range(lockLength, len(lock)):
            tempList = [[0] * length for _ in range(length)]
            xPos = 0
            for x in range(0, len(lock) - length):
                yPos = 0
                for y in range(0, len(lock) - length):
                    tempList[xPos][yPos] = lock[x][y]
                    yPos += 1
                xPos += 1
```
-> 문제 
ㅎㅐ결 
