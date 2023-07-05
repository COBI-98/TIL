# SSL / TLS
Secure Socket Layer (SSL) 및 Transport Layer Security (TLS)는 데이터 통신의 보안과 암호화를 위해 사용되는 프로토콜이다.

 SSL은 초기 버전이며, TLS는 이후에 SSL의 개선 버전으로 개발되었다. <br> 이들은 주로 웹 사이트에서 보안 접속을 제공하기 위해 HTTPS 프로토콜과 함께 사용된다.

<br>

## SSL / TLS  주요 기능 및 특징

인증 (Authentication):

- SSL/TLS는 인증을 통해 통신 상대의 신원을 확인 
- 서버는 공개키 인증서를 사용하여 자신을 인증하고,<br> 클라이언트는 인증서를 검증하여 서버의 신원을 확인가능
    - 이를 통해 중간자 공격과 같은 보안 위협을 방지할 수 있습니다.

<br>

암호화 (Encryption):
- SSL/TLS는 데이터를 암호화하여 안전한 전송을 보장
- 암호화는 공개키 및 대칭키 암호화를 사용
- 공개키 암호화는 세션 키를 안전하게 교환하고,<br> 대칭키 암호화는 실제 데이터를 암호화하는 데 사용

<br>

데이터 무결성 (Data Integrity):
- SSL/TLS는 해시 함수와 메시지 인증 코드(MAC)를 사용하여 데이터의 무결성을 검증
- 데이터가 전송 중에 변경되지 않았는지 확인하여 데이터 무결성을 보장

<br>

전자 상거래 보호 (E-commerce Protection):

- SSL/TLS는 전자 상거래 사이트와 같은 보안 요구사항이 높은 환경에서 중요한 역할
- 개인 정보, 결제 정보 및 기타 민감한 데이터를 보호하여 중간자 공격, 데이터 유출 및 변조로부터 보호

<br>

프로토콜 협상 (Protocol Negotiation):

- SSL/TLS는 클라이언트 및 서버 간의 최적의 보안 프로토콜 버전 및 암호화 알고리즘을 협상

<br>

SSL 및 TLS는 보안 측면에서 매우 중요한 역할을 한다. 웹 사이트에서 개인 정보 보호, 데이터 보안 및 사용자 신뢰를 강화하기 위해 SSL/TLS를 구현하는 것이 권장한다.
그러면 Java SpringFramework를 사용하여 간단하게 구현해보자.

<br>

## Spring SSL/TLS 구현 예시
1. 의존성 추가

```xml
<!-- Maven -->
<dependencies>
    <!-- Spring Security -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>

    <!-- Spring Boot Starter Web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

2. SSL/TLS 구성 (application.xml)
```properties
# SSL/TLS 설정
server.port=8443    #HTTPS port number
server.ssl.key-store=classpath:keystore.p12
server.ssl.key-store-password=your_keystore_password
server.ssl.keyStoreType=PKCS12
server.ssl.keyAlias=your_key_alias
```
- server.port = HTTPS port 번호
- key-store = 스토어 파일의 경로를 설정
- key-store-password = 키스토어 파일의 암호를 설정
- server.ssl.keyStoreType = 키스토어 타입 지정
- keyAlias = 키스토어의 별칭을 설정

3. 컨트롤러 구현 (REST API)
```java
@RestController
@RequestMapping("/api")
public class ApiController {

    @GetMapping("/hello")
    public String hello() {
        return "Hello, SSL/TLS API!";
    }
}
```

4. 보안 구성

WebSecurityConfigurerAdapter를 상속받는 클래스를 생성 <br>
모든 API 요청에 대해 인증을 요구하고 SSL/TLS를 사용하여 암호화된 통신을 수행하는 구성 클래스를 작성하였다.
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .requiresChannel()
            .anyRequest()
            .requiresSecure()
            .and()
            .authorizeRequests()
            .anyRequest()
            .authenticated();
    }
}
```

<br>

이렇게 모든 요청에 대해 SSL/TLS를 요구하고, 인증된 사용자만이 요청을 수행할 수 있도록 설정했다.
이를 통해 암호화된 통신을 통해 안전한 데이터 전송을 보장할 수 있다.