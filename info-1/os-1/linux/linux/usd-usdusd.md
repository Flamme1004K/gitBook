# ; , & , &&, {}

 ; - 앞의 명령어가 실패해도 다음 명령어가 실행

&& : 앞의 멸령어가 성공했을 때 다음 명령어가 실행

& - 앞의 명령어를 백그라운드로 돌리고 동시에 뒤의 명령어를 실

{} - 명령어 그룹핑 



 `mkdir` `test3 && {` `cd` `test3;` `touch` `abc;` `echo` `'success!!'` `} ||` `echo` `'There is no dir';`
