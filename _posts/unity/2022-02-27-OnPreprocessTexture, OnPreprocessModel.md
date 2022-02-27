---
layout: post
title: OnPreprocessTexture, OnPreprocessModel
categories: [C#, Unity]
---

Texture, 3DModel이 임포트될때 호출되는 함수

Texture, 3DModel에 텍스쳐품질, 필터세팅, 애니메이션 품질, 메시압축 정보, 매터리얼 추츨 등을 임포트되는 애셋에 공통 설정할 수 있다.


## 샘플코드

```c#
public class MyAssetPostprocessor : AssetPostprocessor
{
	// Before the texture is imported
	public void OnPreprocessTexture()
	{

		TextureImporter texture_Importer = (TextureImporter)assetImporter;
        texture_Importer.textureCompression = TextureImporterCompression.Uncompressed;
        texture_Importer.filterMode = FilterMode.Point;

        var settings = texture_Importer.GetPlatformTextureSettings("Android");
        settings.format = TextureImporterFormat.ETC2_RGB4;
        texture_Importer.SetPlatformTextureSettings(settings);
        texture_Importer.textureCompression = TextureImporterCompression.CompressedLQ;
    }

    void OnPreprocessModel()
    {
        ModelImporter model_Importer = assetImporter as ModelImporter;

        model_Importer.isReadable = false;
        model_Importer.optimizeMesh = true;
        modelImporter.importMaterials = true;
    }
}
```