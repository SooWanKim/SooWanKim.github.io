---
layout: post
title: Teams에 Jira Server App 붙이기
categories: [Devops]
---

## 목적

회사에서 메신저를 Teams를 쓰고있는데 Jira와 연동하면 작업하기 좋을 거 같아서 사용

***

## 기능

Teams 채팅방에서 Jira 이슈들을 생성 및 편집 가능하고, 채팅방에 Filter 및 Assign 상태에 따라 볼 수 있게 tab을 추가할 수 있습니다..

[https://techcommunity.microsoft.com/t5/Microsoft-Teams-Blog/New-Jira-Server-Integration-for-Microsoft-Teams/ba-p/327688](https://techcommunity.microsoft.com/t5/Microsoft-Teams-Blog/New-Jira-Server-Integration-for-Microsoft-Teams/ba-p/327688)

![](/assets/images/2019-10-18-Teams에%20Jira%20Server/2019-10-08-10-28-03.png)

***

## 붙이는 방법

Jira Server App을 Teams에 우선 설치하고

![](/assets/images/2019-10-18-Teams에%20Jira%20Server/2019-10-08-10-29-30.png)

팀 혹은 채팅방에 추가를 합니다..

![](/assets/images/2019-10-18-Teams에%20Jira%20Server/2019-10-08-10-31-25.png)

Jira Server에 연결하기 위해서는 아래와 같은 unique id가 필요합니다.

![](/assets/images/2019-10-18-Teams에%20Jira%20Server/2019-10-08-10-33-31.png)


unique id는 jira admin 권한이 있는 사람이 밑의 방식으로 jira에서 생성가능 합니다.

[https://www.msteams-atlassian.com/jiraserver/index.html#jira-id](https://www.msteams-atlassian.com/jiraserver/index.html#jira-id)

이 부분이 unique id

![](/assets/images/2019-10-18-Teams에%20Jira%20Server/2019-10-08-10-35-02.png)

채팅방에서 id를 입력하면, jira server에 연결할 수 있고, username 및 password를 입력하면
바로 채팅방에서 jira의 이슈들을 컨트롤 가능합니다.

***

## 지원하는 명령어들

![](/assets/images/2019-10-18-Teams에%20Jira%20Server/2019-10-08-10-36-46.png)


