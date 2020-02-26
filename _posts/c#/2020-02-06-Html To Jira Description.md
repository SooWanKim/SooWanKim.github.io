---
layout: post
title: C# Html To Jira Description
categories: [C#]
---

## 목적

웹크롤러로 얻어온 Html 정보로 Jira Issue 생성할 때,  Jira의 Description의 renderer를 변경할 수 가 없어서

Html Tag들이 있는 text들을 Description에 넣을때 Html tag들을 text, img, table에 맞게 별도로 파싱 해야되기 때문에 이렇게 안하려고 자동으로 html -> wiki

renderer을 할 수 있는 방법을 찾아봄. c#으로 만든거라 c#에서 사용할 수 있는 패키지를 이것저것 찾아봤는데

**ReverseMarkdown**이라는 엄청 좋은게 있었음

[https://github.com/mysticmind/reversemarkdown-net](https://github.com/mysticmind/reversemarkdown-net)

이름그대로 html정보를 쉽게 markdown형식으로 컨터팅 해준다. ("a href", "th", "td")

**지원해주는 tag**

![](/assets/images/2020-02-06-Html%20To%20Jira%20Description/2020-02-06-19-58-11.png)


## 사용 방법


```cs
var converter = new ReverseMarkdown.Converter();

string html = "This a sample <strong>paragraph</strong> from <a href=\"http://test.com\">my site</a>";

string result = converter.Convert(html);

```

엄청 간단하게 쓸수 있고, 자동으로 변환까지 해줌.


## Jira에 사용

Jira에 쓸려고하니 Description에 이미지를 넣을때 문제가 생김

Jira의 이미지 링크 포맷은 "!https://img.png | width=800, height=400!" 이런 방식임

ReverseMarkDown은 markdown형식으로 아래 처럼 return 한다.

```cs
return $"![{StringUtils.EscapeLinkText(alt)}]({src}{title})";
```


파싱해서 리턴하는 부분만 Jira에 맞게 아래 처럼 변경

```cs
return $"!{src}|width=800, height=400!";
```

