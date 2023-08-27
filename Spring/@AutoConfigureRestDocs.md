# @AutoConfigureRestDocs


## 등장배경
`Spring REST Docs`는 API 문서를 생성하기 위해 테스트 기반 접근 방식을 사용한다.

그 과정중 테스트 시나리오는 API 호출을 실행하고 결과를 문서화하는 방식인데, 이로 인해 설정이 복잡해질 수 있다.

@AutoConfigureRestDocs는 이러한 설정 복잡성을 줄이고, Spring Boot 프로젝트에서 자동으로 Spring REST Docs를 설정할 수 있도록 등장했다.

<br>

##  Spring REST Docs 활성화 예시
> 프로젝트 gradle 설정
```groovy
testImplementation 'io.rest-assured:spring-mock-mvc'
```

```java
@SpringBootTest
@AutoConfigureRestDocs
public class MyRestDocsTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    public void testApiDocumentation() throws Exception {
        this.mockMvc.perform(get("/api/some-endpoint"))
            .andExpect(status().isOk())
            .andDo(document("api-documentation")); // 문서화 설정
    }
}
```

1. 문서 출력 디렉토리 <br>
    target/generated-snippets 디렉토리에 자동으로 문서 스니펫이 생성

2. Snippets 생성 <br> 
andDo(document(...))를 사용하여 API 호출과 문서 생성을 연결

3. 문서 포맷 지원 <br> REST Docs에서 지원하는 다양한 문서 포맷에 대한 설정을 처리

4. 스니펫 링크 <br> 
문서에서 스니펫 간의 링크를 자동으로 생성하여 관련 정보를 이어준다.

<br>

## 장,단점
장점:

- 설정 단순화: Spring REST Docs의 설정을 자동으로 처리하므로, <br>
개발자는 더 적은 설정 작업으로 문서 작성에 집중할 수 있다.
- 통합: Spring Boot 프로젝트에서 기본 설정과 자연스럽게 통합되어 사용한다.
- 생산성 향상: 테스트 기반 접근 방식을 사용하여 API 호출과 동시에 문서 작성이 가능하다.

단점:

- 일부 제한: @AutoConfigureRestDocs는 간단한 설정을 위한 것이며, 더 복잡한 시나리오에 대한 커스터마이징이 필요한 경우에는 제한적이다.

<br>

## Spring 적용 예시

다음은 카페 키오스크 `신규 상품을 등록하는 API`  테스트 예시이다.

<br>

```java
@ExtendWith(RestDocumentationExtension.class)
public abstract class RestDocsSupport {

    protected MockMvc mockMvc;
    protected ObjectMapper objectMapper = new ObjectMapper();

    @BeforeEach
    void setUp(RestDocumentationContextProvider provider) {
        this.mockMvc = MockMvcBuilders.standaloneSetup(initController())
                .apply(documentationConfiguration(provider))
                .build();
    }

    protected abstract Object initController();
}
```

```java
public class ProductControllerDocsTest extends RestDocsSupport {

    private final ProductService productService = Mockito.mock(ProductService.class);

    @Override
    protected Object initController() {
        return new ProductController(productService);
    }

    @DisplayName("신규 상품을 등록하는 API")
    @Test
    void createProductAPI() throws Exception{
        // given

        // ...
    }
}
```

의 테스트 시나리오에  @AutoConfigureRestDocs 를 추가해보자.

```java

@ExtendWith(RestDocumentationExtension.class)
@AutoConfigureRestDocs 
public abstract class RestDocsSupport {

    protected MockMvc mockMvc;

    // ...

    // 기존 코드를 유지하며 @AutoConfigureRestDocs를 사용하도록 변경
    @BeforeEach
    void setUp(RestDocumentationContextProvider provider) {
        this.mockMvc = MockMvcBuilders.standaloneSetup(initController())
                .apply(MockMvcRestDocumentation.documentationConfiguration(provider))
                .build();
    }

    protected abstract Object initController();
}
```

`ProductControllerDocsTest 클래스` 동일

<br>

**@AutoConfigureRestDocs 자동 처리**

- MockMvc의 RestDocumentationConfiguration 적용
    - MockMvc를 생성할 때 더 이상 MockMvcRestDocumentation.documentationConfiguration(provider)와 같은 설정을 하지 않아도 됨
- RestDocsSupport 클래스에서 중복된 코드 제거
    - AutoConfigureRestDocs를 사용하게 되면, RestDocumentationContextProvider를 전달하는 코드 또한 자동으로 처리된다. 따라서 setUp 메서드에서 중복되는 코드를 제거하고, 더 간결한 형태로 유지할 수 있음

<br>

### 정리

**@AutoConfigureRestDocs**는 Spring Boot의 테스트 환경을 설정하고 RestDocs 기능을 활성화하는 데 도움을 주는 역할을 한다.

반면에 **@BeforeEach** 메서드 내에서는 MockMvc 객체를 설정하고 RestDocs의 문서화 기능을 초기화하는 작업을 수행한다.

따라서 @AutoConfigureRestDocs로 전체적인 환경을 설정한 후에 @BeforeEach를 사용하여 MockMvc를 초기화하고 문서화 설정을 적용하는 것은 일반적인 패턴이다.


이렇게 함으로써 각각의 테스트 메서드에서 필요한 MockMvc 객체와 RestDocs 설정을 사용할 수 있습니다.
