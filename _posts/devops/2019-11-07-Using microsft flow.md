---
layout: post
title: using microsoft flow
categories: [Devops]
---

## Flow 란

[https://asia.flow.microsoft.com/ko-kr/](https://asia.flow.microsoft.com/ko-kr/)

Microsoft, 혹은 다른 플랫폼에서 사용하는 대부분의 서비스에 대해서 자동화는 워크 Flow

이미 만들어진 다양한 템플릿들이 존재하며, 원하는 자동화를 쉽게 생성하여 사용할 수 있다.

어떤 트리거가 실행되면 다양한 액션들을 자동으로 해준다. 액션사이에는 변수설정및 조건, 분기도 지정이 가능하다.

가장좋은건 만든 Flow들을 자동화 할 수 있다는 것이다.

***

github 트리거 및 템플릿


![](/assets/images/2019-11-07-Outlook%20Flow/2019-11-07-14-16-36.png)


***

slack 트리거 및 템플릿

outlook 메일 수신시 slack에 공유할 수 있고 github 문제에 대해서 자동으로 slack으로 알림을 받을 수 있다.


![](/assets/images/2019-11-07-Outlook%20Flow/2019-11-07-14-09-27.png)

***

dropbox 트리거 및 템플릿

파일이 생성, 수정됬을때 트리거가 실행되게 할 수 있으며, 이럴때 메일이나, 채팅방으로 메시지 알림이 가능하고 다양한 액션을 활용 할 수 있다.

![](/assets/images/2019-11-07-Outlook%20Flow/2019-11-07-14-07-51.png)

***

outlook 트리거 및 템플릿

![](/assets/images/2019-11-07-Outlook%20Flow/2019-11-07-14-08-21.png)

***

위에 설명한 템플릿 말고도 다양한 서비스, 앱이 엄청 많다.

## Flow 사용 예제

**jenkins build mail을 파싱해서 채팅방으로 공유하려고 만든 것**

특정 발신, 수신인 지정된 outlook mail 수신 -> 메일의 body 내용을 변수들에 담고 -> http에 실어서 보낸다 (slack 채팅방이 될수 있고, 텔레그램, Teasm 다 가능)

![](/assets/images/2019-11-07-Outlook%20Flow/2019-11-07-14-11-14.png)


작업 분기 처리도 가능하다.

![](/assets/images/2019-11-07-Outlook%20Flow/2019-11-07-14-13-26.png)


***

**점심 메뉴가 궁금한데 채팅방에서 보고 싶어서 간단히 만든 것.**

teams 채널에 특정 메시지를 입력하면, 채널에 webhook을 통해 messagecard를 보냄

![](/assets/images/2019-11-07-Mircrosoft%20Flow/2019-11-08-10-58-00.png)

![](/assets/images/2019-11-07-Mircrosoft%20Flow/2019-11-08-10-59-32.png)