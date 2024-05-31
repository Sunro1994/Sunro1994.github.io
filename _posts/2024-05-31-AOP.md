# AOP


## AOP란?

- 관점 지향 프로그래밍(AOP)은 프로그램 구조에 대한 또 다른 사고 방식을 제공함으로써 객체 지향 프로그래밍(OOP)를 보완한다.
- AOP는 횡단 관심사(Cross-cutting Concerns)를 모듈화하여 코드의 중복을 줄이고 유지보수를 용이하게 하는 프로그래밍 패러다임
- OOP의 주요 모듈 단위는 클래스인 반면, AOP 의 모듈 단위는 Aspect이다.
- Aspect는 여러 타입과 객체에 걸쳐 있는 관심사를 모듈화할 수 있게 한다.
- 횡단 관심사란 애플리케이션의 여러 부분에 공통적으로 적용되는 기능(로깅, 트랜잭션 관리 등)
- AOP는 하나의 프레임워크이다. 스프링 컨테이너 IoC 컨테이너는 AOP에 의존하지 않는다. AOP는 스프링 IoC를 보완하여 매우 강력한 미들웨어 솔루션을 제공한다.

## AOP의 주요 용어

1. Aspect
    - 여러 클래스에 있는 관심사를 모듈화 한것
    - 트랜잭션 관리는 횡단 관심사의 좋은 예이다
    - 스프링 AOP는 Aspect를 일반 클래스(스키마 기반 접근 방식)이나 @Aspect 어노테이션이 달린 일반 클래스를 사용하여 구현한다(@aspectJ스타일)
2. Target
    - 어드바이스된 객체라고도 표현한다.
    - 스프링 AOP는 런타임 프록시를 사용하여 구현하기 때문에 이 객체는 항상 프록시된 객체이다.
3. Advice
    - 특정 Join Point에서 Aspect가 수행하는 작업
    - 많은 AOP프레임워크에서는 어드바이스를 인터셉터로 모델링하고, Join Point주위에 인터셉터 체인을 유지한다.
    - Before
    - AfterReturning
    - AfterThrowing
    - After
    - Around
4. Joint Point
    - 어디에 적용할 것인가? 메서드, 필드, 객체, 생성자 등
    - 스프링 AOP에서는 Join Point는 항상 메서드 실행을 나타낸다.
5. Point Cut
    - Join Point를 매칭하는 조건
    - Advice는 포인트컷 표현식과 연괸되어 있으며, 포인트컷이 매칭하는 모든 Join Point에서 실행된다.(예: 특정 이름을 가진 메서드의 실행)
    - 스프링은 기본적으로 AspectJ포인트컷 표현식 언어를 사용한다.
    - 실제 advice가 적용될 지점, Spring AOP에서는 advice가 적용될 메서드를 선정
6. AOP Proxy(AOP프록시)
    - AOP 프록시는 Aspect 계약(어드바이스 메서드 실행 등)을 구현하기 위해 AOP프레임워크에 의해 생성된 객체이다.
    - 스프링 프레임워크에서 AOP프록시는 JDK 동적 프록시나 CGLIB프록시이다.
7. Weaving(위빙)
    - Aspect와 다른 애플리케이션 타입이나 객체를 결합하여 어드바이스된 객체를 생성하는 과정
    - 컴파일시, 로드시, 런타임시 수행 될 수 있다.
    - 스프링 AOP는 다른 순수 자바 AOP프레임워크처럼 런타임에 위빙을 수행한다.
    
    ---
    
    Around 어드바이스는 가장 일반적인 어드바이스 유형이다.
    
    스프링 AOP는 AspectJ처럼 모든 어드바이스 유형을 제공하기 떄문에 필요한 동작을 구현할 수 있는 가장 덜 강력한 어드바이스 유형을 사용하는 것이 좋다.
    
    예를 들어, 메서드의 반환 값을 사용하기 위해 캐시를 업데이트하기만 하면 된다면 Around보다 After Returning 어드바이스를 구현하는 것이 더 좋다.
    
    이를 통해 더 간단한 프로그래밍 모델을 제공하고 오류 가능성을 줄일 수 있다.
    
    Around 어드바이스에서 사용하는 JoinPoint의 proceed()메서드를 호출할 필요가 없으며, 따라서 이를 호출하지 않아도 된다.
    

## AOP의 구현 방법

- 컴파일
    - 컴파일 시점에 AOP를 적용시키는 방법
