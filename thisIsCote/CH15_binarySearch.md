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
-> 위의 방법은 첫 target을 찾는데는 O(logN) 만큼의 시간이 걸리지만 그 다음에 해당 숫자의 개수를 구하는 데에는 O(N)만큼의 시간이 걸리기 때문에 아래 답안 코드와 같이 target 수의 첫 index와 마지막 index를 이진 탐색으로 찾아서 개수를 구하는 방식으로 풀어야 한다.
### 정렬된 배열에서 특정 수의 개수 구하기 해설 답안 코드
```
def count_by_value(array, x):
    n = len(array)

    a = first(array, x, 0, n - 1)

    if a == None:
        return 0

    b = last(array, x, 0, n - 1)

    return b - a + 1

def first(array, target, start, end):
    if start > end:
        return None
    mid = (start + end) // 2
    if (mid == 0 or target > array[mid - 1]) and array[mid] == target:
        return mid
    elif array[mid] >= target:
        first(array, target, start, mid - 1)
    else:
        return first(array, target, mid + 1, end)

def last(array, target, start, end):
    if start > end:
        return None
    mid = (start + end) // 2
    if (mid == n - 1 or target < array[mid + 1]) and array[mid] == target:
        return mid
    elif array[mid] > target:
        last(array, target, start, mid - 1)
    else:
        last(array, target, mid + 1, end)

n, x =map(int, input().split())
array = list(map(int, input().split()))

count = count_by_value(array, x)

if count == 0:
    print(-1)
else:
    print(count)
```

### 고정점 찾기
```
def findFixedPoint():
    n = int(input())
    numList = list(map(int, input().split()))

    start = 0
    end = len(numList) - 1

    while True:
        mid = (start + end) // 2
        if start > end:
            print(-1)
            break
        if numList[mid] == mid:
            print(mid)
            break
        elif numList[mid] > mid:
            end = mid - 1
        elif numList[mid] < mid:
            start = mid + 1
```

### 공유기 설치 해설 답안 코드
```
def installRouter():
    n, c = map(int, input().split())

    arr = []
    for _ in range(n):
        arr.append(int(input()))
    arr.sort()

    start = 1
    end = arr[-1] - arr[0]
    result = 0

    while (start <= end):
        mid = (start + end) // 2
        value = arr[0]
        count = 1
        for i in range(1, n):
            if arr[i] >= value + mid:
                value = arr[i]
                count += 1
        if count >= c:
            start = mid + 1
            result = mid
        else:
            end = mid - 1

    print(result)
```
-> 조금 어려운 문제긴 한데, 생각하는 것을 조금 달리해서 gap, 즉 답의 범위를 정해 놓고 이진 탐색으로 가능한 답의 최대값을 찾는 방법으로 문제 해결 가능(해당 유형의 문제는 이진 탐색으로 풀 수 있는 문제로, 여러 문제를 풀어보면 실력을 늘릴 수 있을듯)

