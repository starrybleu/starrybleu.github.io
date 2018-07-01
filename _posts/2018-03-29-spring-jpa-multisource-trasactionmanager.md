---
layout: post
title: Spring Multi DB sources + JPA 트랜잭션 관리
category: Development
comments: true
---


멀티 DB source JPA에서 OneToMany, Lazy fetch 할 경우 @Transactional(transactionManager = "jpaTransactionManager") 를 지정해줘야 함!ㅊ