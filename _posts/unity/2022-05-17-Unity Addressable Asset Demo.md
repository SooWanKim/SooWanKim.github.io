---
layout: post
title: Unity Addressable Asset System
categories: [Unity]
---

유니티에서 지원하는 애셋번들 관리 시스템
패키지매니저를 통해 설치


- 중복 애셋 처리
- 애셋 번들 의존성 처리
- 애셋 번들 메모리 관리 및 해제

[optimization-trenches-saving-memory-with-addressables](https://blog.unity.com/technology/tales-from-the-optimization-trenches-saving-memory-with-addressables)


- 스테이지 방식의 게임이라면 스테이지로 번들 구성
- 번들로 생성한 애셋이 해제 되려면 사용하는곳이 없어야 되기 때문에 같은 번들안에 여러 프리팹을 놓는 방식보다 빈번하게 생성 및 해제하는 프리팹은 개별 애셋으로 구성
![](/assets/images/2022-05-17-Unity%20Addressable%20Asset%20Demo/2022-05-17-13-18-34.png)]
- 중복 애셋을 별도의 번들로 구성해서 복사 방지
![](/assets/images/2022-05-17-Unity%20Addressable%20Asset%20Demo/2022-05-17-13-20-02.png)
![](/assets/images/2022-05-17-Unity%20Addressable%20Asset%20Demo/2022-05-17-13-20-19.png)