---
layout: post
title: Jenkins unity project compile check
categories: [Devops, Unity]
---

## 목적

일반적으로 개별 작업자들 환경에서는 compile error가 나지 않지만, 빌드 해야되는 저장소(Jenkins에서 설정한)에서는 error가 날 수 있어서(define, platform, code 미반영의 이유로)
빌드를 진행 해야하는 담당자가 빌드를 진행할시에 이것을 체크하기 힘들고, 시간도 오래 걸리고, 원인 추적하기도 힘들다.

**unity build를 jenkins로 진행할 때 define 설정이나 플랫폼에 따른 빌드실패가 종종 발생해서 진행**


## jenkins에서 plugin 설정방법


Warning Next Generation 플러그인을 젠킨스 마스터에 설치하고

[https://plugins.jenkins.io/warnings-ng](https://plugins.jenkins.io/warnings-ng)

job configure에서 post build를 누르고 Record compiler warnings and static analysis results를 설정한다.


![](/assets/images/2020-01-16-Jenkins%20code%20build%20check/2020-01-16-18-04-01.png)


빌드체크를 위해 MSBuild 선택, Java, Kotlin  엄청 다양하다.

![](/assets/images/2020-01-16-Jenkins%20code%20build%20check/2020-01-16-18-05-10.png)


advanced에서 아래 flag 체크

![](/assets/images/2020-01-16-Jenkins%20code%20build%20check/2020-01-16-18-05-36.png)

pipeline으로도 세팅 가능하다.
[https://github.com/jenkinsci/warnings-ng-plugin/blob/master/doc/Documentation.md#migration-of-pipelines](https://github.com/jenkinsci/warnings-ng-plugin/blob/master/doc/Documentation.md#migration-of-pipelines)

## 작업 결과

우선 강제로 compile에러가 나게 code commit(jenkis라고 오타남 ㅋ)

![](/assets/images/2020-01-16-Jenkins%20code%20build%20check/2020-01-16-18-07-59.png)


link타고 들어가면 어디서 에러났는지 정확히 보여준다. 어떤 에러가 추가 되고, 계속 유지 되고 수정됬는지 차트로 보여 준다.
실패난곳 위치 말고 코드 전체를 보여주는건 좋은거 같다.

![](/assets/images/2020-01-16-Jenkins%20code%20build%20check/2020-01-16-18-09-14.png)


## pipeline 코드


```
stages
{
    stage('Setting Unity')
    {
        steps
        {
            script
            {
                // 실패해도 Build를 진행해야 되기 때문에 try, catch 처리
                try
                {
                     여기서 유니티 빌드 설정 및 플랫폼 변경, 심볼 변경
                }
                catch(e){}
            }
        }
    }
    stage('Build')
    {
        steps
        {
            // 이 부분은 try catch로 안넣어도 되는데, 넣은 이유-> 동시에 여러 빌드를 진행할때 아래 stage도 계속 진행 하려면 실패해도 넘어가게 해야한다.
            script
            {
                try
                {
                       bat "\"C:/경로/MSBuild.exe\" MyRepo/MySolution.sln /p:Configuration=Release /p:Platform=\"Any CPU\" /m "
                }
                catch(e){}
            }
        }
    }
}
post
{
    always
    {
        ScanIssues()
    }
}


//빌드 레포팅 처리 및 메일 발송
def ScanIssues()
{
     def buildissue = scanForIssues tool: msBuild()
                def action = publishIssues issues: [buildissue]
                def result = action.getResult()
                def newIssues = result.getNewSize()
                def totalErrorSize = result.getTotalErrorsSize()

                println(result)
                println(newIssues)
                println(totalErrorSize)

                // 이슈 상세정보 로그 출력
                String totalIssue = ''
                def infoMessages = result.getIssues()
                println(infoMessages.size())
                infoMessages.each{
                    item -> println(item)
                    totalIssue += item
                    totalIssue += "\r\n"
                }

				if( totalErrorSize != 0)
				{
					def errorURL = "${BUILD_URL}msbuild/new"

					mail (bcc: '',
					subject: """Test """,
					body: """ error!!\n ${result}\n error log : ${errorURL} """,
					cc: '',
					from: '보내는사람.co.kr',
					replyTo: '',
					to: '받는사람.co.kr')
				}

}

```

post에서 always로 체크하는건 유니티 define 세팅을 먼저 불러와서 설정을 변경해야하는데, 이때 컴파일 에러가 나면 밑에 msbuild를 돌릴 수 없다
그래서 try,catch 처리를 하는데 이 처리로 인해 항상 성공하게 된다, 그래서 원래는 failure로 해야되는데, always로 해놓음
