# Spring DispatcherServlet 와 AOP 개념 및 차이

`DispatcherServlet`은 스프링 웹 애플리케이션의 핵심 구성 요소로, **프론트 컨트롤러(Front Controller)** 패턴을 구현하고 `DispatcherServlet`은 웹 애플리케이션의 **중앙 진입점** 역할을 하며,
모든 HTTP 요청을 받아서 이를 적절한 핸들러(컨트롤러)로 전달하고, 요청을 처리한 후 적합한 응답을 생성하여 반환하는 역할을 수행 한다.

#### **DispatcherServlet의 역할** {id="dispatcherservlet_1"}
`DispatcherServlet`은 다음과 같은 중요한 역할을 수행.

- **요청 수신**: 클라이언트로부터 들어오는 모든 HTTP 요청을 받아들이는 역할을 하고, 스프링 웹 애플리케이션에서는 웹.xml이나 자바 설정 파일을 통해 `DispatcherServlet`이 모든 요청을 처리하도록 설정 됨.
- **핸들러 찾기**: `HandlerMapping`을 통해 해당 요청을 처리할 수 있는 **컨트롤러(핸들러)** 를 찾음.
- **요청 처리**: 핸들러(컨트롤러)를 찾아서 그 메서드를 호출해 요청을 처리.
- **뷰 결정**: 요청 처리 결과에 따라 어떤 뷰(view)를 사용해야 할지 결정하고, 그 뷰에 데이터를 전달.
- **응답 반환**: 최종적으로 클라이언트에게 처리된 결과를 응답으로 반환.

### **2. DispatcherServlet 동작 흐름**

스프링에서 `DispatcherServlet`은 요청을 처리하는 데 여러 컴포넌트들과 협력 하며 이 과정은 아래의 흐름과 같다.

#### **요청 처리 흐름**

1. **요청 수신**: 사용자가 URL을 통해 웹 애플리케이션에 요청을 보내고, 이때, 모든 HTTP 요청은 `DispatcherServlet`이 받게 됨.

2. **핸들러 탐색 (HandlerMapping)**: `DispatcherServlet`은 해당 요청을 처리할 핸들러(즉, 컨트롤러)를 찾기 위해 **`HandlerMapping`** 을 사용하며 `HandlerMapping`은 URL 패턴이나 애너테이션을 기준으로 적절한 컨트롤러 메서드를 매핑하여 반환 해 준다.
   
   - 주로 스프링에서 사용하는 `@RequestMapping`, `@GetMapping`, `@PostMapping` 등의 애너테이션이 핸들러 메서드와 URL을 연결하는 역할을 수행함.

3. **핸들러 어댑터 (HandlerAdapter)**: 핸들러(컨트롤러)가 결정되면, `DispatcherServlet`은 그 핸들러를 실행하기 위해 **`HandlerAdapter`** 를 호출하고, 
   `HandlerAdapter`는 선택된 핸들러(컨트롤러)를 실행하는 데 필요한 메커니즘을 제공.
   - ex) 예를 들어, 컨트롤러가 일반 자바 객체일 수도 있고, 다른 종류의 핸들러일 수도 있는데, `HandlerAdapter`는 다양한 핸들러를 실행할 수 있음.

7. **핸들러 실행**: `HandlerAdapter`는 실제로 컨트롤러의 메서드를 호출하여 비즈니스 로직을 실행하는데, 이 단계에서 데이터베이스와 상호작용하거나 다른 비즈니스 로직을 수행한 후 결과를 반환.
8. 
9. **모델과 뷰 처리 (ViewResolver)**: 핸들러가 작업을 완료하면, 결과를 **`ModelAndView`** 객체로 반환 하는데, 이 객체는 뷰 이름과 뷰에 전달할 데이터를 포함 하고 이후 **`ViewResolver`** 가 실행되어 요청에 맞는 뷰를 결정하고, 해당 뷰를 렌더링

10. **응답 반환**: `DispatcherServlet`은 결정된 뷰를 통해 응답을 렌더링하고 클라이언트에게 HTML, JSON, XML 등 적절한 형태로 응답을 반환 해준다.

