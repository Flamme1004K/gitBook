# 유용한 커맨드

// response가 200 밀리 세컨드 이상 오래 걸리는지 여부 확인 awk '{ if \($2 &gt; 200\) { print } }' localhost\_access\_log.2020-12-05.txt \| more

cat localhost\_access\_log.2020-12-24.txt \| awk '{ if \($2 &gt; 200\) { print } }' localhost\_access\_log.2020-12-24.txt \| more \| wc -l

wc-l 줄수 세기



