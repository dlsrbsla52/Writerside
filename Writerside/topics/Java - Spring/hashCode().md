# hashCode()

### 1. **`hashCode()`란?**

`hashCode()`는 객체의 **고유한 정수 값(해시 코드)** 을 반환하는 메서드로, 객체의 **논리적 동등성**을 결정할 때 유용하게 사용됩니다.  
주로 **해시 기반 컬렉션**(예: `HashMap`, `HashSet`, `Hashtable`)에서 객체를 빠르게 검색, 삽입, 삭제하기 위해 사용됩니다.

**메서드 시그니처 (Object 클래스에서 상속됨):**

```java
public native int hashCode();
```

- `native` 키워드는 내부적으로 네이티브 코드(C/C++)로 구현됨을 의미합니다.
- 반환값은 `int` 범위(-2^31 ~ 2^31-1) 내의 정수입니다.

---

### 2. **`hashCode()`의 기본 동작**

기본적으로 `Object` 클래스의 `hashCode()` 구현은 **객체의 메모리 주소를 기반**으로 정수 값을 반환합니다.  
즉, 객체의 물리적 위치(메모리 참조)를 기반으로 해시 코드를 생성하지만, 실제 주소 값이 아닌 특정 변환을 거친 값입니다.

**예제:**

```java
public class HashCodeDemo {
    public static void main(String[] args) {
        Object obj1 = new Object();
        Object obj2 = new Object();
        
        System.out.println(obj1.hashCode());  // 예: 460141958 (JVM마다 다름)
        System.out.println(obj2.hashCode());  // 예: 1163157884
        System.out.println(obj1 == obj2);     // false (다른 객체이므로)
    }
}
```

출력 예시:

```
460141958
1163157884
false
```

**특징:**

- 객체마다 다른 해시 코드가 나오지만, JVM의 구현 방식에 따라 동일한 값이 나올 수도 있음.
- **`System.identityHashCode()`** 를 사용하면 `hashCode()`를 오버라이드한 객체라도 원래 객체의 기본 해시 코드를 얻을 수 있음.

---

### 3. **`hashCode()`의 역할**

- **해시 기반 컬렉션과 연관:**  
    `HashMap`, `HashSet`, `Hashtable` 등은 객체를 저장할 때 내부적으로 `hashCode()`를 사용하여 **버킷(bucket)**에 객체를 배치.
- **성능 최적화:**  
    빠른 검색과 삽입, 삭제를 위해 효율적인 해시 코드가 필요함.

---

### 4. **`equals()`와의 관계**

`hashCode()`를 올바르게 구현하려면, **equals()와의 계약(Contract)**을 지켜야 합니다.

**규칙:**

1. **`equals()`가 `true`를 반환하면, `hashCode()`도 동일한 값을 반환해야 함.**  
    즉, 논리적으로 같은 객체는 같은 해시 코드를 가져야 함.
2. **같은 객체(동일한 인스턴스)는 항상 동일한 해시 코드를 반환해야 함.**
3. **다른 객체라도 해시 코드는 같을 수 있음(해시 충돌 가능).**

**예제 (계약 준수):**

```java
class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age && name.equals(person.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}

public class HashCodeExample {
    public static void main(String[] args) {
        Person p1 = new Person("Alice", 25);
        Person p2 = new Person("Alice", 25);
        System.out.println(p1.equals(p2));       // true
        System.out.println(p1.hashCode());        // 같은 값 반환
        System.out.println(p2.hashCode());        // 같은 값 반환
    }
}
```

출력 예시:

```
true
12345678
12345678
```

---

### 5. **커스텀 `hashCode()` 구현 방법**

객체의 필드를 조합하여 적절한 해시 값을 생성해야 합니다.

#### (1) `Objects.hash()` 사용 (간편한 방법)

```java
@Override
public int hashCode() {
    return Objects.hash(name, age);
}
```

- 내부적으로 `null` 처리를 자동으로 수행.
- 여러 필드를 기반으로 해시 코드를 계산.

#### (2) 직접 수식 적용 (전통적인 방법)

```java
@Override
public int hashCode() {
    int result = 17;
    result = 31 * result + (name == null ? 0 : name.hashCode());
    result = 31 * result + age;
    return result;
}
```

- `31`은 소수(Prime Number)로, 충돌 가능성을 줄이기 위해 사용됨.
- 필드마다 `null` 체크가 필요함.

---

### 6. **해시 충돌과 해결 방안**

**해시 충돌**이란 서로 다른 객체가 동일한 해시 코드를 가질 때 발생합니다.  
충돌이 발생하면, 해시 컬렉션에서는 **체이닝(연결 리스트), 오픈 어드레싱 등**의 전략으로 충돌을 해결합니다.

**예제:**

```java
public class CollisionExample {
    public static void main(String[] args) {
        String s1 = "FB";  // 해시 값: 2236 (예제 기준)
        String s2 = "Ea";  // 해시 값: 2236 (예제 기준)

        System.out.println(s1.hashCode());  // 2236
        System.out.println(s2.hashCode());  // 2236 (충돌 발생)
    }
}
```

**해결 방안:**

- 해시 함수 개선: 필드 조합을 신중하게 선택.
- 충돌을 고려한 자료구조 사용: `LinkedHashMap`, `TreeMap` 등.

---

### 7. **`hashCode()` 테스트 및 활용**

`hashCode()`가 잘 구현되었는지 확인하는 테스트 방법:

```java
public class HashCodeTest {
    public static void main(String[] args) {
        Person p1 = new Person("John", 30);
        Person p2 = new Person("John", 30);
        Person p3 = new Person("Jane", 25);

        System.out.println(p1.hashCode() == p2.hashCode()); // true (동일한 값)
        System.out.println(p1.hashCode() == p3.hashCode()); // false (다른 값)
    }
}
```

**`HashSet` 활용 예제 (중복 방지 확인):**

```java
Set<Person> set = new HashSet<>();
set.add(new Person("Alice", 30));
set.add(new Person("Alice", 30));

System.out.println(set.size()); // 1 (중복 제거됨)
```

---

### 8. **주의할 점**

1. **`hashCode()` 오버라이드 시 `equals()`도 함께 구현해야 함.**
2. **해시 코드는 가능한 한 고유해야 하지만, 과도한 복잡성은 피해야 함.**
3. **필드를 변경할 경우 해시 코드가 달라질 수 있으므로, 불변 객체에서 안전하게 사용.**

---

### 9. **결론**

- `hashCode()`는 해시 기반 컬렉션에서 객체의 효율적인 관리를 위해 필수.
- `equals()`와 함께 올바르게 오버라이드해야 안정적으로 동작.
- 성능과 충돌 방지를 고려한 해시 함수 설계가 중요.

---

이해를 돕기 위해 더 많은 예제가 필요하거나 특정 부분을 추가로 알고 싶다면 질문해 주세요!

