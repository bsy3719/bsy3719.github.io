---
title: "[Algorithm] 그리디 알고리즘 "
date: 2021-09-10 21:30:00 -0000

tagline: "by baeksy"
header:
  overlay_image: /assets/images/splash/programer.jpeg
  overlay_filter: 0.5
  #show_overlay_excerpt : false

toc: true
toc_label: "Algorithm"
toc_sticky: true

categories: 
  - Algorithm
tags: 
  - Python
description: 그리디 알고리즘의 기초와 기본 문제들에 대해 설명한 글입니다.
---

### 그리디 알고리즘
- 그리디 알고리즘은 현재 상황에서 지금 당장 좋은 것만 고른느 방법을 의미함
- 일반적인 그리디 알고리즘은 문제를 풀기 위한 최소한의 아이디어를 떠올릴 수 있는 능력을 요구함
- 단순히 가장 좋아 보이는 것을 반복적으로 선택해도 최적의 해를 구할 수 있는지를 검토함


### 거스름 돈
- 500원, 100원, 50원, 10원 동전이 존재할 때 거슬러 줘야하는 최소한의 동전의 개수를 구하시오

```python
n = 1260

coins = [500, 100, 50, 10]

result = 0

for coin in coins:
    result += n//coin
    n = n%coin
    
print(result)
```

### 1이 될 때까지
- 어떠한 수 N이 1이 될 때까지 다음의 두 과정 중 하나를 반복적으로 선택하여 수행하려고 함
    1. N에서 1을 뺀다
    2. N을 K로 나눈다.
- N과 K가 주어질 떄 N이 1이 될때까지 1번 혹은 2번의 과정을 수행해야하는 최소 횟수를 구하는 프로그램을 작성하시오


```python
n, k = 27, 3

result = 0

while True:
    target = (n//k) * k
    result += (n - target)
    n= target
    
    if n<k:
        break
        
    n //= k
    result+=1
    
    print(n, result)
         
result += (n-1)
print(n, result)
```

### 곱하기 혹은 더하기
- 각 자리가 숫자로만 이루어진 문자열 S가 주어졌을 때, 왼쪽부터 오른쪽으로 하나씩 모든 숫자를 확인하며 숫자 사이에 x 혹은 + 연산자를 넣어 결과적으로 가장 큰 수를 구하는 프로그램을 작성하세요.

```python
data = "02984"

result = int(data[0])

for i in range(1, len(data)):
    num = int(data[i])
    
    if num <= 1 or result <=1:
        result += num
    else:
        result *= num
        
print(result)
```

### 모험가 길드
- 한 마을에 모험가가 N명 있음, 모함가 길드에서는 N명의 모험가를 대상으로 '공포도'를 측정했는데, '공포도'도가 높은 모험가는 쉽게 공포를 느껴 위험 상황에서 제대로 대처할 능력이 떨어짐
- 공포도가 X인 모험가는 반드시 X명 이상으로 구성한 모험가 그룹에 참여해야 여행을 떠날 수 있도록 규정
- 여행을 더날 수 있는 그룹 수의 최댓값을 구하시오

```python
n = 5
data = [2, 3, 1, 2 ,2]

result = 0
count = 0

for i in data:
    count+=1
    
    if count >= i:
        result+=1
        count=0

print(result)
```
