# equals()

작성하신 `equals()` 메서드는 Java에서 객체의 **논리적 동등성**을 정의하는 방식입니다. 이 메서드를 통해 두 객체가 같은지를 비교할 때, 메모리 주소가 아니라 객체의 특정 속성을 기준으로 판단하게 됩니다.

### 코드 설명

```java
@Override
public boolean equals(Object o) {
    if (o == null || getClass() != o.getClass()) return false;
    member member = (member) o;
    return Objects.equals(id, member.id);
}
```

#### 1. `if (o == null || getClass() != o.getClass()) return false;`

- **`o == null`**  
    비교 대상 객체 `o`가 `null`인 경우, 당연히 두 객체는 같을 수 없으므로 `false`를 반환합니다.
    
- **`getClass() != o.getClass()`**  
    두 객체가 같은 클래스인지 확인합니다.  
    `getClass()`는 런타임 시 실제 객체의 클래스를 반환하며, 클래스가 다르면 `false`를 반환합니다.  
    (즉, 같은 클래스의 객체만 비교 대상으로 삼겠다는 의미입니다.)
    
    > **주의:** 이 방식은 **strict equality**를 사용하므로 상속 구조에서는 동작이 다를 수 있습니다.  
    > 만약 상속 관계에서 비교를 허용하고 싶다면 `getClass()` 대신 `instanceof`를 사용할 수 있습니다:
    
    ```java
    if (!(o instanceof member)) return false;
    ```
    

#### 2. `member member = (member) o;`

- 비교 대상 객체 `o`를 현재 클래스(`member`) 타입으로 캐스팅합니다.  
    `getClass()`로 타입 검사를 완료했으므로 안전하게 캐스팅할 수 있습니다.

#### 3. `return Objects.equals(id, member.id);`

- **`Objects.equals(a, b)`**는 두 객체를 비교하며, 다음과 같이 동작합니다:
    
    - 둘 다 `null`이면 `true`를 반환.
    - 하나만 `null`이면 `false`를 반환.
    - 둘 다 `null`이 아니면 `a.equals(b)`를 호출.
- 여기서는 `id` 필드가 두 객체 간 동일한지를 기준으로 **논리적 동등성**을 판단합니다.
    

---

### 코드의 동작 예시

```java
public class Member {
    private Long id;

    public Member(Long id) {
        this.id = id;
    }

    @Override
    public boolean equals(Object o) {
        if (o == null || getClass() != o.getClass()) return false;
        Member member = (Member) o;
        return Objects.equals(id, member.id);
    }
}

public class Main {
    public static void main(String[] args) {
        Member m1 = new Member(1L);
        Member m2 = new Member(1L);
        Member m3 = new Member(2L);

        System.out.println(m1.equals(m2)); // true
        System.out.println(m1.equals(m3)); // false
        System.out.println(m1.equals(null)); // false
    }
}
```

출력:

```
true
false
false
```

---

### 주요 특징 및 동작 원리

1. **`null` 안전성**  
    `o == null`을 먼저 확인하므로, `null`을 인수로 전달해도 `NullPointerException`이 발생하지 않습니다.
    
2. **동일 클래스만 비교**  
    `getClass()`를 사용하여 같은 클래스의 인스턴스만 비교합니다.  
    예를 들어, `Member`를 상속받은 클래스의 객체는 비교 대상에서 제외됩니다.
    
3. **`id` 필드를 기준으로 동등성 판단**  
    논리적 동등성을 `id` 필드 하나로 정의했습니다. 만약 다른 필드도 비교하고 싶다면 조건을 추가하면 됩니다:
    
    ```java
    return Objects.equals(id, member.id) && Objects.equals(name, member.name);
    ```
    
4. **NPE 방지**  
    `Objects.equals()`를 사용하므로 `id`가 `null`이어도 비교 중 `NullPointerException`이 발생하지 않습니다.
    

---

### 개선 가능한 점

#### 1. **`getClass()` vs `instanceof`**

- 현재 방식은 **클래스가 완전히 같을 때만** 비교합니다.  
    만약 상속 구조에서 동등성을 판단하려면 `getClass()` 대신 `instanceof`를 사용하는 것이 더 일반적입니다.

#### 2. **hashCode()와 일관성 유지**

- `equals()`를 오버라이드했으므로, 반드시 `hashCode()`도 함께 오버라이드하여 `equals()`와 일관성을 유지해야 합니다.  
    예를 들어:

```java
@Override
public int hashCode() {
    return Objects.hash(id);
}
```

---

### 결론

작성하신 `equals()` 메서드는 논리적 동등성을 정의하기 위해 `id` 필드를 기준으로 비교하도록 설계되었습니다.  
안전하고 간결하게 작성되었으며, 클래스 간 비교를 엄격히 제한하고 있습니다.  
다만, 상속을 고려하거나 추가 필드를 포함한 비교가 필요하다면 로직을 약간 수정해야 할 수 있습니다.