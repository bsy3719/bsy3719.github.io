---
title: "[Algorithm] 다이나믹 프로그래밍"
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
description: 다이나믹 프로그래밍 알고리즘의 기초와 기본 문제들에 대해 설명한 글입니다.
---

### 다이나믹 프로그래밍
- 다이나믹 프로그래밍은 메모리를 적절히 사용하여 수행 시간 효율성을 비약적으로 향상시키는 방법
- 이미 계산된 결과는 별도의 메모리 영역에 저장하여 다시 계산하지 않도록 함
- 다이나믹 프로그래밍의 구현은 일반적으로 두가지(탑다운, 바텀업)으로 구성
- 탑다운은 재귀를 통해서, 바텀업 방식은 반복문을 통해서 구현 가능

### 다이나믹 프로그래밍의 조건
- 다이나민 프로그래밍은 문제가 다음의 조건을 만족할 때 사용할 수 있음
1. 최적 부분 구조(Optimal Substructure): 큰 문제를 작은 문제로 나눌 수 있으며 작은 문제의 답을 모아서 큰 문제를 해결할 수 있음
2. 중복되는 부분 문제(Overalpping Subproblem) : 동일한 작은 문제를 반복적으로 해결해야 함

## 피보나치 수열
### 재귀
- 시간복잡도를 O(2^n)을 O(n)으로 줄일 수 있음

```python
def fibo(x):
    if x == 1 or x == 2:
        return 1
    return fibo(x-1) + fibo(x-2)

print(fibo(4))
```

### 탑다운

```python
d = [0] * 100

def fibo(x):
    if x == 1 or x == 2:
        return 1
    if d[x] != 0:
        return d[x]
    
    d[x] = fibo(x-1) + fibo(x-2)
    return d[x]

print(fibo(99))
```

### 바텀업

```python
d = [0] * 100

d[1] = 1
d[2] = 1

n = 99

for i in range(3, n+1):
    d[i] = d[i-1] + d[i-2]
    
print(d[n])
```
### 다이나밍 프로그래밍 문제에 접근하는 방법
- 가장 먼저 그리디, 구현, 완전 탐색 등의 아이디어로 문제를 해결할 수 있는지 검토
- 일단 재귀 함수로 비효율적인 완전 탐색 프로그램을 작성한 뒤에 (탑다운) 작은 문제에서 구한 답이 큰 문제에서 그대로 사용될 수 있으면, 코드를 개선하는 방법을 사용함

### 개미전사
- 인접한 창고를 약탈하지 않으면서 가장 많은 식량을 약탈할 수 있는 양을 구하라

```python
n = 4
array = [1,3,1,5]

d = [0]*100

d[0] = array[0]
d[1] = max(array[0], array[1])

for i in range(2, n):
    d[i] = max(d[i-1], d[i-2]+array[i])
    
print(d[n-1])
```

### 1로 만들기
- 5로 나누어 떨어지면 5로 나눈다
- 3으로 나누어 떨어지면 3으로 나눈다
- 2로 나누어 떨어지면 2로 나눈다
- 1을 뺀다

연산을 최소화하여 정수 x를 1로 만든다

```python
x = 26

d = [0]*30001

for i in range(2, x+1):
    
    d[i] = d[i-1] + 1
    
    if i % 2 ==0:
        d[i] = min(d[i], d[i//2]+1)
    if i % 3 ==0:
        d[i] = min(d[i], d[i//3]+1)
    if i % 5 ==0:
        d[i] = min(d[i], d[i//5]+1)
        
print(d[x])
```

### 효율적인 화폐 구성
- N가지 종류의 화폐가 존재할 때 이 화폐들의 개수를 최소한으로 이용해서 그 가치의 합이 M원이 되도록

```python
n = 3
m = 7

array = [2,3,5]

d = [10001] * (m+1)

d[0] = 0

for i in array:
    for j in range(i, m+1):
        if d[j - i] != 10001:
            d[j] = min(d[j], d[j-i]+1)
            
if d[m] == 10001:
    print(-1)
else:
    print(d[m])
```

### 금광문제
- 열 단위로 이동하면서 채굴자가 얻을 수 있는 금의 최대 크기를 출력
- 채굴자는 오른쪽, 오른쪽 위, 오른쪽 아래로만 이동할 수 있음

### 풀이
- 열단위로 for문을 돌면서 left_up, left, left_down중 최대값을 찾아 해당 위치의 값과 더해주면 됨
- left_up과 left_down을 구할 때 배열에서 벗어날 경우 0으로 처리해줌


### 병사배치
- 병사가 일렬로 서 있을 때 전투력이 내림차순이 되도록 하고 싶음
- 최소한의 병사를 열외시켜 전투력이 내림차순이 되도록 유지할 때 열외시켜야하는 병사 수를 구함

### 풀이
- 가장 긴 증가하는 부분 수열(Longest Incresing Subsequence, LIS)
- 가장 긴 감소하는 부분 수열을 찾는 문제로 치환하여 해결

```python
n = 7
array = [15, 11, 4, 8, 5, 2, 4]
array.reverse()

dp = [1] * n

for curr in range(1, n):
    for prev in range(0, curr):
        if array[prev] < array[curr]:
            dp[curr] = max(dp[curr], dp[prev]+1)

# 열외해야하는 병사 수
print(n - max(dp))
```