- 클래스 로드
    - 클래스 컴파일 후 클래스 로더가 메모리상에 올릴 때 AOP를 적용시키는 방법
- 프록시 패턴
    - 어떤 타켓 클래스를 부가 기능을 제공하는 프록시로 감싸서 사용한다.
    - Spring에서 주로 사용
    - IoC와 DI 방식으로 수행되기 때문에 가능한 부분

## AOP의 기능 및 목표

1. 순수 자바 구현
    - 스프링 AOP는 순수 자바로 구현되어 특별한 컴파일 과정이 필요 없고, 클래스 로더 계층을 제어할 필요가 없다.
    - 따라서 서블릿 컨테이너나 애플리케이션 서버에서 사용하기 적합하다.
2. 메서드 실행 조인 포인트 지원
    - 현재 스프링 AOP는 메서드 실행 조인 포인트만 지원
    - 필드 인터셉션은 구현되어 있지 않지만, 필요시 추가할 수 있다.
3. AOP와 IoC의 통합
    - 스프링 AOP의 목표는 완벽한 AOP구현이 아니라 AOP와 IoC의 밀접한 통합을 통해 엔터프라이즈 애플리케이션 문제를 해결하는 것
4. 일반적인 구성 방식
    - 스프링 AOP는 일반 빈 정의 구문을 사용하여 Aspect를 구성한다.
    - 이는 다른 AOP구현과 달리 매우 세밀한 객체를 어드바이스하는 것에는 적절하지 않지만, 엔터프라이즈 애플리케이션의 대부분 문제에 대한 해결책을 제시한다.
5. AspectJ와의 통합
    - 스프링 AOP는 AspectJ와 경쟁하지 않고 서로 상호 보완적이다.
6. 비침투성 원칙
    - 스프링 프레임워크는 비즈니스 모델에 프레임워크 고유의 클래스를 도입하도록 강요하지 않는 비침투성을 원칙으로 한다.
    - 그러나 필요에 따라 선택할 수 있는 유연성을 제공한다.
7. 유연한 제공
    - 사용자에게 AspectJ, Spring AOP 또는 둘 다를 선택할 수 있는 옵션을 제공하며, @AspectJ어노테이션 스타일 또는 스프링 XML 구성 스타일을 선택할 수 있다.
    

### 핵심 포인트

- 스프링 AOP는 특별한 컴파일 과정 없이 순수 자바로 구현됨.
- 메서드 실행 조인 포인트만 지원.
- 스프링 IoC와 밀접하게 통합되어 엔터프라이즈 문제 해결.
- AspectJ와 상호 보완적으로 사용 가능.
- 비침투성 원칙을 준수하면서 유연한 선택 옵션 제공.

## AOP프록시에 대해 더 자세하게!

- 스프링AOP는 기본적으로 표준 JDK동적 프록시를 사용하여 AOP프록시를 생성한다. 이를 통해 인터페이스를 프록시화 할 수 있다.
- 스프링 AOP는 CGLIB프록시도 사용할 수 있다. 이는 인터페이스가 아닌 클래스를 프록시화해야 할 때 필요하다. 기본적으로 비즈니스 객체가 인터페이스를 구현하지 않으면 CGLIB이 사용된다.
- 클래스 보다는 인터페이스를 사용하는 것이 좋은 관행이므로, 일반적인 비즈니스 클래스는 하나 이상의 비즈니스 인터페이스를 구현한다.
- 인터페이스에 선언되지 않은 메서드를 어드바이스 해야 하거나, 프록시된 객체를 구체 타입으로 메서드에 전달해야 하는 경우와 같이 드문 경우에 CGLIB 사용을 강제할 수 있다.

### 핵심 포인트

1. **JDK 동적 프록시**
    - 기본적으로 인터페이스를 프록시화할 때 사용됩니다.
2. **CGLIB 프록시**
    - 인터페이스가 아닌 클래스를 프록시화할 때 사용됩니다.
    - 비즈니스 객체가 인터페이스를 구현하지 않으면 기본적으로 사용됩니다.
3. **프록시 기반 구현**
    - 스프링 AOP는 프록시를 사용하여 AOP 기능을 제공합니다.
    

## @AspectJ

