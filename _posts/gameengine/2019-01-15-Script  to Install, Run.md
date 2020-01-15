---
layout: post
title: Script to Install, Run
categories: [GameEngine]
---


```sh
adb install -r my.apk && adb shell monkey -p <packagename> -c android.intent.category.LAUNCHER 1

```

-r 은 재설치

