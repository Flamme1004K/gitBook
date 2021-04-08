# rc.local

**rc.local 이란 ?** 

* 부팅시 자동실행 명령어 스크립트 수행하며 일반적으로 서버 부팅시마다 매번 자동 실행되길 원하는 명령어를 넣어주는 곳.
* 부팅시 가장 나중에 적용
* 데몬과 같은 것들이 들어간다.

데몬이란 ? 

`멀티태스킹 운영 체제에서 데몬은 사용자가 직접적으로 제어하지 않고, 백그라운드에서 돌면서 여러 작업을 하는 프로그램을 말한다.`

**rc.local 서비스 활성화 :** 

* CentOS 7에서는 /etc/rc.local 에 명령어를 입력해도 재부팅시 실행이 안된다.

```text
chmod u+x /etc/rc.d/rc.local 
systemctl start rc-local.service
```

**rc.local 서비스 확인 :**

```text
systemctl status rc-local.service
```



reboot시 사용가능하게 enable 설정

```text
vi /usr/lib/systemd/system/rc-local.service

[Install]
WantedBy=multi-user.target

systemctl enable rc-local.service

```

서비스 상태 확인

```text
systemctl list-unit-files |grep rc.local
enabled면 활성화 된 상태.
```

