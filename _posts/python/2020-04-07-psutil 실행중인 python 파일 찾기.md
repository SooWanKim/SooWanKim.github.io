---
layout: post
title: psutil, 실행중인 process 찾기
categories: [Python]
---

## 목적

파일 이름을 알고 있고 현재 실행되고 있는지 찾기

psutil 모듈에서 process 정보를 얻어 올 수 있음

## 샘플

```python

for q in psutil.process_iter():
    try:
        if q.name() == 'python.exe':
            if len(q.cmdline()) > 1 and '실행하고 있는 python 파일 이름' in q.cmdline()[1]:
                print("존재")

    except psutil.AccessDenied:
        continue
```