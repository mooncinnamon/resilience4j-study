# 장애 없는 서비스를 만들기 위한 Resilience4j

## Resilience4j란?
공식 docs - https://resilience4j.readme.io/docs
공식 github - https://github.com/resilience4j/resilience4j
비공식 번역 docs - https://godekdls.github.io/Resilience4j/contents/

~~HYSTRIX는 이제 안녕~~ 

hystrix와 다른점은 https://resilience4j.readme.io/docs/comparison-to-netflix-hystrix 를 읽으면 된다.

## Resilience4j 핵심 기능
- CircuitBreaker
- Bulkhead
- RateLimiter
- Retry
- TimeLimiter
- Cache

CircuitBreaker의 핵심 역할은 원래 기능을 대체할 Fallback을 실행시켜 장애의 전파를 방지
## Resilience4j를 활용한 적절한 상황들
Retry - 어떤 작업이 일시적으로 실패하지만 금방 다시 회복될 가능성이 있는 경우 효과적

1. 몇번까지 재시도 할 것인가
2. 재시도 간격은 얼마나 줄 것인가
3. 어떤 상황을 호출 실패로 볼 것인가

일반적인 네트워크 실패는 Retry 하였을때 장애를 회복할 수 있겠지만 Null Exception 같은 장애는 Retry로 장애가 해결되지 않는다.

CircuitBreaker - 여러 정보를 가져와 조합해 전달하는 API에서 상대적으로 중요한 정보를 우선적으로 전달해야할 때 중요 ex) 배송예고제는 누락되더라도 상품 정보는 전달되어야한다.

특정 조건에 빠질 때 비정상 상황으로 규정, 해당 기능에 대한 실행을 차단.
CircuitBreaker 동작 후, 특정 조건에 진입 시 시스템의 회복이라고 판단하고 정상적으로 기능 실행

Naver D2 - https://d2.naver.com/helloworld/6070967

## Resilience4j Project Dependencies

demo - https://github.com/lleellee0/resilience4j-demo-by-foo

의존성부터 보면
```xml
 <!-- resilience4j 사용에 필요한 의존성 시작 -->
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-aop</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>io.github.resilience4j</groupId>
        <artifactId>resilience4j-spring-boot2</artifactId>
        <version>${resilience4jVersion}</version>
    </dependency>
    <dependency>
        <groupId>io.github.resilience4j</groupId>
        <artifactId>resilience4j-all</artifactId>
        <version>${resilience4jVersion}</version>
    </dependency>
</dependencies>
```
resilience4j-spring-boot2 는 Spring Boot 2를 위해 필요한 의존성
resilience4j-all 은 retry나 circuitbreaker 같은 것을 사용하기 위한 의존성

## Resilience4j Retry Demo
```Java
@Retry(name = SIMPLE_RETRY_CONFIG, fallbackMethod = "fallback")
```
Retry 어노테이션으로 Resilience4j 의 Retry 모듈 동작을 확인하는 데모
