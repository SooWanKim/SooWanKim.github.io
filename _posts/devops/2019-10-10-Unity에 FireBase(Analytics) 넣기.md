---
layout: post
title: Unity에 FireBase Analytics 사용하기
categories: [GameEngine, Devops]
---

# 왜 사용?

Event 분석을 위해 사용

[https://console.firebase.google.com/?hl=ko&pli=1](https://console.firebase.google.com/?hl=ko&pli=1)

![](/assets/images/2019-10-10-Unity에%20FireBase%20넣기/2019-10-10-14-08-53.png)


임의 프로젝트 추가로 생성

![](/assets/images/2019-10-10-Unity에%20FireBase%20넣기/2019-10-10-14-09-40.png)

톱니바퀴를 누르면 앱추가를 해서 등록할 수 있습니다. Android, Ios 각각 별도로 등록하고, Android는 google-services.json 을 /Assets 밑에 다운받아 놓고

Ios는 GoogleServices-Info.Plist를 /Assets 밑에 다운받습니다. 사실상 2개 다 다운받습니다.

![](/assets/images/2019-10-10-Unity에%20FireBase%20넣기/2019-10-10-14-13-12.png)


세팅 하는법이 잘나와 있습니다.
[https://firebase.google.com/docs/unity/setup?authuser=0](https://firebase.google.com/docs/unity/setup?authuser=0)

이 패키지 받아서 import해서 사용하면 됩니다.
![](/assets/images/2019-10-10-Unity에%20FireBase%20넣기/2019-10-10-14-15-31.png)


FireBase init하는 코드입니다.
![](/assets/images/2019-10-10-Unity에%20FireBase%20넣기/2019-10-10-14-18-06.png)


Event를 보내는 함수 입니다.
![](/assets/images/2019-10-10-Unity에%20FireBase%20넣기/2019-10-10-14-18-42.png)


Parameter마다 함수가 등록되어 있어서, Generic하게 사용하기 위해 별도 Temlate 함수 등록했습니다.
![](/assets/images/2019-10-10-Unity에%20FireBase%20넣기/2019-10-10-14-19-18.png)


Ios는 FirebaseCore lib 경로이슈가 있어서 여기서 2번째 선택해야 됩니다.
![](/assets/images/2019-10-10-Unity에%20FireBase%20넣기/2019-10-10-14-19-56.png)