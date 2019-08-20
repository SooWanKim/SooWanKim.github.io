---
layout: post
title: Python And Concurrency
categories: [Python]
---

#동시성

동시성(Concurrency)란 각 프로그램 조각들이 실행 순서와 무관하게 동작할 수 있도록 만들어 한 번에 여러 개의 작업을 처리할 수 있도록 만든 구조. 좀 더 쉽게 말해 하나의 작업자가 여러 개의 작업을 번갈아가며 수행할 수 있도록 만드는 것이다. 동시성을 확보하게 되면, 작업 순서와 상관없이 각 작업이 완료되지 않았더라도 필요에 따라 번갈아 가며 작업을 수행함으로써 전체 작업 수행 속도를 향상시킬 수 있다.

- 독립적으로 실행하는 프로세스들을 구성
- 한 번에 여러 개를 다루는 것과 관련
- 구조에 대한 것
- 동시성으로, 병렬 실행이 가능한(꼭 필요하지는 않음) 문제를 해결할 수 있는 해법을 만들 수 있다
- I/O Bound

example) 마우스, 키보드, 디스플레이, 디스크

#병렬성

병렬성(Parallelism)이란 많은 작업을 물리적으로 동시에 수행하는 것으로써, 작업자를 물리적으로 여럿 둠으로써 같은 작업을 동시에 수행할 수 있도록 만드는 것. 병렬성을 확보하게 되면 물리적으로 동일한 시간 내에 동일한 작업을 더 여러 번 수행할 수 있게된다. Concurrency와 Parallelism은 혼동하기 쉬운 개념. 서로 의존관계가 없이 분리되어 있는 개념. Parallelism은 한 개의 프로세서에서는 확보할 수 없는 개념. 한 개의 프로세서가 같은 시간에 두 개의 작업을 수행하는 것은 물리적으로 불가능. 반면, 한 개의 프로세서만 있다고 하더라도 동시성을 확보할 수 있다. (Nodejs의 비동기방식 )잘개 쪼갠 작업들이 서로 영향을 끼치지 않는다면, 하나의 작업자가 각 작업이 완료되지 않았더라도 번갈아가며(switching) 수행하는 것이 가능하다

Node.js의 비동기 처리
![](/assets/images/pythonAndConcurrency/1.png)

- 계산을 동시에 실행
- 한 번에 여러 개를 실행하는 것과 관련
- 실행에 대한 것
- CPU Bound

example) (한 번에 여러 점을 출력하는) 벡터 점 제품


#Python Thread
Thread 1
![](/assets/images/pythonAndConcurrency/2.png)

Thread 2
![](/assets/images/pythonAndConcurrency/3.png)

MultiProcessing
![](/assets/images/pythonAndConcurrency/4.png)

ProcessPoolExecutor
![](/assets/images/pythonAndConcurrency/5.png)


#Python Generator

- 값을 생성하고, 함수의 실행을 일시 중지 시킬 수 있음
- 호출 스코프로 반환되며, 실행한 것을 다시 시작 가능
  
![](/assets/images/pythonAndConcurrency/6.png)

#Python Coroutine
- 파이썬 2.5에서 yield는 표현식의 기능을 겸하게 됨
- 대입문의 우변 = 즉 값이 될 수 있음
- Next가 실행될때 Hello = yield 여기서 멈춤
- 외부에서 들여오는 값으로 yield가 평가

![](/assets/images/pythonAndConcurrency/7.png)


#Reference
https://okky.kr/article/388971
https://deview.kr/2014/session?seq=47
http://ohgyun.com/741
http://www.nextree.co.kr/p7292/  node js 잘설명
