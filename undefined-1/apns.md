# APNs 라이브러리 교체

\[2020-12-21\]

APNS Java.

사용 라이브러리 : Pushy

라이브러리 주소 : [https://github.com/jchambers/pushy](https://github.com/jchambers/pushy)

\[사용 이유\] : com.notnoop.apns은 더이상 라이브러리 업데이트가 안되고 있음. \[2016년이 마지막 업데이트\]

--&gt; 업데이트가 안되고 있어 현재 APNs에서 요청하는 http2에 대한 지원을 안함.

\[Pushy 사용하기 위해 필요한 라이브러리\]

* netty 4.1.53
* gson 2.6
* slf4j 1.8
* fast-uuid 0.1

  변경 점 1. : netty 4.1.53을 사용하기 위하여 vert.x 버전 Upgrade \[기존 vert.x version : 3.5.2 -&gt; 업그레이드 버전 : 3.9.4\] \[기존 netty version : 4.1.19 -&gt; 업그레이드 버전 : 4.1.49\]

\[코드 변경 점\]

리뷰 하도록 하겠습니다.

