---
layout: post
title: Using Json in Unity
categories: [C#, Unity]
---

## 기능

Type T에 따라 Class를 json으로 변환

## 코드

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

// 전달할 개별 LogClass가 상속받아야 할 인터페이스
public interface ISendLog
{
    void SendLog();
}

public class LogManager
{
        Dictionary<Type, ISendLog> m_Logs = new Dictionary<Type, ISendLog>();

    public void SendLog<T>() where T : ISendLog
    {
        ISendLog sendLog = GetLog<T>();
        sendLog?.SendLog();
    }

    async public void SendLog_PerTime<T>(int seconds) where T : ISendLog
    {
        Type t = typeof(T);
        ISendLog sendLog = GetLog<T>();

        if (sendLog == null)
            return;

        while (true)
        {
            await Task.Delay(seconds * 1000);

            sendLog?.SendLog();

            await Task.Yield();
        }
    }

    private ISendLog GetLog<T>() where T : ISendLog
    {
        Type t = typeof(T);
        ISendLog sendLog = null;

        if (m_Logs.ContainsKey(t) == false)
        {
            sendLog = Activator.CreateInstance(t) as ISendLog;
            m_Logs.Add(t, sendLog);
        }
        else
        {
            sendLog = m_Logs[t];
        }

        return sendLog;
    }
}

// 전달할 객체
[Serializable]
public class UserItemLog : ISendLog
{
    public string itemtype;
    public string itemid;
    public string itemgrade;
    public long itemlevel;

    public void SendLog()
    {
        itemtype = "some itmetype"
        itemid = "some itemid"
        itemgrade = "some itemgrade"
        itemlevel =  "some itemlevel"

        string jsonEncoded = JsonConvert.SerializeObject(this);
        SomePlatform.Log(jsonEncoded);
    }
}

```

***

사용시에 Type 전달, 보내는건 각 Class에서 개별 구현

```c#
// singleton구현하면 다르게 사용
var logManager = new LogManager();
logManager.SendLog<UserItemLog>();

```
***

