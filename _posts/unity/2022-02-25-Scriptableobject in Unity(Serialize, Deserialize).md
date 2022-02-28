---
layout: post
title: Scriptableobject Serialize, Deserialize  in unity
categories: [C#,Unity]
---

ScriptableObject 사용하여 class serialize, deserialize 하기

**ScriptableObject**을 사용하여 class를 serialize하여 asset으로 export

게임에서 import(deserialize)하여 사용할 수 있다.


## 샘플 코드

# 1.Serialize할 class 만들기
ObjectInfo class에 name, position, rotation의 값을 asset으로 변경해서 게임에서 import하여 사용한다.
class를 serialize하기 위해서는 attribute에 **[System.Serializable]** 추가해 줘야 한다.

```c#
[System.Serializable]
public class ObjectInfo
{
    public string name;
    public Vector3 position;
    public Vector3 rotation;
}
```

***

## Dictionary를 Serialize 하기

List는 되는데 Dictionary는 되지 않아서 별도로 ScriptableObject 상속받아 구현해야된다.

나중에 다시 dictionary로 변경 할 key, value를 List<TKey>, List<TValue>에 저장한다.

```c#
public class ScriptableAsset<TKey, TValue> : ScriptableObject
{
    public List<TKey> _listKey = new List<TKey>();
    public List<TValue> _listValue = new List<TValue>();
}

// key에 string, value에 objectInfo가 들어가는 asset
public class ObjectInfoAsset : ScriptableAsset<string, ObjectInfo> { }
```

```c#
// dictionary를 List에 넣기 위해서 만든 class
// ISerializationCallbackReceiver  serialize전 deserialize후 callback 함수 사용하기위해 상속

[Serializable]
public class SerializableDictionary<TKey, TValue> : Dictionary<TKey, TValue>, ISerializationCallbackReceiver
{
    [SerializeField]
    public List<TKey> keys = new List<TKey>();
    [SerializeField]
    public List<TValue> values = new List<TValue>();

    // serialize하기전에 dictionary에 있는 key값과 value를 옮겨 넣는다.
    public void OnBeforeSerialize()
    {
        keys.Clear();
        values.Clear();
        var enumer = GetEnumerator();
        while (enumer.MoveNext())
        {
            keys.Add(enumer.Current.Key);
            values.Add(enumer.Current.Value);
        }
    }
}

// Deserialize하고 나서 List에 있는 key값과 value를 dictionary에 옮겨 넣는다.
public void OnAfterDeserialize()
{
    this.Clear();
    int keysCount = keys.Count;
    int valuesCount = values.Count;

    if (keysCount == 0)
    {
        return;
    }

    for (int i = 0; i < keysCount; i++)
    {
        this.Add(keys[i], values[i]);
    }
}


***

DataInfoAsset은 key정보는 List<int>, value정보는 List<string, Dictionary<string, string>>을 갖게 된다.

public class DataInfoAsset : ScriptableAsset<int, DictionaryOfStringAndString> { }
 ```

***

# 2. 애셋 만들기
.asset 확장자로 생성

```c#
ObjectInfoAsset asset = ScriptableObject.CreateInstance<ObjectInfoAsset>();
asset.position = new Vector3(1, 2, 3);
asset.rotation = 회전값
asset.name = "애셋이름"

// ObjectInfo 정보가 있는 .asset파일을 path 경로로 생성
AssetDatabase.CreateAsset(asset, path + name + ".asset");
AssetDatabase.SaveAssets();
```

***

# 3.Export Asset
export path, path + name + extension, buildtarget을 지정해줘서 뽑으면 ObjectInfo.asset이 애셋으로 export된다.

```c#
BuildPipeline.BuildAssetBundles()
```


***

# 4. Import Asset

```c#
Object[] obj = assetBundle.LoadAllAssets(); //Object type의 obj를  ObjectInfo 로 형변환 후 사용하면된다.
var objectInfo = obj[0] as ObjectInfo;
```
