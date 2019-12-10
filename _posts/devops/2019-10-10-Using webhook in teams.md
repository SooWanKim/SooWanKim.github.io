---
layout: post
title: Using webhook in teams
categories: [Devops]
---

## 기능

Teams에서 incomingwebhook 으로 생성한 주소로 json 방식으로 메시지를 보내면

![](/assets/images/2019-10-10-Teams%20Webhook%20활용하기/2019-10-10-10-12-29.png)

이렇게 teams 채팅방에 보여준다.

![](/assets/images/2019-10-10-Teams%20Webhook%20활용하기/2019-10-10-10-12-55.png)


jira에서도 아래와 같이 필요한 정보를 실어서 request하면 여러 값들을 받을 수 있다.

![](/assets/images/2019-10-10-Using%20webhook%20in%20teams/2019-12-10-18-26-11.png)

위의 정보들을 다시 teams에서  incommingwebhook로 생성한 주소로 요약해서 보내게 되면 jira에 정보들을

채팅방에 필터링해서 볼 수 있게된다.

***

python에 jira 모듈이 있어서 아래와 같이 쉽게 이슈들을 가져올 수 있다.

[https://jira.readthedocs.io/en/master/examples.html#issues](https://jira.readthedocs.io/en/master/examples.html#issues)

```python

issues = jira.search_issues("jql", maxResults=1000)

```



