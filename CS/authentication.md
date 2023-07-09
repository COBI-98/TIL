# Oauth (Open Authorization)

<br>

## OAuth란?

<br>

OAuth(Open Authorization)
  - 위임 권한부여를 위한 표준 프로토콜. 사용자 PW없이도 사용자의 데이터 접근을 허가함.
  - 인증 플로우 2가지
    - Server Side Application 플로우
    - 브라우저 베이스의 앱을 위한 묵시적 플로우

OAuth2.0
- OAuth의 버전으로, 이전 버전인 OAuth 1.0보다 간단하고 효율적인 인증 및 권한 부여 프로세스를 제공
- 인증을 위해 액세스 토큰과 리프레시 토큰을 사용하며 권한을 얻을수있음.
- 클라이언트가 서드파티 서비스에서 사용자의 인증 정보를 직접 제공하지 않고 <br> 사용자의 리소스에 접근 가능


#### 용어정리
> - 서드파티 애플리케이션 : 기본적으로 제공되는 서비스나 플랫폼 외부에서 개발되고 운영되는 애플리케이션
> - Resource Owner: 클라이언트 어플리케이션이 접근할 데이터의 소유자
> - Client: 사용자 데이터에 접근하려는 어플리케이션
> - Authorization Server: 사용자로부터 권한을 부여받고 클라이언트에게 권한을 부여하는 서버
> - Resource Server: 클라이언트 어플리케이션이 접근할 데이터 저장 시스템. 권한부여 서버와 같은 경우도 있음
> - Access Token: 데이터 권한을 부여받은 클라이언트가 Resource Server 데이터에 접근하기 위한 인증 키 


<br>

## Oauth 동작방식

Oauth 동작 방식은 JWT와 밀접한 관련이 있는 권한 부여 토큰 기반 인증 방식이다. 

[JWT](https://github.com/COBI-98/TIL/blob/main/NetWork/jwt.md)

1. 서드파티 애플리케이션은 사용자에게 인증 요청을 보낼 수 있는 권한을 얻기 위해 인증 서비스 제공자(예: 페이스북, 구글)와 사전에 등록된 클라이언트 ID와 비밀 키를 사용하여 등록

2. 서드파티 애플리케이션을 통해 로그인하거나 인증을 요청

3. 서드파티 애플리케이션은 사용자를 인증 서비스 제공자의 로그인 페이지로 리다이렉트

4. 사용자는 인증 서비스 제공자에 대한 자격 증명(예: 아이디와 비밀번호)을 입력하여 로그인

5. 인증 서비스 제공자는 사용자에게 인증을 요청하는 서드파티 애플리케이션의 권한 요청을 표시하고, 사용자에게 승인 여부

6. 사용자가 인증 요청을 승인하면, 인증 서비스 제공자는 서드파티 애플리케이션에게 인증 코드를 발급

7. 서드파티 애플리케이션은 인증 코드를 사용하여 인증 서비스 제공자에게 액세스 토큰을 요청

8. 인증 서비스 제공자는 서드파티 애플리케이션에게 액세스 토큰을 발급

9. 서드파티 애플리케이션은 발급받은 액세스 토큰을 사용하여 사용자의 리소스에 접근하거나 서비스를 이용

<br>

## OAuth의 장단점

#### **OAuth의 장점:**
- 사용자 인증 분리
    - 사용자의 인증 정보가 애플리케이션에 직접 노출되지 않아 보안성이 강화된다.
- 다양한 서비스 통합
    - OAuth를 사용하여 여러 서비스의 인증과 권한을 처리할 수 있어 통합이 용이하다.

#### **OAuth의 단점:**
- 복잡성 증가
    - OAuth 프로토콜의 도입으로 인해 애플리케이션과 서비스 간에 추가 복잡성이 도입될 수 있다.
- 악용 가능성
    -  OAuth를 통해 인증을 위임하므로, 오용이나 악용될 가능성이 있을 수 있다.

<br>

## Spring에서 OAuth 사용 예시 코드
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .antMatchers("/login").permitAll()
                .anyRequest().authenticated()
                .and()
            .oauth2Login()
                .loginPage("/login")
                .defaultSuccessUrl("/home")
                .and()
            .logout()
                .logoutSuccessUrl("/login")
                .and()
            .csrf().disable();
    }

    @Bean
    public ClientRegistrationRepository clientRegistrationRepository() {
        return new InMemoryClientRegistrationRepository(
            ClientRegistration.withRegistrationId("google")
                .clientId("COBI_CLIENT_ID")
                .clientSecret("COBI_CLIENT_SECRET")
                .redirectUri("COBI_REDIRECT_URI")
                .authorizationGrantType(AuthorizationGrantType.AUTHORIZATION_CODE)
                .build());
    }
}

```

위의 예시 코드는 Spring Security OAuth를 사용하여 OAuth 인증 및 권한 부여를 구현하는 코드다.

 - SecurityConfig 클래스는 Spring Security의 WebSecurityConfigurerAdapter를 확장하여 보안 구성을 정의
 
  - OAuth 2.0을 사용하여 로그인 및 인증을 처리하고, 클라이언트 등록 정보를 ClientRegistrationRepository에서 설정 
  
이를 통해 Spring에서 OAuth를 구현하는 방법을 확인해보았다.