# JWT

### JWT (JSON Web Token)란?

JWT는 **JSON Web Token**의 약자로, 두 시스템 간에 안전하게 정보를 전달하기 위한 **토큰**의 일종입니다. 보통 사용자 인증을 위해 많이 사용됩니다. 예를 들어, 서버와 클라이언트 간에 사용자를 인증하거나 권한을 검증할 때 사용됩니다.

JWT는 다음과 같은 특징을 가지고 있습니다:
1. **자기 포함(Self-contained)**: JWT 자체에 모든 정보를 담고 있어 추가적인 데이터베이스 조회 없이도 인증을 처리할 수 있습니다.
2. **JSON 기반**: JWT는 JSON 형식으로 데이터를 표현하며, 쉽게 생성하고 해독할 수 있습니다.
3. **서명 기반**: JWT는 **서명**을 포함하고 있어, 데이터가 위변조되지 않았는지 확인할 수 있습니다.

### JWT의 구조 {id="jwt_1"}

JWT는 **세 부분**으로 나뉩니다:
1. **헤더(Header)**
2. **페이로드(Payload)**
3. **서명(Signature)**

각각을 점(`.`)으로 구분하여 JWT가 만들어집니다. 예를 들어:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjoiMTIzNDU2IiwiZXhwIjoxNjE2MjM5MDIyfQ.PszfG9gK3xZP0ayDznjttP4VVUNePV69MxTpObN5G4Y
```

이것이 JWT의 예시입니다. 점(`.`)으로 나뉘어 있는 세 부분을 설명드릴게요.

#### 1. 헤더 (Header)
헤더는 토큰의 타입과 서명에 사용되는 알고리즘을 포함합니다. 예를 들어, HMAC SHA256 알고리즘을 사용하는 경우, 헤더는 다음과 같이 표현됩니다:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

#### 2. 페이로드 (Payload)
페이로드는 사용자 정보 또는 클레임(Claim)을 담고 있습니다. 클레임은 토큰에 담긴 정보의 집합입니다. 예를 들어, 사용자 ID, 이름, 만료 시간 등 다양한 정보를 담을 수 있습니다.

예를 들어:

```json
{
  "user_id": "123456",
  "exp": 1616239022
}
```

페이로드에는 세 가지 타입의 클레임이 있을 수 있습니다:
- **등록된 클레임(Registered Claim)**: 권장되거나 예약된 클레임입니다. 예를 들어 `iss` (발급자), `exp` (만료 시간), `sub` (주제) 등이 있습니다.
- **공개 클레임(Public Claim)**: 사용자 정의 클레임으로, 예를 들어 `user_id`와 같은 정보입니다.
- **비공개 클레임(Private Claim)**: 클라이언트와 서버 간의 고유한 정보를 담을 수 있습니다.

#### 3. 서명 (Signature)
서명은 JWT의 무결성을 확인하는 부분입니다. 헤더와 페이로드를 인코딩한 후, 서명을 생성하기 위해 비밀 키(secret key)와 함께 특정 알고리즘으로 암호화합니다.

예를 들어:

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret
)
```

이 서명 덕분에 JWT가 위변조되지 않았음을 확인할 수 있습니다.

### JWT 사용 예제 {id="jwt_2"}

JWT를 실제로 어떻게 사용하는지 간단한 예제를 통해 설명드릴게요.

**예시 시나리오**: 사용자가 로그인할 때 서버가 JWT를 발급하고, 그 후의 요청에서 사용자가 해당 JWT를 보내는 방식으로 인증을 진행합니다.

#### 1. 사용자가 로그인 요청을 보냄
클라이언트(예: 웹 애플리케이션)가 사용자 로그인 정보를 서버에 보냅니다.

```
POST /login
{
  "username": "john",
  "password": "password123"
}
```

#### 2. 서버에서 로그인 정보를 확인하고 JWT 발급
서버가 사용자의 로그인 정보를 확인한 후, JWT를 생성해 사용자의 정보를 포함한 토큰을 발급합니다.

