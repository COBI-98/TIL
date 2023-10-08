# Spring Security 테스트 적용기 02
OAuth 2.0 또는 JWT와 같은 보다 복잡한 인증 메커니즘을 사용하는 경우, @WithMockUser와 @WithUserDetails만으로는 충분하지 않을 수 있다.
 
OAuth 2.0 및 JWT와 같은 토큰 기반 인증 방식은 다른 방식으로 테스트해야한다.

일반적인 방식으로는  Spring Security Test 프레임워크에서는 @WithMockOAuth2User 애노테이션을 사용하여 OAuth 2.0 인증을 가정하는 테스트를 작성할 수 있다.

<br>

## @AutoConfigureMockMvc
OAuth 2.0 보안을 테스트하기 위해 MockMvc를 설정하는 데 사용되는 애노테이션이다. 

MockMvc를 자동으로 구성하고 OAuth 2.0 클라이언트 자격 증명을 처리하는 데 도움을 준다.
```java
@SpringBootTest
@AutoConfigureMockMvc
public class CustomOAuth2UserServiceTest {
    // 테스트 코드 작성
}
```

<br>

## @WithMockOAuth2Scope
애노테이션을 사용하여 OAuth 2.0 스코프를 모의(mock)로 생성하고 테스트에 사용할 수 있다. 

OAuth 2.0 클라이언트가 필요한 스코프를 테스트하는 데 유용하다.
```java
@WithMockOAuth2Scope("read")
public void testOAuth2Scope() {
    // read 스코프를 모의로 가지고 있는 상태로 테스트를 진행
}
```

<br>

## @WithMockOAuth2Client
Spring Security OAuth 2.0를 사용하여 보호된 엔드포인트에 접근하려고 할 때를 테스트하려고한다. 

my-client라는 클라이언트로 인증된 사용자로 엔드포인트에 접근하는 테스트를 작성하려고 할 때, <br>
@WithMockOAuth2Client는 가상 Oauth2.0 클라이언트로 인증된 상태를 만들어 줄 수 있다.
```java
@WithMockOAuth2Client(clientId = "my-client", authorities = "ROLE_USER")
public void testOAuth2Client() {
    // 가상 OAuth 2.0 클라이언트로 인증된 상태로 테스트를 진행

    // 가정한 클라이언트로 보호된 엔드포인트에 접근하는 테스트 코드를 작성
    
}
```

<br>

## @WithOAuth2TestUser
@WithMockUser는 userName, password, role 등을 어노테이션 value를 통해 설정해줄 수 있었다면, <br> @WithOAuth2TestUser는
사용자 이름과 클라이언트 정보를 기반으로 실제 OAuth 2.0 테스트 사용자를 생성해줄 수 있다.

 사용자 이름, 클라이언트 ID, 권한 등을 지정해줄 수 있다.

```java
@Test
  @DisplayName("사용자 이름 변경")
  @WithOAuth2TestUser(username = "john.doe", clientId = "my-client", authorities = "ROLE_USER")
  void modifyName() throws Exception {

    // given
    NameDto dto = NameDto.builder()
        .name("홍길동").build();

    // when
    ResultActions result = mockMvc.perform(put("/api/v1/user/name")
        .header(HttpHeaders.AUTHORIZATION, ACCESS_TOKEN_PREFIX + "ACCESS_TOKEN")
        .content(objectMapper.writeValueAsString(dto))
        .contentType(MediaType.APPLICATION_JSON)
    );

    // then
    result.andExpect(status().isOk())
        .andDo(document("member/put-name",
            requestHeaders(
                headerWithName(HttpHeaders.AUTHORIZATION).description("액세스 토큰")
            ),
            requestFields(
                fieldWithPath("name").description("사용자 이름")
            ),
            responseFields(
                fieldWithPath("status").description("응답 상태 코드"),
                fieldWithPath("message").description("응답 메시지")
            )
        ));
  }
```

## 정리
이러한 도구와 애노테이션을 사용하여 Spring Security OAuth를 테스트하면 보안 및 인증 관련 시나리오를 다룰 수 있다.

각 애노테이션은 특정 시나리오를 시뮬레이션하고 필요한 인증 및 권한을 설정하는 데 도움되어 쉽게 테스트 코드를 작성할 수 있었다.

## reference

[Spring docs, Testing OAuth 2.0](https://docs.spring.io/spring-security/reference/servlet/test/mockmvc/oauth2.html)