---
layout: post
title: PreviewRender in Custom EditorWindow
categories: [C#, Unity]
---
## 기능

컨텐츠 생산에 용이하게 EditorWindow를 상속받은 Custom에디터를 자주 사용하는데
여기에 GameObject 그려야되는 이슈가 있어 찾아보니
PreviewRenderUtility로 GameObject 추가가 가능했다.


아래와 같이 EdiorWindow를 만들 수 있다.

![](/assets/images/2021-04-14-PreviewRender%20in%20Custom%20EditorWindow/여기에%20EditorWindow에%20추가된%20이미지.png)


## 샘플코드

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEditor;

public class MyCustomEditorWindow : EditorWindow
{
    Rect _objectRect  = 그릴 영역
    PreviewRenderUtility _prevRenderUtility;
    float _renderScale = 1.0f;
    Vector2 _renderCameraPos = Vector2.Zero;

    [MenuItem("MyTool/Tool")]
    public static void OpenTool()
    {
        MyCustomEditorWindow window = (MyCustomEditorWindow)EditorWindow.GetWindow(typeof(MyCustomEditorWindow));
        window.titleContent = new GUIContent("MyCustomEditorWindow");
        window.Init();
        window.minSize = new Vector2(1024, 720);
        window.Show();
    }

    void Init()
    {
        _prevRenderUtility = new PreviewRenderUtility();
        _prevRenderUtility.camera.orthographic = true;
        _prevRenderUtility.camera.transform.rotation = Quaternion.identity;
        _prevRenderUtility.camera.nearClipPlane = 1;
        _prevRenderUtility.camera.farClipPlane = 30;
        _prevRenderUtility.camera.clearFlags = CameraClearFlags.Nothing;

        _prevRenderUtility.camera.cameraType = CameraType.SceneView;

        Handles.SetCamera(_prevRenderUtility.camera);

        // 테스트용 큐브
        var cube = GameObject.CreatePrimitive(PrimitiveType.Cube);
        _prevRenderUtility.AddSingleGo(cube);
    }

    void OnGUI()
    {
        DrawObject(_objectRect);
    }

    void DrawObject(Rect rect)
    {
        _prevRenderUtility.BeginPreview(rect, bgColor);

        _prevRenderUtility.camera.Render();

        Texture texture = _prevRenderUtility.EndPreview();
        texture.filterMode = FilterMode.Point;
        GUI.DrawTexture(rect, texture);

        UpdateMouseEvent();
        UpdateRenderCamera(rect);
    }

      // Preview의 카메라 위치 조절
    void UpdateRenderCamera(Rect rect)
    {
        float scaleInv = 1.0f / (_renderScale);
        _prevRenderUtility.camera.orthographicSize = 0.5f *rect.height * scaleInv;
        _prevRenderUtility.camera.transform.position = new Vector3(-_renderCameraPos.x * scaleInv, _renderCameraPos.y * scaleInv, -15f);
    }

    void UpdateMouseEvent()
    {
        Event e = Event.current;

        if(EventYpe.ScrollWheel == e.type)
        {
            float scale = 1000.0f;
            _renderScale -= e.delta.y * scale * 0.05f;
            _renderScale = Mathf.Clamp(_renderScale, 0.1f, 100.0f);
            Repaint();
            e.Use();
        }
        else if(EventType.MouseDrag == e.type)
        {
            if(1 == e.button || 2 == e.button)
            {
                _renderCameraPos += e.delta;
                Repaint();
                e.Use();
            }
        }
    }
}
```