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
