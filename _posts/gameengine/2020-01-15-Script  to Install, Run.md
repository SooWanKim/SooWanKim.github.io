---
layout: post
title: Script to Install, Run
categories: [GameEngine]
---

## 목적
Unity로 빌드된 Apk를 연결된 device에서 자동 설치, 자동 실행

```sh
adb install -r my.apk && adb shell monkey -p <packagename> -c android.intent.category.LAUNCHER 1
```

-r 은 재설치

