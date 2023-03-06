## Chapter 14 정렬 문제
### 국영수
```
def GukYoungSoo():
    n = int(input())
    dataTable = []

    for i in range(n):
        dataTable.append(list(input().split()))
        for j in range(1, 4):
            dataTable[i][j] = int(dataTable[i][j])

    dataTable.sort(key = lambda x : (-x[1], x[2], -x[3], x[0]))

    for i in range(n):
        print(dataTable[i][0])
```
-> lambda함수 사용법, 오름차순은 정부호, 내림차순은 음부호 기억하기
