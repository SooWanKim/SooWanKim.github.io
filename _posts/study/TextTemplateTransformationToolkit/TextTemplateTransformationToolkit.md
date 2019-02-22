
 # What is TextTemplateTransformationToolkit?
 
 Text Template Transformation Toolkit (usually referred to as "T4") is a template based text generation framework 
 
 included with Visual Studio.
 
 T4 source files are usually denoted by the file extension ".tt".
 
 T4 is used by developers as part of an application or tool framework to automate the creation of text files with a variety of parameters. 
 
 These text files can ultimately be any text format, such as code (for example C#), XML, HTML or XAML.
 
 
 # T4 텍스트 템플릿을 사용하여 디자인 타임 코드 생성
 
 디자인 타임? 
 
 참조하는 모델을 변경 하거나 수정
 
 모델는 응용 프로그램의 특정 측면을 설명 하는 데이터 원본입니다. 모든 형태와 종류의 파일 또는 데이터베이스일 수 있으며 
 UML 모델이나 DSL(Domain-Specific Language) 모델 등의 특정 형태가 아니어도 됩니다. 일반적인 모델은 테이블 또는 XML 파일 형식입니다.
 
 모델을 변경하는 시점
 
 # 자동 파일 만들기
 
 ![](/_posts/study/TextTemplateTransformationToolkit/1.png)

파일을 만들면 
 
![](/_posts/study/TextTemplateTransformationToolkit/2.png)

사용하는 laguage,  namespace와 확장자.  debug할지 여부( 중단점을 걸면 가능) . hostspecific 밑에서

![](/_posts/study/TextTemplateTransformationToolkit/3.png)

property에 customtool 항목이 TextTemplatingFileGenerator 이렇게 되있어야한다. 이거에 따라 기능이 달라짐

![](/_posts/study/TextTemplateTransformationToolkit/4.png)

TestTextTemplate1.tt파일 밑에 자동으로 TextTextTemplate.txt파일이 만들어진다.

 # 파일 만들어지는 시점
 
1. 템플릿을 편집하고 다른 Visual Studio 창으로 포커스를 변경
2. 템플릿을 저장하는 경우
3. Build Menu에서 Transform All T4 Template 누르기.Visual Studio 솔루션에서 모든 템플릿을 변환
4. 템플릿이 읽는 데이터 파일이 변경 된 경우 실행 되도록 Visual Studio 프로젝트를 설정가능

# 변수 텍스트 생성

```markdown
<#@ template hostspecific="false" language="C#" #>
<#@ output extension=".txt" #>
<#int top = 10;

for (int i = 0; i<=top; i++)
{ #>
   The square of <#= i #> is <#= i*i #>
<# } #>
```
출력결과물
```markdown
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
```markdown
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
```markdown
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

# 텍스트 템플릿 구조 지정

실제 프로그래밍에서는 대개 템플릿 코드를 다음의 두 부분으로 구분

변수에서 값은 설정하지만 텍스트 블록은 포함하지 않는 구성(데이터 수집) 부분. 위의 예에서 이 부분은 properties 초기화

변수의 값을 사용하는 텍스트 생성 부분. 위의 예에서는 foreach(...){...}에 해당

코드를 반드시 이와 같이 분리해야 하는 것은 아니지만 이 스타일을 사용하면 텍스트를 포함하는 부분을 보다 단순하게 작성하여 

템플릿을 더 쉽게 읽을 수 있다고 함

# 파일 또는 기타 소스 읽기

```markdown
<# var properties = File.ReadLines("C:\\propertyList.txt");#>
...
<# foreach (string propertyName in properties) { #>
...
```

# 상대 경로 이름을 사용하여 파일 열기

텍스트 템플릿을 기준으로 하는 위치에서 파일을 로드하려는 경우 this.Host.ResolvePath()를 사용

this.Host를 사용하려면 hostspecific="true"

```markdown
<# string fileName = this.Host.ResolvePath("filename.txt");
  string [] properties = File.ReadLines(filename);
#>
...
<#  foreach (string propertyName in properties { #>
...
```

# 파일을 읽어서 내용을 변수 이름으로 할당하기
```markdown

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
// readFile.txt에 myVariable 입력했으면 string 변수는 myVariable

# T4 텍스트 템플릿을 사용하여 런타임 텍스트 생성

Text Template로 파일 만들고 Property를  TextTemplatingFilePreprocessor 이걸로 변경

tt파일 만들기 
```markdown

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

```markdown
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
위에것 실행했을때 아래 파일이 만들어짐
```markdown
public class PlayerabcTest
{
	public void Foo1()
	{

	}
}
```

# 유니티에서 사용하기

1. tt 확장자 File, 속성은 TextTemplatingFilePreprocessor
```markdown
<#@ template debug="true" hostspecific="true" language="C#" #>
<#@ assembly name="System.Core" #>
<#@ import namespace="System.Linq" #>
<#@ import namespace="System.Text" #>
<#@ import namespace="System.Collections.Generic" #>
<#@ parameter type="System.String" name="StateName" #>
<#@ output extension=".cs" #>
using UnityEngine;
using System.Collections;
using System;

namespace Fish_
{
	public class <#=StateName#>State : State
	{
		FSMFish m_FSMFish = null;
		// Fish m_Fish;

		public <#=StateName#>State(FSMAnimation fsmAnimation) : base(fsmAnimation)
		{
			m_FSMFish = fsmAnimation as FSMFish;
		}

		public override void OnStart()
		{
			if (m_FSMFish == null)
				return;

			//m_Fish = m_FSMFish.fish;

			if (m_FSMFish.animator != null)
			{
				//m_FSMFish.animator.ResetTrigger(MetaData.k_Hash_Move);
				m_FSMFish.animator.SetTrigger(MetaData.k_Hash_Move);
			}
		}
	}
}
```

2. CodeGeneratorEditor

```markdown
FishStateTemplate page = new FishStateTemplate();
page.Session = new TextTemplatingSession(); 
page.Session["StateName"] = m_ClassName;
page.Initialize();

string pageContent = page.TransformText();
System.IO.File.WriteAllText($"{filePath}/Fish{m_ClassName}State.cs", pageContent);
```
TextTemplatingSession 이것을 사용하려면 
https://www.nuget.org/packages/Mono.TextTemplating/ 를 다운받아야 되고
Editor가 Mono.TextTemplating.dll을 포함해야된다.
