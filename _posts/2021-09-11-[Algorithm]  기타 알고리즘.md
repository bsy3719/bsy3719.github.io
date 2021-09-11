---
title: "[Algorithm] 기타 알고리즘"
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
description: 기타 알고리즘의 기초와 기본 문제들에 대해 설명한 글입니다.
---

### 소수(Prime Number)
- 소수란 1보다 큰 자연수 중에서 1과 자기 자신을 제외한 자연수로는 나누어 떨어지지 않는 자연수
- 코딩 테스트에서는 어떠한 자연수가 소수인지 판별해야하는 문제가 자주 출제

```python
def is_prime_number(x):
    if x == 1:
        return True
    
    for i in range(2,x):
        if x%i==0:
            return False
        
    return True

print(is_prime_number(4))
print(is_prime_number(7))
```

### 약수의 성질
- 모든 약수가 가운데 약수를 기준으로 곱셉연산에 대칭을 이룸
- 따라서 우리는 특정한 약수를 찾을 때 가운데 약수(제곱근)까지만 확인하면 됨

```python
import math

def is_prime_number(x):
    if x == 1:
        return True
    
    for i in range(2,int(math.sqrt(x)+1)):
        if x%i==0:
            return False
        
    return True

print(is_prime_number(4))
print(is_prime_number(7))
```

### 에라토스테네스의 체 알고리즘
- 다수의 자연수에 대하여 소수 여부를 판별할 때 사용
- 구체적인 동작 과정
    1. 2부터 N까지의 모든 자연수를 나열한다
    2. 남은 수 중에서 아직 처리하지 않은 가장 작은 수 i를 찾는다
    3. 남은 수 중에서 i의 배수를 모두 제거한다.(i는 제거히지 않는다)
    4. 더 이상 반복할 수 없을 때까지 2번과 3번의 과정을 반복한다

```python
import math

n = 1000

array = [True for i in range(n+1)]

for i in range(2, int(math.sqrt(n))+1):
    if array[i] == True:
        j=2
        while i * j <= n:
            array[i*j] = False
            j+=1
            
for i in range(2, n+1):
    if array[i]:
        print(i, end=" ")
```

### 투 포인터(Two Pointers)

- 투 포인터 알고리즘은 리스트에 순차적으로 접근해야 할 때 두 개의 점의 위치를 기록하면서 처리하는 알고리즘을 의미
- 리스트에 담긴 데이터에 순차적으로 접근해야 할 때는 시작점과 끝점 2개의 점으로 접근할 데이터의 범위를 표현할 수 있음

### 특정한 합을 가지는 부분 연속 수열 찾기
1. 시작점(start)과 끝점(end)이 첫 번째 원소의 인덱스(0)을 가리키도록 한다.
2. 현재 부분 합이 M과 같다면, 카운트한다.
3. 현재 부분 합이 M보다 작다면, end를 1 증가시킨다.
4. 현재 부분 합이 M보다 크거나 같다면, start를 1 증가시킨다.
5. 모든 경우를 확인할 때까지 2번부터 4번까지의 과정을 반복한다.

```python
n, m = 5,5
data = [1,2,3,2,5]

count = 0
interval_sum = 0
end = 0

# start를 차례대로 증가시키며 반복
for start in range(n):
    # end를 가능한 만큼 이동시키기
    while interval_sum < m and end < n:
        interval_sum += data[end]
        end+=1
    # 부분합이 m일 때 카운트 증가
    if interval_sum == m:
        count+=1
    interval_sum -= data[start]
    
print(count)
```

### 구간 합(Interval Sum)
- 구간 합 문제 : 연속적으로 나열된 N개의 수가 있을 때 특정 구간의 모든 수를 합한 값을 계산하는 문제

### 구간 합 빠르게 계산하기 : 문제설명
- N개의 정수로 구성된 수열이 있음
- M개의 쿼리 정보가 주어짐
    - 각 쿼리는 Left와 Right으로 구성됨
    - 각 쿼리에 대하여 [Left, Right]구간에 데이터들의 합을 출력해야함
- 수행 시간 제한은 O(N+M)임

### 구간 합 빠르게 계산하기 : 문제 해결 아이디어
- 접두사 합(Prefix Sum): 배열의 맨 앞부터 특정 위치까지의 합을 미리 구해 놓은것
- 접두사 합을 활용한 알고리즘은 다음과 같음
    - N개의 수 위치 각각에 대하여 접두사 합을 계산하여 P에 저장
    - 매 M개의 쿼리 정보를 확인할 때 구간 합은 P[Right]-P[Left-1]임

```python
n = 5
data = [10,20,30,40,50]

sum_value = 0
prefix_sum = [0]

for i in data:
    sum_value += i
    prefix_sum.append(sum_value)
    
left = 3
right = 4
print(prefix_sum[right] - prefix_sum[left-1])
```