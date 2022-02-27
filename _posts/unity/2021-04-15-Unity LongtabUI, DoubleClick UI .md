---
layout: post
title: Unity LongTabUI, DoubleClickUI
categories: [C#, Unity]
---

LongTabUI, DoubleClickUI 구현

## 샘플코드

***
LongTabUI

```c#
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Events;
using UnityEngine.EventSystems;

public class LongTabUI : MonoBehaviour, IPointerDownHandler, IPointerUpHandler
{
    public UnityEvent longTabEvent;

    // longTab 유지 시간
    public float longTabTime = 0.3f;

    // longTab 시작 시간
    float m_longTabStartTime = 0.0f;

    public void OnPointerDown(PointerEventData eventData)
    {
        m_longTabStartTime = Time.realtimeSinceStartup;
    }

    public void OnPointerUp(PointerEventData eventData)
    {
        if (longTabEvent == null)
            return;

        if ((Time.realtimeSinceStartup - m_longTabStartTime) > longTabTime)
        {
            longTabEvent?.Invoke();
        }
    }
}

```

***
DoubleClickUI

```c#
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Events;
using UnityEngine.EventSystems;

public class DoubleClickUI : MonoBehaviour, IPointerClickHandler
{
    // 호출 이벤트
    public  UnityEvent doubleClickEvent;

    // doubleClick 체크 시간(sec)
    public float checkTime = 0.2f;

    float m_preClickTime = 0.0f;
    int m_clickCount = 0;

    public void OnPointerClick(PointerEventData eventData)
    {
        float diffTime = eventData.clickTime - m_preClickTime;
        if (diffTime < checkTime)
            ++m_clickCount;
        else
            m_clickCount = 1;

        m_preClickTime = eventData.clickTime;

        if (m_clickCount == 2)
        {
            doubleClickEvent?.Invoke();
            m_clickCount = 0;
        }
    }
}

```