### **3. DispatcherServlet과 관련된 주요 컴포넌트**

스프링의 요청 처리 과정에서 `DispatcherServlet`은 여러 컴포넌트와 협력하여 작업을 수행 하는데, 주요 컴포넌트는 다음과 같다.

#### **1) HandlerMapping**
- **역할**: URL 패턴이나 기타 매핑 정보를 기반으로 요청을 처리할 적합한 핸들러(컨트롤러)를 찾아줌.
- **예시**: `RequestMappingHandlerMapping`은 `@RequestMapping` 애너테이션을 사용하여 URL 패턴과 컨트롤러 메서드를 매핑.

#### **2) HandlerAdapter**
- **역할**: 찾은 핸들러를 실제로 실행할 수 있도록 돕는 어댑터. 다양한 종류의 핸들러(컨트롤러)를 처리할 수 있도록 해준다.
- **예시**: `RequestMappingHandlerAdapter`는 `@RequestMapping`이 있는 메서드를 처리하는 어댑터.

#### **3) ViewResolver**
- **역할**: 컨트롤러가 반환한 뷰 이름에 따라 어떤 뷰를 렌더링할지를 결정 한다.
- **예시**: `InternalResourceViewResolver`는 JSP 파일을 뷰로 사용하는 경우에 사용되며, 뷰 이름에 대해 물리적인 JSP 파일 경로를 찾는다.

#### **4) ExceptionResolver**
- **역할**: 요청 처리 중 발생한 예외를 처리하여 적절한 응답을 반환하도록 한다.
- **예시**: `HandlerExceptionResolver`는 발생한 예외에 맞는 처리 방법을 결정하고, 오류 페이지를 렌더링하거나 다른 적절한 처리를 수행 한다.

### **4. DispatcherServlet 설정**

스프링 부트에서는 기본적으로 `DispatcherServlet`이 자동으로 설정되지만, XML이나 Java Config를 사용하여 명시적으로 설정할 수도 있다.

#### **스프링 부트에서 DispatcherServlet 설정** {id="dispatcherservlet_2"}
스프링 부트는 기본적으로 `DispatcherServlet`을 자동으로 설정해주므로 별도의 설정이 필요하지 않지만, 필요할 경우 `application.properties` 또는 `application.yml` 파일에서 설정을 수정할 수 있다.

```yaml
spring.mvc.servlet.path=/api/*
```
이 설정은 `DispatcherServlet`이 `/api/*` 경로에 대한 요청만 처리하도록 경로를 제한 한다.

#### **DispatcherServlet 수동 설정 (Java Config)**

```java
@Configuration
public class WebAppInitializer implements WebApplicationInitializer {
    @Override
    public void onStartup(ServletContext servletContext) throws ServletException {
        AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
        context.register(AppConfig.class);
        
        DispatcherServlet dispatcherServlet = new DispatcherServlet(context);
        ServletRegistration.Dynamic registration = servletContext.addServlet("dispatcher", dispatcherServlet);
        registration.setLoadOnStartup(1);
        registration.addMapping("/");
    }
}
```
이 코드는 스프링 설정을 수동으로 적용하여 `DispatcherServlet`을 초기화 한다.

### **5. DispatcherServlet의 장점**

- **중앙화된 요청 처리**: 모든 요청이 `DispatcherServlet`을 거치므로, 로깅, 보안, 국제화, 예외 처리 등 공통된 기능을 한곳에서 처리할 수 있다.
- **유연성**: 다양한 핸들러, 뷰 리졸버, 모델 객체 등을 손쉽게 통합할 수 있다.
- **모듈화**: 각 기능이 컴포넌트 단위로 분리되어 있어, 필요에 따라 교체 및 확장이 용이하다.

### **결론**

스프링의 `DispatcherServlet`은 웹 애플리케이션의 중앙 진입점이자 요청 처리 흐름을 제어하는 중요한 구성 요소 이고, `HandlerMapping`, `HandlerAdapter`, `ViewResolver` 등의 컴포넌트와 협력하여 복잡한 웹 요청을 체계적으로 처리하며 개발자가 비즈니스 로직에 집중할 수 있도록 도와준다.