```java
// Java 예시: JWT 생성 코드
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;

String secretKey = "mySecretKey";

String jwt = Jwts.builder()
    .setSubject("123456") // user ID
    .claim("username", "john")
    .setIssuedAt(new Date())
    .setExpiration(new Date(System.currentTimeMillis() + 3600000)) // 1시간 만료
    .signWith(SignatureAlgorithm.HS256, secretKey)
    .compact();

System.out.println(jwt);
```

이 JWT는 사용자 정보(예: `user_id`, `username`)를 포함하고 있으며, 서명되어 있기 때문에 변조되면 서버에서 쉽게 확인할 수 있습니다.

#### 3. 클라이언트는 JWT를 헤더에 담아 요청
클라이언트는 이후 요청을 보낼 때마다 JWT를 요청 헤더에 담아 보냅니다.

```
GET /my-orders
Authorization: Bearer <JWT 토큰>
```

서버는 이 JWT를 사용해 사용자를 인증합니다.

#### 4. 서버에서 JWT 검증
서버는 클라이언트가 보낸 JWT를 확인하여 유효성을 검증합니다. 예를 들어 만료되지 않았는지, 서명이 올바른지 등을 확인합니다.

```java
// Java 예시: JWT 검증 코드
import io.jsonwebtoken.Claims;

Claims claims = Jwts.parser()
    .setSigningKey(secretKey)
    .parseClaimsJws(jwt)
    .getBody();

System.out.println("User ID: " + claims.getSubject());
System.out.println("Username: " + claims.get("username"));
```

JWT가 유효하다면, 서버는 사용자에게 필요한 리소스를 제공합니다.

### JWT의 장점과 단점

#### 장점
1. **무상태 인증**: 서버는 JWT를 발급한 후, 사용자의 상태를 따로 저장할 필요가 없습니다. 즉, 세션 저장소가 필요 없습니다.
2. **확장성**: 서버 확장이 쉽습니다. 다른 서버 인스턴스에서도 동일한 비밀 키를 사용하면 JWT를 검증할 수 있습니다.
3. **안전한 데이터 전달**: JWT는 서명되어 있어 위변조를 방지할 수 있습니다. 또한 페이로드를 암호화하여 데이터의 기밀성을 높일 수도 있습니다.

#### 단점
1. **크기**: JWT는 비교적 크기가 크기 때문에, 대역폭에 부담을 줄 수 있습니다.
2. **만료 관리**: JWT는 발급 시 정해진 만료 시간이 지나면 더 이상 사용할 수 없습니다. 만약 사용자가 로그인 상태를 갱신하지 않는다면, 만료된 후 다시 로그인해야 합니다.
3. **취소 불가능**: 한번 발급된 JWT는 취소하기 어렵습니다. 사용자 로그아웃 시 토큰을 취소하거나 무효화하는 것이 어렵기 때문에, 이를 해결하기 위해 별도의 블랙리스트 저장소가 필요할 수 있습니다.

### 요약

- JWT는 JSON 기반의 인증 토큰으로, 두 시스템 간에 정보를 안전하게 전달하기 위한 방식입니다.
- JWT는 헤더, 페이로드, 서명 세 부분으로 구성됩니다.
- 서버에서 JWT를 발급하고 클라이언트가 이를 보관하여 요청 시 사용합니다.
- 서버는 JWT의 서명을 통해 무결성을 검증하고, 유효한 JWT라면 인증을 허용합니다.

### 간단한 예제 요약
1. 사용자가 로그인하면 서버에서 JWT를 발급합니다.
2. 클라이언트는 이후 모든 요청에 JWT를 헤더에 담아 보냅니다.
3. 서버는 JWT를 검증하여 사용자를 인증합니다.

JWT는 많은 현대 웹 애플리케이션에서 사용되는 인기 있는 인증 방식입니다. 이해하기 어려울 수 있지만, 이러한 예제와 개념을 통해 조금씩 익숙해지면 효율적으로 사용할 수 있을 거예요!


