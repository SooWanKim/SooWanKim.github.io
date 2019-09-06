---
layout: post
title: jenkins commandLine Parsing
categories: [Unity, CI%2CCD]
---

Jenkins로 유니티 빌드를 할때 static함수를 호출하는데 여기에 인자를 전달할 방법이 없다.
예를 들어 level asset을 뽑는다고 할 시, export_level_1(), export_level_2() 이렇게 함수를 만들고 호출해야 한다.
이러한 문제점을 해결하기 위해서 jenkins build시 호출되는 commandLine을 parsing하여 인자로 사용하게 작업
파싱하는 함수는 아래와 같다.

```c#
public static string GetCommand(string strLabel)
   {
       string[] args = System.Environment.GetCommandLineArgs();
       string strArg = string.Empty;
 
       for(int i=0; i< args.Length; i++)
       {
           strArg = args[i];
 
           if(strArg.Contains(strLabel) == true)
           {
               string[] strSplitLabels = strArg.Split(':');
               if(strSplitLabels.Length > 1)
               {
                   return strSplitLabels[1];
               }
 
               return strArg;
           }
       }
 
       return string.Empty;
   }
```


# example jenkins batch command 
-quit -batchmode -nographics -executeMethod ProjectBuilder.BuildiOS_Distribution -BundleName:com.my.org 
 

 
# example parsing commandline code
```c#
public staic void BuildiOS_Distribution ()
{
    string bundleName = GetCommand("-BundleName");
    // bundleName  == com.my.org    파싱 된다
}
```