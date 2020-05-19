# Jmeter 기반 실전 성능테스트

KT DS 조정일 강사

## 성능테스트

1. 성능테스트 개요
2. 성능테스트 절차\(계획\)
3. 성능테스트 도구\(준비\)
4. 성능테스트 실습\(실행\)

## 개요

* 성능테스트 이해
* 성능테스트 용어
* 성능테스트 유형
* 성능테스트 방식

  **성능테스트란 ?**

특정한 부하 환경에서 응답과 안정성의 측면에서 시스템이 어떻게 동작하는지를 알아내기 위해 수행하는 시험.

성능테스트에 대해서 도움이 될 것이다.

### 목적

정부사업을 했을때 기술협약할때 성능테스트의 결과물을 제출해야한다.

시스템 성능 검증

* 예상되는 부하시의 성능목표에 대한 검증

  아키텍처 성능 검증

* 시스템 및 애플리케이션 아키텍처 설계에 대한 성능 검증
* AP 및 Configuration 등의 변경에 대한 성능 검증
* 제품/솔루션 도입 시 성능비교를 위한 BMT

  성능 진단 및 개선

* 장애발생 및 성능 문제 개선을 위한 유사 부하 재현을 통한 분석 지원 및 검증

### 사용자

* NamedUser\(가입자\) : 해당 시스템을 사용할 수 있는 전체 사용자
* Active User \(서비스 사용자\) : 해당 시점에 요청을 보낸 다음 응답이 올 때까지 대기하고 있는 사용자
* Inactive User \(서비스 대기자\) : 요청에 대한 응답결과 화면을 보고 있거나 다음 요청을 보내지 않고 대기하는 사용자 \(접속한 사람\)
* Concurrent User \(동시 사용자\) : 특정 시점에 시스템에 접속하여 사용하고 있는 사용자로 부하를 결정하는 중요한 수치

  Current User = Active User + Inactive User

### 시간

