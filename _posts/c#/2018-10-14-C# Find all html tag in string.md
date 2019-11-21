---
layout: post
title: C# Finding All HTML Tag in string
categories: [C#]
---

## Regex를 사용해서 string안에 HTMLTag 다찾기

```c#
string pattern2 = @"(<[^>]*>)";
var allTagList = new List<string>();

var matches2 = Regex.Matches(inputHTML, pattern2);
for (int i = 0; i < matches2.Count; i++)
{
    allTagList.Add(matches2[i].ToString());
}
```
