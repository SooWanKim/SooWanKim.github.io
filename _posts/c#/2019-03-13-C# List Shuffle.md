---
layout: post
title: C# List Shuffle
categories: [C#]
---

```cs
public static void Shuffle<T>(System.Random random, List<T> list)
{
    int n = list.Count;
    for (int i = 0; i < n; i++)
    {
        int r = i + random.Next(n - i);
        T t = list[r];
        list[r] = list[i];
        list[i] = t;
    }
}
```
