---
layout: post
title: Java&Spring 개발자가 Ruby&RoR 을 해보고 마주친 생각들
category: Development
comments: true
---

## Spring 개발자가 왜 Rails 에 관심을 갖게 됐는가?

저는 Spring Framework 로 제품 개발을 3년 정도를 했습니다. 이후 최근 11개월동안은 Rails 로 제품 개발을 하고 있습니다. Spring, 특히 Spring Boot 라는 Framework 에 부족함을 느끼지 못했습니다. 그런데 왜 지금 리멤버를 서비스하고 있는 드라마앤컴퍼니에 와서 Rails 로 개발을 하고 있는지를 떠올려봅니다. 언제인지 정확한 기억은 나지 않지만, Ruby 의 기초를 공부한 적이 있었는데 문법이 저랑 잘 맞을 것 같단 느낌과 함께 마냥 긍정적인 시각이 생겼습니다. 많은 Web Framework 들에 영감을 준 Ruby on Rails 에 대해서도 궁금증이 생겼습니다. 그 이후, 기회가 되면 Ruby 로 개발해보면 재밌겠다라는 생각이 들었고, 드라마앤컴퍼니로 이직하는 것이 계기가 되어 Rails 로 개발을 하고 있습니다. 이 글에서는 Spring 개발자가 RoR 을 해보고 난 후 드는 생각들을 정리해봤습니다.

## Ruby on Rails 프레임워크의 철학