# DispatcherServlet과 AOP의 차이
스프링의 **DispatcherServlet**과 **AOP**(Aspect-Oriented Programming)는 각각 스프링 프레임워크의 중요한 기능이지만, 그 목적과 동작 방식은 상당히 다르다.

### 1. **DispatcherServlet**
#### **역할**:
- **DispatcherServlet**은 스프링 웹 애플리케이션에서 **요청을 처리**하는 중심 역할을 한다.. 
- **프론트 컨트롤러 패턴**을 구현하며, 모든 **HTTP 요청을 수신**하여 적절한 컨트롤러에게 전달하고, 결과를 반환하는 과정을 관리한다.
  
#### **DispatcherServlet의 주요 기능**: {id="dispatcherservlet_3"}
- **요청 라우팅**: 클라이언트의 HTTP 요청을 핸들러(컨트롤러)에 전달한다.
- **응답 처리**: 컨트롤러가 처리한 결과를 뷰로 전달하고 최종 응답을 생성한다.
- **뷰 해석**: 요청 처리 결과를 뷰에 매핑하여 사용자에게 데이터를 렌더링한다.
- **애플리케이션 진입점**: 모든 요청은 먼저 `DispatcherServlet`을 거치기 때문에, 필터링, 로깅, 보안 등 공통 기능을 쉽게 적용할 수 있다.

#### **DispatcherServlet의 동작 방식**: {id="dispatcherservlet_4"}
1. 클라이언트가 서버에 HTTP 요청을 보낸다.
2. 요청은 `DispatcherServlet`이 먼저 받는다.
3. `DispatcherServlet`은 요청을 처리할 적절한 컨트롤러를 찾고 그 컨트롤러의 메서드를 실행한다.
4. 컨트롤러가 비즈니스 로직을 처리하고 결과를 반환하면, `DispatcherServlet`이 적절한 뷰를 찾아서 응답을 생성하고 클라이언트에게 반환한한다.

**핵심 역할**: DispatcherServlet은 주로 **웹 애플리케이션에서 요청/응답 처리 흐름**을 관리하는 데 중점을 둔다.

---

### 2. **AOP (Aspect-Oriented Programming)**

#### **역할**:
- **AOP**는 애플리케이션에서 **`횡단 관심사(cross-cutting concern)`** 를 처리하는 데 사용되는데. 
  횡단 관심사는 로깅, 보안, 트랜잭션 관리, 예외 처리 등 비즈니스 로직과 직접 관련되지 않지만, 여러 곳에서 공통적으로 필요로 하는 기능을 말한다.
- AOP의 목적은 비즈니스 로직과 공통 기능을 **명확하게 분리**하여 코드를 모듈화하고 유지보수성을 향상시키는 것.

#### **AOP의 주요 기능**: {id="aop_1"}
- **Aspect**: 횡단 관심사를 모듈화하는 객체. 예를 들어, 로깅 기능을 하나의 Aspect로 구현할 수 있다.
- **Join Point**: AOP가 적용될 수 있는 지점으로, 일반적으로 메서드 실행 전후나 예외 발생 시점.
- **Advice**: 실제로 실행되는 코드로, 특정 시점에 실행될 동작을 정의한다. 종류로는 **Before, After, Around** 등이 있다.
- **Pointcut**: AOP가 적용될 구체적인 위치를 정의 한다. 특정 패키지나 메서드에만 AOP를 적용하고 싶을 때 사용 한다.

#### **AOP의 동작 방식**: {id="aop_2"}
1. **Advice**가 정의된 코드(Aspect)가 특정 지점(Join Point)에 적용된다.
2. 스프링 AOP는 런타임 시 **프록시 패턴**을 사용하여 특정 메서드 호출 전에, 후에, 또는 메서드를 둘러싸서 부가 기능을 실행한다.
3. 이 방식으로 횡단 관심사(예: 트랜잭션 처리, 로깅, 보안)가 메인 비즈니스 로직과 분리된다.

