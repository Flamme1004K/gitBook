# WireShark

tcp dump 방

tcpdump -vv -nn -i bond0 host \[아이피\] and tcp port \[포트번호\] -w \[이름\].pcap

bond0은 ipconfig를 검색 후 확

Linux 환경에서 tcpdump 실행

```text
tcpdump -qns 0 -X -r 파일명
```

