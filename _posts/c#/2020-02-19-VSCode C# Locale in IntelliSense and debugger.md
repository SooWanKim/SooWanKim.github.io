---
layout: post
title: VSCode C# Locale in IntelliSense and debugger
categories: [C#]
---


## 문제

C# Intellisense Local 안바뀐다.

Display Languange Mode를 영어로 바꿨는데도, c# Error가 한글로 나옴

![](/assets/images/2020-02-19-VSCode%20C#%20Locale%20in%20IntelliSense%20and%20debugger/2020-02-19-11-34-07.png)


## 해결책

Omnisharp의 local이 안바뀌는 문제여서 찾아봄

[https://github.com/OmniSharp/omnisharp-vscode/issues/2513](https://github.com/OmniSharp/omnisharp-vscode/issues/2513)


>Navigate to the directory eg C:\Users\rchande\.vscode-insiders\extensions\ms-vscode.csharp-1.16.1\.omnisharp\1.32.5, and enter the subfolder that corresponds to your system language. the folder would be "es" because the system language is Spanish. Remove all the DLLs whose name starts with Microsoft.CodeAnalysis.

위의 페이지 설명처럼 system language 경로인 ko로 들어가서 Microsoft.CodeAnalysis가 붙은건 다 지우니까 해결됨