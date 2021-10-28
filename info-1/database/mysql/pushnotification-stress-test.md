# PushNotification Stress Test

PushNotification Stress Test 방법 입니다.

현재 방법은 mysql event Scheduler를 사용하여 매번 Data를 Insert하여 notification 처리 test를 하는 방법입니다.

1. mysql Procedure을 생성합니다.

``CREATE PROCEDURE [프로시저이름]() BEGIN DECLARE i INT DEFAULT 1; WHILE i <= [횟수] DO INSERT INTO talk_tblnotification (`UserID`, `PostDate`, `Sender`, `Subject`, `Type`, `LinkURL`, `ShowMsg`) VALUES ( CASE WHEN i%3 = 1 THEN [테스트아이디1] WHEN i%3 = 2 THEN [테스트아이디2] WHEN i%3 = 0 THEN [테스트아이디3] end , now(), i, 'test', '1', '1', 'N'); SET i = i + 1; END WHILE; END``

2\. event Scheduler가 켜져있는지 확인합니다.`SHOW VARIABLES LIKE 'event%'; – 이벤트 스케쥴러 on/off 확인 SELECT * FROM information_schema.events; – 이벤트 스케쥴러 조회`

3\. mysql event Scheduler를 생성합니다.`CREATE EVENT IF NOT EXISTS [이벤트이름] ON SCHEDULE EVERY 30 SECOND -- 30초마다 실행하도록 함. STARTS '2020-12-22 16:37:00' --시작 시간 ENDS '2020-12-22 17:37:00' -- 종료 시간 ON COMPLETION NOT PRESERVE DO call [1번에서 만든 프로시저 ];`

이벤트 스케쥴러가 On인 상태에서 현재 스케쥴러를 만들면 바로 실행이 되기 때문에

특별한 이벤트 스케쥴러가 없다면 event Scheduler를 off하여 등록하고 그 후에 on 해주시길 바랍니다.`SET GLOBAL event_scheduler = ON; -- 이벤트 스케쥴러 On SET GLOBAL event_scheduler = OFF; -- 이벤트 스케쥴러 off`

또한 이벤트 스케쥴러는 사용자에 대한 이벤트 사용권한이 필요한 작업이므로 root 계정 혹은 해당 계정에 권한을 주시길 바랍니다.

4\. Server Log 와 DB를 확인합니다.

Server Log - Notification 요청이 제대로 넘어가는지 혹은 fail로 떨어지는지 확인을 해봐야함.

DB -`SELECT * FROM talk_tblnotification; -- notification이 쌓이는 Table -- 해당 notification Table에 데이터가 쌓이고 어느정도 되야 없어지는지 타임 체크가 필요함. SELECT * FROM jmocha.tbl_dev_master; -- 각 기기마다 badge 수를 확인 할 수 있음. -- 해당 요청을 받은 유저의 badge수를 더하여 요청이 제대로 갔는지 확인 할 수 있음.`

\
5\. 해당 이벤트 스케쥴러를 끄기 위해서는 event\_scheduler를 껴주거나 해당 이벤트 스케쥴러를 삭제하면 됩니다.

`DROP EVENT [이벤트 스케쥴러 이름]; SET GLOBAL event_scheduler = OFF; -- 이벤트 끄기`
