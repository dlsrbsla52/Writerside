# IOC, ID 설명

### IoC (Inversion of Control) - 제어의 역전

**정의**: IoC는 프로그램의 제어 흐름을 개발자가 직접 제어하지 않고, 프레임워크나 컨테이너가 대신 제어하는 디자인 원칙입니다.

**특징**:

- **제어권의 이동**: 애플리케이션의 주요 제어권을 프레임워크가 가지고 있습니다. 즉, 객체의 생성, 초기화, 라이프사이클 관리 등을 개발자가 아닌 프레임워크가 담당합니다.
- **유연성**: 객체 간의 결합도가 낮아지며, 코드의 유연성과 재사용성이 높아집니다.
- **프레임워크의 역할**: 스프링 컨테이너는 애플리케이션 구성 요소들을 관리하고, 필요한 시점에 자동으로 객체를 생성하고 주입합니다.

### DI (Dependency Injection) - 의존성 주입

**정의**: DI는 객체가 필요로 하는 의존성을 외부에서 주입하는 설계 패턴입니다. IoC의 구체적인 구현 방법 중 하나입니다.

**특징**:

- **외부 주입**: 객체가 필요로 하는 의존성을 생성자, 세터 메서드, 또는 필드를 통해 외부에서 주입합니다.
- **유지보수 용이성**: 의존성 주입을 통해 객체 간의 결합도가 낮아져, 코드의 유지보수가 용이해집니다.
- **테스트 용이성**: 객체의 의존성을 쉽게 대체할 수 있어 단위 테스트가 용이해집니다.

### IoC와 DI의 관계 {id="ioc-di_1"}

- **IoC는 개념, DI는 구현**: IoC는 더 넓은 개념으로, 제어의 역전 자체를 의미합니다. DI는 IoC를 구현하는 한 가지 방법입니다. 따라서 모든 DI는 IoC의 한 형태지만, 모든 IoC가 DI인 것은 아닙니다.
- **프레임워크의 역할**: 스프링 프레임워크는 IoC 컨테이너를 통해 DI를 구현합니다. 즉, 스프링은 IoC를 사용하여 객체의 생성과 의존성 주입을 관리합니다.

### 예제

다음은 IoC와 DI를 설명하기 위한 간단한 예제입니다.

### 전통적인 방법 (IoC와 DI가 없는 경우) {id="ioc-di_2"}

```java
java코드 복사
public class Service {
    private Repository repository;

    public Service() {
        this.repository = new Repository();
    }

    // 비즈니스 로직
}

```

### IoC와 DI를 사용하는 방법

**스프링 설정 파일 (XML 기반)**:

```xml
xml코드 복사
<beans>
    <bean id="repository" class="com.example.Repository" />
    <bean id="service" class="com.example.Service">
        <constructor-arg ref="repository" />
    </bean>
</beans>

```

**스프링 설정 파일 (자바 기반)**:

```java
java코드 복사
@Configuration
public class AppConfig {

    @Bean
    public Repository repository() {
        return new Repository();
    }

    @Bean
    public Service service() {
        return new Service(repository());
    }
}

```

**Service 클래스**:

```java
java코드 복사
public class Service {
    private final Repository repository;

    @Autowired
    public Service(Repository repository) {
        this.repository = repository;
    }

    // 비즈니스 로직
}

```

### 결론

- **IoC**: 객체의 생성과 관리, 제어를 프레임워크(스프링 컨테이너)가 담당하는 원칙.
- **DI**: 객체의 의존성을 외부

