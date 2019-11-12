---
layout: post
title: Jenkins slave
categories: [Devops]
---

##  SetUp Node

![](/assets/images/2019-09-06-Jenkins%20Slave/2019-09-06-12-26-29.png)

**Labels**는 연결된 Job만 돌리게 설정

***

## example

![](/assets/images/2019-09-06-Jenkins%20Slave/2019-09-06-12-27-24.png)


```
$ ssh-keygen -t rsa


$ cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys
$ mv .ssh/id_rsa .ssh/jenkins-master.key
$ chmod 600 ~/.ssh/jenkins-master.key


$ ssh -i ~/.ssh/jenkins-master.key jenkins@ipaddress
$ scp -i ~/.ssh/jenkins-master.key testfile.txt jenkins@ipaddress:~/target_dir/targetfile.txt

```


```
PROJECT=ProjectTest
EXT=ipa
# FILE=`ls ${WORKSPACE}/ProjectTest/Build/*.apk | head -n1`
FILE=${WORKSPACE}/ProjectTest/Build/iOS/build/ProjectTest.ipa/Unity-iPhone.ipa
TARGET='jenkins@ipaddress:~/web2/static/build'
KEY=~/.ssh/jenkins-master.key

# copy files to target
scp -i ${KEY} ${FILE} ${TARGET}/${PROJECT}.${EXT}
scp -i ${KEY} ${FILE} ${TARGET}/${PROJECT}_${BUILD_NUMBER}.${EXT}
rm -f $FILE

# create meta file
cat << EOF > ${PROJECT}.${EXT}.meta
`date '+%Y-%m-%d %H:%M:%S'`
`echo http://local:8080/job/${JOB_NAME}/${BUILD_NUMBER}`
`echo ${BUILD_NUMBER}`
EOF

# move meta file to target
scp -i ${KEY} ${PROJECT}.${EXT}.meta ${TARGET}/${PROJECT}.${EXT}.meta
rm -f ${PROJECT}.${EXT}.meta

```