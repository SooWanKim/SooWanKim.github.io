---
layout: post
title: Jenkins unity build error parser
categories: [Devops, Python, Unity]
---


## 사용 목적

Jenkins에서 실행되는 Unity Build Error를 요약하여 원하는곳(메신저, 아웃룩)에 알림

Unity build방식이 unity static 함수를 jenkins에서 bat을 통해 실행하는 방식이라 runtime에 나오는 error를 알아야 한다.(함수가 호출되고 나오는 error)

compile time ([Jenkins-code-build-check](https://soowankim.github.io/devops/gameengine/2020/01/16/Jenkins-code-build-check.html))은 스케쥴링하면서

체크하고 있음


## 사용 방법


```groovy
Unity.exe -quit -batchmode -nographics -executeMethod MyBuilder.BuildTest -projectPath "D:/project/unity/myproject" -buildTarget android -logFile "D:/

project/unity/myproject/my_log.txt"
```

1. MyBuilder.BuildTarget라는 빌드용 함수를 호출하고, 빌드시에 생성되는 log를 지정된 경로에 만든다.
2. 빌드 실패시 간단한 규칙으로 파싱하는 parser를 실행
3. parser에서 unity error만 추출한 별도의 파일 생성
4. jenkins pipeline에서 추출된 log 파일 read
5. 메신저 및 메일로 발송


## python으로 unity log 추출 parser 만들기

```python

import sys

CONST_FIRST_ERROR_STRING = "-----CompilerOutput:-stderr----------\n"
CONST_LAST_ERROR_STRING = "-----EndCompilerOutput---------------\n"

## sys.argv[1]는 Unity_error_log 위치
def extract_log():

    file_name = sys.argv[1]
    f = open(file_name, "r")
    error_list = []
    exist_compile_error = False
    exist_runtime_error = False

    lines = f.readlines()
    line_count = -1
    for line in lines:
        line_count += 1

        if line == CONST_FIRST_ERROR_STRING:
            exist_compile_error = True
            continue

        if exist_compile_error is True:
            if "error" in line:
                error_list.append(line)

        if line == CONST_LAST_ERROR_STRING:
            exist_compile_error = False
            continue

        if "threw exception" in line:
            exist_runtime_error = True
            break

    if exist_runtime_error is True:
        error_list.append("\n")
        error_list.append(lines[line_count - 5])
        error_list.append(lines[line_count - 4])
        error_list.append(lines[line_count - 3])
        error_list.append(lines[line_count - 2])
        error_list.append(lines[line_count - 1])
        error_list.append(lines[line_count])
        print(lines[line_count])
        pass

    f.close()

    return error_list

## sys.argv[2]는 build error log 생성하는 위치
def write_error_log(error_list):
    error_log_file_path = sys.argv[2] + "build_error_log.txt"
    f = open(error_log_file_path, "w")
    for log in error_list:
        f.write(log)

    f.close()
    pass


error_list = extract_log()
write_error_log(error_list)


```

## error log 읽고 발송하는 jenkins pipeline code

```groovy

post
    {
       always
       {

        script
         {

            if( "${currentBuild.result}" == "FAILURE")
           {
               bat ( script: "D:/project/unity/myproject/jenkins_error_log_parser.exe D:/project/unity/myproject/my_log.txt D:/project/unity/myproject/" ,  returnStdout: true )

               def error_log = readFile 'D:/project/unity/myproject/build_error_log.txt'
               println(error_log)

               mail (bcc: '',
               subject: """제목""",
               body: """<p> <font size="2" color="red">error!!<br> ${error_log}<br><br>error log : ${BUILD_URL}</font></p>""",
               mimeType: 'text/html',
               cc: '',
               from: '보내는 사람@co.kr',
               replyTo: '',
               importance: true,
               to: '받는사람@.co.kr')
           }
         }

        }
    }

```

