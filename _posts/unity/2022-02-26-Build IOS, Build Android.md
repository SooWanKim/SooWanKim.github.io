---
layout: post
title: Build IOS, Build Android
categories: [C#, Unity]
---

GenericBuild를 사용하는 IOS, Android Build


## 샘플코드

```c#
// 에디터 상단메뉴에 추가
[MenuItem("Build/Build IOS")]
public static void BuildIOS()
{
    PlayerSettings.applicationIdentifier = "com.my.ios";

    // Signing 설정은 수동
    PlayerSettings.iOS.appleEnableAutomaticSigning = false;

    // 원하는 symbol을 넣거나 삭제 할 수 있다.(ex DEBUG, TEST, SDK,,,)
    string defineSymbols = PlayerSettings.GetScriptingDefineSymbolsForGroup(BuildTargetGroup.iOS);
    defineSymbols += ";MySymbol"
    PlayerSettings.SetScriptingDefineSymbolsForGroup(BuildTargetGroup.iOS, defineSymbols);

    PlayerSettings.iOS.buildNumber = "1.0.0";

    char sep = Path.DirectorySeparatorChar;
    string buildDirectory = Path.GetFullPath(".") + sep + "Build";
    Directory.CreateDirectory(buildDirectory);

    // xcode 프로젝트 생성 위치
    string buildTargetPath = buildDirectory + "/iOS";
    Directory.CreateDirectory(buildTargetPath);

    // IL2CPP로 설정
    PlayerSettings.SetScriptingBackend(BuildTargetGroup.iOS, ScriptingImplementation.IL2CPP);

    // BuildSetting에 추가된 Scene 목록 가져 오기
    List<string> sceneList = new List<string>();
    foreach (EditorBuildSettingsScene scene in EditorBuildSettings.scenes)
    {
        if (!scene.enabled)
            continue;

        sceneList.Add(scene.path);
    }

    var enableSceneArray =  sceneList.ToArray();

    Build(enableSceneArray, buildTargetPath, BuildTargetGroup.iOS, BuildTarget.iOS, BuildOptions.None);
}

// 에디터 상단메뉴에 추가
[MenuItem("Build/Build Android")]
public static void BuildAndroid()
{
    PlayerSettings.applicationIdentifier = "com.my.android";

    // 원하는 symbol을 넣거나 삭제 할 수 있다.(ex DEBUG, TEST, SDK,,,)
    string defineSymbols = PlayerSettings.GetScriptingDefineSymbolsForGroup(BuildTargetGroup.Android);
    defineSymbols += ";MySymbol"
    PlayerSettings.SetScriptingDefineSymbolsForGroup(BuildTargetGroup.Android, defineSymbols);

    PlayerSettings.Android.bundleVersionCode = "1.0.0";

    // gradle로 빌드
    EditorUserBuildSettings.androidBuildSystem = AndroidBuildSystem.Gradle;

    char sep = Path.DirectorySeparatorChar;
    string buildDirectory = Path.GetFullPath(".") + sep + "Build";

    // apk 생성 위치
    string buildTargetPath = buildDirectory + "/Android.apk";

    if (Directory.Exists(buildDirectory) == false)
        Directory.CreateDirectory(buildDirectory);

    // BuildSetting에 추가된 Scene 목록 가져 오기
    List<string> sceneList = new List<string>();
    foreach (EditorBuildSettingsScene scene in EditorBuildSettings.scenes)
    {
        if (!scene.enabled)
            continue;

        sceneList.Add(scene.path);
    }

    var enableSceneArray =  sceneList.ToArray();

    Build(m_SceneArray, buildTargetPath, BuildTargetGroup.Android, BuildTarget.Android, BuildOptions.None);
}

static void Build(string[] sceneArray, string targetPath, BuildTargetGroup buildTargetGroup, BuildTarget buildTarget, BuildOptions buildOptions)
{
    Caching.ClearCache();

    EditorUserBuildSettings.SwitchActiveBuildTarget(buildTargetGroup, buildTarget);

    BuildReport buildReport = BuildPipeline.BuildPlayer(sceneArray, targetPath, buildTarget, buildOptions);
    BuildSummary summary = buildReport.summary;
    if (summary.result == BuildResult.Failed)
    {
        throw new Exception("BuildPlayer failure: " + report);
    }

    else if (summary.result == BuildResult.Succeeded)
    {
        Debug.Log("Build Succeeded: " + summary.totalSize + " bytes");
    }
}

```