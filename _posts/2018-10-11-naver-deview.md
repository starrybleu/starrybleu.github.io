---
layout: post
title: 처음으로 네이버 데뷰에 왔다
category: Development
comments: true
---

# Keynote

연사 : 송창현님 CEO @ 네이버랩스

## Ambient Intelligence(생활환경지능)를 강조
- Understanding
> 사물, 상황, 위치 인식 / 이해
- Anticipatory
> 적시에 답 / 추천 / 액션 제공
- Natural UX
> 자연스러운 인터페이스 => Clova, papago

### Green dot
- 네이버의 여러 서비스를 dot 의 UI 로 제공

### 위치와 이동에 대한 기술에 집중

### 위의 노력의 결과를 공개
- 어제 업데이트 된 네이버 지도
> 네이버 지도에서 예약 서비스를 이용 => 지도에 표시가 됨
- **Naver Map Enterprise API** 11월 13일 출시 예정
> 지도, 길찾기, Places
> Mobile 지도 로딩 Open API, 모바일 SDK 무제한 무료 제공

### 아직 풀지못한 실생활과 공간과 이동의 `연결`에 대한 문제
> - 우회전이 많이 막히는 길을 안내해주면 좋겠다.

## Naver xDM Platform
- 네이버 랩스가 지금까지 준비해온 패키지
- mobile, autonomous vehicle, autonomous robot => MAP(지도)
- 측위 + 지도 + 내비게이션
> indoor navigation 등을 만들고 싶다 => xDM Platform 을 구매하면 가능(하다고 한다.)
- 증강 현실
> Coex 에서 Indoor AR 을 이용한 길찾기를 동영상으로 보여줌. => 신기하고 좋다.

#### 키즈폰 `AKI kit` 가 이 xDM Platform 을 아주 잘 쓰고 있음 (준비중이라 함)

### SELF-UPDATING MAP
- 지도 업데이트 : 시간, 물리적 비용이 엄청 남 => 많은 기업들이 Self-updating map 을 할 수 있도록 연구/개발하고 있음
> Using Deep learning, semantic segmentation, Robot, 3D Mapping, etc.

### 자율주행 자동차
- HD Map => Hybrid HD Map
- Trueortho map

### ADAS (Advanced Driver Assistance System)
- 자율주행 자동차가 아니더라도 안전을 위해 도입할 수 있는 기술
- Ordinary HUD(눈의 초점에 따라 HUD 맵도 움직임) -> AHEAD(네이버에서 개발, 눈의 초점과 상관없이 도로를 기준으로 HUD 맵이 고정되게 보임)

## Robot AMBIDEX
- 끝이 무거운 막대를 손바닥 위에 올려서 균형을 잡는 영상을 보여줌 => 대단...


# 첫번째 세션 : 해커의 관점에서 바라보기

연사 : 박세준님 CEO @ Theori (무려 CMU 출신이시다..)

* Theory : 보안 스타트업

발표자료 : https://www.slideshare.net/deview/131-119007645

### ice breaking
- 본인, 회사 소개
- 대중에 알려진 해킹
> 안 좋은 것
- 진짜 해킹
> 분석 -> 이해 => 즐거움, 보람

### 해커의 종류
- Black hat (aka. 나쁜 해커)
- Gray hat (법적 테두리 주변..)
- White hat (aka. 보안 전문가)

### 개발과 해킹의 경제론
- 개발자는 데드라인, 시간/금전적 비용 제약 등 : 불리함
- 개발자는 10을 만들면 10을 모두 안전하게 만들어야 함
- 해커는 1개만 뚫으면 됨

### 취약점은 버그의 일종

### 소프트웨어 해킹
- 웹
- 모바일
- 네이티브

### 하드웨어 해킹
- 핀과 칩으로부터 펌웨어
- 펌웨어

# 두번째 세션 : 서비스 오리엔티드 블록체인을 위한 스케일링 문제 해결

연사 : 이홍규님 CEO @ 언체인

* dApp

### dApp : 4300만개의 계좌가 열려있음

- 블록체인이 대중적인 플랫폼이 아님
- 얼리 어답터도 dApp 서비스를 사용하기는 어렵다.

