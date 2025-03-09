# 팩토리 메소드(fectory method)

팩토리 메소드는 객체 생성 책임을 생성자에서 분리하여 별도의 메소드(주로 static 메소드)를 통해 객체를 생성하도록 한 디자인 패턴입니다. 이 방식을 사용하면 생성자 호출의 단점을 보완하고, 보다 유연하고 명확한 객체 생성 로직을 구현할 수 있습니다.

---

## 팩토리 메소드의 주요 장점

- **의미 있는 메소드 이름 제공:**  
    생성자는 항상 클래스 이름과 동일하지만, 팩토리 메소드는 `createUser()`, `newInstance()`, `of()` 등 구체적인 이름을 부여하여 어떤 방식으로 객체가 생성되는지 명확히 표현할 수 있습니다.
    
- **객체 생성 로직 캡슐화:**  
    객체 생성 시 필요한 초기화, 검증, 디폴트 값 설정, 또는 캐싱 등의 로직을 한 곳에 모아서 처리할 수 있습니다. 이는 생성자가 복잡해지는 것을 막아주며, 코드의 유지보수성을 높여줍니다.
    
- **반환 타입의 유연성:**  
    팩토리 메소드는 반환 타입을 인터페이스나 상위 클래스로 선언할 수 있으므로, 내부에서 다양한 구체 클래스(서브 클래스)를 반환할 수 있습니다. 이렇게 하면 클라이언트 코드는 구체 클래스에 의존하지 않게 되어 확장성과 캡슐화가 향상됩니다.
    
- **객체 재사용 및 최적화:**  
    이미 생성된 객체를 캐싱하여 재사용하는 등의 최적화 기법을 적용할 수 있습니다. 예를 들어, 싱글턴 패턴이나 불변 객체 캐싱에 유리합니다.
    
- **객체 생성 방식 변경 용이:**  
    나중에 객체 생성 로직을 변경하거나, 반환하는 객체의 종류를 다르게 하더라도 클라이언트 코드는 팩토리 메소드만 호출하면 되므로 변경의 영향을 최소화할 수 있습니다.
    

---

## 예제 1: 단순 객체 생성 (Static Factory Method)

아래 예제는 사용자(User) 객체를 생성할 때, 검증 및 초기화 로직을 포함하여 생성자를 감춘 후, static 팩토리 메소드를 통해 객체를 생성하는 방법을 보여줍니다.

```java
import java.time.LocalDateTime;

public class User {
    private String username;
    private String password;
    private LocalDateTime createdAt;

    // 생성자를 private으로 설정하여 외부에서 직접 호출하지 못하도록 함
    private User(String username, String password) {
        this.username = username;
        this.password = password;
        // 객체 생성 시 현재 시간을 생성 시간으로 설정
        this.createdAt = LocalDateTime.now();
    }
    
    /**
     * 사용자 객체를 생성하는 static 팩토리 메소드
     * @param username 사용자 이름 (null 또는 빈 문자열이면 예외 발생)
     * @param password 사용자 비밀번호
     * @return 생성된 User 객체
     */
    public static User createUser(String username, String password) {
        // 간단한 검증 로직 추가 (유효성 검사)
        if (username == null || username.isEmpty()) {
            throw new IllegalArgumentException("username cannot be null or empty");
        }
        // 필요에 따라 password 검증 로직 추가 가능
        
        // User 객체를 생성하고 반환
        return new User(username, password);
    }
    
    // 게터(getter) 메소드들 (필요에 따라 추가)
    public String getUsername() {
        return username;
    }
    
    public LocalDateTime getCreatedAt() {
        return createdAt;
    }
}
```

**예제 1 설명:**

- **생성자 감춤:**  
    `User` 클래스의 생성자는 `private`로 설정되어, 외부에서는 직접 호출할 수 없습니다. 이는 객체 생성 로직을 팩토리 메소드에 집중시키기 위함입니다.
    
- **팩토리 메소드 `createUser()`:**  
    이 메소드는 사용자 입력값에 대한 유효성 검사를 수행한 후, 객체를 생성하고 초기화합니다. 향후 객체 생성 로직이 변경되더라도 팩토리 메소드 내부만 수정하면 되므로 유지보수가 용이합니다.
    

---

