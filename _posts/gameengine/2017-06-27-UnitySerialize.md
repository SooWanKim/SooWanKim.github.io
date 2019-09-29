---
layout: post
title: Unity Serialize Asset
categories: [GameEngine]

---

# ScriptableObject 사용하여 class serialize하기

**Unity** 안의 **ScriptableObject**을 사용하여 class를 serialize하여 asset으로 변경 후 게임에서 읽어서 사용할 수 있다.

ex)

```c#
public class ObjectInfo
{
	public string name;
	public Vector3 position;
	public Vector3 rotation;
}
```

ObjectInfo class에 name, position, rotation의 값을 unity editor상에서 세팅한것을 asset 하여 게임에서 읽어 사용한다.

# 1.Serialize할 class 만들기

```c#
[System.Serializable]
public class ObjectInfo
{
	public string name;
	public Vector3 position;
	public Vector3 rotation;
}
```

class를 serialize하기 위해서는 attribute에 **[System.Serializable]** 추가해 줘야 한다. class 멤버 변수 타입중에  List는 되는데 dictionary는 되지 않아서 따로 만들어야된다

**ScriptableObject**을 상속받는 class의 data를 List<TKey>, List<TValue> 로 만듬.

```c#
public class ScriptableAsset<TKey, TValue> : ScriptableObject
{
	public List<TKey> _listKey = new List<TKey>();
	public List<TValue> _listValue = new List<TValue>();
}
```

```c#
// ObjectInfo List<string>, List<ObjectInfo>정보를 갖게 된다.
public class ObjectInfoAsset : ScriptableAsset<string, ObjectInfo> { }
```

```c#
// dictionary를 List에 넣기 위해서 따로 만든 class

[Serializable]
public class SerializableDictionary<TKey, TValue> : Dictionary<TKey, TValue>, ISerializationCallbackReceiver
{
	[SerializeField]
	public List<TKey> keys = new List<TKey>();
	[SerializeField]
	public List<TValue> values = new List<TValue>();

	public void OnBeforeSerialize()  // serialize하기전에 dicionary에 있는 key값과 value를 옮겨 넣는다.
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

public void OnAfterDeserialize()   // deserialize하고 나서 List에 있는 key값과 value를 dictionary에 옮겨 넣는다.
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

DataInfoAsset은  key정보는 List<int>, value정보는 List<string, Dictionary<string, string>>을 갖게 된다.

public class DataInfoAsset : ScriptableAsset<int, DictionaryOfStringAndString> { }
 ```

# 2.Asset 만들기

```c#
ObjectInfoAsset asset = ScriptableObject.CreateInstance<ObjectInfoAsset>();
asset.position = 값
asset.rotation = 값
asset.name = 값
AssetDatabase.CreateAsset(asset, path + name + extension);   // ObjectInfo 정보가 있는 .asset파일을 path 경로 name.extention 으로 생성
AssetDatabase.SaveAssets();
```

# 3.Asset Export하기

// export path, path + name + extension, buildtarget을 지정해줘서 뽑으면 ObjectInfo class가 export 된다.

```c#
BuildPipeline.BuildAssetBundles()
```

# 4. Asset Import

```c#
Object[] obj = assetBundle.LoadAllAssets(); //Object type의 obj를  ObjectInfo 로 형변환 후 사용하면된다.
```
