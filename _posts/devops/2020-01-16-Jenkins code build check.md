---
layout: post
title: Jenkins code build check(Unity)
categories: [Devops, GameEngine]
---

## 목적

일반적으로 개별 작업자들 환경에서는 compile error가 나지 않지만, 빌드 해야되는 저장소(Jenkins에서 설정한)에서는 error가 날 수 있어서(define, platform, code 미반영의 이유로)
빌드를 진행 해야하는 담당자가 빌드를 진행할시에 이것을 체크하기 힘들고, 시간도 오래 걸리고, 원인 추적하기도 힘들다.

**unity build를 jenkins로 진행할 때 define 설정이나 플랫폼에 따른 빌드실패가 나거나 commit된 코드에서 생성되는 문제를 더 빠르게, 즉시, 자동으로 찾기 위함**


## jenkins에서 plugin 설정방법


Warning Next Generation 플러그인을 젠킨스 마스터에 설치하고

[https://plugins.jenkins.io/warnings-ng](https://plugins.jenkins.io/warnings-ng)

job configure에서 post build를 누르고 Record compiler warnings and static analysis results를 설정한다.


![](/assets/images/2020-01-16-Jenkins%20code%20build%20check/2020-01-16-18-04-01.png)


빌드체크를 위해 MSBuild 선택, Java, Kotlin  엄청 다양하다.

![](/assets/images/2020-01-16-Jenkins%20code%20build%20check/2020-01-16-18-05-10.png)


advanced에서 아래 flag 체크

![](/assets/images/2020-01-16-Jenkins%20code%20build%20check/2020-01-16-18-05-36.png)

pipeline으로도 세팅 가능하다.
[https://github.com/jenkinsci/warnings-ng-plugin/blob/master/doc/Documentation.md#migration-of-pipelines](https://github.com/jenkinsci/warnings-ng-plugin/blob/master/doc/Documentation.md#migration-of-pipelines)

## 작업 결과

우선 강제로 compile에러가 나게 code commit(jenkis라고 오타남 ㅋ)

![](/assets/images/2020-01-16-Jenkins%20code%20build%20check/2020-01-16-18-07-59.png)


link타고 들어가면 어디서 에러났는지 정확히 보여준다. 어떤 에러가 추가 되고, 계속 유지 되고 수정됬는지 차트로 보여 준다.
실패난곳 위치 말고 코드 전체를 보여주는건 좋은거 같다.

![](/assets/images/2020-01-16-Jenkins%20code%20build%20check/2020-01-16-18-09-14.png)


