# cp

cp 명렁어 정리

**cp 1.txt 2.txt  - 1.txt를 2.txt로 이름을 변경하여 복사**

**cp 1.txt fileroot - 1.txt를 fileroot에 복사**

**cp 1.txt fileroot/2.txt - 1.txt를 fileroot안에 2.txt로 이름을 변경하여 복사**

**cp -r fileroot1 fileroot2 - fileroot1을 fileroot2 안에 복사**

**cp -r fileroot1 fileroot2/asd - fileroot1을 fileroot2에 asd로 복사**

\- a : 복사시 속성까지 복사됨

\- p : 원본파일의 소유자 그룹 권한까지 복사&#x20;

\- i : 덮어쓰기 할 것인지 물어봄

\- r : 디렉터리 복사

\- v : 복사 시 작업 진행 표시

\- u : 최신파일이면 복사

\- b : 이미 존재하는 파일이면 백업파일 생성
