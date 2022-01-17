---
layout: post
title: Create screenshot ui image in unity
categories: [C#, Unity]
---

## 기능

SpriteRenderer의 bound(rect)값과 position으로 photo image를 만들어서 ui raw image에 넣는다

원하는 크기의 사진을 만들어서 ui에 입힌다고 생각하면 된다.

## 코드

```c#
void CaptureStart()
{
    SpriteRenderer captureRenderer = ui sprite image // 이 값의 영역 = photo size
    Vector3 capturePosition = world position // 이 값은 world space 값이라 screen space로 변환해줘야 한다.

    CaptureCamera(capturePosition, captureRenderer.bounds);
}

private void CaptureCamera(Vector3 capturePosition, Bounds captureBounds)
{

#if UNITY_IPHONE || UNITY_EDITOR || UNITY_STANDALONE // RenderTexture 생성하는게 iphone, Editor와 android를 인자를 다르게해야한다.
		m_RenderTexture = new RenderTexture(Screen.width, Screen.height, 24);
#elif UNITY_ANDROID
        m_RenderTexture = new RenderTexture(Screen.width, Screen.height, 24, RenderTextureFormat.Default, RenderTextureReadWrite.Linear);
#endif

    Camera captureCamera = CameraManager.Instance.captureCamera; // capture camera. main camera랑 sorting order를 다르게 한다.

    captureCamera.fieldOfView = CameraManager.Instance.mainCamera.fieldOfView; // maincamera랑 fov를 같게

    captureCamera.transform.position = CameraManager.Instance.mainCamera.transform.position;

    Rect captureRect = captureBounds.ToScreenSpace(captureCamera); // wrold space -> screen space

    int width = Mathf.FloorToInt(captureRect.width);
    int height = Mathf.FloorToInt(captureRect.height);

    // gammaspace로 설정하면 android에서는 인자값을 다르게 해야햔다.
#if UNITY_IPHONE || UNITY_EDITOR || UNITY_STANDALONE
	Texture2D tex2DScreenShot = new Texture2D(width, height, TextureFormat.RGB24, false);
#elif UNITY_ANDROID
    Texture2D tex2DScreenShot = new Texture2D(width, height, TextureFormat.RGB24, false, true);
#endif

    captureCamera.targetTexture = m_RenderTexture;
    RenderTexture.active = m_RenderTexture;

    OnReadPixcels(captureCamera, captureRect, tex2DScreenShot);

    screenShotImage.texture = tex2DScreenShot;

    // 찍은 이미지의 Byte정보, 이값으로 다시 이미지로 변환가능
    // tex2DScreenShot.GetRawTextureData();

    captureCamera.targetTexture = null;
    RenderTexture.active = null;
}

// Camera에서 render를 호출, pixel값을 읽어와서 texture에 적용
private void OnReadPixcels(Camera captureCamera, Rect captureRect, Texture2D tex2DScreenShot)
{
    captureCamera.Render();

    tex2DScreenShot.ReadPixels(new Rect(captureRect.x, captureRect.y, captureRect.width, captureRect.height), 0, 0);

    tex2DScreenShot.Apply();
}

// world space -> screen space
public Rect ToScreenSpace(this Bounds bounds, Camera camera)
{
	var origin = camera.WorldToScreenPoint(new Vector3(bounds.min.x, bounds.max.y, bounds.center.z));
	var extents = camera.WorldToScreenPoint(new Vector3(bounds.max.x, bounds.min.y, bounds.center.z));

    // eidtor에서와 기기랑 screen space로 변환하는 값이 다르다.
#if UNITY_EDITOR || UNITY_STANDALONE
	return new Rect(origin.x, Screen.height - origin.y, extents.x - origin.x, origin.y - extents.y);
#else
    return new Rect(origin.x, extents.y, extents.x - origin.x, origin.y - extents.y);
#endif
}


```
