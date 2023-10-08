# Spring Security 테스트 적용기 01

Spring Security와 관련된 기능을 테스트하려면 인증 정보를 미리 주입해야 한다.

테스트를 진행할 때 초록 빛을 보기위한 가장 간단한 방법은 테스트 전에 SecurityContext에 직접 Authentication을 주입하는 경우이다.
```java
class UserServiceTest {
    @BeforeEach
    void setUp() {
        UserDetails user = createUserDetails();

        SecurityContext context = SecurityContextHolder.getContext();
        context.setAuthentication(new UsernamePasswordAuthenticationToken(user, user.getPassword(), user.getAuthorities()));
    }

    private UserDetails createUserDetails() {
        ...
    }

    @Test
    void test() {
        ...
    }
}
```
이렇게 할 경우 인증 정보를 필요로 하는 메서드를 테스트할 때 항상 SecurityContext에 Authentication을 주입해야 하는 번거로움이 생길 수 있으며, <br> setUp을 통해 관리를 한다고 해도 메서드에서 요구되는 권한 설정이 바뀔 경우, <br> SecurityContext를 비우고 다시 원하는 정보로 채워야하는 번거로움이 생기게 된다.

이를 위해 Spring은 Security Test 프레임워크를 제공한다.

 Spring Security Test 프레임워크는 Spring Security를 테스트하고 로그인 로직을 효과적으로 테스트하기 위한 다양한 도구와 애노테이션을 제공한다. 

아래에서 다양한 도구와 애노테이션을 코드로 자세히 살펴보자.

<br>

## @WithMockUser
```java
@WithMockUser(username = "user", roles = "USER")
public void testUserLogin() {
    // "user"로 가정한 사용자로 로그인한 상태로 테스트를 진행
}
```

@WithMockUser는 userName, password, role 등을 어노테이션 value를 통해 설정해줄 수 있으며, default value로 username = “user”, password = “password”, role = “USER”가 설정되어 있다.

이는 특정 사용자로 가정하여 사용자로 로그인한 것처럼 간편하게 테스트를 진행할 수 있다.

<br>

## @WithUserDetails
간단한 정보만 필요로 하는 테스트는 위에서 소개한 @WithMockUser를 활용할 수 있으나,

테스트할 메소드가 Authentication의 principal을 직접 사용한다면 @WithMockUser는 잘못 동작할 가능성이 있다.

@WithUserDetails는 등록된 Bean 중 UserDetailsService 찾아 미리 어노테이션에서 설정한 username으로 사용자를 찾는다.

value을 통해 테스트에 필요한 사용자 이름을 지정할 수 있다.

```java
@Service
public class TestUserDetailsService implements UserDetailsService {
    ...

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        ...
        return new CustomUserResponse("cobi", "password", "ADMIN");
    }
}
```

```java
@WithUserDetails("cobi")
public void testUserDetails() {
    // "cobi" 사용자로 로그인한 상태로 테스트를 진행
}
```

사용자 이름(username)을 통해 실제 사용자 정보를 로드하고 해당 사용자로 인증할 수 있다.

<br>

## @WithAnonymousUser
인증되지 않은(익명) 사용자로 가정하여 테스트를 진행할 수 있다.

@WithMockUser가 인증된 사용자에 정보를 어노테이션을 통해 간단히 주입해주었다면, 반대로 @WithAnonymousUser는 인증되지 않은 사용자를 테스트에서 사용할 때 필요한 어노테이션이다.

```java 
@WithAnonymousUser
public void testAnonymousUser() {
    // 익명 사용자로 로그인한 상태로 테스트를 진행
}
```
<br>

## @WithSecurityContext
애노테이션을 사용하여 커스텀한 보안 컨텍스트를 설정할 수 있습니다. 이를 통해 사용자, 권한 및 인증 속성을 정의하고 테스트 시나리오에 맞게 사용자 컨텍스트를 만들 수 있다.

- 커스텀한 보안 컨텍스트를 설정
```java
@Retention(RetentionPolicy.RUNTIME)
@WithSecurityContext(factory = WithMockCustomUserSecurityContextFactory.class)
public @interface WithMockCustomUser {
    String first() default "first";

    String second() default "second";
}
```
- 실제로 SecurityContext를 설정할 factory 클래스
```java
public class WithMockCustomUserSecurityContextFactory implements WithSecurityContextFactory<WithMockCustomUser> {
    @Override
    public SecurityContext createSecurityContext(WithMockCustomUser customUser) {
        SecurityContext context = SecurityContextHolder.createEmptyContext();

        CustomUser principal = new CustomUser(customUser.first(), customUser.second(), "ADMIN");
        Authentication auth = new UsernamePasswordAuthenticationToken(principal, principal.getPassword(), principal.getAuthorities());
        context.setAuthentication(auth);
        return context;
    }
}
```

-  테스트할 메소드에 Custom 어노테이션 달기
```java
@Test
@WithCustomUser(first = "myName", second = "myPassword")
void customUserSecurityContextFactoryTest() {
    demoService.print();
    ...
}
```

### **@WithSecurityContext 정리**
@WithMockUser, @WithUserDetails, @WithAnonymousUser 모두 @WithSecurityContext를 활용한 어노테이션들이며 미리 구현된 factory를 통해 SecurityContext에 인증 정보를 넣을 수 있었다.
```java 
final class WithMockUserSecurityContextFactory implements WithSecurityContextFactory<WithMockUser> {
	public SecurityContext createSecurityContext(WithMockUser withUser) {
		...
		List<GrantedAuthority> grantedAuthorities = new ArrayList<>();
		for (String authority : withUser.authorities()) {
			grantedAuthorities.add(new SimpleGrantedAuthority(authority));
		}

		if (grantedAuthorities.isEmpty()) {
			for (String role : withUser.roles()) {
				...
				grantedAuthorities.add(new SimpleGrantedAuthority("ROLE_" + role));
			}
		} ...

		User principal = new User(username, withUser.password(), true, true, true, true,
				grantedAuthorities);
		Authentication authentication = new UsernamePasswordAuthenticationToken(
				principal, principal.getPassword(), principal.getAuthorities());
		SecurityContext context = SecurityContextHolder.createEmptyContext();
		context.setAuthentication(authentication);
		return context;
	}
}
```
위 코드는 @WithMockUser가 사용하는 WithMockUserSecurityContextFactory의 코드이다. @WithMockUser는 Spring Security에서 미리 구현된 User 객체를 통해 Authentication의 principal 정보를 주입할 수 있었다.

여기서 우리는 @WithSecurityContext를 사용해서 목적에 맞게 보안 컨테스트를 설정하고 <br> 이를 사용자, 권한 및 인증 속성을 정의하여 테스트 시나리오에 맞게 사용자 컨텍스트를 만들 수 있다.

<br>

## 정리 
spring security를 효과적으로 테스트하기위한 애노테이션은 특정 테스트 시나리오를 시뮬레이션하고 필요한 사용자 컨텍스트를 제공하는 데 도움이 된다. 

각 애노테이션은 테스트 코드의 목적에 따라 선택적으로 사용할 수 있으며, 로그인 및 인증 관련 테스트 시나리오를 다양하게 다룰 수 있도록 도움을 주기 때문에 쉽게 테스트 코드를 작성할 수 있다.

<br>

## reference
[
Spring Security가 적용된 곳을 효율적으로 테스트하자](https://tecoble.techcourse.co.kr/post/2020-09-30-spring-security-test/)

[Spring docs, Testing ](https://docs.spring.io/spring-security/reference/servlet/getting-started.html)