### 가사 검색
```
def solution(words, queries):
    def differentiateHeadTail(word):
        if word[0] == '?':
            return 'tail'
        else:
            return 'head'

    def findFirstIndex(numList, num, start, end):
        mid = (start + end) // 2
        if start > end:
            return None
        if numList[mid] == num and mid == 0:
            return mid
        elif numList[mid] == num and numList[mid - 1] < num:
            return mid
        elif numList[mid] >= num:
            end = mid - 1
            return findFirstIndex(numList, num, start, end)
        elif numList[mid] < num:
            start = mid + 1
            return findFirstIndex(numList, num, start, end)

    def findLastIndex(numList, num, start, end):
        mid = (start + end) // 2
        if start > end:
            return None
        if numList[mid] == num and (mid == len(numList) - 1):
            return mid
        elif numList[mid] == num and numList[mid + 1] > num:
            return mid
        elif numList[mid] <= num:
            start = mid + 1
            return findLastIndex(numList, num, start, end)
        elif numList[mid] > num:
            end = mid - 1
            return findLastIndex(numList, num, start, end)

    def searchFirstIndexForWord(wordsList, word, start, end):
        mid = (start + end) // 2
        if start > end or mid >= len(wordsList):
            return None
        if wordsList[mid][0] >= word and mid == 0:
            return mid
        elif wordsList[mid][0] >= word and wordsList[mid - 1][0] < word:
            return mid
        elif wordsList[mid][0] >= word:
            end = mid - 1
            return searchFirstIndexForWord(wordsList, word, start, end)
        elif wordsList[mid][0] < word:
            start = mid + 1
            return searchFirstIndexForWord(wordsList, word, start, end)

    def searchLastIndexForWord(wordsList, word, start, end):
        mid = (start + end) // 2
        if start > end:
            return None
        if wordsList[mid][0] <= word and mid == len(wordsList) - 1:
            return mid
        elif wordsList[mid][0] <= word and wordsList[mid + 1][0] > word:
            return mid
        elif wordsList[mid][0] <= word:
            start = mid + 1
            return searchLastIndexForWord(wordsList, word, start, end)
        elif wordsList[mid][0] > word:
            end = mid - 1
            return searchLastIndexForWord(wordsList, word, start, end)


    for i in range(len(words)):
        words[i] = [words[i], len(words[i])]
    words.sort(key=lambda x:(x[1], x[0]))

    reverseWords = []
    for i in range(len(words)):
        temp = words[i][0]
        temp = "".join(reversed(temp))
        reverseWords.append([temp, len(temp)])
    reverseWords.sort(key=lambda x:(x[1], x[0]))

    wordsLengthList = []
    for i in range(len(words)):
        wordsLengthList.append(words[i][1])

    ### 수정 가능
    for i in range(len(queries)):
        queries[i] = [queries[i], differentiateHeadTail(queries[i])]


    answer = []
    for i in range(len(queries)):
        query = queries[i]
        if query[1] == 'head':
            firstIndex = findFirstIndex(wordsLengthList, len(query[0]), 0, len(wordsLengthList))
            lastIndex = findLastIndex(wordsLengthList, len(query[0]), 0, len(wordsLengthList))
            if firstIndex == None or lastIndex == None:
                answer.append(0)
                print(0)
                continue
            newWordsList = words[firstIndex:lastIndex + 1]

            biggerWord = list(query[0])
            for j in range(len(biggerWord)):
                if biggerWord[j] != '?' and biggerWord[j + 1] == '?':
                    biggerWord[j] = chr(ord(biggerWord[j]) + 1)
            biggerWord = "".join(biggerWord)

            smallerWord = list(query[0])
            for j in range(len(smallerWord)):
                if smallerWord[j] == '?':
                    smallerWord[j] = 'a'
            smallerWord = "".join(smallerWord)


            fIndex = searchFirstIndexForWord(newWordsList, smallerWord, 0, len(newWordsList))
            lIndex = searchLastIndexForWord(newWordsList, biggerWord, 0, len(newWordsList))
            if fIndex == None or lIndex == None:
                answer.append(0)
                continue

            count = lIndex - fIndex + 1
            answer.append(count)

        else:
            query[0] = ''.join(reversed(query[0]))
            firstIndex = findFirstIndex(wordsLengthList, len(query[0]), 0, len(wordsLengthList))
            lastIndex = findLastIndex(wordsLengthList, len(query[0]), 0, len(wordsLengthList))
            if firstIndex == None or lastIndex == None:
                answer.append(0)
                print(0)
                continue
            newWordsList = reverseWords[firstIndex:lastIndex + 1]

            biggerWord = list(query[0])
            for j in range(len(biggerWord)):
                if biggerWord[j] != '?' and biggerWord[j + 1] == '?':
                    biggerWord[j] = chr(ord(biggerWord[j]) + 1)
            biggerWord = "".join(biggerWord)

            smallerWord = list(query[0])
            for j in range(len(smallerWord)):
                if smallerWord[j] == '?':
                    smallerWord[j] = 'a'
            smallerWord = "".join(smallerWord)

            fIndex = searchFirstIndexForWord(newWordsList, smallerWord, 0, len(newWordsList))
            lIndex = searchLastIndexForWord(newWordsList, biggerWord, 0, len(newWordsList))
            if fIndex == None or lIndex == None:
                answer.append(0)
                continue

            count = lIndex - fIndex + 1
            answer.append(count)

    print(answer)
    return answer
```
-> 답은 맞았는데 시간 초과로 정답률 43% 나옴🥲, 기본 아이디어는 이진 탐색 활용해서 먼저 단어의 길이 체크, 그 다음엔 크기 비교
