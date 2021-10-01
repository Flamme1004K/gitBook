# DB락에 대해서 알아보기

\[lock\]

nolock, shared\(S\) lock , exclusive\(X\) lock

- select for update \(mysql\)

- updlock \(mssql\)

\[isolation level\]

read uncommitted :  no shared lock 

read committed: x lock when modify,  shared lock \(read\) 

  modified data 에 대해 읽을 수 없슴\(commit 되기 전에는\)

  그러나 각 sql statement 간에 다른 transaction 이 write 할 수 있슴.

repeatable read: 각 sql statement 간에 다른 transaction 이 write 할 수 없슴

                shared lock 읽은 모든 데이타에 대해 걸림. 따라서 다른 transaction 이 그 row를 변경 못함

serializable:  select shared lock, select 에 대해 영향을 받을 수 있는 모든 잠재적인 row 에 대한 insert/delete 못함

\[jpa\]

pessimistic lock

proxy

