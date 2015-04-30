# AWS EC2 upstart 설정하기

AWS 의 EC2 의 Auto Scale 설정을 하면 EC2 가 실제로 생성되고 나서의 Action 을 설정할 필요가 있다.
예를들어 Logging 서비스, Server Monitoring 서비스의 기동, 실제 노드 서버 기동 등은 EC2 가 생성(혹은 재부팅)되고 수행되야 한다.

아마존에서는 EC2 의 user data 에 시작 script 를 작성해서 이와 같은 동작을 할 수 있게 해놨지만, 
Linux 의 upstart 설정이 서비스마다 conf 파일로 분리해서 관리할 수 있어서 조금 더 편리하게 느껴졌다. 
물론 conf 의 내용이 바뀌면 AMI 를 새로 생성해야하는 치명적인 단점이 있어서 운영을 해보면서 어떤 방법이 더 효율적인지는 고민이 필요하다.

아래는 papertrail 의 upstart 설정 파일 내용이다.

```
description "Monitor files and send to remote syslog for Volo-API servier"
author      "Jungseob Shin"

start on runlevel [2345]
stop on runlevel [!2345]

respawn

script
  exec remote_syslog -f --hostname "api_as_`echo $HOSTNAME`"
end script

pre-start script
  echo "[`date -u +%Y-%m-%dT%T.%3NZ`] remote_syslog Start" >> /home/ubuntu/logs/ec2.log
  exec /usr/bin/test -e /etc/log_files.yml
end script

pre-stop script
  echo "[`date -u +%Y-%m-%dT%T.%3NZ`] remote_syslog End" >> /home/ubuntu/logs/ec2.log
end script
```

`script` 와 `end script` 안에 시스템의 on 상태에서 수행할 명령어를 작성하면 되고 `pre-start`, `pre-stop` 도 가능하다.
`start on` 과 `stop on` 의 조건으로 runlevel 을 사용했는데, Linux Run Level(Ubuntu 계열) 은 아래와 같다.

```
0.   시스템 종료
1.   단일 사용자 콘솔 모드(복구 모드)
2.   멀티 사용자 GUI 모드 (네트워크 있음)
3-5. 사용하지 않음, 사용자 지정 
6.   다시 부팅
```

EC2 를 재기동해보면 papertrail 대시보드에 api_as_ip-000.000.000.000 식으로 시스템이 새롭게 추가된 것을 확인 할 수 있다.
