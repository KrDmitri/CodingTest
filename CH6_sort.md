## Chapter 06 정렬
### 위에서 아래로 문제
```
def upToDown():
    n = int(input())
    array = []
    for i in range(n):
        array.append(int(input()))

    array.sort(reverse=True)

    for i in array:
        print(i, end=' ')
```

### 성적이 낮은 순서로 학생 출력하기 문제
```
def printStudents():
    n = int(input())
    students = []
    for i in range(n):
        student = list(input().split())
        students.append(student)

    students.sort(key=setting)

    for i in range(len(students)):
        print(students[i][0], end=' ')

def setting(arr):
    return arr[1]
```

### 성적이 낮은 순서로 학생 출력하기 문제 해설 답안
```
def printStudents2():
    n = int(input())

    array = []
    for i in range(n):
        inputData = input().split()
        array.append((inputData[0], inputData[1]))

    array = sorted(array, key=lambda student:student[1])

    for student in array:
        print(student[0], end=' ')
```



