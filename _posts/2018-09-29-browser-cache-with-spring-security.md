---
layout: post
title: Spring Security 환경에서 브라우저 캐시 사용하기
category: Development
comments: true
---

웹 개발자이면서 브라우저 캐시에 대해 잘 몰랐다. `그냥 브라우저가 다 알아서 해주는 거겠지`라고만 생각했었다.

진행하고 있는 프로젝트는 리액트로, 시간이 지나면 지날수록 사이즈가 커짐에 따라 webpack 으로 bundle 한 결과물의 사이즈도 점점 커져만 갔다.

이에 따라 최적화를 위해 여러가지를 적용했다.

트리 쉐이킹, 비동기 import 청크, 공통 청크 등등..

그럼에도 불구하고 청크 수가 너무 많으면 그것 또한 문제이고, `node_modules` 의 패키지를 묶어놓은 `vendor` 는 청크하지 않는 것이 웹팩 설정 파일을 깔끔하게 유지하는 것 같아 그렇게 하진 않았다.

그러던 찰나, 생각해보니 브라우저 캐시가 작동하지 않는 것 같았다. 매번 resources 를 네트워크에서 새로 받는 것이었다. 파일 이름과 내용이 변하지 않았음에도 불구하고 그랬다.

당연히 브라우저 캐싱이 되어야 한다고 생각했는데, 그게 아니었다.

크롬의 개발자도구를 열어 Response Header 를 살펴보니 `Cache-Control` 헤더가 `no-cache no-store must-revalidate`의 값을 갖고 있었다.

이건 내가 설정한 게 아닌데 어디서 온걸까? 란 질문으로 시작하여 찾아보니 Spring MVC 설정 인터페이스인 `WebMvcConfigurer`(spring 4 이전까진 WebMvcConfigurerAdapter 클래스) 에서 캐시관련 설정을 할 수 있는 것을 알게 되어 아래와 같이 적용하였다.

```java
@Configuration
public class WebMvcConfiguration implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/{path:[\\w\\-]+}")
                .setViewName("forward:/");
        registry.addViewController("/**/{path:[\\w\\-]+}")
                .setViewName("forward:/");
    } // 그렇다, 스프링 애플리케이션 안의 프론트 엔드 렌더링을 템플릿 엔진이 아니라 SPA인 React로 Serve 하고 있다.

    // ... other configurations

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/resources/**")
                .addResourceLocations("classpath:/static/", "/bundle/")
                .setCacheControl(CacheControl.maxAge(14, TimeUnit.DAYS));
    }

}
```

그래서 이제 잘 되겠지! 라고 생각했는데, 여전히 Cache-Control 헤더의 값은 변하지 않았다.

또 찾아봤다.

스프링 시큐리티 Spring Security 가 `WebMvcConfigurer` 를 통해 설정한 캐시 설정을 덮어씌운다는 것을 알게 되었다.

기존에

```java
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
```
의 어노테이션들이 달린 설정 클래스가 있었는데, 이게 문제였던 것이다. 그러나, 이것을 없앨 순 없다.

workaround 로 해결가능한 방법은 특정 ant pattern과 일치하는 요청에 대해서는 덮어 쓰지 않도록 시큐리티 설정을 무시하는 설정이었다.

```java
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig {

    // ... other fields and constructor

    @Bean
    @Profile({"default", "dev", "local", "stg", "prod"})
    public WebSecurityConfigurerAdapter forDevelopment() {
        return new WebSecurityConfigurerAdapter() {
            @Override
            protected void configure(HttpSecurity http) throws Exception {
                // ... other configurations
            }

            @Override
            public void configure(WebSecurity webSecurity) throws Exception {
                webSecurity.ignoring().antMatchers("/bundle/**");  // 요놈이 핵심
            }
        };
    }
```

이제 브라우저 캐시가 잘 동작한다.

웹팩의 결과물인 resources 만 브라우저가 캐시하도록 하는 것이 내 목적이었으므로 이 방법으로 해결되었다.