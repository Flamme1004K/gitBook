# tar 압축 시 어떻게 심볼릭 링크도 같이 압축할까?

현재 나는 tar 압축시 어떻게 심볼릭 링크도 같이 압축할 것인지.

그리고 압축을 해제했을때 심볼릭 링크의 절대경로에 파일이 만들어지는지에 대해 궁금해서 

테스트 해보려고 한다.

일단.

![](https://k.kakaocdn.net/dn/dsde40/btqCtYE4ARQ/que7o5Nw19gYG1zG9No5NK/img.png)

root파일 처음에다가 symbolicFile을 만들었다.

![](https://k.kakaocdn.net/dn/bTB95B/btqCwWNeWPp/xciDQZTku20JTk3Sv9NE4k/img.png)

그다음에 flamme 계정에 들어가 testFile을 만들고 그 안에다가 아까전에 만들었던 symbolicFile을 

심볼릭 링크화 시켜주었다.

그리고 

![](https://k.kakaocdn.net/dn/B8RRX/btqCoQuT8hW/YAtIsyW9vf09KKDmIpx9E0/img.png)

압축시켜주었다.

이제 테스트 파일을 삭제하고 압축을 풀어보자.

... -h를 썻더니 심볼릭 링크가 해체된 빈 파일만 남아있다...

![](https://k.kakaocdn.net/dn/BveMZ/btqCqNEqjQ0/amrxowMTLNGw7vXcVEPLjK/img.png)

다시해보자.......

현재까지 하고있음.. 조금남았다!

잘보니깐 tar파일을 만들어줄때 같이 만들어준게 아니라... 

추후 pl파일 설치할때 같이 만들어준것이었다.

즉.. tar로 절대경로까지는 복사가 불가능 '/'이 포함되지 않는다는 에러가 뜬다.

다음에는 펄스크립트를 이용해서 tar 알집을 풀어보자

ps. 잘보니깐 tar압축을 풀때 뒤에 -c / 로 압축을 풀어주면 루트디렉토리에 풀림.  


