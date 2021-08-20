---
title: "[Algorithm] 알고리즘 문제 입력받기"
categories: [Algorithm]
tags: [algorithm, input]
last_modified_at: 2021-08-20
permalink: /algorithm/:year/:month/:day/:title/
comments: true
---

### 1. 파이썬에서 입력받기

#### A. 기본입력

```python
# string
S = input()

# string 여러개
S1, S2 = map(str, input().split())

# string list
lst = list(map(str, input().split()))

# int
N = int(input())

# int 여러개
N1, N2 = map(int, input().split())

# int list
lst = list(map(int, input().split()))
```

#### B. 더 빠르게 입력을 받아야할 때

> 속도가 중요한 문제에서 `input()` 대신에 `sys.stdin.readline()` 을 사용한다.

```python
import sys

S1, S2 = map(str, sys.stdin.readline().split())
```

### 2. Kotlin에서 입력받기

#### A. 기본입력

> `` Scanner(System.`in`) `` 를 사용한다.

```kotlin
java.util.*

fun main(args: Array<String>) = with(Scanner(System.`in`)) {
    // string
    val S = nextLine()

    // string list
    val lst = readLine().split(' ')

    // int
    val N = nextInt()

    // int 여러개
    val N1 = nextInt()
    val N2 = nextInt()

    // int list
    val lst = readLine().split(' ').map { it.toInt() }
}
```
