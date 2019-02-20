---
layout: post
title: C# T4 code generator로 cs 파일 자동 생성하기
---

**T4를 사용해서 cs 파일 변수 전달해서 만들기**

```markdown
*main*

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Diagnostics;
using Microsoft.VisualStudio.TextTemplating;

namespace T4Generate_
{
    class Program
    {
        static void Main(string[] args)
        {
            TextTemplate1 page = new TextTemplate1();
            page.Session = new TextTemplatingSession();
            page.Session["StateName"] = "abc"; // 변수전달 
            page.Initialize();

            string pageContent = page.TransformText();
            System.IO.File.WriteAllText("../outputPage.cs", pageContent); // 파일 생성

        }
    }
}

*tt 확장자 파일*

<#@ template debug="true" hostspecific="true" language="C#" #>
<#@ assembly name="System.Core" #>
<#@ import namespace="System.Linq" #>
<#@ import namespace="System.Text" #>
<#@ import namespace="System.Collections.Generic" #>

<#@ parameter type="System.String" name="StateName" #> // 변수 설정

<#@ output extension=".cs" #> // 확장자

public class Player<#=StateName#>Test
{
	public void Foo1()
	{

	}
}

```