- @AspectJ는 애스펙트를 어노테이션이 붙은 일반 자바 클래스로 선언하는 스타일을 의미
- 스프링은 spectJ5와 동일한 어노테이션을 해석한다.
- 포인트컷 파싱 및 매칭을 위해 AspectJ에서 제공하는 라이브러리를 사용한다.
- 그러나 AOP 런타임은 여전히 순수 스프링 AOP이며, AspectJ컴파일러나 위버에 대한 의존성은 없다.

### @AspectJ JavaConfiguration에서 사용하기

```java
@Configuration
@EnableAspectJAutoProxy
public class AppConfig{
}
```

## Aspect 선언하기

- @AspectJ 지원이 활성화된 상태에서, 클래스에 @Aspect어노테이션이 붙은 Aspect가 있는 경우 애플리케이션 컨텍스트에 정의된 모든 빈은 스프링에 의해 자동으로 감지되어 스프링 AOP를 구성하는데 사용된다.

### Component Scanning을 통한 Aspect 자동 감지

- Aspect 클래스는 @Configuration 클래스의 @Bean 메서드를 통해 등록하거나 스프링 XML설정에서 일반 빈으로 등록 할 수 있다.
- 스프링이 클래스패스 스캐닝을 통해 자동으로 감지할 수 있다.
- 그러나 클래스패스에서 자동 감지를 하기 위해서는 @Aspect 어노테이션만으로는 충분하지 않다. 이 경우 별도의 @ComponentScan 어노테이션을 추가해야 한다.
- 또는 스프링의 컴포넌트 스캐너 규칙에 따라 자격을 갖춘 스테레오타입 어노테이션을 사용할 수 있다.

```java
@Aspect
@Component
public class NotVertyUsefulAspect{
}
```

### 다른 Aspect로 Aspect를 Advice할 수 있을까?

- Spring AOP에서는 Aspect자체가 다른 Aspect Advice Target이 될 수 없다.
- 클래스에 @Aspect 어노테이션이 붙으면 해당 클래스는 애스팩트로 표시되며, 자동 프록시 대상에서 제외된다.
- 해당 클래스가 Aspect가 되면 프록시로 만들 대상이 아니라는것, 이게 이해가 잘 안간다면 어떤것을 프록시 객체로 감싸서 Advice를 적용하는지 생각해보자

## PointCut 선언

- PointCut 선언은 관심있는 조인 포인트를 결정하여 어드바이스가 언제 실행될지를 제어한다.
- 스프링 AOP는 스프링 빈의 메서드 실행 조인 조인포인트만 지원하므로, 포인트 컷은 스프링 빈의 메서드 실행을 매칭하는 것으로 생각할 수 있다.
- 포인트 컷 언언에는 이름과 매개변수를 포함하는 시그니처와, 어떤 메서드 실행을 관심 대상으로 할 지를 결정하는 포인트컷 표현식 이렇게 두가지가 있다.
- @AspectJ 어노테이션 스타일에서는 포인트컷 시그니처가 일반 메서드 정의에 제공되며, @PointCut 어노테이션을 사용하여 포인트컷 표현식을 저장한다.(포인트컷 시그니처로 사용되는 메서드는 void 반환형이어야 한다.)

### 예제

```java
@Pointcut("execution(* transfer(..))") // 포인트컷 표현식
private void anyOldTransfer() {}  // 포인트컷 시그니처

```

- PointCut 어노테이션의 값으로 지정된 포인트컷 표현식은 일반적으로 AspectJ포인트컷 표현식이다.
- AspectJ 포인트컷 언어에 대한 자세한 내용은 AspectJ프로그래밍 가이드 관련 책을 참고하자

### 지원되는 포인트컷 지정자

- **execution**: 메서드 실행 조인 포인트를 매칭합니다. 스프링 AOP를 사용할 때 주로 사용하는 포인트컷 지정자.
- **within**: 특정 타입 내의 조인 포인트(스프링 AOP 사용 시 메서드 실행)를 매칭합니다.
- **this**: 프록시 객체가 주어진 타입의 인스턴스인 경우에 조인 포인트를 매칭합니다.
- **target**: 실제 대상 객체가 주어진 타입의 인스턴스인 경우에 조인 포인트를 매칭합니다.
- **args**: 메서드의 인수가 주어진 타입의 인스턴스인 경우에 조인 포인트를 매칭합니다.
- **@target**: 실행 중인 객체의 클래스에 주어진 타입의 어노테이션이 있는 경우에 조인 포인트를 매칭합니다.
- **@args**: 실행 중인 메서드의 인수의 런타임 타입에 주어진 타입의 어노테이션이 있는 경우에 조인 포인트를 매칭합니다.
- **@within**: 주어진 어노테이션이 있는 타입 내의 조인 포인트를 매칭합니다.
- **@annotation**: 조인 포인트의 대상이 주어진 어노테이션을 가지는 경우에 매칭합니다.

