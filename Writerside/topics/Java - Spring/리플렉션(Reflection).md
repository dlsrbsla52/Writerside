# 리플렉션(Reflection)

### **리플렉션(Reflection)이란?**

**리플렉션(Reflection)** 은 자바에서 제공하는 강력한 기능으로, **실행 시간(Run-time)에 클래스, 메서드, 생성자, 필드** 등의 정보를 동적으로 분석하고 접근할 수 있는 메커니즘입니다. 즉, 코드가 실행 중일 때, **클래스의 구조를 파악하고 이를 조작**할 수 있는 기능입니다.

리플렉션은 **`java.lang.reflect` 패키지**에서 제공되며, 이를 통해 다음과 같은 작업이 가능합니다:

- 클래스의 메서드, 필드, 생성자 등 정보를 탐색
- 객체 생성
- 메서드 호출
- 필드 값 읽기/쓰기

---

### **리플렉션의 주요 사용 사례**

1. **의존성 주입** (스프링에서의 DI)
    - 스프링이 객체를 동적으로 생성하고, 필드나 메서드에 의존성을 주입하는 데 사용.
2. **프레임워크 개발**
    - 스프링, 하이버네이트 등 프레임워크는 리플렉션을 이용해 동적으로 객체를 다룹니다.
3. **런타임 분석 도구**
    - 디버거나 프로파일러 등 실행 중인 프로그램을 분석하는 도구에서 사용.
4. **플러그인 시스템**
    - 외부 플러그인을 동적으로 로드하고 실행할 때 활용.

---

### **리플렉션으로 할 수 있는 주요 작업**

#### 1. **클래스 정보 얻기**

```java
Class<?> clazz = MyService.class; // 정적 방식
Class<?> clazz = Class.forName("com.example.MyService"); // 동적 방식
```

#### 2. **생성자 정보 얻기 및 객체 생성**

```java
Constructor<?> constructor = clazz.getConstructor(MyRepository.class);
Object instance = constructor.newInstance(myRepositoryInstance);
```

#### 3. **메서드 호출**

```java
Method method = clazz.getMethod("someMethod", String.class);
method.invoke(instance, "Hello World");
```

#### 4. **필드 접근 및 값 수정**

```java
Field field = clazz.getDeclaredField("privateField");
field.setAccessible(true); // private 필드 접근 허용
field.set(instance, "New Value");
```

---

### **리플렉션의 주요 클래스**

1. **`Class`**
    
    - 클래스 자체의 정보를 담고 있는 객체.
    - 클래스 이름, 메서드, 필드, 생성자 등 메타데이터를 제공.
2. **`Field`**
    
    - 클래스의 멤버 변수(필드)에 접근할 수 있는 객체.
3. **`Method`**
    
    - 클래스의 메서드에 접근할 수 있는 객체.
4. **`Constructor`**
    
    - 클래스의 생성자에 접근할 수 있는 객체.

---

### **리플렉션 예제**

#### 클래스 정보 조회

```java
public class MyClass {
    private String name;

    public MyClass(String name) {
        this.name = name;
    }

    public void printName() {
        System.out.println("Name: " + name);
    }
}
```

리플렉션을 활용한 클래스 정보 조회:

```java
Class<?> clazz = MyClass.class;

// 클래스 이름 출력
System.out.println("Class Name: " + clazz.getName());

// 생성자 정보 출력
Constructor<?>[] constructors = clazz.getConstructors();
for (Constructor<?> constructor : constructors) {
    System.out.println("Constructor: " + constructor.getName());
}

// 메서드 정보 출력
Method[] methods = clazz.getMethods();
for (Method method : methods) {
    System.out.println("Method: " + method.getName());
}
```

#### 객체 동적 생성 및 메서드 호출

```java
Constructor<?> constructor = clazz.getConstructor(String.class);
Object myObject = constructor.newInstance("John Doe");

Method printMethod = clazz.getMethod("printName");
printMethod.invoke(myObject);  // 출력: Name: John Doe
```

---

### **리플렉션의 장점**

1. **동적 동작 가능**
    
    - 컴파일 시점에 알 수 없는 클래스나 메서드를 실행 시점에 분석하고 사용 가능.
2. **유연성 제공**
    
    - 플러그인, DI 프레임워크, 테스트 프레임워크 등 다양한 동적 시스템을 구현할 수 있음.
3. **재사용성 증가**
    
    - 다양한 클래스에 대해 동일한 코드로 동작 가능.

---

### **리플렉션의 단점**

1. **성능 저하**
    
    - 실행 시점에 클래스, 메서드 등을 분석하고 접근하므로 일반적인 코드보다 느림.
2. **보안 문제**
    
    - `setAccessible(true)`를 사용하면 private 멤버에도 접근 가능하므로, 악용될 위험이 있음.
3. **컴파일 타임 안전성 부족**
    
    - 리플렉션은 실행 시점에 동작하므로, 잘못된 메서드 이름이나 필드명을 사용해도 컴파일 에러가 발생하지 않고 런타임 에러가 발생.

---

### **리플렉션과 스프링의 연관성**

스프링은 **리플렉션을 이용**해 다음 작업을 수행합니다:

- **빈(Bean) 생성**: 생성자, 필드를 분석해 동적으로 객체 생성.
- **의존성 주입**: 필드 또는 생성자에 필요한 객체를 동적으로 주입.
- **AOP(Aspect-Oriented Programming)**: 리플렉션으로 메서드 호출을 가로채서 동작 추가.

---

리플렉션은 스프링과 같은 프레임워크의 핵심 메커니즘 중 하나로, **코드의 동적 처리**를 가능하게 만들어 줍니다.