**핵심 역할**: AOP는 **`비즈니스 로직 외의 횡단 관심사를 분리`** 하여 여러 클래스에서 중복되는 공통 로직을 하나의 장소에서 관리할 수 있게 하는 데 중점을 둔다.

---

### 3. **DispatcherServlet과 AOP의 주요 차이점**

| **`특징`**       | **`DispatcherServlet`**                        | **`AOP`**                                      |
| -------------- | ---------------------------------------------- | ---------------------------------------------- |
| **`목적`**       | 웹 요청/응답을 처리하는 중앙 제어 역할                         | 비즈니스 로직과 횡단 관심사의 분리                            |
| **`적용 대상`**    | HTTP 요청 처리 플로우 (컨트롤러, 뷰 리졸버 등)                 | 로깅, 트랜잭션, 보안 등 비즈니스 로직과 관련 없는 횡단 관심사           |
| **`동작 방식`**    | 요청을 컨트롤러로 전달하고 응답을 처리하는 흐름 제어                  | 프록시 패턴을 사용하여 특정 메서드 실행 전후 또는 둘러싸서 부가 기능 실행     |
| **`관련 컴포넌트`**  | HandlerMapping, HandlerAdapter, ViewResolver 등 | Aspect, Join Point, Advice, Pointcut 등         |
| **`기본 개념`**    | 프론트 컨트롤러 패턴 구현                                 | 애스펙트 지향 프로그래밍 (Aspect-Oriented Programming) 구현 |
| **`주 사용 사례`**  | REST API, MVC 패턴의 웹 요청 처리                      | 로깅, 트랜잭션 관리, 보안, 예외 처리 등 공통 기능 처리              |
| **`코드 흐름 제어`** | 주로 HTTP 요청/응답 사이클에서 동작                         | 특정 메서드 실행 전후 또는 예외 발생 시 횡단 관심사 실행              |

---

### 4. **예시 코드로 DispatcherServlet과 AOP 비교**

#### **DispatcherServlet에서의 요청 처리 예시 (컨트롤러 기반)**

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        // 사용자 정보를 조회하는 비즈니스 로직
        return userService.getUserById(id);
    }
}
```

여기서 `DispatcherServlet`은 `/users/{id}` 경로로 들어온 요청을 받아서 `UserController`의 `getUser` 메서드로 전달 한다. 컨트롤러에서 비즈니스 로직을 처리한 후, 결과를 JSON으로 반환 한다.

#### **AOP를 통한 횡단 관심사 적용 예시 (로깅 기능 추가)**

```java
@Aspect
@Component
public class LoggingAspect {

    @Before("execution(* com.example.service.*.*(..))")
    public void logBeforeMethod(JoinPoint joinPoint) {
        System.out.println("Method called: " + joinPoint.getSignature().getName());
    }
}
```

위 코드는 `@Before` 어드바이스를 사용하여 서비스 계층의 모든 메서드가 호출되기 전에 로그를 남기는데, 이 때 `DispatcherServlet`은 전혀 관여하지 않으며, AOP가 서비스 메서드 실행 전에 로깅 기능을 추가 하고, 비즈니스 로직을 호출하는 모든 지점에 AOP가 로깅을 적용하는 방식으로 횡단 관심사를 처리 한다.

---

### 5. **결론**

- **DispatcherServlet**은 **웹 애플리케이션에서 HTTP 요청과 응답의 흐름을 제어**하는 중심 역할을 한다. 이를 통해 웹 애플리케이션의 모든 요청을 한 곳에서 처리하고, 적절한 핸들러로 전달하여 응답을 생성 한다.
  
- **AOP**는 **애플리케이션의 공통된 기능을 비즈니스 로직과 분리**하여 코드의 중복을 최소화하고 유지보수를 용이하게 한다. 횡단 관심사를 메인 로직과 분리하여 모듈화된 방식으로 처리할 수 있도록 도와준다.

이처럼 **DispatcherServlet**과 **AOP**는 서로 다른 문제를 해결하는 스프링 프레임워크의 중요한 도구이고, 서로 보완적으로 사용된다.