* 응답시간\(Response Time\) : 사용자 요청시점부터 결과값 시간
* 대기시간\(Think Time\) : 사용자 요청 후 다음 요청하기까지 준비시간
* 호출간격\(Request Interval\) : 사용자의 요청을 보낸 후 다음 요청을 할때까지의 간격

  응답성이란 ?

  [http://cloudqos.or.kr/page/responsiveness](http://cloudqos.or.kr/page/responsiveness)

  클라우드컴퓨팅서비스 품질성능 확인 현황

### 트랜잭션, 처리량

* 트랜잭션
  * 업무 처리의 단위로 일반적으로 화면 조작 및 응답을 트랜잭션으로 정의
  * 특성에 따라 트랜잭션의 단위 및 범위를 정의하여 사용
* 처리량
  * 단위 시간당 처리되는 비즈니스 트랜잭션의 요청 건수 
  * TPS 값을 가장 많이 활용함, TPM, TPH, OPS 등으로 기준을 정의하기도 함.
* 동시사용자와 호출간격을 이용하여 ,TPS를 예측할 수 있음
  * TPS = Concurrent User / Request Interval

### WorkLoad

* 시스템에 어떤 형태로 부하를 주는지에 대한 부하 모델
* 업무, 호출정보, 동시사용자, 처리건수, 호출비율 등의 정보로 구성됨
* 운영 시스템의 경우 프로파일링 툴, 로그 데이터 등을 활용하여 분석 및 추정 가능
* 신규 시스템의 경우 인터뷰 및 유사사례 등의 방법을 이용하여 예측

  WebLog Expert란?

* 웹 시간별 테스트 가능 &lt;-- 오픈하고나서 일주일 동안 확인해보는것도 나쁘지 않을듯..?
* apach, ngnix 사용

  [https://www.weblogexpert.com/](https://www.weblogexpert.com/)

### 병목현상

[https://namu.wiki/w/%EB%B3%91%EB%AA%A9 %ED%98%84%EC%83%81](https://namu.wiki/w/%EB%B3%91%EB%AA%A9%20%ED%98%84%EC%83%81)

* 적은 수의 디스크에 많은 양의 요청이 들어와서 발생하는 현상
* 시스템의 가용 자원 중 부하가 많이 걸려 전체 시스템 효율의 저하를 초래하는 현상

  ex\) 출근길 지하철은 항상 병목현상이 일어납니다., 교통체증\(무빙보틀렉\)

### 성능테스트 유형

ISTQB에서는 Perfomance Test의 유형을 다음 3가지로 정의

* load Testing : 일반적인 사용 시나리오에 따라 사용자의 평균 응답 시간과 자원 사용률 등을 측정하고 분석
* Stress Testing : 시스템 또는 구성 요소를 최대 처리량으로 측정하여 처리 할 수 있는지 테스트
* Saclability Testing : 성능 요구 사항을 초과하거나 실패하지 않는 시스템의 처리 용량을 측정 
  * 프로덕션 환경이 적절한 양의 하드웨어로 저정 될 수 있음
* Spike : 짧은 기간 동안에 부하가 집중되어 있을때의 시시틈의 성능 특성의 효율성을 검사
  * 시스템 장애를 유발하는 가장 대표적인 부하 유형 
  * Time Sale 이벤트, 아이폰 예약 판매, 특정 예매 시스템과 같이 특정 시간에 부하가 집중적으로 몰리는 형태의 부하 유입시 검증
  * 5만명을 한번에 들여보내서 과정이 하나하나 처리가 되는것이 아닌 얼마나 결과적으로 처리가 되는지 확인
* Endurance\(Stablility Soak\) : 오랜 시간 동안 부하가 지속될때 나타날 수 있는 결합이 없음을 확인하지 위한 성능 테스트 유형
  * 24 Hour 테스트, 5 Day 테스트 등과 같이 장시간 동안 사용
* Availability : 부하상태에서의 가용성 측정을 위해 수행함\(부하를 발생한 상태에서 HA 구성에 대한 Fail-Over 기능 검증

  KT는 Scalability Testing, Spike, Endurance Test를 중심적으로 한다고 한다.

  **성능테스트 방식**

* 인력 기반 층적 방식 : 테스트를 수행활 다수의 사람이 동시에 직접 서비스를 이용함에 따른 부하를 발생하는 방식
* 자동화 도구 기반 측정 방식 : 사용자의 행위를 재현할 수 있는 스크립트를 동시에 발생함으로 성능테스트를 수행하는 방식

  **성능테스트 절차**

* 성능테스트 착수 단계
* 성능테스트 계획 단계
* 성능테스트 준비 단계
* 성능테스트 실행 단계
* 성능테스트 종료 단계

SNS/ 캡챠 인증은 성능테스트가 불가능하다.

## 성능테스트 도구

실습 github 저장소

[https://github.com/bearscho/Academy/tree/master/TTA%EC%95%84%EC%B9%B4%EB%8D%B0%EB%AF%B8/2020%EB%85%8405%EC%9B%94](https://github.com/bearscho/Academy/tree/master/TTA%EC%95%84%EC%B9%B4%EB%8D%B0%EB%AF%B8/2020%EB%85%8405%EC%9B%94)

실습 : TPS계산 -&gt; 분석형 데이터 -&gt; Peak시간대 도출 -&gt; 호출상위업무 도출 -&gt; 워크로드 모델정의 -&gt; 시나리오설계 -&gt; 성능테스트 -&gt; 대상업무 선정 -&gt; 시나리오 설정 -&gt; 결과정리 순으로 간다.

* 성능테스트 툴
* Jmeter 설치
* Jmeter Plugins 설치
* 주요 모듈
* 툴 사용 

### 성능테스트 툴

HP LoadRunner로 대표되는 상용 툴과 Apache Jmeter로 대표되는 오픈소스 툴 등 다양한 종류의 성능테스트 툴 존재

성능테스트 툴 선정 시 여러가지 사항을 고려하여 선정되어야 함.

* 수행 인력의 경험 및 사용 가능한 툴
* 고객이 선호나는 툴 \(RFP 등에 테스팅 툴 명시\)
* 고객 시스템에 대한 라이선스의 가용\(Sap, ERP, ActiveX 등\)
* 프로토콜의 지원 \(.Net, COM/DCOM, Socket 등\)
* 도구의 효율성 \(툴 작업 용이성, 유지보수 등\)
* 라이선스 비용
* 밴더 지원\(IBM, Borland, HP등\)

Apache JMeter 는 가장 대표적인 오픈소스 성능테스트 툴

* 오픈소스 기반으로 무료로 사용 가능
* 100% Java 기반으로 쉬운 설치\(압축파일 해제\)
* 다양한 프로토콜 지원 \(HTTP, Soap, JDBC, Ldap, JMS, TCP, Java Objects 등\)
* Java 호환 OS\(Linux, windows, Mac OSX\) 지원
* Java 기반으로 테스트 영역 무한 확장 가능 \(Custom Sampler 지원\)
* 3rd party 연계한 Continuous Integration 지원\(Maven,  Graddle and Jenkins 등\)
* 테스트 자동화를 위한 Bean Shell & Selenium 등과의 통합 지원

  `Jmeter를 주로 하는 회사는 없다. 거의 성능테스트는 성능테스트 회사에 맡긴다.`

  **Jmeter 설치**

  Mac \)

  설치방법 : brew install jmeter \(컴퓨터에 JDK가 설치되어있어야 한다.\)

  실행 : open /usr/local/bin/jmeter

  새로운 터미널 창이 열리면서 jmeter GUI가 열림.

  Window \)

  설치방법 : Jmeter 사이트에서 5.3버전 설치

  jmeter-plugins.org에서 라이브러리 설치하고 lib-ext 폴더에 넣어준다.

  Custom SOAP Sampler, 3 Basic Graphs, 5 Additional Graphs 설치

  **WebTesting Site :  www.blazemeter.com**

### 성능테스트 실습

* 기본 사용법
* 실전실습 \(가상 쇼핑몰 Recording\)
* Jmeter Advanced
* 프로젝트 실습

  **JMeter의 과정**

  가장 많이 사용되는 Web \(HTTP\) 서비스에 대한 기본 Templates을 이용하여 구성 후 기본적인 사용방법을 습득

  Templates 로드\(Web Test Plan\) -&gt; HTTP Request\(작성\) -&gt; Assertion\(추가\) -&gt; Think Time\(설정\) -&gt; View Result Tree\(확인\) -&gt; Thread Group\(설정\)

  1. Template 로드
  2. Template는 테스트 유형별로 기본적으로 사용되는 TestPlan 구조를 제공
  3. HTTP Request 작성
  4. HTTP Requset 샘플러는 아래 입력된 정보를 이용하여 실체 요청을 하는 Sample임
  5. Config Element 중 HTTP Request Defaults가 추가되어 있고, 값이 지정되어 있을 경우 HTTP Request에 값이 지정되지 않았다면, HTTP Requset Defaults의 값이 적용됨.
  6. Assertion 추가
  7. Assertion은 요청에 대한 응답값에서 정상적으로 처리 되었는지를 판단하는 기능을 제공
  8. Junit4의 Assertion과 같다.
  9. ThinkTime 설정
  10. Think Time 은 업무 처리 간에 대기시간 적용하며, Flow Control Action을 이용
  11. View Result Tree\(확인\)
  12. View Result Tree는 각 Sample에 대한 처리 정보, 요청 및 응답 데이터를 확인할 수 있음.
  13. Thread Group\(설정\)
  14. Thread Group는 동시사용자 수, 유입시간, 호출횟수\(유지시간\)을 지정할 수 있음.
  15. 테스트 중인 한명의 사용자를 나타내는 Thread의 집합. 전체 Thread 수, Ramp Up 시간, 수행 횟수 지정
  16. 기본 'Thread Group'이 있으나, 사용 편의를 위해 Plugin을 활용

  **JMeter 도구**

  * Elements 구조

  테스트 정보는 Element 들의 트리 구조 형태로 이루어짐

  * Non-Test Elements\(HTTP Test Script Recorder\)
  * Config Element\(설정 해주는 것\)

  Config Element는 Sampler에서 사용할 수 있는 기본값 및 변수를 설정하는데 사용

  CSV Data Set Config, HTTP Cache Manager, HTTP Cookie Mnanger, HTTP Header Manager, Random Variable을 많이 사용

  * Logic Controller

  요청의 처리순서\(조건분기, 반복, 임의 등\) 적용시 사용

  Once Only Controller, If Controoler, Transaction Controller, Recording Controller를 많이 사용

  * Pre Processers / Post Processers

  Sampler 처리 전/후의 작업을 지원

  Regular Expression Extractor, Result Status Action Handler, Boundary Extractor\(원하는 값을 뽑아냄\)

  * Timer

  요청 후 대기시간을 적용하는데 사용

  Constant Timer, Uniform Random Timer를

  * Sampler

  HTTP, FTP, JBC 및 여러 프로토콜 테스트를 지원

  HTTP Request, Test Action, Debug Sampler를 사용

  * Assertion

  Sampler의 수신 응답에 대한 유효성을 검사하는데 사용

  Response Assertion

  * Listener

  테스트 실행 결과를 보여주며 트리, 테이블, 그래프 등으로 표시할 수 있음

  View Results Tree, Summary Report 사용

  **실습**

  테스트 샘플러 -&gt; web Test Plan 클릭

