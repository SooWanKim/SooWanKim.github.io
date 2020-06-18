---
layout: post
title: Python socket option no delay
categories: [Python]
---

서버랑 작업 진행중 Network Packet 지연문제가 발생

개발 PC에서 클라이언트에서 작업할때는 **recv packet**이 일정하게 오는데

핸드폰 디바이스에 올려서 테스트할때는 **recv packet**이 쌓여서 한번올 때 여러개가 와서

확인해 보니 서버쪽 **socket option**이 **TCP_NODELAY**로 세팅이 안되어 있음.

**TCP_DELAY**는  [Nagle](http://egloos.zum.com/depiness/v/772710) 알고리즘을 사용함.

네트워크가 지연되는 상태에서 packet을 쌓아놓고 보냄.

