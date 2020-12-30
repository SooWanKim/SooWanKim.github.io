---
layout: post
title: Making photo ui image in unity
categories: [C#, GameEngine]
---


## 기능

spine을 export하면 나오는 json, text(byte), png 3개의 파일로 skeletonmecanim을 생성한다.

## 코드

```c#
const string k_AtlasExt = ".txt";
const string k_PngExt = ".png";
const string k_JsonExt = ".json .bytes";

public TextAsset m_SkeletonJson; // spine json
public TextAsset m_AtlasText; // spine atlas
public Texture2D[] m_Textures = new Texture2D[1]; // spine png로 생성


void OnSpineData()
{
    if(SelectAtlas())
        return;

    Material material = CreateMaterial();
    CreateSpineDataPrefab(material);
}

Material CreateMaterial(string shaderName = "Sprites/Dafault")
{
	string currentDir = GetCurrentDirectory();

	if (string.IsNullOrEmpty(currentDir) == true)
	{
		UnityEditor.EditorUtility.DisplayDialog("Error", "Need to Select Folder", "ok");
		return null;
	}

	string folderPath = currentDir + "/Material";

	if (AssetDatabase.IsValidFolder(folderPath) == false)
	{
		string folderGuid = AssetDatabase.CreateFolder(currentDir, "Material");
		folderPath = AssetDatabase.GUIDToAssetPath(folderGuid);
	}

	string assetPrefixName = "/" + new DirectoryInfo(currentDir).Name + "_Material.mat";
	Material material = new Material(Shader.Find(shaderName));
	material.SetTexture("_MainTex", m_Textures[0]);
	AssetDatabase.CreateAsset(material, folderPath + assetPrefixName);

	return material;
}

string GetCurrentDirectory()
{
	UnityEngine.Object[] selection = Selection.GetFiltered(typeof(UnityEngine.Object), SelectionMode.Assets);
	if (selection.Length > 0)
	{
		UnityEngine.Object selectionAsset = selection[0];
		string assetPath = AssetDatabase.GetAssetPath(selectionAsset);
		string realPath = Path.GetDirectoryName(assetPath);

		if (Path.HasExtension(assetPath) == false)
			return realPath + "/" + selectionAsset.name;

		return realPath;
	}

	return null;
}

// 3개의 파일 선택 체크
bool SelectAtlas()
{
	UnityEngine.Object[] selection = Selection.GetFiltered(typeof(UnityEngine.Object), SelectionMode.Assets);
	int selectionLength = selection.Length;
	if (selectionLength != 3)
		return false;

	string assetPath;
	string ext;
	for (int i = 0; i < selectionLength; i++)
	{
		UnityEngine.Object selectionAsset = selection[i];
		assetPath = AssetDatabase.GetAssetPath(selectionAsset);
		ext = Path.GetExtension(assetPath);

		if (string.Equals(ext, k_AtlasExt) == true)
		{
			m_AtlasText = selectionAsset as TextAsset;
		}
		else if (k_JsonExt.Contains(ext) == true)
		{
			m_SkeletonJson = selectionAsset as TextAsset;
		}
		else if (string.Equals(ext, k_PngExt) == true)
		{
			m_Textures[0] = selectionAsset as Texture2D;
		}
	}

	if (m_AtlasText == null || m_SkeletonJson == null || m_Textures[0] == null)
	{
		UnityEditor.EditorUtility.DisplayDialog("Error", "need to select asset png, json OR bytes, text", "ok");
		return false;
	}

	return true;
}

// spine data가 붙어있는 prefab 생성
void CreateSpineDataPrefab(Material material)
{
	Material[] tempMaterial = new Material[1];
	tempMaterial[0] = material;
	m_AtlasAsset = SpineAtlasAsset.CreateRuntimeInstance(m_AtlasText, tempMaterial, true);

	m_SkeletonDataAsset = SkeletonDataAsset.CreateRuntimeInstance(m_SkeletonJson, m_AtlasAsset, true);
	material.mainTexture = m_Textures[0];
	m_SpineName = Path.GetFileNameWithoutExtension(m_SkeletonJson.name);

	m_AtlasText = null;
	m_Textures[0] = null;
	m_SkeletonJson = null;

	if (m_AtlasAsset == null || m_SkeletonDataAsset == null)
	{
		UnityEditor.EditorUtility.DisplayDialog("Error", "생성 실패", "ok");
		return;
	}

	m_AtlasAsset.materials[0] = material;

	string currentDir = GetCurrentDirectory();

	AssetDatabase.CreateAsset(m_AtlasAsset, currentDir + "/" + m_SpineName + "_AtlasData.asset");
	AssetDatabase.CreateAsset(m_SkeletonDataAsset, currentDir + "/" + m_SpineName + "_SkeletonData.asset");
	SkeletonBaker.GenerateMecanimAnimationClips(m_SkeletonDataAsset); // spine에 있는 정보로 clip정보를 생성한다.

	GameObject rootObject = new GameObject(m_SpineName);
	rootObject.tag = 'tag you want to';
	rootObject.layer = LayerMask.NameToLayer('layer you want to);

	SkeletonMecanim skeletonAnimator = rootObject.AddComponent<SkeletonMecanim>();
	skeletonAnimator.skeletonDataAsset = m_SkeletonDataAsset;  // <<--- 이 부분이 중요
	Animator animator = skeletonAnimator.GetComponent<Animator>();
	animator.cullingMode = AnimatorCullingMode.CullCompletely;

	int length = skeletonAnimator.skeletonDataAsset.GetSkeletonData(false).Skins.Count;

	for (int i = 0; i < length; i++)
	{
		string skinName = skeletonAnimator.skeletonDataAsset.GetSkeletonData(false).Skins.Items[i].Name;
		skeletonAnimator.initialSkinName = skinName;
		rootObject.name = skinName.Equals("default") ? m_SpineName : skinName;
		CreatePrefab(currentDir, rootObject, true);
	}

	AssetDatabase.SaveAssets();
	AssetDatabase.Refresh();
	EditorUtility.FocusProjectWindow();

	DestroyImmediate(rootObject);
}

void CreatePrefab(string dir, GameObject gameObject, bool isReplace = false)
{
	string folderPath = dir + "/Prefabs";

	if (AssetDatabase.IsValidFolder(folderPath) == false)
	{
		string folderGuid = AssetDatabase.CreateFolder(dir, "Prefabs");
		folderPath = AssetDatabase.GUIDToAssetPath(folderGuid);
	}

	var go = PrefabUtility.SaveAsPrefabAsset(gameObject, dir + "/Prefabs/" + gameObject.name + ".prefab");
	Selection.activeObject = go;
}


```