### 기타 포인트컷 타입

AspectJ 포인트컷 언어는 스프링에서 지원하지 않는 추가적인 포인트컷 지정자도 지원합니다: 

**call, get, set, preinitialization, staticinitialization, initialization, handler, adviceexecution, withincode, cflow, cflowbelow, if, @this, @withincode.** 스프링 AOP에서 이러한 포인트컷 지정자를 사용하면 IllegalArgumentException이 발생합니다.

### 스프링 AOP의 프록시 기반 특성

스프링 AOP는 프록시 기반 시스템이므로, 프록시 객체 내의 메서드 호출은 가로챌 수 없습니다. JDK 프록시의 경우, 프록시에서 공용 인터페이스 메서드 호출만 가로챌 수 있습니다. CGLIB를 사용할 때는 프록시의 공용 및 보호된 메서드 호출을 가로챌 수 있습니다(필요한 경우 패키지 가시성 메서드도 가로챌 수 있습니다). 그러나 프록시를 통한 일반적인 상호작용은 항상 공용 시그니처를 통해 설계되어야 합니다.

포인트컷 정의는 일반적으로 가로챈 모든 메서드에 대해 매칭됩니다. 포인트컷이 공용 메서드만을 엄격히 대상으로 한다면, CGLIB 프록시 시나리오에서도 해당 포인트컷을 공용 메서드에만 매칭되도록 정의해야 합니다.

### 포인트컷 표현식 결합

- &&, ||, ! 연산자를 사용하여 포인트컷 표현식을 결합 할 수 있다.
- 또한 포인트컷 표현식을 이름으로 참조할 수 있다.

### 예시

- **publicMethod**: 공용 메서드 실행 조인 포인트를 매칭합니다.
- **inTrading**: trading 모듈 내에서 실행되는 메서드를 매칭합니다.
- **tradingOperation**: trading 모듈 내의 모든 공용 메서드 실행을 매칭합니다.

더 복잡한 포인트컷 표현식은 위와 같이 더 작은 이름 있는 포인트컷으로 구성하는 것이 좋습니다. 포인트컷을 이름으로 참조할 때는 일반적인 자바 가시성 규칙이 적용됩니다.

```java
package com.xyz;

public class Pointcuts {

    @Pointcut("execution(public * *(..))")
    public void publicMethod() {} 

    @Pointcut("within(com.xyz.trading..*)")
    public void inTrading() {} 

    @Pointcut("publicMethod() && inTrading()")
    public void tradingOperation() {} 
}
```

### 공통 포인트컷 정의 공유

- 엔터프라이즈 애플리케이션에서 자주 사용되는 포인트컷 표현식을 캡처하기 위해 전용 클래스를 정의하는 것이 좋습니다. 이러한 클래스는 다음 예제와 유사합니다
- 아래 예제에서는 여러 레이어에 대한 포인트컷을 정의합니다. `CommonPointcuts` 클래스에서 정의된 포인트컷은 필요한 곳 어디에서든 포인트컷 표현식으로 참조할 수 있습니다.

```java
package com.xyz;

import org.aspectj.lang.annotation.Pointcut;

public class CommonPointcuts {

    @Pointcut("within(com.xyz.web..*)")
    public void inWebLayer() {}

    @Pointcut("within(com.xyz.service..*)")
    public void inServiceLayer() {}

    @Pointcut("within(com.xyz.dao..*)")
    public void inDataAccessLayer() {}

    @Pointcut("execution(* com.xyz..service.*.*(..))")
    public void businessService() {}

    @Pointcut("execution(* com.xyz.dao.*.*(..))")
    public void dataAccessOperation() {}

}
```

### 예제: 포인트컷 표현식

- 스프링 AOP 사용자들은 대부분 excution 포인트컷 지정자를 가장 자주 사용한다.

```java
execution(modifiers-pattern? ret-type-pattern declaring-type-pattern? name-pattern(param-pattern) throws-pattern?)

```