### icebreaking 어떤 블록체인이 필요한가?
- 사용자가 안심하고 쉽게 사용 가능
> private key 안심 금고
> login, blockchain 속도 등 동일한 UX
- dApp 을 만들기 쉽고 사용자들에게 소개할 수 있음
> smart contract 안정성
> 개발 편의성

## Scale Out

### Trials (시도들)
- 라이덴 네트워크
- 플라즈마, 플라즈마 캐시
- 샤딩

### 사이드체인, 인터체인

### 샤딩
- 쿼크체인
- 질리카

# 세번째 세션 : Javascript 배틀 그라운드로부터 살아남기

연사 : 박재성님 @ Naver

- Mocha -> Live Script -> Javascript
- 표준화 타임라인

### 자바스크립트 변천사 간략히 소개

* 2005 - 2013 jQuery, Prototype, MooTOols, ...
* 2013 - Now Angular, React, Vue, ...

- Evergreen Browser 개념의 등장 (과거와 달라진 버전의 중요성)

### 오늘날의 JS 개발
- 프레임워크 + 개발도구
- npm 패키지 사용 -> 조립

### 개발 방식의 진화
- Library -> Framework
- Virtual DOM 등

### 2018년 동향
- 버전 구분 중요성 감소 (ES20XX 이후)
- 과거(버저닝에 큰 변화)와 달리 feature 단위로 릴리즈
- Transpiler(Babel)은 최신 스펙 사용 이슈 해결
- Evergreen Browser 는 스펙을 빠르게 구현

#### WebAssembly
- 어느 브라우저에서나 빠르게 실행되는 바이너리로 컴파일

#### 정적 타입 사용
- Typescript, Purescript, ReasonML 등

#### React, Vue, Angular, Web Components, Polymer 의 특징 소개

- Angular, Vue.js -> 웹 컴포넌트로 컴파일 가능

### 0CJS (Zero Configuration JS
- Webpack : 설정, 또 설정
- Parcel : 무설정 지향

### 모바일 애플리케이션
- NativeScript
> - Angular-friendly
- React-Native
> - create-react-native-app
> - TV 플랫폼 (Android, Apple) 지원

### 데스크톱 앱
- 모두 Chromium 기반
- 일렉트론
- NW.js
> 웹앱을 빠르게 데스크톱앱으로 전환시 강점

### PWA (Progressive Web Application)
- Safari, Edge 지원 추가로 드디어 기본 사용환경 갖춤
> - `is ServiceWorker ready?`
- 아직은 미미하지만, 앞으로 사용 증가할 것으로 예측됨
- 다양한 도구의 등장
> Workbox, HNPWA, PWA Starter Kit, pwa-helpers, ...

### WebXR Device API
- WebVR 을 대체
- Immersive Web 

### 매몰비용 오류의 함정
- magpie syndrome

# 네번째 세션 : 네이버에서 사용되는 여러가지 Data Platform, 그리고 MongoDB

연사 : 이덕현님 DBA @ Naver Business/Data Platform

### 네이버에서의 Data platform
- Cache(Redis, ARCUS) - RDBMS(Mysql, Oracle, Cubrid)
- NoSQL (mongo, hbase(하둡 기반))

### 몽고를 쓰게된 이유
- schemaless
> Pros : 미리 데이터의 구조를 정의하지 않아도 됨
> Cons : 저장 용량이 큼 (저장 효율 떨어짐)
- sharding, secondary index, transaction 처리 가능
> auto sharding 가능
> transaction : ~3.6 까지 1개의 document 에 대한 트랜잭션 보장(row level lock)
> transaction : 4.0 부터 단일 replica set 에서 Multi-document transaction 보장
- json 지원
- IDC 간 Auto failover (IDC DR : disaster recovery) 가 가능한 Data platform 임
> 네이버에서도 중요한 서비스에 도입하려고 시도하는 단계
> 몽고 db 는 IDC가 3개 이상의 홀수 개수만큼 필요함

### 네이버에서 몽고db를 사용하며 겪은 에피소드들 (약 2-3년 전부터 사용)
- Mongos 관리
> `Mongos` : 샤딩을 사용할 경우 단순 라우터 기능
> 제조사 권장은 Mongos 를 client 와 같이 배포를 권장하고 있으나 네이버에서는 사용하고 있지 않음

