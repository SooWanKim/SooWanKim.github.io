---
layout: post
title: Teams Webhook 활용하기
categories: [CI.CD]
---

# 기능

Teams에서 incomingwebhook 으로 생성한 주소로 json 방식으로 메시지를 보내면

![](/assets/images/2019-10-10-Teams%20Webhook%20활용하기/2019-10-10-10-12-29.png)

이렇게 teams 채팅방에 보여줍니다.

![](/assets/images/2019-10-10-Teams%20Webhook%20활용하기/2019-10-10-10-12-55.png)


jira에서도 아래와 같이 필요한 정보를 실어서 요청하면

![](/assets/images/2019-10-10-Teams%20Webhook%20활용하기/2019-10-10-10-13-21.png)

이렇게 json 형식으로 정보를 받을 수 있는데

![](/assets/images/2019-10-10-Teams%20Webhook%20활용하기/2019-10-10-10-13-39.png)

위의 정보들을 다시 teams에서  incommingwebhook로 생성한 주소로 요약해서 보내게 되면 jira에 정보들을

채팅방에 필터링해서 볼 수 있게됩니다.

