# Repositroy\(20201123\)



Repositroy 리뷰

리뷰 내용 :

MYSQL 및 MSSQL 혼용에 대한 SQL properties, yml 파일 관리에 대한 해결책

1. 서버 재시작 없이 유연한 쿼리 교체 배포

나의 선택은 config 자동화 server를 따로 둬서 유연한 배포를 준비

진행 사항 :

1. ConfigServer 생성 
2. Query문을 관리하는 yml 파일 관리 git 생성
3. testController 생성 

진행 순서 :

Spring-cloud-config-client\(Greenwich.SR1\) Spring-starter-actuator

1. Query문을 밖으로 빼내고 좀 더 유연한 쿼리 교체 배포를 위한 자료검색 이 중에 Spring 프로젝트 간의 Refresh가 유연한 Spring-cloud-Config를 사용.

   사용 이유 1. 유연한 쿼리 교체 배포를 위함 2. 추후 공통 Application.yml을 ConfigServer로 따로 관리할 수 있음.

2. Spring-cloud-Config 구현 후 1. 공통 query.yml 파일을 [https://gitlab.com/eztalk\_java/eztalk\_query](https://gitlab.com/eztalk_java/eztalk_query) 에서 관리 \(해당 query.yml도 MSSQL과 MYSQL용으로 상황에 따라 변환해서 사용할 수 있음.\)
3. Query문을 Refresh하기 위한 Refresh Class를 만들어서 /actuator/refresh \(Post\) 요청시 바로 적용 될 수 있도록 만듬.
4. 그 후 TestController 및 Service, Repositroy 구현