- 여기서 `ret-type-pattern`, `name-pattern`, `param-pattern`을 제외한 모든 부분은 선택 사항입니다. 반환 타입 패턴은 조인 포인트를 매칭하기 위해 메서드의 반환 타입이 무엇이어야 하는지를 결정한다.
- `*`는 모든 반환 타입을 매칭합니다. 완전한 타입 이름을 사용하면 해당 타입을 반환하는 메서드만 매칭한다.
- 이름 패턴은 메서드 이름을 매칭하며, `*` 와일드카드를 사용하여 이름 패턴의 일부 또는 전체를 매칭할 수 있다.
- 선언 타입 패턴을 지정할 경우, 이를 이름 패턴 구성 요소에 연결하기 위해 점(`.`)을 포함해야  한다.
- 파라미터 패턴은 다음과 같습니다: `()`는 매개변수가 없는 메서드를 매칭하며, `( .. )`는 매개변수가 0개 이상인 메서드를 매칭합니다. `(*)` 패턴은 하나의 매개변수를 가지는 메서드를 매칭합니다.
- `(*,String)`은 두 개의 매개변수를 가지는 메서드를 매칭합니다. 첫 번째 매개변수는 모든 타입이 될 수 있으며, 두 번째는 반드시 `String`이어야 합니다.

- **모든 public 메서드 실행:**
    
    ```java
    execution(public * *(..))
    ```
    
- **이름이 `set`으로 시작하는 모든 메서드 실행:**
    
    ```java
    execution(* set*(..))
    ```
    
- **`AccountService` 인터페이스에 정의된 모든 메서드 실행:**
    
    ```java
    execution(* com.xyz.service.AccountService.*(..))
    ```
    
- **`service` 패키지에 정의된 모든 메서드 실행:**
    
    ```java
    execution(* com.xyz.service.*.*(..))
    ```
    
- **`service` 패키지 또는 그 하위 패키지에 정의된 모든 메서드 실행:**
    
    ```java
    execution(* com.xyz.service..*.*(..))
    ```
    
- **`service` 패키지 내의 모든 조인 포인트 (스프링 AOP에서 메서드 실행만):**
    
    ```java
    within(com.xyz.service.*)
    ```
    
- **`service` 패키지 또는 그 하위 패키지 내의 모든 조인 포인트 (스프링 AOP에서 메서드 실행만):**
    
    ```java
    within(com.xyz.service..*)
    ```
    
- **프록시가 `AccountService` 인터페이스를 구현하는 경우의 모든 조인 포인트 (스프링 AOP에서 메서드 실행만):**
    
    ```java
    this(com.xyz.service.AccountService)
    ```
    
- **대상 객체가 `AccountService` 인터페이스를 구현하는 경우의 모든 조인 포인트 (스프링 AOP에서 메서드 실행만):**
    
    ```java
    target(com.xyz.service.AccountService)
    ```
    
- **하나의 매개변수를 가지며, 런타임 시 전달된 인수가 `Serializable`인 경우의 모든 조인 포인트 (스프링 AOP에서 메서드 실행만):**
    
    ```java
    args(java.io.Serializable)
    ```
    
- **대상 객체의 클래스에 `@Transactional` 어노테이션이 있는 경우의 모든 조인 포인트 (스프링 AOP에서 메서드 실행만):**
    
    ```java
    @target(org.springframework.transaction.annotation.Transactional)
    ```
    
- **대상 객체의 선언된 타입에 `@Transactional` 어노테이션이 있는 경우의 모든 조인 포인트 (스프링 AOP에서 메서드 실행만):**
    
    ```java
    @within(org.springframework.transaction.annotation
    ```
    

## 좋은 포인트컷 작성하기란?

