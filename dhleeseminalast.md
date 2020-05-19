# HTTP Protocol

HTTP? TEXT기반의 PROTOCOL

TCP Connection으로 연결되어 응답/요청을 받는다.

Telnet과 generic tcp connection을 받음.

RequestLine &lt;------------------------------

```text
                RequestHeader(CRLF)

                (CRLF)emptyLine

                RequestBody
```

Message의 요소하나하나가 바로 Token이다.

첫번째로 나오는 것이 바로 Http Method\(GET,POST,PUT,DELETE\) +SPACE\(공백도 문자이다.\) + URI\(RESOURCE\) + SPACE\(공백\) + HTTP VERSION\(HTTP/ 1.1\)

공백 문자를 정의하는 곳은?

일단 공백문자의 코드는?

먼저 ASCII 코드부터 알고 넘어가야 한다.

ASCII\(American Standard Code For Information Interchange\) 대문자부터 소문자로 나온다. 숫자부터 영어까지!

코드의 개수는? 128개라고 한다.

공백은 32번에 뜻한다.

LF\(Line Feed\) 10번\(0A\) 라인\( 줄 바꿈 했을 시 같은 위치의 다음 라인으로 넘어감\)

CR\(Cawiuage Return\) 13\(0B\)번 엔터\( 줄 바꿈 시 다음 라인의 첫번째 위치로 넘어감.\)

즉 Http 마지막은 crlf로 넘어간다.

윈도우는 lf만 실행된다.

윈도우 맥 git을 사용했을때 항상 crlf 설정을 해줘야 한다.

아스키코드 하나는 몇비트일까? 128. 2의 7승 즉 7비트이다.

RequestHearder 뒤에 CRLF로 줄바꿈을 해주고

EMPTYLINE이 또 CRLF로 적용된다.

덤프뜨면 13/10 \(0A/0\)B으로 나온다.

hexadeclimal\(16진법\)

Linux file명령어 \( 파일의 형태를 보여줌\)

```text
  xxd명령어 (16진수로 보여줌)

  xxd window.txt | moreㅗ

  | 파이프란? 한 프로그래밍이 모든 프로그램 기능을 다 할 수 있도록 만듬.

  window.txt: ASCII text with CRLF line terminators (CRLF로 마지막으로 쓴다.)
```

UNICODE 65535개의 11172개가 한글이다.

HTTP/ 1.0 부터 시작되었다.

GET URI HTTP/1.1

HOST : gw.kaoni.com

[http://gw.kaoni.com](http://gw.kaoni.com) --&gt; IP주소

1.0에서는 사용자가 첫던 서버명을 모른다.

하지만 1.1은 VirtualHOST를 위해서 쓴다.

Telnet gw.kaoi.com

GET / HTTP/1.0

Connection closed by foreign host

HTTP 자체가 Redirection

GET / HTTP/1.1 --&gt; Bad Request 400

400 -&gt; Client Error

500 -&gt; Server Error

GET /user/login/login.do

HOST값을 넣어야한다.

nsloopup --&gt; 서버 아이피를 찾기위함.

Virtual HOST란?? 찾아보자.

prompt '&gt;'

