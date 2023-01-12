## Chapter 08 다이나믹 프로그래밍
### 1로 만들기
```
# 2, 3, 5로 나누거나 1을 빼가면서 1을 만드는데 연산 몇 번 걸리는지
def makeOne():
    d = [0] * 100
    d[1] = 0

    # 5, 3, 2로 나눈거랑 1뺀거중 가장 작은거를 d에 저장
    for i in range(2, 100):
        div5 = 100
        div3 = 100
        div2 = 100
        min1 = 100
        if i // 5 > 0:
            remainder = i % 5
            if remainder > 0:
                div5 = remainder + d[i - remainder]
            else:
                div5 = 1 + d[i // 5]
        if i // 3 > 0:
            remainder = i % 3
            if remainder > 0:
                div3 = remainder + d[i - remainder]
            else:
                div3 = 1 + d[i // 3]
        if i // 2 > 0:
            remainder = i % 2
            if remainder > 0:
                div2 = remainder + d[i - remainder]
            else:
                div2 = 1 + d[i // 2]
        if i - 1 > 0:
            min1 = 1 + d[i - 1]

        d[i] = min(div2, div3, div5, min1)
```
