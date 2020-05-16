---
layout: post
title: C# MakeGenericMethod
categories: [C#]
---


## 사용 목적

```c#
void Func()
{
    object objClass = MakeClass(); // object를 받는다
    var classType = objClass.GetType();

    GenericClass gClass = new GenericClass();

    // 문제 발생
    gClass.GenericFunc()
}

public class GenericClass
{
    void GenericFunc<T>(T someClass)
    {
        SomeFunc<T>(someClass);
        ~~~~
        ~~~~
    }
}

```


### gClass.GenericFunc() 호출할때 object type이 아닌 classType으로 gClass.GenericFunc()를 호출하고 싶다.

문제는  classType은 변수이기 때문에 gClass.GenericFunc<classType>은 사용할 수 없다.


## 해결책

Reflection의 **MakeGenericMethod** 사용하자.

```c#

void Func()
{
    object objClass = MakeClass(); // object를 받는다
    var classType = objClass.GetType();

    GenericClass gClass = new GenericClass();

    // 문제 해결
    var gClassType = gClass.GetType();
    var method = gClassType.GetMethod("GenericFunc");

    var genericMethod = method.MakeGenericMethod(classType); // 명시적으로 변수인 classType의 인자를 받는 함수를 생성한다.
    genericMethod.invoke(gClass, new object[]{objClass}); // 인자로 objClass, T Type은 object가 아닌 classType.
}


```
