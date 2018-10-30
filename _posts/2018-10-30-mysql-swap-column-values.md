---
layout: post
title: Mysql 같은 테이블 내의 컬럼 값을 서로 swap 하기
category: Development
comments: true
---

프론트엔드에서 서로 컬럼이 다르게 들어가도록 구현해놔서 한동안 컬럼이 바뀐 채로 INSERT 되는 게 있었다.

두 컬럼의 값을 서로 바꾸기 위해서 임시 변수를 활용할 수 있다.


```sql
update target_table set 
first_column=@tmp:= first_column,
first_column = second_column,
second_column = @tmp
where `id` > 99
;
```