- Ruby on Rails 는 MVC 패턴 기반의 개발을 빠르고 편리하게 해주는 Web framework 이며, 아래의 철학을 바탕으로 하고 있습니다.
  - DRY(Don't Repeat Yourself)
  - CoC(Convention over Configuration)
    - Spring Boot 가 나오게 된 배경도 Spring MVC 만을 사용할때 비즈니스 로직보다 설정에 관련된 코드를 많이 작성해야했기 때문인 점과 일맥상통하다고 볼 수 있습니다.

## Ruby on Rails 의 강점

### Ruby 언어의 강력함

- Ruby 는 정말 인간 중심으로 설계된 언어라는 것을 체감하였습니다. Ruby world 에서는 모든 것이 Object 인 점을 만족하는 순수 객체지향 언어인 점은 Rails 라는 Framework 로 확장하는데 아주 큰 역할을 하였습니다.
- 프로그래밍을 해보지 않은 사람에게 입문용으로 추천해줄 수 있을 정도로 쉬운 문법과 러닝 커브가 가파르지 않은 Web Framework 입니다.
- 새로운 클래스, 메소드를 만들지 않고 기존 클래스의 기능을 손쉽게 확장 가능합니다. (meta programming)
- 이 점은 Java 에서 각종 Util 성 메소드를 모아놓은 class 를 별도로 만들어 쓰는 것과 대조적입니다.

### rails console

- Rails 에서 제공하고 있는 REPL(Read-Evaluate-Print-Loop) 도구인 rails console 을 통해 방금 작성한 코드의 실제 실행 결과를 바로 바로 눈으로 확인할 수 있습니다. 이 점은 개발 과정에서도 아주 유용하게 사용됩니다. 특히, 저는 환경 설정 파일에 설정해놓은 key,value 값들이 실제로 오타 없이 잘 입력되었는지 확인하는데 많이 썼습니다. 그리고 TDD 로 개발한다고 하더라도 복잡한 계산을 요하는 로직을 개발하다보면, Java + Spring 에서는 debug 모드로 중간 중간 값들을 확인/변경하면서 잘 된 것인지, 잘못됐다면 어디가 잘못된 것인지 확인하게 되는데, rails console 을 이용하면 debug 모드로 확인하는 것보다 훨씬 빠르게 작업을 마칠 수 있습니다.
- 그리고 운영 업무 시 아주 막강한 힘을 발휘합니다. Java + Spring 의 경우에는 실행이 필요한 코드 스니펫을 작성해놓으면 서버에 배포를 해야만, 코드 실행이 가능한데 rails console 을 이용하면 실행 전에 sandbox 모드를 이용하여 미리 dry-run 에 가깝게 실행 과정을 예상해볼 수 있고, 코드를 배포하지 않고도 원하는 코드 스니펫을 실행시킬 수 있습니다.
- 이 점은 어떤 관점에서 보면 단점이 될 수 있기도 합니다. rails console 을 실행할 수 있는 권한이 있는 누군가가 실수할 가능성도 함께 있기 때문입니다. 그렇지만 rails console 은 rails 로 개발하는 데 있어 아주 편리한 도구인 것은 틀림없는 사실입니다.

### 테스트 코드 작성의 편리성

- Rails 를 처음 입문했을 때 가장 충격받았던 점은 테스트 코드를 작성하기가 너무 쉽고, 편해서 TDD 가 너무 당연하게 여겨지는 것이었습니다.
- 웬만한 Rails tutorial 코드를 보면 테스트를 가장 먼저 작성합니다.
- RSpec 이라는 테스팅 라이브러리가 가장 많이 사용되고 있는데, 사람이 작성하고 읽기 편한 테스트를 작성하도록 자연스럽게 이끌고 있습니다.
- 그리고 테스트에 필요한 여러 Fixture 를 편리하게 만들어주는 Faker, FactoryBot 등은 접해보지 않은 사람들은 놀랄 정도로 테스트 작성에 부스터 역할을 하고 있습니다.
- 이렇게 테스트 코드 작성이 편리하니, 테스트 작성을 통해 얻을 수 있는 장점을 모두 쉽게 얻을 수 있으며 리멤버 커리어 서비스의 API 프로젝트의 테스트 커버리지를 분석해보면 85% 이상을 유지하고 있습니다.
    [##_Image|kage@lT5bW/btqNf6ZnTt8/wm2d8hJ0Xp5W33M9iJO3Zk/img.png|alignCenter|data-filename="스크린샷 2020-10-31 오후 12.07.24.png" data-origin-width="2898" data-origin-height="310" data-ke-mobilestyle="widthContent"|||_##]

### Rails way

- duck typing 이 가능한 Ruby 의 특성을 이용하여, 한 마디로 정의하기 어려운 Rails way 를 나날이 습득하다보면서 생산성이 증가함을 느낄 수 있었습니다.
- 언어 차원에서 단수/복수를 고려하고 있다는 자체가 사람 중심의 언어라는 증거 중 하나입니다.
- 그 예시로 시간 관련 클래스의 강력한 메소드들이 기본적으로 사람이 이해하기 쉬운 메소드 이름으로 표현되어 있습니다.

```
1.day.ago.all_day
2.days.ago.all_day
3.days.from_now
```

- java 에서도 1.8 버전 이전에는 날짜관련 API 가 매우 빈약하여, Joda 라는 라이브러리를 사용하기도 했는데, 1.8버전 이후로는 개선된 API 가 나와서 편리해지긴 했지만 여전히 Rails way 로 구현된 Rails 의 API 에 비하면 많이 부족한 것 같습니다.

### DB 스키마 관리

- Rails 에서는 DB 스키마가 code level 에서 관리되고 있습니다. Java + Spring Boot 의 경우에는 이를 개발자가 별도의 노력으로 관리를 해야만 code level 에서 확인하고 유지보수를 할 수 있는 반면, Rails 에서는 DB 스키마 변화에 대한 이력 관리가 거의 강제됩니다. 그래서 현재 상태만 표현하는 DB 의 이력 변화 내용을 code level 에서 확인할 수 있습니다.
- DB 스키마 관리 방법도 2가지 이상 제공되고 있어서, 개발자(팀)의 선호도에 따라 그 방법을 선택할 수 있게 해준 점도 Rails 에서 잘 한 점이라고 생각합니다.
  - strucutre.sql, schema.rb

### 이메일 발송 Preview

- Rails 에서는 기본적으로 이메일 발송 기능도 내장되어 있고, template 으로 작성된 이메일을 미리 볼 수 있는 Preview 를 제공합니다.
- Spring Boot 에서도 개발자가 그런 기능을 만들어서 활용할 수도 있겠지만, 제 경험으로는 그 목적으로 작성된 코드를 못봤고, Spring Framework 자체에서 지원하지 않습니다. 모를 땐 괜찮았는데, 알고 나니 Preview 가 없이는 작업하기가 매우 번거로울 것 같다는 생각이 들었습니다.

#### routes.rb

- _(장점이자 단점으로 느껴지는데 어떻게 써야할까?)_

#### 비동기 로직 처리를 위한 sidekiq

- 서버 코드를 작성하다보면 시간이 오래 걸리거나, 비즈니스 로직과 직접적인 관련이 없지만 실행되어야 하는 로직들이 종종 있는데, 이를 비동기로 처리하는 경우가 많이 있습니다.
- Rails 진영에서는 sidekiq 이라는 Backgroud Processing 도구를 많이 쓰는 것 같습니다.
- sidekiq 에서는 여러 작업 queue 를 제공하고 있고, 각 qeuue 에 작업(Job)을 넣는 것 만으로도 안정적으로 해당 비동기 작업이 정상처리될 것임을 보장받을 수 있습니다.
- Spring 에서는 비동기 로직을 처리하기 위해 메소드에 @Async 라는 어노테이션을 지정하는 방식을 많이 쓰고 있고, 또는 Future, CompletableFuture 의 API 를 활용하는 경우가 많은데 이 떈 실제로 잘 처리 중인지, 아닌지 확인하기가 많이 어렵습니다. 코드에 버그라도 있으면 실패를 알아차리지도 못하고, 실행되어야 하는 로직은 실행이 되지 못하고 종료처리되는 경우도 생길 수 있습니다.
- 반면 sidekiq 에서는 Web UI 를 제공하여 실패한 Job 목록을 볼 수도 있고 재시도 알고리즘에 의해 한 번 실패했더라도 여러번 재시도를 통해 실행될 것임을 확인할 수 있습니다.
- 또한 cron 으로 표현되는 Scheduled Job 도 손쉽게 스케줄링할 수 있습니다.
- Spring 진영에서도 Spring Batch, Spring Quartz 라는 대안이 있긴 있지만, 약간의 러닝 커브가 있고 단순 비동기 로직을 실행시키기 위한 framework 로서는 너무 비대합니다.

여기까지 Ruby on Rails 의 강점에 대해 정리해봤습니다. 다음으로 Spring 의 강점을 정리해보겠습니다.

## Spring 의 강점

### 선언적인 API endpoints routing

- Rails 에서는 routes.rb 파일에 웹 어플리케이션의 모든 API endpoint 들을 정의하게 되어있습니다. Sprng 에서는 Controller 클래스들과 Controller 의 메소드들에 개별적으로 어노테이션을 이용하여 선언하도록 되어있습니다. 선호도의 차이가 있을 수는 있지만, 선언적인 방식을 사용하는 Spring 의 방식이 훨씬 좋다고 생각합니다.
- rotues.rb 에 모든 endpoints 를 정의했다고 하더라도, 연결된 controller 코드를 보면 어떤 endpoint 에 연결되어있는지 찾기가 쉽지 않습니다. 이를 보완하기 위해 컨트롤러 메소드 위에 endpoint 를 주석으로 달아놓기도 하지만, 그런다는 자체가 Spring 의 선언적인 방식을 쓰는 것이 더 좋다는 것을 인정하는 것처럼 느껴지기도 합니다. 주석으로 달아놓은 것은 실제로 실행되는 코드와는 무관하고, 틀릴 수도 있어서 팀에서 잘 관리하지 않으면 안 됩니다.
- 그리고 특정 오류가 생긴 API 를 로그 시스템으로부터 발견하여 어떤 컨트롤러의 어떤 메소드에서 실행되는 코드인 것인지 찾기 위해 들이는 시간이 Spring 의 선언적인 방법으로 했을 떄가 훨씬 적게 듭니다.

### 선언적인 DB 트랜잭션 관리

- Rails 에서도 DB 트랜잭션 관리를 위한 API 가 있습니다. Spring 의 그것에 비하면 기능이 너무 제한적입니다. 그리고 트랜잭션 관리를 하기 위해 개발자가 직접 트랜잭션을 여는 코드를 비즈니스 로직 내에 작성하도록 되어 있습니다.

Spring 에서는 아래와 같이 @Transactional 을 메소드 바깥에 선언하므로서 비즈니스 로직과 분리하여 생각할 수 있게 해줍니다.

```java
@Transactional
public List<User> saveUser(User user) {
  return userRepository.save(user);
}
```

Rails 에서는 아래와 같이 작성해야 합니다.

```ruby
def save_user(user)
  User.transaction do
    user.save!
  end
end
```

### Container 와 DI

- Spring 에서 Container, DI, IoC 에 대해 얘기해보자면 끝이 없는 이야기가 될 정도로 중요한 개념일 것입니다. 이 글은 그걸 설명하기 위한 글은 아니라 Spring 의 강점으로 Container 와 DI 를 떠올린 이유를 얘기해보겠습니다.
- Spring 에서 넘어와 Rails 를 하면서 가장 큰 충격을 받았던 부분이 비즈니스 로직 처리를 담당하는 Service Layer 를 구성하고 있는 클래스의 인스턴스들을 직접 new 키워드를 통해 생성하고 있던 부분이었습니다. 그러다 보니 상태를 갖는 객체들을 개발자가 직접 컨트롤 해야하는 위험에 빠지기 쉬운 것 같습니다. 이를 조금이나마 방지하기 위해 class method (루비에서 모든 것은 객체이기 때문에 진정한 의미에서의 class method 는 아니지만) 를 정의하는 방법을 사용하기도 합니다.
- Spring 에서는 Bean 이라고 부르는 객체들의 라이프 사이클을 관리해주는 Container 가 있고, Service Layer 의 객체들은 이 Container 가 생성해주고 해당 Bean 들이 필요한 곳에서는 의존성을 주입(DI) 받아서 사용하는 형태가 일반적이기 때문이었습니다.
- 상태(변경이 가능한 인스턴스 변수)를 갖는 객체들이 많다보면 코드의 흐름을 파악하는데 있어서 매우 복잡해짐을 느끼는데, Rails 에서는 Framework 차원에서 이를 방지하기 위한 장치가 없다는 것이 아쉬웠습니다.
  - 조금 억지스럽지만 예제 코드를 살펴보겠습니다. 아래는 상태를 갖지 않는 CardService 라는 java class 입니다. 상태를 갖지 않기 때문에 thread safe 하고, singleton 으로 만들어도 됩니다. (실제로 spring 에서 singleton instance 로 생성합니다.)

```java
@Service
class CardService {
  private final TeamService teamService;
  private final CardRepository cardRepository;

  CardService(TeamService teamService, CardRepository cardRepository) {
    this.teamService = teamService;
    this.cardRepository = cardRepository;
  }

  public void connectCardToTeam(Integer cardId, Integer teamId) {
    // ...
    teamService.createConnection(card, team);
  }
}
```

CardService 의 connectCardToTeam 메소드를 사용하기 위해 CardService 객체를 생성하는 코드는 개발자가 어디에도 작성할 필요가 없습니다.

### DB Entity layer 와 Query layer 의 명확한 분리

### cloud platform, MSA

### Static Type (java, kotlin)

### 선언적 방법으로 외부 API 연동의 편리성

#### 개발 생태계

- Java 는 Oracle, Spring 은 Pivotal 이라는 기업에서 governorship 을 가지고 유지보수를 하고 있습니다.
- 반면에 Ruby 와 RoR 모두 오픈 커뮤니티에 의존하고 있습니다.
- 각 언어, 프레임워크의 발전 속도, 안정성 측면에서 본다면, 아무래도 Java, Spring 쪽의 미래가 더 밝을 것이라고 예상해봅니다.

## Rails, Spring 각각의 강점 말고 다른 이야기

### 생산성

- Rails 는 생산성이 좋은 Web Application Framework 로 유명합니다. Spring 진영에서도 Spring Boot 가 나온 이래로 생산성은 Rails 못지 않다고 생각합니다. 단순히 생산성 측면 때문에 Spring 이 아니라 Rails 를 선택하는 일은 더 이상 존재하지 않는다고 생각합니다.

### Case convention

- Ruby 는 sneak\_case 를, Java 에서는 camelCase 를 변수명, 메소드명을 지을 때의 관례로 사용하고 있습니다.
- 개인적으로는 sneak\_case 보다 camelCase 가 가독성이 더 좋다고 생각하기도 하며, sneak\_case 를 사용할 때는 언더스코어라는 문자를 하나 더 입력해야하기 때문에 변수명, 메소드명이 좀 더 길어질 수 있는데 사소하지만, 언더스코어 문자 때문에 길어지는 것 자체는 다소 불편합니다.

### rails 의 ActiveModel 은 java 진영의 sql framework 인 jooq 와 비슷한 것 같다.

## 번외 : Ruby on Rails 에 아쉬운 점

## 마무리

- _(현재 드라마앤컴퍼니에서 다른 언어, Framework 의 도입에 대한 논의가 활발하게 진행중이다. 를 언급하며 마무리)_
