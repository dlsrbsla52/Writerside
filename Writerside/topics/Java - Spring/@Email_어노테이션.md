# @Email 어노테이션

`@Email` 어노테이션은 Java Bean Validation API의 일부로, 이메일 형식을 검증할 때 사용됩니다. Spring Boot에서는 Jakarta Validation API(`jakarta.validation.constraints.Email`) 또는 Hibernate Validator와 함께 사용할 수 있습니다.

이 어노테이션을 필드에 적용하면, 해당 필드의 값이 유효한 이메일 형식인지 검증합니다. 이메일 주소의 형식이 RFC 5322 표준을 따르는지 확인하는 방식입니다. 예를 들어, `abc@domain.com`은 유효한 이메일 주소로 인정되고, `abc@domain`처럼 도메인에 TLD가 없는 형식은 유효하지 않다고 판정됩니다.

### 사용 방법

다음과 같이 `@Email` 어노테이션을 필드에 붙여서 사용할 수 있습니다:

```java
import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.NotEmpty;

public class UserRegistrationRequest {

    @NotEmpty(message = "이메일은 필수 항목입니다.")
    @Email(message = "올바른 이메일 주소를 입력하세요.")
    private String email;

    // Getter and Setter
    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```

- **@NotEmpty**: 이메일이 빈 문자열이 아니도록 설정합니다. `@Email`만 사용할 경우 빈 문자열도 통과할 수 있기 때문에, 이메일 필드가 비어 있는지 확인하려면 `@NotEmpty` 또는 `@NotBlank`와 함께 사용하는 것이 일반적입니다.
- **@Email**: 이메일 형식을 검증하며, 형식이 잘못된 경우 오류 메시지를 반환합니다. 여기서는 `"올바른 이메일 주소를 입력하세요."`라는 메시지를 설정했습니다.

### 주요 속성

- **message**: 검증 실패 시 반환할 오류 메시지를 설정할 수 있습니다.
- **regexp**: 이메일 검증에 사용하는 정규 표현식을 사용자 정의할 수 있습니다.
- **flags**: 대소문자 구분 여부 등을 설정할 수 있는 `Pattern.Flag`의 배열입니다.

### 검증 과정 예제

이 어노테이션을 사용하는 클래스의 인스턴스를 만들고 검증해보면, 이메일 형식이 잘못된 경우 오류가 발생하게 됩니다. 예를 들어 다음과 같은 요청 객체가 있다고 가정해 보겠습니다:

```java
UserRegistrationRequest request = new UserRegistrationRequest();
request.setEmail("invalid-email"); // 이메일 형식이 올바르지 않음

ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
Validator validator = factory.getValidator();

Set<ConstraintViolation<UserRegistrationRequest>> violations = validator.validate(request);

for (ConstraintViolation<UserRegistrationRequest> violation : violations) {
    System.out.println(violation.getMessage());
}
```

위의 코드를 실행하면 `"올바른 이메일 주소를 입력하세요."`라는 메시지가 출력됩니다. 이는 사용자가 입력한 이메일 주소가 형식에 맞지 않기 때문입니다.

### @Email의 장점과 주의사항

#### 장점:
- **간단한 이메일 형식 검증**: 필드에 쉽게 추가할 수 있어 간단한 이메일 형식의 유효성을 확인할 수 있습니다.
- **일관된 검증**: 여러 클래스에 걸쳐 동일한 형식의 유효성 검사를 수행하므로 코드의 재사용성과 유지보수가 쉬워집니다.

#### 주의사항:
- **기본적인 형식만 검사**: `@Email`은 기본적인 이메일 형식만 확인합니다. 이메일 주소의 유효성을 완벽하게 보장하지는 않습니다. 예를 들어, 이메일 도메인이 실제로 존재하는지 또는 사용자 메일 서버가 존재하는지와 같은 검사는 하지 않습니다.
- **빈 값에 대한 검사**: `@Email`은 이메일 필드가 비어 있을 때 실패하지 않습니다. 따라서 `@NotNull`, `@NotEmpty` 또는 `@NotBlank`와 함께 사용하는 것이 일반적입니다.

### 정리

`@Email` 어노테이션은 이메일 형식의 유효성을 검증하기 위한 간단하고 유용한 도구입니다. 주로 사용자 회원 가입 또는 로그인 등에서 이메일 주소가 올바른지 확인하기 위해 사용됩니다. 다른 검증 어노테이션들과 함께 사용하여 더 정교한 데이터 검증을 수행하는 것이 좋습니다.

