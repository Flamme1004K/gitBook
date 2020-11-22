# ps aux \| grep 프로세스이름

ps aux \| grep 프로세스 이름

![](https://k.kakaocdn.net/dn/cVPi98/btqCtXe0lqg/BpVFybuRqKb1qxwVfdEZ2k/img.jpg)

여기서 STAT는 

- R\(Runable\) : 실행 대기 상태

- S\(Sleeping\) : 수면 상태

- D\(inDIskwait\) : 입출력을 기다리는 상태

- T\(sTopped\) : 멈춰 있거나 흔적이 남아있는 상태

- Z\(Zombie\) : 죽었지만 프로세스에 남아있는 상태 \(자원 낭비\)

  
  
출처: [https://eehoeskrap.tistory.com/245](https://eehoeskrap.tistory.com/245) \[Enough is not enough\][ \[Linux\] 프로세스 확인하기1. 프로세스 목록 확인 ~$ ps 2. 프로세스 목록 확인 - 자세한 정보 ~$ ps -f 3. 모든 프로세스 리스트 확인 ~$ ps -e ~$ ps -ef 4. 프로세스 목록 배열 및 시스템 자원 사용률 확인 ~$ ps -aux \* -aux 옵션으로..eehoeskrap.tistory.com](https://eehoeskrap.tistory.com/245)

aux 뜻 : : 프로세스 현황 표시, 유저지향,  터미널 제어 없이 프로세스 현황 보기

grep 뜻 : 문자열을 찾는다.

즉 ps aux \| grep 프로세스 이름의 뜻은

프로세스 이름으로 된 녀석을 grep으로 찾고 이 프로세스의 정보를 aux로 나타낸다.

ex\) 프로세스 kill도 할 수 있다고함.

