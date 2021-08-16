---
title: "[VScode] VScode에서 파이썬으로 알고리즘 공부를 위한 환경세팅"
categories: [Etc]
tags: [vscode, 파이썬, 알고리즘, 환경세팅]
last_modified_at: 2021-08-16
toc: true
comments: true
---

### 1. 기본 실행

1.  새로운 폴더 생성 > 새로운 python 파일 생성 (`test.py`)

```python
 print("hello world")
```

2. 터미널 탭에서 실행
   `$python test.py`

### 2.디버깅 세팅

1. extension 설치

   - 사이드 메뉴 > EXTENSIONS
   - Python

2. 디버그 창 단축키: `cmd + shift + d`

3. 디버그 실행

- 왼쪽 디버그 메뉴창 > 재생버튼 클릭 > Python File 선택

### 3. 입출력 편하게 하기

> 알고리즘 문제를 풀 때 input을 받아야 할 때 (`ex. N = int(input())`)

1. `.vscode/launch.json` 세팅 파일을 수정
   - `"args":["<", "input.txt", ">", "output.txt"]` 값을 추가
     - input.txt에서 INPUT 값을 읽어서 output.txt 에서 OUTPUT을 확인.
     - 파일명 (input.txt, output.txt) 은 변경가능.
   - ```json
       "configurations": [
       {
              "name": "Python: Current File",
              // ...
              "args": ["<", "input.txt", ">", "output.txt"]
          }
     ]
     ```
