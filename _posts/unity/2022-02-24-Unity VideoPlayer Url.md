---
layout: post
title: Unity VideoPlayer Url
categories: [C#, Unity]
---

Unity VideoPlayer를 사용해서 url에 있는 video를 UI에 그리기

OnShow() 함수로 호출

## 샘플코드

```c#
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.Video;

public enum VideoResult
{
    None = 0,
    Failed = 1,
    Skipped = 2,
    Finished = 3
}

public class VideoUI : MonoBehaviour
{
    // rawImage가 포함된 GameObject
    public GameObject movieObject;
    // skip button이 포함된 GameObject
    public GameObject skipObject;

    // 영상이 그려질 이미지
    public RawImage rawImage;
    // radial timer UI 0~1
    public Image timerImage;
    // 경과 시간 표시 Text UI
    public Text timerText;

    // 영상 표시 시간
    public float totalTime = 15.0f;
    // 넘길 수 있는 시간
    public float skipTime = 10.0f;

    float m_elapsedTime;

    Action<VideoResult> m_resultCallback;
    VideoResult m_result = VideoResult.None;
    VideoPlayer m_VideoPlayer;

    // 랜덤으로 나오게 배열로 생성
    static readonly string[] k_VideoClipArray = {
        "https://test1.mp4",
        "https://test2.mp4",
        "https://test3.mp4",
    };

    void Awake()
    {
        m_VideoPlayer = gameObject.AddComponent<VideoPlayer>();

        // 영상 시작전 호출
        m_VideoPlayer.prepareCompleted += OnCompletePrepare;
        m_VideoPlayer.errorReceived += OnReceivedError;
    }

    public void Init()
    {
        gameObject.SetActive(false);
        movieObject.SetActive(false);
        skipObject.SetActive(false);

        timerText.text = "";
        timerImage.fillAmount = 0.0f;
    }

    public void OnReceivedError(VideoPlayer source, string message)
    {
        Debug.Log("Error :" + message);
        m_result = VideoResult.Failed;
    }

    public void OnShow()
    {
        Show(OnVideoEnd);
    }

    private void Show(Action<VideoResult> resultCallback)
    {
        m_resultCallback = resultCallback;

        gameObject.SetActive(true);
        movieObject.SetActive(true);

        skipObject.SetActive(false);

        m_elapsedTime = 0f;

        timerText.text = "";
        timerImage.fillAmount = 0.0f;

        StartCoroutine(PlayVideo());
    }

    IEnumerator PlayVideo()
    {
        // url 경로로 스트리밍 설정
        m_VideoPlayer.source = VideoSource.Url;
        m_VideoPlayer.url = k_VideoClipArray[UnityEngine.Random.Range(0, k_VideoClipArray.Length - 1)];

        m_VideoPlayer.Prepare();

        yield return null;
    }

    public void OnCompletePrepare(VideoPlayer source)
    {
        rawImage.texture = source.texture;

        source.Play();
    }

    void ShowSkipButton()
    {
        skipObject.SetActive(true);
    }

    public void OnFinishShow()
    {
        if (m_VideoPlayer.isPlaying)
            m_VideoPlayer.Stop();

        m_resultCallback(VideoResult.Finished);
    }

    void Update()
    {
        if ((m_result != VideoResult.Failed) && ( m_VideoPlayer.isPlaying == false))
            return;

        if (skipObject.activeSelf == false && m_elapsedTime > skipTime)
        {
            ShowSkipButton();
        }
        if (m_elapsedTime > skipTime)
        {
            OnFinishShow();
        }
        else
        {
            m_elapsedTime += Time.unscaledDeltaTime;
            timerText.text = (adsTime - m_Timer).ToString("0");
            timerImage.fillAmount = MathUtils.Remap(m_Timer, 0f, adsTime, 0f, 1f);
        }
    }

    // skip button event로 호출
    public void OnClickSkipButton()
    {
        m_resultCallback(VideoResult.Skipped);
    }

    public void OnVideoEnd(VideoResult result)
    {
        switch (result)
        {
            // total시간 지남
            case VideoResult.Finished:
                {
                    gameObject.SetActive(false);
                    break;
                }
            // 스킵버튼 누름
            case VideoResult.Skipped:
                {
                    if (m_VideoPlayer.isPlaying)
                        m_VideoPlayer.Stop();

                    gameObject.SetActive(false);
                    break;
                }
            case VideoResult.Failed:
                {
                    break;
                }
        }
    }
}
```