### L4와 getmore
- Sharding 에서의 L4 (network switch) 사용을 초기에는 권장했으나 getmore 이슈로 제거
> getmore 할 때마다 오버헤드 발생
> getmore : 20건 단위로 데이터 전달하는 과정 ( `find()` -> `getmore()` )
- => L4 제거

### mongos <-> shard 커넥션 (유용한 팁)
- Mongos 와 shard server 사이에 커넥션 관리에 문제가 있음
- 아래의 옵션을 적용하여 사용 (튜닝)
```yaml
shardingTaskExecutorPoolHostTimeoutMS: 3600000
ShardingTaskExecutorPoolMaxSize: 20 (default : Unlimited) - 단위 : 개 per core
ShardingTaskExecutorPoolMinSize: 10 (default: 1) - 단위 : 개 per core
```
- mongos 와 shard 사이의 커넥션 수가 갑자기 늘어나는 현상 거의 사라짐
- ex) 실제로 추석연휴때, 평소 사용량이 없던 서비스가 급작스럽게 사용량이 늘어나서 mongo 증설해야한다는 연락을 받았다는 썰.

### Node.js driver
- Node.js 모듈인 mongoose 는 성능 이슈로 인해 권장하지 않음
- Node.js 드라이버 3.x 버전보다는 2.x 버전이 안정적임
> => 네이버도 2.x 버전을 사용하고 있음

### Storage Engine
- WiredTiger 엔진만 사용하고 있음
- eviction problem
> eviction : `memory buffer 에서 오래된 data 삭제하는 작업`
> Mongo 3.2.11 또는 3.4 이상 버전을 사용하면 해결됨(안정적 운영 하고 있음)
- Checkpoint problem
> `checkpoint`: memory buffer 와 disk 사이의 데이터 불일치(dirty page)를 해소하기 위해서 memmory -> disk 로 data sync 하는 작업

### eviction & Checkpoint
- Mongo 에서 Buffer pool : 전체 메모리의 50% 사용 => 여기엔 자세한 이유가 있지만 설명하지 않음
> 한 서버에서 2개 이상의 replica set 을 돌리지 않음 <= OOM 으로 몽고 데몬이 죽을 수 있음

### Background Index
- RDBMS의 onine index create
- MongoDB 는 bacground index 생성 가능
> 인덱스를 만드는 컬렉션이 포함되어 있는 전체 db에 lock이 걸림 (cf. mysql 은 해당 테이블에만 걸림)

### Compact
- 디스크 조각모음 같은 작업
- 절대 수행하지 않음

### Balancer
- MongoDB의 Chunk MIgration은 많은 비용이 듬
- 여러 이유로 Balancing 작업이 실패할 수 있음
> - Config server 에서 changelog 를 자주 살펴봄

### Clustered Index
- `Clustered Index` : 해당 key 값 기준으로 실제 데이터가 정렬되어 있음
- MongoDB 는 Clustered Index 가 없어서 사용할 수 없음.
> - Clustered Index 가 중요하다면 MongoDB를 사용하면 안 됨

### Unique Index
- Sharding 환경에서 unique index 는 전체 shard 내에서 유니크하지 않음
> shard 내에서만 unique 함 보장.
> Application 에서 방어로직을 작성해야 함

### 개인정보 - 전자금융거래법 관련
- MongoDB 3.6 버전의 authenticationrestrictions 기능 사용 중
- bind_ip 는 DB ACL 을 제한하는 기능이 아님

## 전망 (주관적)
- 잘 써야 함
- 성능과 안정성 이슈 존재
> - Checkpoint 관련 issue 해결 시급
- 우리나라에서 Main Stream 으로 도입될 가능성은 높지 않을 것 같다.

# 마지막 세션 : 웹 성능 최적화에 필요한 브라우저의 모든 것

연사 : 이형욱님 @ Whale

### HTML Parse
- incrementally parsing

### Javascript Engine

### Vsync

### Raster
- 비트맵을 찍는 과정이라 생각하면 됨

