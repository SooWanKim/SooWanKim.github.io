---
layout: post
title: C# Finding All HTML Tag in string
categories: [C#]
---

## Regex를 사용해서 string안에 HTMLTag 다찾기

```c#
string pattern2 = @"(<[^>]*>)";
var allTagList = new List<string>();

var patternMatch = Regex.Matches(inputHTML, pattern2);
for (int i = 0; i < patternMatch.Count; i++)
{
    allTagList.Add(patternMatch[i].ToString());
}
```
