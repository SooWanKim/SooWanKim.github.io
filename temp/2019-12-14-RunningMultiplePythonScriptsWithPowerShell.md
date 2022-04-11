---
layout: post
title: Running multiple python scripts sequentially with powershell(set width, set height)
categories: [ETC]
---

## 사용 목적
여러개의 .py 파일을 한번에 powershell을 통해 실행시키기 위함

## 기능
지정된 사이즈로(width, height)로 .py 파일들을 각각의 powershell로 실행

## Powershell Script
```
start powershell  '"[console]::windowwidth=100; [console]::windowheight=6; [console]::bufferwidth=[console]::windowwidth" ; "python .\MyFolder1\MyPython1.py"'
Start-Sleep -s 1.5
start powershell '"[console]::windowwidth=100; [console]::windowheight=6; [console]::bufferwidth=[console]::windowwidth" ; "python .\MyFolder2\MyPython2.py"'
Start-Sleep -s 1.5
start powershell '"[console]::windowwidth=100; [console]::windowheight=6; [console]::bufferwidth=[console]::windowwidth" ; "python .\MyFolder3\MyPython3.py"'
Start-Sleep -s 1.5
start powershell '"[console]::windowwidth=100; [console]::windowheight=6; [console]::bufferwidth=[console]::windowwidth" ; "python .\MyFolder4\MyPython4.py"'

```
