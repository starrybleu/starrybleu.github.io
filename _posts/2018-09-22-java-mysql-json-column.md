---
layout: post
title: Java에서 Mysql JSON 컬럼을 쓸 때, 인코딩 문제
category: Development
comments: true
---

(자바, 스프링와 관련)
최근 개발 서버에서 TEXT 컬럼타입으로 저장하던 JSON 데이터를 JSON컬럼으로 ALTER를 하였는데, 갑자기 해당 JSON 데이터가 브라우저에서 볼 때 인코딩이 제대로 되지 않아 글자가 다 깨져보이는 현상을 겪게 되었다.

이것 때문에 약 1시간을 삽질했는데, 다시금 라이브러리/프레임워크도 완벽하진 않다라는 생각을 하게 된 경험이었다.

문제의 원인은 프로젝트 init 할 때는 mysql-connector 의 버전이 5.1.6 이어서 그걸 사용하고 있었는데, 5.1.39 버전(포함)이하에서는 JSON 컬럼의 인코딩을 utf8mb4(표준) 가 아닌 ISO-8859-1 로 인코딩하고 있어 그랬던 것.(ResultSet.getString())

라이브러리의 해당 버그는 5.1.40 버전에서 픽스가 되었고, 버전을 올리니 문제가 사라졌다.

5.1.40 버전의 릴리즈 노트
https://dev.mysql.com/doc/relnotes/connector-j/5.1/en/news-5-1-40.html