- 코드 검토 및 각 조인 포인트가 주어진 포인트컷과 일치하는 여부를 결정하는 과정(정적,  동적 매칭)은 비용이 많이 든다.
- 동적 매칭이란 정적 분석만으로는 일치 여부를 완전히 결정 할 수 없으며, 코드 실행 시 실제 일치를 결정하기 위해 테스트 코드가 포함된다는 의미이다.
- 포인트컷 선언을 처음 접하면 AspectJ는 이를 매칭 과정에 최적화된 형태로 다시 작성한다.
- 기본적으로 포인트컷은 DNF(Disjunctive Normal Form)로 다시 작성되며, 평가 비용이 저렴한 구성 요소가 먼저 확인되도록 정렬된다.
- 따라서 다양한 포인트컷 지정자의 성능을 이해할 필요 없이 포인트컷 선언에서 원하는 순서대로 제공이 가능하다.
- 하지만! AspectJ는 제공된 정보만을 토대로 작업할 수 없다. 매칭의 최적 성능을 위해서는 무엇을 달성하려는지 생각하고 정의에서 매칭 검색 범위를 최대한 좁혀야한다.
- 기존 지정자는 자연스럽게 세가지 그룹으로 나뉜다. (종류,범위,콘텍스트)
    - **종류 지정자**: 특정 종류의 조인 포인트를 선택합니다. 예: `execution`, `get`, `set`, `call`, `handler`.
    - **범위 지정자**: 관심 있는 조인 포인트 그룹을 선택합니다(아마도 여러 종류). 예: `within`, `withincode`.
    - **컨텍스트 지정자**: 컨텍스트에 따라 매칭하고 선택적으로 바인딩합니다. 예: `this`, `target`, `@annotation`.
- 잘 작성된 포인트컷은 최소한 종류 지정자와 범위 지정자를 포함해야 한다.
- 컨텍스트 지정자를 포함하여 조인 포인트 컨텍스트에 따라 매칭하거나 해당 컨텍스트를 어드바이스에서 사용하기 위해 바인딩 할 수 있다.종류 지정자나 컨텍스트 지정자만 사용하면 작동은 하지만 추가 처리와 분석으로 인해 위빙 성능에 영향을 미칠 수 있다.
- 범위 지정자는 매우 빠르게 매칭되므로 AspectJ가 더이상 처리할 필요가 없는 조인 포인트로 그룹을 빠르게 제외할 수 있다.
- **`가능한 경우 항상 범위 지정자를 포함해야 한다.`**
- **잘 작성된 포인트컷**
    
    ```java
    @Pointcut("execution(* com.xyz.service.*.*(..)) && within(com.xyz.service..*)")
    public void serviceLayer() {}
    
    ```
    
    이 포인트컷은 `com.xyz.service` 패키지와 그 하위 패키지에 정의된 모든 메서드 실행을 매칭합니다. `execution` 지정자와 `within` 지정자를 모두 사용하여 범위를 좁히고 성능을 최적화했습니다.
    
- **컨텍스트 지정자를 포함한 포인트컷**
    
    ```java
    @Pointcut("execution(* com.xyz.service.*.*(..)) && within(com.xyz.service..*) && @annotation(org.springframework.transaction.annotation.Transactional)")
    public void transactionalMethods() {}
    ```
    
    - 이 포인트컷은 `com.xyz.service` 패키지와 그 하위 패키지에 정의된, `@Transactional` 어노테이션이 붙은 모든 메서드 실행을 매칭합니다. 종류 지정자, 범위 지정자, 컨텍스트 지정자를 모두 사용하여 매칭 범위를 더욱 구체화했습니다.

## 어드바이스 선언

