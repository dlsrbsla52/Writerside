# @Repository 어노테이션

### **`@Repository` 어노테이션의 역할과 사용법**

#### **1. `@Repository`란?**

`@Repository`는 **Spring 프레임워크의 데이터 액세스 계층(DAO, Data Access Object)을 나타내는 어노테이션**입니다.  
이 어노테이션을 붙이면 **Spring이 자동으로 빈(bean)으로 등록하고, 데이터베이스 예외 처리 기능을 제공**합니다.

---

#### **2. `@Repository`를 왜 사용하는가?**

1. **Spring 빈으로 등록**
    
    - `@Repository`를 사용하면 **Spring 컨테이너가 해당 클래스를 빈으로 인식**하고 관리함.
    - 따라서 `@Autowired` 또는 생성자 주입을 통해 다른 빈에서 쉽게 주입받을 수 있음.
2. **데이터 액세스 계층(DAO) 역할 명확화**
    
    - `@Repository`는 해당 클래스가 **데이터베이스 관련 로직을 수행하는 클래스**임을 명확하게 나타냄.
    - 유지보수 및 가독성이 향상됨.
3. **데이터베이스 예외 처리 (`@Repository`의 예외 변환 기능)**
    
    - Spring은 **JDBC, JPA, Hibernate 등의 예외를 `DataAccessException`으로 변환**하는 기능을 제공함.
    - 예를 들어, `SQLException` 같은 데이터베이스 예외가 발생하면 Spring이 자동으로 런타임 예외로 변환하여 던짐.
    - 이를 통해 **checked exception을 직접 처리할 필요 없이 런타임 예외로 다룰 수 있어 코드가 간결해짐.**

---

#### **3. `@Repository` 사용 예제**

##### **(1) Spring Data JPA와 함께 사용**

Spring Data JPA를 사용할 경우, `@Repository`를 생략해도 자동으로 빈으로 등록되지만, 명확한 역할을 강조하기 위해 추가할 수도 있음.

```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    User findByUsername(String username);
}
```

- `JpaRepository<User, Long>`를 상속받으면, 기본적인 CRUD 메서드를 자동으로 제공.
- `findByUsername(String username)`처럼 **메서드 이름을 기반으로 자동으로 쿼리를 생성**하는 기능도 가능.

##### **(2) MyBatis, JDBC Template에서 사용**

Spring Data JPA가 아닌, **MyBatis** 또는 **JDBC Template**을 사용할 때 `@Repository`를 명확하게 지정하는 것이 일반적임.

```java
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Repository;

@Repository
public class UserDao {

    private final JdbcTemplate jdbcTemplate;

    public UserDao(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    public User findById(Long id) {
        String sql = "SELECT * FROM users WHERE id = ?";
        return jdbcTemplate.queryForObject(sql, new Object[]{id}, (rs, rowNum) -> 
            new User(rs.getLong("id"), rs.getString("username"))
        );
    }
}
```

- `@Repository`를 사용하면, **Spring이 해당 DAO 클래스를 자동으로 빈으로 등록**하여 주입할 수 있음.

---

#### **4. `@Repository`를 언제 사용해야 할까?**

- **데이터베이스와 직접 상호작용하는 클래스(DAO)를 만들 때**
    - JPA, JDBC, MyBatis 등을 사용하여 데이터를 조회, 삽입, 수정, 삭제하는 계층에서는 `@Repository`를 붙이는 것이 좋음.
- **예외 처리를 Spring 방식으로 자동 변환하고 싶을 때**
    - 데이터베이스 관련 예외를 `DataAccessException`으로 변환하여 다루는 기능을 사용하고 싶다면 `@Repository`를 사용해야 함.
- **Spring Data JPA에서는 생략 가능하지만, 명확한 의미 전달을 위해 사용 가능**
    - Spring Data JPA를 사용할 경우, 인터페이스만 정의해도 자동으로 빈으로 등록되므로 `@Repository`를 생략할 수 있음.
    - 하지만 **DAO 계층을 명확히 구분하고 싶다면** `@Repository`를 붙이는 것이 권장됨.

---

#### **5. `@Repository` vs `@Service` vs `@Component`**

| 어노테이션         | 역할               | 주로 사용하는 클래스                           |
| ------------- | ---------------- | ------------------------------------- |
| `@Repository` | 데이터 액세스 계층 (DAO) | JPA, MyBatis, JDBC를 이용한 데이터베이스 관련 클래스 |
| `@Service`    | 비즈니스 로직 계층       | 서비스 클래스 (비즈니스 로직 담당)                  |
| `@Component`  | 일반적인 빈 등록        | 특정 계층에 속하지 않는 일반적인 스프링 빈              |

> **✅ 정리:**
> - `@Repository`: 데이터 저장/조회 관련 클래스에 사용 (DAO, Repository)
> - `@Service`: 비즈니스 로직을 처리하는 서비스 계층
> - `@Component`: 특별한 역할이 없는 일반 빈을 등록할 때 사용

---

### **6. 결론**

- `@Repository`는 **DAO(데이터 접근 계층) 클래스를 선언할 때 사용**하며, Spring이 해당 클래스를 **자동으로 빈으로 등록하고 예외 변환 기능을 제공**하도록 도와줌.
- Spring Data JPA에서는 생략 가능하지만, MyBatis, JDBC Template 등을 사용할 때는 **명확한 역할 구분을 위해 반드시 사용하는 것이 좋음**.
