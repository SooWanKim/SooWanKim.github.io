---
layout: post
title: UnitySerialize Asset
---
	
ScriptalbeAsset을 사용한 data serialize하기   

**Unity** 안의 **ScriptableAsset**을 사용하여 instance된 class를 serialize하여 게임에서 읽어서 바로 사용할 수있다.

ex)  

```markdown
public class animInfo`
{ 
	string name; 
	Vector3 position;
	Vector3 rotation; 
}
```

animInfo라는 class에 name position, rotation의 값을 unity editor상에서 세팅한것을 바로 data하여 게임에서 불러서 사용한다.
 
# 1.Serialize할 class 만들기

[System.Serializable]
public class AnimInfo
{ var a, var b , var c}   class를 serialize하기 위해서는attribute에 [System.Serializable]이것을 추가해 줘야 한다.   class 멤버 변수 타입중에  List는 되는데 dictionary는 되지 않아서 따로 만들어야된다
 
public class ScriptableAsset<TKey, TValue> : ScriptableObject  //  ScriptableAsset을 상속받는 class의 data를 List<TKey>, List<TValue> 로 만듬.
{    
public List<TKey> _listKey = new List<TKey>();    
public List<TValue> _listValue = new List<TValue>();
}
 
public class AnimInfoAsset : ScriptableAsset<string, AnimInfo> { }  // AnimInfoAsset은 List<string>, List<AnimInfo>정보를 갖게 된다.
 
[Serializable] public class SerializableDictionary<TKey, TValue> : Dictionary<TKey, TValue>, ISerializationCallbackReceiver  //dictionary를 List에 넣기 위해서 따로 만든 class
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
 
public void OnAfterDeserialize()   // deserialize하고 나서 List에 있는 key값과 value를 dictionary에 옮겨 넣는다.
{   
 this.Clear();
int keysCount = keys.Count;       
int valuesCount = values.Count;       
if (keysCount == 0)       
{            return;        }
for (int i = 0; i < keysCount; i++)     
{          
this.Add(keys[i], values[i]);    
}
}

public class PlanInfoAsset : ScriptableAsset<int, DictionaryOfStringAndString> { }  // PlanInfoAsset은  key정보는 List<int>  , value정보는 List<string, Dictionary<string, string>>을 갖게 된다.
 
2.Asset 만들기
 
AnimInfoAsset asset = ScriptableObject.CreateInstance<AnimInfoAsset>(); 
asset .position = 값
asset.rotation = 값
asset.name = 값
AssetDatabase.CreateAsset(asset, path + name + extension);   // AnimInfo정보가 있는 .asset파일을 path 경로 name.extention 으로 생성
AssetDatabase.SaveAssets();
 
3.Asset export하기
 
BuildPipeline.BuildAssetBundles() // export path, path + name + extension, buildtarget을 지정해줘서 뽑으면 animInfo class가 export 된다.
 
4. Asset Import
 Object[] obj = assetBundle.LoadAllAssets();
Object type의 obj를  animInfo로 형변환후 사용하면된다.
animInfo.position 에 export할때 입력한 값이 있을것이다.
