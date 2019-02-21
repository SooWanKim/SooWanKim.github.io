
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

사용하는 laguage,  namepspace와 확장자.  debug할지 여부( 중단점을 걸면 가능) . hostspecific이거는 모름

![](/_posts/study/TextTemplateTransformationToolkit/3.png)

property에 customtool 항목이 TextTemplatingFileGenerator 이렇게 되있어야한다. 이거에 따라 기능이 달라짐

![](/_posts/study/TextTemplateTransformationToolkit/4.png)

TestTextTemplate1.tt파일 밑에 자동으로 TextTextTemplate.txt파일이 만들어진다.

 # 파일 만들어지는 시점
 
1. 템플릿을 편집하고 다른 Visual Studio 창으로 포커스를 변경
2. 템플릿을 저장하는 경우
3. Build Menu에서 Transform All T4 Template 누르기.Visual Studio 솔루션에서 모든 템플릿을 변환
4. 템플릿이 읽는 데이터 파일이 변경 된 경우 실행 되도록 Visual Studio 프로젝트를 설정할 수 있습니다. 

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
