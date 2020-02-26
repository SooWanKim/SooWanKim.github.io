---
layout: post
title: Jenkins SSH Publisher
categories: [Devops]
---

## 목적

Jenkins에서 Build 후 실행 파일 배포

## 사용 플러그인

[https://wiki.jenkins.io/display/JENKINS/Publish+Over+SSH+Plugin](https://wiki.jenkins.io/display/JENKINS/Publish+Over+SSH+Plugin)

## Pipeline Code

```groovy
def ssh_publisher(CONFIG, SOURCE, TARGET)
{
     sshPublisher(continueOnError: false, failOnError: true,
        publishers:
        [
            sshPublisherDesc
            (
                configName: "${CONFIG}",
                verbose: true,
                transfers:
                [
                    sshTransfer
                    (
                        sourceFiles:        "${SOURCE}",
                        //removePrefix:       "${REMOVE}",
                        remoteDirectory:    "${TARGET}",
                        execCommand:        "echo COMPLETE"
                    )
                ]
            )
        ]
    )
}

```

CONFIG는 Jenkins Config에서 ssh plugin에서 만든 Server 이름

sourceFiles 복사하고 싶은 파일

remoteDirectory는 옮겨질 장소

removePrefix는 복사후 지울 파일 및 폴더

execCommand 끝나고 실행할 명령어


## pscp.exe(putty)

remote linux file -> window space