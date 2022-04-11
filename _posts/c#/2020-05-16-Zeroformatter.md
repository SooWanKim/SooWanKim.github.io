---
layout: post
title: Zeroformatter in Unity
categories: [C#,Unity]
---

## 작업

UnityEngine을 사용하면서 GameData를 binary로 저장, 읽는 부분을 개선하고 싶음

GameData의 내용을 읽어 들일때 값 하나 하나 파싱 하면서 읽는부분을 간편하게 변경!

변경전 구조:

**GameData -> Binary -> GameLoad -> Binary Parsing**

변경하려는 구조:

**GameData를 DataClass에 값을 입력 -> DataClass Serialize -> GameLoad -> DataClass DeSerialize**

DataClass, Parser는 GameData에 따라서  [T4](https://soowankim.github.io/2019-02-20/TextTemplateTransformationToolkit/)로 Runtime에 자동 생성하게 한다.

GameData를 읽고 .tt(T4확장자)파일에 값들을 넘겨주고 DataClass 생성 및 Parser를 생성한다.

.tt(T4확장자)파일에 Data 정보를 넘겨줄때 class로 넘겨줄 수 있고, .tt파일 안에서 GameData를 읽어서 사용할 수 있다.

DataClass와 Parser를 자동 생성하고, Editor에서 GameData를 읽어 와서 DataClass에 넣는것은 잘됨.

C# Serializezation.Formatter에 있는 BinaryFormatter와  [ZeroFormatter](https://github.com/neuecc/ZeroFormatter)의 속도와 크기를 비교해서 어느것을 사용할지 결정하는 작업을 테스트

## Test C# BinaryFormatter

```cs
public static byte[] SerializeClass(object classInstance)
{
    IFormatter formatter = new BinaryFormatter();
    byte[] bytes;
    using (MemoryStream m = new MemoryStream())
    {
        formatter.Serialize(m, classInstance);
        bytes = m.ToArray();
    }

    return bytes;
}

public static object DeserializeClass(byte[] bytes)
{
    var formatter = new BinaryFormatter();
    object classInstance;
    using (MemoryStream m = new MemoryStream(bytes))
    {
        classInstance = formatter.Deserialize(m);
    }

    return classInstance;
}

```

DeserializeClass는 object Type을 리턴하지만, 사용하는 Type으로 캐스팅 해야 된다.

## ZeroFormatter

![](/assets/images/2020-05-16-Zeroformatter/2020-05-16-17-35-19.png)

이미지에서 보듯이 Serialize, Deserialize 속도, 용량에서 압도적으로 좋다.

하지만 Unity에서 사용하려면 Serialize, Deserialize하기 위해서 별도의 tool을 돌려서 .cs파일을 생성 해야된다.

여기서 생성되는 .cs파일은 Serialize하려는 class들의 정보이며 attribute를 붙여줘야 생성된다.

***

## Class 사용법

[ZeroFormattable] 붙여줘야하며, 변수에는 index순서를 넣어줘야 한다.
[Serializable]는 BinaryFormatter와 비교하기위해 넣어준것.

```cs
[ZeroFormattable]
[Serializable]
public class Base_Data
{
    [Index(0)]
    public virtual int data1 { get; set; }
    [Index(1)]
    public virtual int data2 { get; set; }
    [Index(2)]
    public virtual int data3 { get; set; }
    [Index(3)]
    public virtual int data4 { get; set; }
    [Index(4)]
    public virtual int data5 { get; set; }
    [Index(5)]
    public virtual int data6 { get; set; }
    [Index(6)]
    public virtual int data7 { get; set; }
    [Index(7)]
    public virtual int data8 { get; set; }
    [Index(8)]
    public virtual int data9 { get; set; }
}

[ZeroFormattable]
[Serializable]
public class DataClass
{
    [Index(0)]
    public virtual IList<BaseData> listdata { get; set; }

    public DataClass Make_Data(List<string[]> values)
    {
        DataClass table_data = new DataClass();
        table_data.listdata = new List<BaseData>();

        int row = values.Count;

        for (int curRow = 0; curRow < row; curRow++)
        {
            BaseData data = new BaseData();
            data.data1 = Int32.Parse(values[curRow][0]);
            data.data2 = Int32.Parse(values[curRow][1]);
            data.data3 = Int32.Parse(values[curRow][2]);
            data.data4 = Int32.Parse(values[curRow][3]);
            data.data5 = Int32.Parse(values[curRow][4]);
            data.data6 = Int32.Parse(values[curRow][5]);
            data.data7 = Int32.Parse(values[curRow][6]);
            data.data8 = Int32.Parse(values[curRow][7]);
            data.data9 = Int32.Parse(values[curRow][8]);
            table_data.listdata.Add(data);
        }
        return table_data;
    }
}
```

***

zfc.exe 을 사용해서 Editor상에서 cs파일 생성(zfc는 zeroformatter에 들어 있다)

```cs
void GenerateZeroformatterClass()
{
    string targetProjectName = "target.csproj";
    string sourceName = "GeneratedZeroFormatterScript.cs";
    DirectoryInfo di = new DirectoryInfo(Application.dataPath);

    string zfctoolpath = string.Format("{0}/Zfctool/zfc.exe", di.Parent);
    string projPath = string.Format("{0}/{1}", di.Parent, targetProjectName);
    string sourcePath = string.Format("{0}/{1}", Application.dataPath, sourceName);

    if (System.IO.File.Exists(zfctoolpath) == true)
    {
        System.Diagnostics.ProcessStartInfo start = new System.Diagnostics.ProcessStartInfo()
        {
            FileName = zfctoolpath,
            Arguments = string.Format("-i \"{0}\" -o \"{1}\" ", projPath, sourcePath),
            UseShellExecute = false,
            RedirectStandardOutput = false,
        };

        var process = System.Diagnostics.Process.Start(start);
        process.WaitForExit();
    }
}
```

***

## ZeroFormatter Serialize, Deserialize

```cs
public static byte[] Serialize<T>(T serializeClass)
{
    var bytes = ZeroFormatterSerializer.Serialize(serializeClass);

    return bytes;
}

public static T DeSerialize<T>(byte[] serializeData)
{
    var deserializeClass = ZeroFormatterSerializer.Deserialize<T>(serializeData);

    return deserializeClass;
}

```
BinaryFormatter과 다르게 Serialize, Deserialize둘다 Type을 명시해야한다.

Serialize, Deserialize를 사용하기 위해서는 zfc.exe로 생성된 ZeroFormatterInitializer Class의 Register를 호출 해줘야한다.


## 속도 비교

DataClass를 만들고 DataClass안의 listdata에 Base_Data를 10000개 넣는다

그리고 serialize 용량, deserialize 속도를 비교.

serialize 속도 비교 하지 않는 이유가 serialize는 Editor에서 사용 하는거라 엄청 느리지 않는 이상 크게 의미가 없다.


```cs
void SpeedTest()
{
    ZeroFormatterInitializer.Register();

    System.Diagnostics.Stopwatch stopwatch = new System.Diagnostics.Stopwatch();

    var zeroformatspeedTest =  MakeDataClass(); // 10000개의 BaseData를 갖는다

    var bytes1 = ZeroFormatterWrapper.Serialize(zeroformatspeedTest);
    Debug.Log(bytes1.Length);

    stopwatch.Start();
    var deSerializeClass = ZeroFormatterSerializer.Deserialize<BoxItemRecipe_Data>(bytes1);
    stopwatch.Stop();
    TimeSpan ts = stopwatch.Elapsed;

    Debug.Log(ts.TotalMilliseconds);

    System.Runtime.Serialization.IFormatter formatter = new BinaryFormatter();
    byte[] bytes;
    using (MemoryStream m = new MemoryStream())
    {
        formatter.Serialize(m, zeroformatspeedTest);
        bytes = m.ToArray();
    }

    object classInstance;
    formatter = new BinaryFormatter();
    stopwatch.Reset();
    stopwatch.Start();
    using (MemoryStream m = new MemoryStream(bytes))
    {
        classInstance = formatter.Deserialize(m);
    }
    Debug.Log(bytes.Length);

    stopwatch.Stop();
    ts = stopwatch.Elapsed;
    Debug.Log(ts.TotalMilliseconds);
}
```

## 결과


|   |BinaryFormatter   | ZeroFormatter  |  결과 |
|---|---|---|---|---|
|  DeSerialize Time(ms) |  335(3번 평균) | 0.0038(3번 평균)  |  zeroformatter가 28.3배 빠름 |
| Serialize Size(byte) | 500791  |  840020 | zeroformatter가 1.67배 큼  |

요약하자면 용량은 ZerofFormatter가 더 크고, 속도는 ZeroFormatter가 빠르다.

class 말고 단순히 List<int> 값을 Serialize, Deserialize했을때는 속도 용량 모두 Zeroformatter가 좋았다.