- 어드바이스는 포인트컷 표현식과 연결되어 포인트컷이 매칭된 메서드 실행 전에, 후에, 또는 주변에서 실행된다.
    
    ### Before 어드바이스
    
    Before 어드바이스는 @Before 어노테이션을 사용하여 애스펙트에서 선언할 수 있습니다.
    
    ### 인라인 포인트컷 표현식을 사용하는 예제
    
    ```java
    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.Before;
    
    @Aspect
    public class BeforeExample {
    
        @Before("execution(* com.xyz.dao.*.*(..))")
        public void doAccessCheck() {
            // 접근 권한 검사 로직
        }
    }
    ```
    
    ### 이름이 지정된 포인트컷을 사용하는 예제
    
    ```java
    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.Before;
    
    @Aspect
    public class BeforeExample {
    
        @Before("com.xyz.CommonPointcuts.dataAccessOperation()")
        public void doAccessCheck() {
            // 접근 권한 검사 로직
        }
    }
    ```
    
    ### After Returning 어드바이스
    
    After returning 어드바이스는 매칭된 메서드가 정상적으로 반환될 때 실행됩니다. @AfterReturning 어노테이션을 사용하여 선언할 수 있습니다.
    
    ### 기본 예제
    
    ```java
    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.AfterReturning;
    
    @Aspect
    public class AfterReturningExample {
    
        @AfterReturning("execution(* com.xyz.dao.*.*(..))")
        public void doAccessCheck() {
            // 접근 권한 검사 로직
        }
    }
    
    ```
    
    ### 반환 값을 사용하는 예제
    
    ```java
    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.AfterReturning;
    
    @Aspect
    public class AfterReturningExample {
    
        @AfterReturning(pointcut = "execution(* com.xyz.dao.*.*(..))", returning = "retVal")
        public void doAccessCheck(Object retVal) {
            // 반환 값 처리 로직
        }
    }
    
    ```
    
    ### After Throwing 어드바이스
    
    After throwing 어드바이스는 매칭된 메서드가 예외를 던지며 종료될 때 실행됩니다. @AfterThrowing 어노테이션을 사용하여 선언할 수 있습니다.
    
    ### 기본 예제
    
    ```java
    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.AfterThrowing;
    
    @Aspect
    public class AfterThrowingExample {
    
        @AfterThrowing("execution(* com.xyz.dao.*.*(..))")
        public void doRecoveryActions() {
            // 복구 작업 로직
        }
    }
    
    ```
    
    ### 특정 예외를 사용하는 예제
    
    ```java
    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.AfterThrowing;
    
    @Aspect
    public class AfterThrowingExample {
    
        @AfterThrowing(pointcut = "execution(* com.xyz.dao.*.*(..))", throwing = "ex")
        public void doRecoveryActions(DataAccessException ex) {
            // 복구 작업 로직
        }
    }
    
    ```
    
    ### After (Finally) 어드바이스
    
    After (finally) 어드바이스는 매칭된 메서드 실행이 종료될 때 실행됩니다. @After 어노테이션을 사용하여 선언할 수 있습니다.
    
    ### 예제
    
    ```java
    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.After;
    
    @Aspect
    public class AfterFinallyExample {
    
        @After("execution(* com.xyz.dao.*.*(..))")
        public void doReleaseLock() {
            // 리소스 해제 로직
        }
    }
    
    ```
    
    ### Around 어드바이스
    
    Around 어드바이스는 매칭된 메서드 실행 전후에 실행되며, 메서드 실행 여부 및 실행 방법을 결정할 수 있습니다. @Around 어노테이션을 사용하여 선언할 수 있습니다.
    
    ### 예제
    
    ```java
    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.Around;
    import org.aspectj.lang.ProceedingJoinPoint;
    
    @Aspect
    public class AroundExample {
    
        @Around("execution(* com.xyz.service.*.*(..))")
        public Object doBasicProfiling(ProceedingJoinPoint pjp) throws Throwable {
            // 시작 타이머
            Object retVal = pjp.proceed();
            // 종료 타이머
            return retVal;
        }
    }
    
    ```
    
    ### 어드바이스 매개변수
    
    스프링은 완전한 타입의 어드바이스를 제공하므로, 항상 `Object[]` 배열을 사용하는 대신 어드바이스 시그니처에서 필요한 매개변수를 선언할 수 있습니다.
    
    ### 현재 조인 포인트에 대한 접근
    
    모든 어드바이스 메서드는 첫 번째 매개변수로 `org.aspectj.lang.JoinPoint` 타입의 매개변수를 선언할 수 있습니다. Around 어드바이스의 경우, 첫 번째 매개변수로 `ProceedingJoinPoint` 타입을 선언해야 합니다.
    
    ### JoinPoint 인터페이스의 유용한 메서드
    
    - `getArgs()`: 메서드 인수를 반환합니다.
    - `getThis()`: 프록시 객체를 반환합니다.
    - `getTarget()`: 대상 객체를 반환합니다.
    - `getSignature()`: 어드바이스되는 메서드의 설명을 반환합니다.
    - `toString()`: 어드바이스되는 메서드의 유용한 설명을 출력합니다.
    
    이 예제들은 스프링 AOP의 다양한 어드바이스 타입을 설명하며, 각 어드바이스가 어떻게 작동하는지 보여줍니다.
    

이미 반환 값 또는 예외 값을 바인딩하는 방법에 대해 살펴보았습니다(After Returning 및 After Throwing 어드바이스 사용). 어드바이스 본문에서 매개변수 값을 사용할 수 있게 하려면 `args`의 바인딩 형식을 사용할 수 있습니다. `args` 표현식에서 타입 이름 대신 매개변수 이름을 사용하면 어드바이스가 호출될 때 해당 매개변수 값이 전달됩니다.

### 예제: DAO 작업의 첫 번째 매개변수로 `Account` 객체를 사용하는 메서드 실행에 어드바이스 추가

