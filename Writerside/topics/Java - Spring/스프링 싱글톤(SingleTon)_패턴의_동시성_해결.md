# 스프링 싱글톤(SingleTon) 패턴의 동시성 해결

스프링 프레임워크는 싱글톤 패턴을 통해 애플리케이션 컨텍스트 내에서 빈(Bean)을 단 하나만 생성하고 재사용합니다. 이는 자원을 절약하고, 빈 관리의 일관성을 유지하는 데 도움이 됩니다. 그러나 싱글톤 빈이 멀티스레드 환경에서 동시성 문제를 일으킬 수 있다는 걱정이 있을 수 있습니다. 스프링은 이러한 동시성 문제를 다양한 방식으로 해결합니다.

### 1. Stateless Bean (무상태 빈)

가장 흔한 해결 방법은 빈을 무상태로 설계하는 것입니다. 무상태 빈은 인스턴스 변수를 가지지 않고, 모든 메서드가 입력 파라미터에만 의존하며, 메서드 호출 간에 상태를 유지하지 않습니다. 무상태 빈은 본질적으로 스레드 안전합니다.

**예제**:

```java
java코드 복사
@Service
public class MyService {
    public int calculate(int a, int b) {
        return a + b;
    }
}

```

### 2. Thread-Local Variables (스레드 로컬 변수)

스레드 로컬 변수를 사용하면 각 스레드마다 별도의 변수를 가질 수 있습니다. 이는 상태를 스레드 단위로 분리하여 동시성 문제를 피할 수 있게 합니다.

**예제**:

```java
java코드 복사
@Service
public class MyService {
    private ThreadLocal<Integer> threadLocalValue = ThreadLocal.withInitial(() -> 0);

    public void process(int value) {
        threadLocalValue.set(value);
        // ... 로직 수행 ...
    }
}

```

### 3. Prototyped Beans (프로토타입 빈)

싱글톤 빈 대신 프로토타입 빈을 사용하여 각 요청마다 새로운 인스턴스를 생성할 수 있습니다. 이는 특정 빈이 상태를 가지며, 각 요청마다 별도의 인스턴스를 필요로 할 때 유용합니다.

**예제**:

```java
java코드 복사
@Service
@Scope("prototype")
public class MyPrototypeService {
    private int state;

    public void setState(int state) {
        this.state = state;
    }

    public int getState() {
        return state;
    }
}

```

### 4. Synchronized Methods (동기화 메서드)

동기화 메서드를 사용하여 특정 코드 블록이 동시에 하나의 스레드에 의해서만 실행되도록 할 수 있습니다. 이는 병렬 처리가 불가능하게 만들지만, 동시성 문제를 해결할 수 있습니다.

**예제**:

```java
java코드 복사
@Service
public class MyService {
    private int counter = 0;

    public synchronized void increment() {
        counter++;
    }

    public synchronized int getCounter() {
        return counter;
    }
}

```

### 5. Concurrent Collections (동시성 컬렉션)

자바의 `java.util.concurrent` 패키지에서 제공하는 동시성 컬렉션을 사용하여 스레드 안전하게 데이터를 관리할 수 있습니다. 예를 들어, `ConcurrentHashMap`, `CopyOnWriteArrayList` 등이 있습니다.

**예제**:

```java
java코드 복사
@Service
public class MyService {
    private Map<String, String> concurrentMap = new ConcurrentHashMap<>();

    public void putValue(String key, String value) {
        concurrentMap.put(key, value);
    }

    public String getValue(String key) {
        return concurrentMap.get(key);
    }
}

```

### 6. @RequestScope, @SessionScope, @ApplicationScope

스프링에서 제공하는 다양한 스코프 애노테이션을 사용하여 빈의 생명주기를 제어할 수 있습니다. 예를 들어, HTTP 요청당 새로운 인스턴스를 생성하는 `@RequestScope`, 세션당 하나의 인스턴스를 유지하는 `@SessionScope` 등을 사용할 수 있습니다.

**예제**:

```java
java코드 복사
@Service
@RequestScope
public class MyRequestScopedService {
    // 이 빈은 각 HTTP 요청마다 새로운 인스턴스를 가집니다.
}

```

### 결론

스프링 프레임워크는 싱글톤 패턴을 사용하여 효율성을 높이지만, 동시성 문제를 다양한 방법으로 해결할 수 있는 유연성을 제공합니다. 무상태 빈, 스레드 로컬 변수, 프로토타입 스코프, 동기화 메서드, 동시성 컬렉션, 그리고 다양한 스코프 애노테이션을 통해 동시성 문제를 효과적으로 해결할 수 있습니다. 상황에 맞는 적절한 방법을 선택하여 동시성 문제를 해결하면 됩니다.

4o

Stateless Bean (무상태 빈) 예제 보여줘

ChatGPT

물론입니다. 무상태 빈(Stateless Bean)은 인스턴스 변수나 상태를 가지지 않고, 모든 로직이 메서드 호출의 입력 파라미터와 반환 값에만 의존하는 빈입니다. 이는 여러 스레드가 동시에 같은 빈 인스턴스를 사용하더라도 동시성 문제를 발생시키지 않습니다.

아래는 무상태 빈의 예제입니다.

### 예제: 무상태 빈

```java
java코드 복사
import org.springframework.stereotype.Service;

@Service
public class CalculationService {

    // 무상태 메서드: 입력 파라미터에만 의존하고, 인스턴스 변수나 상태를 사용하지 않음
    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }

    public int multiply(int a, int b) {
        return a * b;
    }

    public int divide(int a, int b) {
        if (b == 0) {
            throw new IllegalArgumentException("Division by zero is not allowed.");
        }
        return a / b;
    }
}

```

### 사용 예제: 무상태 빈 호출

스프링 부트 애플리케이션에서 이 서비스를 사용하는 방법을 보여드리겠습니다.

```java
java코드 복사
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class StatelessBeanExampleApplication implements CommandLineRunner {

    @Autowired
    private CalculationService calculationService;

    public static void main(String[] args) {
        SpringApplication.run(StatelessBeanExampleApplication.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        int a = 10;
        int b = 5;

        System.out.println("Addition: " + calculationService.add(a, b)); // 출력: Addition: 15
        System.out.println("Subtraction: " + calculationService.subtract(a, b)); // 출력: Subtraction: 5
        System.out.println("Multiplication: " + calculationService.multiply(a, b)); // 출력: Multiplication: 50
        System.out.println("Division: " + calculationService.divide(a, b)); // 출력: Division: 2
    }
}

```

### 설명

- `CalculationService` 클래스는 무상태 빈입니다. 이 클래스의 모든 메서드는 인스턴스 변수를 사용하지 않고, 입력 파라미터와 반환 값에만 의존합니다.
- `StatelessBeanExampleApplication` 클래스는 스프링 부트 애플리케이션의 진입점이며, `CalculationService`를 자동으로 주입 받아(`@Autowired`), `CommandLineRunner` 인터페이스를 구현하여 애플리케이션이 시작될 때 계산 메서드를 호출합니다.

이 예제는 `CalculationService`가 여러 스레드에서 동시에 호출되더라도 인스턴스 변수나 상태를 사용하지 않기 때문에 안전하게 동작하는 무상태 빈임을 보여줍니다.
