# 브루트포스 - 순열
### 부등호
```
import copy
k = int(input())
signList = list(input().split())
answerList = []
numList = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

def checkRight(sign, x, y):
    if sign == '>':
        if x > y:
            return True
        else:
            return False
    else:
        if x < y:
            return True
        else:
            return False

def stringListToInt(tempList):
    bstr = ''
    for elem in tempList:
        bstr += str(elem)
    return int(bstr)


def insertNumber(numLists, tempList, index):
    if index == 0:
        for num in numLists:
            newNumList = copy.deepcopy(numLists)
            newNumList.remove(num)
            tempList.append(num)
            insertNumber(newNumList, tempList, index + 1)
            tempList.remove(num)
    else:
        sign = signList[index - 1]
        for num in numLists:
            if not checkRight(sign, tempList[-1], num):
                continue
            else:
                newNumList = copy.deepcopy(numLists)
                newNumList.remove(num)
                tempList.append(num)
                if index == k:       # answer에 추가하는 코드
                    answerList.append(stringListToInt(tempList))
                else:
                    insertNumber(newNumList, tempList, index + 1)
                tempList.remove(num)

insertNumber(numList, [], 0)
maxNum = max(answerList)
minNum = min(answerList)

if len(str(maxNum)) > len(str(minNum)):
    print(maxNum)
    print('0', end='')
    print(minNum)
else:
    print(maxNum)
    print(minNum)
```
-> 이제 실버 1 문제는 easy
