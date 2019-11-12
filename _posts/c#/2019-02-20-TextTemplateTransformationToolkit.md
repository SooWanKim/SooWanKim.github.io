---
layout: post
title: C# Test TextTemplateTransformationToolkit
categories: [C#]
---

# What is TextTemplateTransformationToolkit?

Text Template Transformation Toolkit의 약자

템플릿 기반 텍스트 생성 프레임워크 (included with Visual Studio)

확장자 .tt 사용

T4 is used by developers as part of an application or tool framework to automate the creation of text files with a variety of parameters.

These text files can ultimately be any text format, such as code (for example C#), XML, HTML or XAML.

***

# T4 텍스트 템플릿을 사용하여 디자인 타임 코드 생성

모델을 변경하는 시점에 참조하는 모델을 변경 하거나 수정

***

# 자동 파일 만들기

Text Template타입의 파일을 만들면

![](/assets/images/TextTemplateTransformationToolkit/1.png)

아래와 같은 내용이 자동 생성된다. (사용하는 laguage, namespace와 확장자, debug할지 여부)

![](/assets/images/TextTemplateTransformationToolkit/2.png)

property에 customtool 항목이 TextTemplatingFileGenerator 이렇게 되있어야 자동생성 기능이 됨.(이 속성에 따라 기능이 달라진다)

![](/assets/images/TextTemplateTransformationToolkit/3.png)

위의 파일 수정 후 같은 이름의 .txt 확장 파일이 자동으로 만들어 진다.

![](/assets/images/TextTemplateTransformationToolkit/4.png)

***

# 파일이 만들어지는 시점 4개

1. 템플릿을 편집하고 다른 Visual Studio 창으로 포커스를 변경
2. 템플릿을 저장하는 경우
3. Build Menu에서 Transform All T4 Template 누르기.Visual Studio 솔루션에서 모든 템플릿을 변환
4. 템플릿이 읽는 데이터 파일이 변경 된 경우 실행 되도록 Visual Studio 프로젝트를 설정가능

***

# 변수 텍스트 생성

```c#
<#@ template hostspecific="false" language="C#" #>
<#@ output extension=".txt" #>
<#int top = 10;

for (int i = 0; i<=top; i++)
{ #>
   The square of <#= i #> is <#= i*i #>
<# } #>
```
출력결과물
```c#
 The square of 0 is 0
 The square of 1 is 1
 The square of 2 is 4
 The square of 3 is 9
 The square of 4 is 16
 The square of 5 is 25
 The square of 6 is 36
 The square of 7 is 49
 The square of 8 is 64
 The square of 9 is 81
 The square of 10 is 100
```

변수 사용구문은 <# 내용 #> 이렇게

클래스 변수에 템플릿 정보 사용
```c#
<#@ template debug="false" hostspecific="false" language="C#" #>
<#@ output extension=".cs" #>
<# var properties = new string [] {"P1", "P2", "P3"}; #>
// This is generated code:
class MyGeneratedClass {
<# // This code runs in the text template:
  foreach (string propertyName in properties)  { #>
  // Generated code:
  private int <#= propertyName #> = 0;
<# } #>
}
```

출력결과물
```c#
// This is generated code:
class MyGeneratedClass {
  // Generated code:
  private int P1 = 0;
  // Generated code:
  private int P2 = 0;
  // Generated code:
  private int P3 = 0;
}
```

프로그램 코드를 생성할 때는 템플릿에서 실행되는 생성 코드와 그 결과로 생성된, 솔루션의 일부분이 되는 코드를 혼동하지 않아야됨.

***

# 텍스트 템플릿 구조 지정

실제 프로그래밍에서는 대개 템플릿 코드를 다음의 두 부분으로 구분

1. 변수에서 값은 설정하지만 텍스트 블록은 포함하지 않는 구성(데이터 수집) 부분. 위의 예에서 이 부분은 properties 초기화

2. 변수의 값을 사용하는 텍스트 생성 부분. 위의 예에서는 foreach(...){...}에 해당

코드를 반드시 이와 같이 분리해야 하는 것은 아니지만 이 스타일을 사용하면 텍스트를 포함하는 부분을 보다 단순하게 작성하여 템플릿을 더 쉽게 읽을 수 있다고 한다.

***

# 파일 또는 기타 소스 읽기

```c#
<# var properties = File.ReadLines("C:\\propertyList.txt");#>
...
<# foreach (string propertyName in properties) { #>
...
```

***
# 상대 경로 이름을 사용하여 파일 열기

텍스트 템플릿을 기준으로 하는 위치에서 파일을 로드하려는 경우 this.Host.ResolvePath()를 사용

this.Host를 사용하려면 hostspecific="true"

```c#
<# string fileName = this.Host.ResolvePath("filename.txt");
  string [] properties = File.ReadLines(filename);
#>
...
<#  foreach (string propertyName in properties { #>
...
```

***

# 파일을 읽어서 내용을 변수 이름으로 할당하기
```c#
<#@ template debug="false" hostspecific="true" language="C#" #>
<#@ assembly name="System.Xml.dll" #>
<#@ import namespace="System.Xml" #>
<#@ import namespace="System.IO" #>
<#@ output extension=".cs" #>
<# string fileName = this.Host.ResolvePath("readFile.txt");#>
<#
	var fileContents = File.ReadAllText(fileName);
#>

// This is generated code:
class MyGeneratedClass {

 private string <#= fileContents #>;

}

```
readFile.txt에 myVariable 입력했으면 string 변수는 myVariable

***

# T4 텍스트 템플릿을 사용하여 런타임에 텍스트 생성

Text Template로 파일 만들고 Property를  TextTemplatingFilePreprocessor 이걸로 변경(**중요**)

tt파일 만들기
```c#
<#@ template debug="true" hostspecific="true" language="C#" #>
<#@ assembly name="System.Core" #>
<#@ import namespace="System.Linq" #>
<#@ import namespace="System.Text" #>
<#@ import namespace="System.Collections.Generic" #>

<#@ parameter type="System.String" name="StateName" #>

<#@ output extension=".cs" #>

public class Player<#=StateName#>Test
{
	public void Foo1()
	{

	}
}

```

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Diagnostics;
using Microsoft.VisualStudio.TextTemplating;
using System.IO;

namespace T4Generate_
{
    class Program
    {
        static void Main(string[] args)
        {
            TextTemplate1 page = new TextTemplate1();
            page.Session = new TextTemplatingSession();
            page.Session["StateName"] = "abc";
            page.Initialize();

            string pageContent = page.TransformText();
            System.IO.File.WriteAllText("../outputPage.cs", pageContent);
        }
    }
}

```
위에 파일을 실행 했을때 아래 파일이 런타임에 만들어짐
```c#
public class PlayerabcTest
{
	public void Foo1()
	{

	}
}
```

