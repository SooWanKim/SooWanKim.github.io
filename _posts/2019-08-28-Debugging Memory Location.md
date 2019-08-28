---
layout: post
title: Debugging With Memory Location
categories: [C++]
---

# 주소값안에 값이(Value) 변경 됬을때 시점 찾기


Breakpoints Window에서 

![](../assets/images/2019-08-28-Debugging%20Memory%20Location/2019-08-28-12-16-52.png)

***
new 버튼을 누르고 

![](../assets/images/2019-08-28-Debugging%20Memory%20Location/2019-08-28-12-17-46.png)

***
Data Breakpoint를 누르고 

![](../assets/images/2019-08-28-Debugging%20Memory%20Location/2019-08-28-12-18-25.png)

***
원하는 주소값을 넣으면 ex) a의 주소값

![](../assets/images/2019-08-28-Debugging%20Memory%20Location/2019-08-28-12-19-53.png)

***
이렇게 새로운 Break point가 생성된다

![](../assets/images/2019-08-28-Debugging%20Memory%20Location/2019-08-28-12-20-52.png)

***
이 상태에서 다음스텝으로 넘어가면 

![](../assets/images/2019-08-28-Debugging%20Memory%20Location/2019-08-28-12-22-51.png)

이렇게 a가 변경되는 시점에 걸리게 된다.