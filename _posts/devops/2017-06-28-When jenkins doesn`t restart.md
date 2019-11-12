---
layout : post
title : When jenkins doesn`t restart
categories: [Devops]
---

## Jenkins가 Restart가 안될때

이미 젠킨스가 사용하는 port번호를 다른 PID가 사용하고 있을때 젠킨스가 Restart가 안된다. 그럴때는 cmd에서

```
netstat -a -n -o
```

로 젠킨스 port번호를 차지하고 있는 PID를 찾은다음 taskManager에서 종료 시킨후 다시 Jenkins 를 reload, restart를 한다