```java
@Before("execution(* com.xyz.dao.*.*(..)) && args(account,..)")
public void validateAccount(Account account) {
    // 어드바이스 로직
}

```

위의 `args(account,..)` 부분은 두 가지 목적을 가집니다. 첫째, 메서드가 최소한 하나의 매개변수를 가지고 있으며, 해당 매개변수가 `Account` 인스턴스인 경우에만 매칭되도록 합니다. 둘째, 실제 `Account` 객체를 어드바이스의 `account` 매개변수를 통해 사용할 수 있게 합니다.

### 이름이 지정된 포인트컷을 사용하는 방법

```java
@Pointcut("execution(* com.xyz.dao.*.*(..)) && args(account,..)")
private void accountDataAccessOperation(Account account) {}

@Before("accountDataAccessOperation(account)")
public void validateAccount(Account account) {
    // 어드바이스 로직
}

```

### 어노테이션을 사용한 예제

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Auditable {
    AuditCode value();
}

@Before("com.xyz.Pointcuts.publicMethod() && @annotation(auditable)")
public void audit(Auditable auditable) {
    AuditCode code = auditable.value();
    // 어드바이스 로직
}

```

### 어드바이스 매개변수와 제네릭

스프링 AOP는 클래스 선언과 메서드 매개변수에 사용된 제네릭을 처리할 수 있습니다. 다음과 같은 제네릭 타입이 있다고 가정해봅니다:

```java
public interface Sample<T> {
    void sampleGenericMethod(T param);
    void sampleGenericCollectionMethod(Collection<T> param);
}

```

메서드 타입의 인터셉션을 특정 매개변수 타입으로 제한하려면 어드바이스 매개변수를 인터셉션하려는 매개변수 타입에 바인딩할 수 있습니다:

```java
@Before("execution(* ..Sample+.sampleGenericMethod(*)) && args(param)")
public void beforeSampleMethod(MyType param) {
    // 어드바이스 구현
}

```

그러나 제네릭 컬렉션에는 이 접근 방식이 적용되지 않습니다. 따라서 다음과 같은 포인트컷을 정의할 수 없습니다:

```java
@Before("execution(* ..Sample+.sampleGenericCollectionMethod(*)) && args(param)")
public void beforeSampleMethod(Collection<MyType> param) {
    // 어드바이스 구현
}

```

이 경우 매개변수를 `Collection<?>`으로 타이핑하고, 요소의 타입을 수동으로 확인해야 합니다.

### 매개변수 이름 결정

어드바이스 호출에서 매개변수 바인딩은 포인트컷 표현식에서 사용된 이름을 어드바이스 및 포인트컷 메서드 시그니처에 선언된 매개변수 이름과 일치시키는 것에 의존합니다. 스프링 AOP는 매개변수 이름을 결정하기 위해 여러 ParameterNameDiscoverer 구현을 사용합니다. 각각의 디스커버러는 매개변수 이름을 결정할 기회를 가지며, 첫 번째 성공한 디스커버러가 승리합니다. 등록된 디스커버러 중 어느 것도 매개변수 이름을 결정할 수 없는 경우 예외가 발생합니다.

### 매개변수 이름 명시

@AspectJ 어드바이스 및 포인트컷 어노테이션에는 `argNames` 속성을 사용하여 어노테이션된 메서드의 매개변수 이름을 지정할 수 있습니다. 예를 들어:

```java
@Before(value = "com.xyz.Pointcuts.publicMethod() && target(bean) && @annotation(auditable)", argNames = "bean,auditable")
public void audit(Object bean, Auditable auditable) {
    AuditCode code = auditable.value();
    // 어드바이스 로직
}

```

매개변수 타입이 `JoinPoint`, `ProceedingJoinPoint`, 또는 `JoinPoint.StaticPart`인 경우, `argNames` 속성에서 매개변수 이름을 생략할 수 있습니다:

```java
@Before(value = "com.xyz.Pointcuts.publicMethod() && target(bean) && @annotation(auditable)", argNames = "bean,auditable")
public void audit(JoinPoint jp, Object bean, Auditable auditable) {
    AuditCode code = auditable.value();
    // 어드바이스 로직
}

```

이 예제들은 스프링 AOP의 다양한 어드바이스 타입을 설명하며, 각 어드바이스가 어떻게 작동하는지 보여줍니다.