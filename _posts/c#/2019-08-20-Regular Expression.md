---
layout: post
title: Regular Expression
categories: [C#]
---

# 정규 표헌식이란
정규표현식(正規表現式, Regular Expression)은 문자열을 처리하는 방법 중의 하나로 특정한 조건의 문자를 '검색'하거나 '치환'하는 과정을 매우 간편하게 처리 할 수 있도록 하는 수단이다.

***

## C#에서 사용
System.Text.RegularExpressions.Regex 정규식 엔진 사용
Regex.IsMatch, Regex.Match, Regex.Replace 사용

***

## Match
Match 1

![](/assets/images/regular_expression/1.png)
- Index 처음 발견한 index값,
- Value는 찾은 값

Match 2

![](/assets/images/regular_expression/2.png)
- NextMatch() 로 찾은거 하나씩 넘어가기

***

## Matches
Matches 1

![](/assets/images/regular_expression/3.png)

***

## Split
Split 1

![](/assets/images/regular_expression/4.png)

Split 2

![](/assets/images/regular_expression/5.png)

- 기본적으로 Regex가 꽤 느림. String에서 지원하는게 있으면 쓰는게 효율적임. 15배 정도 느림
-
***

## 메타문자

- Regular Expression에는 일반 문자 리터럴과 특별한 의미를 갖는 메타문자를 사용 가능

 자주 사용하는것들

![](/assets/images/regular_expression/6.png)

***

## 수량자
- 찾을 항목의 문자, 그룹등 수를 지정
- 탐욕적 수량자: 패턴과 일치하면 모두 찾음(n번 이상)
- 게으른 수량자: 최소한만 찾음(n번 이상이면 n개)

![](/assets/images/regular_expression/7.png)

수량자1

![](/assets/images/regular_expression/8.png)

수량자2

![](/assets/images/regular_expression/0.png)

***

## 문자클래스
- 문자 집합을 정의
- 긍정 문자 그룹 : 입력 문자열의 문자는 지정된 문자 집합 중 하나와 일치해야 된다.
- 부정 문자 그룹: 입력 문자열의 문자는 지정된 문자 집합중 하나와 일치하면 안된다.

***

## 긍정 문자 그룹
- [character_group] 개별 문자 목록 지정 구문
- 하나 이상의 리터럴 문자, 이스케이프문자, 문자 클래스로 이루어진 조합

![](/assets/images/regular_expression/9.png)

![](/assets/images/regular_expression/10.png)

***

## 부정 문자
- [^character_group]입력 문자열에 나타나서는 안 되는 문자 목록을 지정

![](/assets/images/regular_expression/11.png)

![](/assets/images/regular_expression/12.png)

***

## 그룹화
- () 표현. 찾은것을 Match 클래스 객체의 Match.Groups 속성에 결과를 넣는다.
- Group[0]은 전체 정규식 패턴에 의해 일치되는 텍스트

![](/assets/images/regular_expression/13.png)

***

## 명명된 일치하는 하위 식
- (?<name>subexpression)  OR (?'name'subexpression)
- Name은 유효한 그룹 이름이고, 뒤는 정규식 패턴
- 이름 또는 번호로 해당 하위 식에 액세스 할 수 있게 해줌

![](/assets/images/regular_expression/14.png)

***

## 대체
- 바꾸기 패턴에서만 인식 . Regex.Replace

![](/assets/images/regular_expression/15.png)



