---
layout: post
title: Unity SpriteAtlas LateBinding
categories: [Unity]
---

SpriteAtlas에 **Include in Build** flag를 켜고 빌드하면 

Atlas는 포함된 상태로 빌드된다. 

문제는 이러면 개별로 만든 AssetBundle에도 Atlas에 포함된 Sprite가 있으면 AssetBundle에도 포함되서 뽑힌다.

용량만 커지는게 아니라 실제로 Game에서 AssetBundle을 불러와서 사용 하면 빌드에 포함된 Atlas가 있는데도

Memory에 올라가게 된다. Assetbundle하나당 Atlas 하나씩 불러오기 때문에 Memory에 문제가 된다.

AssetBundle도 쓰고 SpriteAtlas 시스템을 사용하려면 Include in Build를 해제하고 빌드 및 AssetBundle을 뽑아야 된다.

이렇게 하면 AssetBundle에는 Atlas는 빠진 상태로 빌드되고 Scene에 배치된 Sprite도 연결된 SpriteAtlas를 사용하게 된다.

이 방식을 쓰기위해서는 SpriteAtlas를 AssetBundle로 만들어야 하고 LateBinding을 할때 AssetBundle을 불러와서 생성해야한다.

```markdown

# Asset을 받는 시점(Atlas가 필요하다고 판단되서 호출되는듯)에서 호출되는 event를 등록
    void OnEnable()
    {
        SpriteAtlasManager.atlasRequested += RequestLateBindingAtlas;
    }

    void OnDisable()
    {
        SpriteAtlasManager.atlasRequested -= RequestLateBindingAtlas; 
    }
    
    void RequestLateBindingAtlas(string spriteAtlasName, System.Action<SpriteAtlas> action)
    {
# 받아 놓은 Atlas가 있는지 체크
        if (SpriteCollection.Instance.IsExistSpriteAtlas(spriteAtlasName))
        {
            return;
        }
       
# Atlas가 필요하다고 한 순간에 바로 Atlas AssetBundle를 다운 받는게 아니라 다음에 한꺼번에 몰아서 받기
        if (m_ActionSpriteAtlas.ContainsKey(spriteAtlasName) == false)
            m_ActionSpriteAtlas.Add(spriteAtlasName, action);
    }
    
# 전체 Atlas를 다운 및 생성
    public IEnumerator LoadSpriteAtlas()
    {
        foreach(var atlasAsset in m_ActionSpriteAtlas)
        {
            yield return LoadSpriteAtlas(atlasAsset.Key, atlasAsset.Value);
        }

        m_ActionSpriteAtlas.Clear();

        AssetBundleLoader.UnloadLoadedAssetbundle(MetaData.k_AssetBundleFileName_SpriteAtlas);

        yield return null;
    }

    private IEnumerator LoadSpriteAtlas(string assetName, System.Action<SpriteAtlas> action)
    {
        yield return StartCoroutine(AssetBundleLoader.LoadFromCachedOrDownloadAssetBundle(MetaData.k_AssetBundleFileName_SpriteAtlas, null));

        var spriteAtlas = AssetBundleLoader.ExtractAsset<SpriteAtlas>(MetaData.k_AssetBundleFileName_SpriteAtlas, assetName);

# 등록된 event에 생성한 Atlas 전달하면 binding 끝
        action(spriteAtlas);

        SpriteCollection.Instance.AddSpriteAtlas(assetName, spriteAtlas);

        AssetBundleLoader.UnloadLoadedAssetbundle(MetaData.k_AssetBundleFileName_SpriteAtlas);
    }
```
