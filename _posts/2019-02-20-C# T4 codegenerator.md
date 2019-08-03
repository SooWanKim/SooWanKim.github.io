---
layout: post
title: C# T4 code generator로 cs 파일 자동 생성하기
categories: [C#]
---

**T4를 사용해서 cs 파일 변수 전달해서 만들기**


```markdown
# Main

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
```


``` markdown
# tt 확장자 파일
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

```markdown
# 유니티에서 Editor로 state 코드 생성하기 
# https://www.nuget.org/packages/Mono.TextTemplating/  TextTemplating를 받아야 사용가능

using UnityEngine;
using UnityEngine.UI;
using System.IO;
using System.Collections.Generic;
using UnityEngine.U2D;
using Sirenix.OdinInspector;

#if UNITY_EDITOR
using Microsoft.VisualStudio.TextTemplating;
using UnityEditor;
using Assets.Editor.Scripts.CodeGen.Editor;

public class CodeGeneratorEditor : EditorWarpper
{
    const string k_SavedPath = "CodeGeneratorEditor";

    enum GenerateType
    {
        none,
        player,
        fish, 
    }

    GenerateType m_CodeType = GenerateType.player;
    string m_CodeGenFilePath = null;
    string m_ClassName = null;

    [MenuItem("EidtorWindow/CodeGen", priority = 96)]
    static public void ShowSceneListWindow()
    {
        CodeGeneratorEditor generatorWindow = GetWindow<CodeGeneratorEditor>();
        generatorWindow.Show();
    }

    void OnEnable()
    {
        m_CodeGenFilePath = PlayerPrefs.GetString(k_SavedPath);
    }

    private void OnGUI()
    {
        OnCodeType();
        if (m_CodeType != GenerateType.none)
        {
            OnClassName();
            OnCodeGenerate();
        }
    }

    void OnCodeType()
    {
        GUILayout.BeginHorizontal();

        m_CodeType = (GenerateType)EditorGUILayout.EnumPopup(m_CodeType, GUILayout.Width(100.0f), GUILayout.ExpandWidth(false));

        GUILayout.EndHorizontal();
    }

    void OnClassName()
    {
        GUITitleText("class Name", ref m_ClassName, 200, 20.0f, false, false);
    }

    void OnCodeGenerate()
    {
        if (GUILayout.Button(new GUIContent("Code 생성", null, ""), GUILayout.Width(120.0f), GUILayout.Height(20.0f), GUILayout.ExpandWidth(false)) == true)
        {
            if (string.IsNullOrEmpty(m_ClassName))
            {
                EditorUtility.DisplayDialog("class is null", "input class name", "error");
                return;
            }

            string filePath = EditorUtility.OpenFolderPanel("File Path", m_CodeGenFilePath, "");

            if (string.IsNullOrEmpty(filePath))
                return;

            m_CodeGenFilePath = filePath;

            PlayerPrefs.SetString(k_SavedPath, m_CodeGenFilePath);

            switch (m_CodeType)
            {
                case GenerateType.player: 
                    {
                        PlayerStateTemplate page = new PlayerStateTemplate();
                        page.Session = new TextTemplatingSession(); 
                        page.Session["StateName"] = m_ClassName;
                        page.Initialize();

                        string pageContent = page.TransformText();
                        System.IO.File.WriteAllText($"{filePath}/Player{m_ClassName}State.cs", pageContent);

                        break;
                    }
                case GenerateType.fish:   
                    {
                        FishStateTemplate page = new FishStateTemplate();
                        page.Session = new TextTemplatingSession(); 
                        page.Session["StateName"] = m_ClassName;
                        page.Initialize();

                        string pageContent = page.TransformText();
                        System.IO.File.WriteAllText($"{filePath}/Fish{m_ClassName}State.cs", pageContent);
                        break;
                    }
            }
        }
    }
}


#endif
	
	

```