## 예제 2: 다형성을 활용한 객체 생성

다음 예제는 팩토리 메소드를 활용하여 입력값에 따라 서로 다른 구체 클래스(예: Circle, Rectangle)를 생성하는 방법을 보여줍니다. 여기서는 `Shape` 인터페이스를 구현하는 여러 클래스가 있으며, 팩토리 메소드가 클라이언트에게 구체 클래스를 숨깁니다.

```java
// Shape 인터페이스 정의 (공통 기능 정의)
public interface Shape {
    void draw();
}

// Circle 클래스: Shape 인터페이스를 구현
public class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a circle");
    }
}

// Rectangle 클래스: Shape 인터페이스를 구현
public class Rectangle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a rectangle");
    }
}

// ShapeFactory 클래스: 팩토리 메소드를 통해 Shape 객체를 생성
public class ShapeFactory {

    /**
     * 팩토리 메소드: 입력된 타입에 따라 적절한 Shape 객체를 생성하여 반환
     * @param shapeType 생성하고자 하는 도형의 타입 ("circle" 또는 "rectangle")
     * @return 생성된 Shape 객체
     * @throws IllegalArgumentException 잘못된 타입이 입력될 경우 예외 발생
     */
    public static Shape getShape(String shapeType) {
        if (shapeType == null) {
            throw new IllegalArgumentException("shapeType cannot be null");
        }
        // 입력된 문자열에 따라 서로 다른 객체를 생성하여 반환
        if (shapeType.equalsIgnoreCase("circle")) {
            return new Circle();
        } else if (shapeType.equalsIgnoreCase("rectangle")) {
            return new Rectangle();
        }
        throw new IllegalArgumentException("Unknown shape type: " + shapeType);
    }
}
```

**예제 2 설명:**

- **인터페이스 활용:**  
    `Shape` 인터페이스를 구현한 `Circle`과 `Rectangle` 클래스를 정의하여, 클라이언트는 구체적인 클래스가 아닌 인터페이스를 통해 객체를 사용합니다.
    
- **조건에 따른 객체 생성:**  
    `ShapeFactory.getShape()` 메소드는 전달된 문자열 값에 따라 적절한 도형 객체를 생성합니다. 이처럼 팩토리 메소드는 입력 값이나 환경 조건에 따라 서로 다른 객체를 생성할 수 있는 유연성을 제공합니다.
    
- **캡슐화와 의존성 감소:**  
    클라이언트는 객체 생성 시 어떤 구체 클래스가 사용되는지 알 필요 없이, 팩토리 메소드만 호출하면 됩니다. 이를 통해 객체 생성 방식을 캡슐화하고, 클라이언트와 구현체 간의 의존성을 줄일 수 있습니다.
    

---

## 언제 팩토리 메소드를 사용해야 할까?

- **객체 생성 로직이 복잡할 때:**  
    단순한 생성자 호출 외에 여러 초기화 과정, 검증, 기본값 설정 등이 필요할 경우.
    
- **여러 타입의 객체를 반환해야 할 때:**  
    입력 값이나 조건에 따라 서로 다른 서브 클래스의 인스턴스를 반환해야 할 때 유용합니다.
    
- **객체 생성 방식 변경의 유연성이 필요할 때:**  
    후에 객체 생성 로직을 변경하거나, 반환하는 객체의 구체 클래스를 바꿔야 할 경우, 클라이언트 코드를 수정할 필요 없이 팩토리 메소드 내부만 수정하면 됩니다.
    
- **명확한 의도를 표현하고자 할 때:**  
    생성자만 사용하면 의도가 드러나지 않는 경우, 팩토리 메소드의 이름으로 어떤 로직이 수행되는지 명시할 수 있습니다.
    

---

## 결론

팩토리 메소드는 생성자보다 더 유연하고 명시적인 객체 생성 방식을 제공합니다. 복잡한 초기화나 조건에 따른 객체 생성을 한 곳에 집중시켜 관리할 수 있으며, 클라이언트 코드에서는 구체적인 구현에 의존하지 않고 인터페이스나 상위 클래스를 통해 객체를 사용할 수 있게 해줍니다. 이러한 이유로 복잡한 시스템이나 확장이 예상되는 상황에서는 팩토리 메소드의 사용이 권장됩니다.
