---
layout: post
title: Sending mail using jenkins pipeline
categories: [Devops]
---

## 기능

jenkins build 결과를 post에서 success, fail 체크해서 빌드 결과 및 job의 정보를 메일로 보낸다.

## pipeline code

아래 script를 pipeline code 젤 밑에 달아주면 된다.

```
post {
        success{
            echo "success"
            mail (bcc: '',
                subject: """ - Successful - ${env.JOB_NAME}""",
                body: """BUILD_TIME:${env.CLIENT_BUILD_TIME}\nBUILD_NUMBER:${BUILD_NUMBER}\nBUILD_URL:"${env.BUILD_URL}" """,
                cc: '',
                from: '받는사람메일주소',
                replyTo: '',
                to: '보내는사람메일주소')
        }
        failure{
            echo "failure"
            mail (bcc: '',
                subject: """ - Failure - ${env.JOB_NAME} - BuildNumber:${BUILD_NUMBER}  currentBuild.displayName """,
                body: """BUILD_TIME:${env.CLIENT_BUILD_TIME}\nBUILD_NUMBER:${BUILD_NUMBER}\nBUILD_URL:"${env.BUILD_URL}" """,
                cc: '',
                from: '받는사람메일주소',
                replyTo: '',
                to: '보내는사람메일주소')
        }
    }
```

body가 메일내용

env.CLIENT_BUILD_TIME는 BuildTimeStamp plugin이 설치되어 있어야 한다.