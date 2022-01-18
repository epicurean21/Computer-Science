## Index의 기본 



![blockandpage](./img/index/blockandpage.png)

### Database 처리 단위

Data Block & Page

- ORACLE 에서는 block 이라 부르며
  - 2kb, 4kb, 8kb, 16kb ... 64kb 크기로 처리할 수 있다.
  - 이는 dynamic 하다는게 아닌, 고정적이다. 중간에 바꾸지 못 한다.
- MySQL 에서는 page 단위로 부른다.
  - 16Kb 고정 Page 크기 [처리 단위].



각 테이블의  row의 사이즈에 따라 한 Page에 들어있을 수 있는 Row의 개수가 다르다.

16kb 이상의 row의 경우에는 2개 이상의 Page에 나눠서 저장한다.



Page 가 하나의 Row를 두 개의 Page에 나눠서 저장을 했을 때, 이게 물리적으로 이어져있는걸 보장하지 않는다.

![procs](./img/index/procs.png) 

- 만약 client 가 4번 itemId를 요구했는데, 이게 Buffer Memory [진열대]에 없다면?
  - Disk [창고] 에 가서 가져오는데, 이걸 해당 데이터 [empno=4] 만 가져오는게 아닌, db 처리 단위인 Page 단위로 해당 데이터를 가져온다.
- 즉, Page의 크기가 무작정 커질수록 buffer memory 에 실제 사용 할 가능성이 낮은 데이터를 놓게된다.



### Index 구조와 원리

![btree](./img/index/btree.png) 

#### B tree index에 대해 알아보자.

인덱스란 무엇인가 ?

> 방대한 데이터에서 원하는 데이터를 빨리 찾을 수 있도록 도움을 주는 역할을 한다.
>
> 책에서 맨 뒤에 index를 생각해 보자!

인덱스는 기본적으로 **Sorted** 상태이다.

- 정렬이 되어 있어야, 불필요한 공간을 읽지 않아도 된다.
  - 즉 Scan의 시작점, 종료점을 안다는 것 !



![idx](./img/index/idx.png) 



##### Index는 보통 삼각형으로 표시한다.

그리고, **Table은 보통 사각형으로 표현한다.**

Index는 보통 **<u>Keyword + 실제 데이터 주소</u>** 로 구성되어 있다.