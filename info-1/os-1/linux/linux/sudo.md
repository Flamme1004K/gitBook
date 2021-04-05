# Su, Sudo

 sudo \(super user do\)

 Unix 계열의 운영체제의 특별한 기능은 다중 사용자 개념이다.

 하나의 운영체제를 여러 사람들끼리 사용하게되면, A라는 사람이 B라는 사람의 파일을 사용할 수 있게 만들 수 없기 때문에 권한\(Permission\)이라는 개념을 만들었다.

 여기서 가장 강한 권한이 바로 sudo이다. 

sudo : 현재 계정에서 다른 계정의 권한만 빌림

* /etc/sudoers 파일에 지정된 사용자만 sudo 명령어를 사용할 수 있다.
* sudo  : 해당 계정이 root 권한을 가지게함.
  * i -&gt; login
  * s -&gt; target user에 대한 shell을 사용할 수 있음.
* journalctl -t sudo -f 를 통하여 sudo 명령어를 이용한 제스처를 볼 수 있음.

su\(Swich user\) : 다른 계정으로 전환

su - : 다른계정으로 전환 + 그 계정의 환경변수 적용

who am i : 현재 계정에 대해 알려줌.

 

 



