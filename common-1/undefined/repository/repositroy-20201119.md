# Repositroy\(20201119\)

Repository 코드 리뷰.

WebAPI Query GETROOMMESSAGEBYID

GETROOMMESSAGEBYID는 1~8까지의 Query가 있습니다.

그 중에 GETROOMESSGEBYID1을 진행 하였습니다.

진행.

1. 어떻게 작업을 하였는가?

   1-1. QueryDSL 세팅 및 테스트 1-2. 기존 WebAPI Query 분석 후 현재 테이블에 맞는 구조로 변경 1-2-1.중간에 필요한 Table의 DummyData 삽입\(기존 ezTalkDB를 이용하여 Dummy Query를 만듬\) 1-3. 완성 후 Controller webAPI 호출 테스트하여 기존 Response 확인

2. GETROOMMESSAGEBYID Service에 리뷰.

   2-1. QueryDSL을 어떻게 사용했는가. 2-2. MSSQL과 MYSQL의 문법 차이를 어떻게 해결하였는가? ifnull, isnull 2-3. 서브쿼리 처리

클라이언트 요청에 따른 응답 결과.

차후 해결해야할 부분.

1. delFlag에 대한 처리.
2. LocalDateTime 처리.
3. FileLimit와 같은 CommonConfig 처리

jdbc template 사용하여 만들어보기.

