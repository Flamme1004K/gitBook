# WireShark 패킷분석

![](../../.gitbook/assets/image%20%2810%29.png)

mobile은 &gt; \[ ---&gt;&gt; 안에 패킷이 적어져 있다.

![](../../.gitbook/assets/image%20%2811%29.png)

Seq= 784  Ack= 652 Len=91

Seq= 875 Ack= 652 Len 259

Seq가 Ack 652한테 Len 91을 주었기 떄문에 다음 Seq가 875가 되었다.

그리고 Seq 652는 ack 784+ 91 = 875의 답을 주었고

Seq652는 ack에게 875+ 259 = 1134라는 답을 주었다.



Wire Shark는 분석을 쉽게하기 위해 Seq를 항상 1로 둔다.

TCP가 보내는 3 hand shake에 대해서 알아야한다.

JAVA NIO를 알아야한다.

