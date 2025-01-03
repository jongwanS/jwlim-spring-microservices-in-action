# 9. 마이크로서비스 보안
- 마이크로서비스 아키텍처를 갖게 되면서 `보안 취약점`을 **막는 작업은 중요**해진다.
- 마이크로서비스 아키텍처 보안 계층
  - `애플리케이션 계층`
    - 작업의 수행 권한이 있는지 확인
  - `인프라스트럭처`
    - 서비스를 항상 실행하고 패치하고 최신화
  - `네트워크 계층`
    - 서비스가 명확히 정의된 포트를 통해 인가된 소수의 서버에만 접근

> 공개된 취약점을 찾아내는 **OWASP**

- 스프링 기반의 서비스 보안
  - **스프링 클라우드 시큐리티**
  - **키클록**(ID 및 액세스 관리)

## 9.1 OAuth2소개
- `OAuth2`는 **토큰 기반의 보안 프레임워크**
- 사용자는 `ID 제공자` 라고 하는 `제삼자 인증 서비스`로 **자신을 인증**
- 사용자는 인증에 성공하면 모든 요청과 함께 전달할 토큰을 제공받고 인증 서비스에 이 **토큰의 유효성을 확인**
- 요청을 처리하는 모든 서비스에 자격 증명을 제시하지 않고도 **각 서비스에서 사용자를 인증**
- JWT를 사용하여 `더욱 안전한 OAuth2 솔루션을 제공`
- **OAuth2 명세에는 네 가지 그랜트 타입**
  - **패스워드(password)**
    - 클라이언트 애플리케이션이 사용자로부터 직접 사용자 이름과 비밀번호를 받아 인증 서버에 전달하여 Access Token을 발급받는 방식
  - **클라이언트 자격 증명(client credential)**
    - **클라이언트 애플리케이션 자체를 인증**하여 Access Token을 발급받는 방식
    - 클라이언트 애플리케이션이 인증 서버에 `client_id`와 `client_secret`을 전송, 인증 서버는 클라이언트를 검증하고 Access Token 발급
  - **인가 코드(authorization code)** => 가장 널리 사용되는 방식이다.
    - 클라이언트 애플리케이션이 사용자를 인증 서버로 리디렉션하고, 인증 후 발급받은 인가 코드를 사용해 Access Token을 교환하는 방식
  - **암시적(implicit)** => 권장되지 않음
    - Access Token을 바로 발급받는 방식, 클라이언트 애플리케이션이 서버가 아닌 브라우저 기반에서 동작할 때 사용.

### OAuth 2.0 Grant Types 비교 요약

| Grant Type         | 사용 사례                                  | 보안 수준  | 주요 특징                                          |
|---------------------|-------------------------------------------|------------|---------------------------------------------------|
| Password           | 신뢰할 수 있는 앱 내부 사용자 인증         | 낮음       | 사용자 자격 증명을 직접 처리                      |
| Client Credentials | 서버 간 통신 또는 시스템 계정 사용         | 높음       | 사용자 관여 없이 클라이언트 자체 인증             |
| Authorization Code | 보안이 중요한 사용자 인증 (웹/모바일 앱)  | 매우 높음   | Refresh Token과 함께 사용 가능                   |
| Implicit           | 브라우저 기반 SPA                         | 낮음       | Access Token 직접 발급. 보안 이슈로 사용 줄어듦. |

---

#### **권장 사항**
- **Authorization Code** 방식이 가장 널리 사용되며, 보안이 중요한 대부분의 애플리케이션에 적합합니다.
- **Implicit** 방식은 보안 이슈로 인해 대체로 권장되지 않으며, PKCE(Proof Key for Code Exchange)와 같은 보안 강화 기법과 결합해 사용합니다.


- OAuth2의 진정한 강점
  - 애플리케이션 개발자가 제삼자 ID 제공자와 쉽게 통합
  - 자격증명을 제삼자 서비스에 계속 전달하지 않고도 해당 서비스에서 사용자를 인증하고 인가
  - OpenID Connect(OIDC)는 OAuth2 프레임워크에 기반을 둔 상위 계층
    - 로그인한 사람에 대한 인증 및 프로파일 정보를 제공
    - 인가(authorization)서버가 OIDC를 지원하는 경우, **ID 제공자(identity provider)** 라고도 한다.

## 9.2 키클록 소개
- 키클록
  - `서비스`와 `애플리케이션`을 위한 **ID 및 액세스 관리용** 오픈 소스 솔루션
- **키클록의 주요 특징**
  - 인증을 중앙 집중화, `SSO 인증`을 가능하게 한다.
  - 보안 측면에 대해 걱정하기보다는 비즈니스 기능에 집중
  - 2단계 인증 가능
  - LDAP와 호환
  - 애플리케이션과 서버를 쉽게 보호할 수 있는 여러 어댑터를 제공
  - 패스워드 정책을 재정의


![img_1.png](images/ch09/img.png)               
출처 : 길벗 - 스프링 마이크로서비스 코딩 공작소 개정2판  

[키클록 보안 네가지 구성 요소로 구분]
- 보호 자원
  - 적절한 권한이 있는 인증된 사용자만 접근할 수 있게 보호하려는 자원
- 자원 소유자
  - 자원 소유자는 인증 서버를 이용하여 자원에 접근할 수 잇는 애플리케이션 및 사용자를 승인
  - 자원 소유자가 등록한 모든 애플리케이션에는 **애플리케이션을 식별**하는 `애플리케이션 이름`과 `시크릿 키` 가 지정된다.
- 애플리케이션
- 인증 및 인가 서버

> **인증과 인가**
> - 인증 : 자격 증명을 제시하여 사용자가 누구인지 증명하는 행위
> - 인가 : 인가(권한 부여)는 사용자가 원하는 작업을 수행할 수 있는지 여부를 결정

## 9.3 작게 시작하기: 스프링과 키클록으로 한 개의 엔드포인트 보호
````shell
docker run --name keycloak \
-p 8080:8080 \
-e KEYCLOAK_ADMIN=admin \
-e KEYCLOAK_ADMIN_PASSWORD=admin \
quay.io/keycloak/keycloak:26.0.7 \
start-dev
````
### 9.3.1 도커에 키클록 추가하기
### 9.3.2 키클록 설정
![img_1.png](images/ch09/img_1.png)   
- `realm`은 키클록이 사용자, 자격 증명, 역할(role), 그룹을 한데 묶어서 관리하는 저장소
### 9.3.3 클라이언트 애플리케이션 등록
![img_1.png](images/ch09/img_2.png)               
출처 : 길벗 - 스프링 마이크로서비스 코딩 공작소 개정2판  
### 9.3.4 O-stock 사용자 구성
![img_1.png](images/ch09/img_3.png)  
![img_1.png](images/ch09/img_4.png)  
````
http://localhost:8080/realms/spmia-realm/.well-known/openid-configuration
````
### 9.3.5 O-stock 사용자 인증
![img_1.png](images/ch09/img_5.png)  
````
http://localhost:8080/realms/spmia-realm/protocol/openid-connect/token
````
![img_1.png](images/ch09/img_6.png)  
- grant_type : 실행항 그랜트 타입
- username : 로그인 사용자 이름
- password : 로그인하는 사용자 비밀번호
````
{
    "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICI4ZTlpT0Q3bkpBdVJXbTUybW9PYkNSU0xxbnl2TVBNZm9FemhURWFUN2ZjIn0.eyJleHAiOjE3MzQ4NTk5NTYsImlhdCI6MTczNDg1OTY1NiwianRpIjoiOWU1MjNiNjAtZDg1YS00N2FiLWJiODItMDA0NWVhZTgzZjBiIiwiaXNzIjoiaHR0cDovL2xvY2FsaG9zdDo4MDgwL3JlYWxtcy9zcG1pYS1yZWFsbSIsImF1ZCI6ImFjY291bnQiLCJzdWIiOiJkYmRiODIwNS1kOGQwLTQzYzgtOGFiOS1mNThkY2U3MmJjMGUiLCJ0eXAiOiJCZWFyZXIiLCJhenAiOiJvc3RvY2siLCJzZXNzaW9uX3N0YXRlIjoiM2E1OTgzYjAtMzgwOS00MzFmLTkxYTQtNjZhYTJjZTdkY2Q2IiwiYWNyIjoiMSIsImFsbG93ZWQtb3JpZ2lucyI6WyIvKiJdLCJyZWFsbV9hY2Nlc3MiOnsicm9sZXMiOlsib2ZmbGluZV9hY2Nlc3MiLCJkZWZhdWx0LXJvbGVzLXNwbWlhLXJlYWxtIiwidW1hX2F1dGhvcml6YXRpb24iLCJvc3RvY2stYWRtaW4iXX0sInJlc291cmNlX2FjY2VzcyI6eyJhY2NvdW50Ijp7InJvbGVzIjpbIm1hbmFnZS1hY2NvdW50IiwibWFuYWdlLWFjY291bnQtbGlua3MiLCJ2aWV3LXByb2ZpbGUiXX19LCJzY29wZSI6ImVtYWlsIHByb2ZpbGUiLCJzaWQiOiIzYTU5ODNiMC0zODA5LTQzMWYtOTFhNC02NmFhMmNlN2RjZDYiLCJlbWFpbF92ZXJpZmllZCI6ZmFsc2UsInByZWZlcnJlZF91c2VybmFtZSI6ImlsbGFyeS5odWF5bHVwbyJ9.Ou3xxryyoUVXKHe2nauv-mtQgfiHx_mE7MG_pMp486fJCnjmO73p5tdnT1dBPHkbTZpBy1X8J5hhZ5XZc4V_h1Ljpiiipj741sStYOf9E96kAvGLLhYmDLHJY1SPHj0CcrtD3E_7Kua0PUT8RsGuTxkuzg4UKg4YdxNqURcIb1WbiDpzbXRk3eyjhQgcnEsS77SgOj4L91UbzzcAuCK3OOb-CtIDhDC-f4uNDmVED7zs-Pexv641T-x8WW_ATcScmAcedG-ERoPzBydkJOOvn5L1EFRCsQrokDbldUL9El3ebw6a2fcbVEbF496E3-32ly_HUeQ18e8alDHgFFYOiw",
    "expires_in": 300,
    "refresh_expires_in": 1800,
    "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICIyMTMzNzRiMy1hYjc5LTRkNTktODg5OS03N2E3NDY2ZTE3MzgifQ.eyJleHAiOjE3MzQ4NjE0NTYsImlhdCI6MTczNDg1OTY1NiwianRpIjoiNThiYjIzZjQtYzcyNy00ODc1LWE1NTMtNTNhZGM1ZTcyMGVmIiwiaXNzIjoiaHR0cDovL2xvY2FsaG9zdDo4MDgwL3JlYWxtcy9zcG1pYS1yZWFsbSIsImF1ZCI6Imh0dHA6Ly9sb2NhbGhvc3Q6ODA4MC9yZWFsbXMvc3BtaWEtcmVhbG0iLCJzdWIiOiJkYmRiODIwNS1kOGQwLTQzYzgtOGFiOS1mNThkY2U3MmJjMGUiLCJ0eXAiOiJSZWZyZXNoIiwiYXpwIjoib3N0b2NrIiwic2Vzc2lvbl9zdGF0ZSI6IjNhNTk4M2IwLTM4MDktNDMxZi05MWE0LTY2YWEyY2U3ZGNkNiIsInNjb3BlIjoiZW1haWwgcHJvZmlsZSIsInNpZCI6IjNhNTk4M2IwLTM4MDktNDMxZi05MWE0LTY2YWEyY2U3ZGNkNiJ9.Rc9mms5-Nc6vjt2OGH3DsapXDqYfVd8zgaZmXmSTDA4",
    "token_type": "Bearer",
    "not-before-policy": 0,
    "session_state": "3a5983b0-3809-431f-91a4-66aa2ce7dcd6",
    "scope": "email profile"
}
````
- access_token : 보호 자원에 대한 서비스 호출시, 제시하는 액세스 토큰
- token_type : 인가 명세에 따른 토큰 타입
- refresh_token : 만료된 액세스 토큰 재발급시 제시하는 토큰
- expires_in : 액세스 토큰이 만료되기 까지 걸리는 시간(초)
- scope : 액세스 토큰의 유효 범위

![img_1.png](images/ch09/img_7.png)  

## 9.4 키클록으로 조직 서비스 보호하기
- 키클록 서버 : 토큰의 생성 및 관리
  - 스프링 시큐리티 & 스프링 부트 어댑터 : 자원 보호, 권한 부여
### 9.4.1 스프링 시큐리티와 키클록 JARs를 서비스에 추가
````xml
<dependency>
    <groupId>org.keycloak</groupId>
    <artifactId>keycloak-spring-boot-starter</artifactId> <!-- 키클록 스프링 부트 의존성 -->
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId> <!-- 스프링 시큐리티 스타터 의존성 -->
</dependency>
...
<dependency>
    <groupId>org.keycloak.bom</groupId>
    <artifactId>keycloak-adapter-bom</artifactId>   <!-- 키클록 스프링 부트 의존성 관리 -->
    <version>11.0.2</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
````
### 9.4.2 키클록 서버 접속을 위한 서비스 구성
````yaml
keycloak.realm = spmia-realm  # 생성된 realm 이름
keycloak.auth-server-url = http://localhost:8080/auth # 키클록 서버 URL 인증 엔드포인트
keycloak.ssl-required = external
keycloak.resource = ostock  # 생성된 클라이언트 ID
keycloak.credentials.secret = f7TPP6ef5jK7p1VhsDPUbkmkzvBZHerg # 생성된 클라이언트 시크릿
keycloak.use-resource-role-mappings = true
keycloak.bearer-only = true
````
### 9.4.3 서비스에 접근할 수 잇는 사용자 및 대상 정의
- 서비스에 대한 접근 제어 규칙 정의
  - 
````java
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(jsr250Enabled = true)   //@RoleAllowed 활성화처리
public class SecurityConfig extends KeycloakWebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception { //Keycloak 인증제공재 등록
      super.configure(http);
      http.authorizeRequests()
              .anyRequest().authenticated();
      http.csrf().disable();
    }
  
    @Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception { // AuthenticationProvider 정의
      KeycloakAuthenticationProvider keycloakAuthenticationProvider = keycloakAuthenticationProvider();
      keycloakAuthenticationProvider.setGrantedAuthoritiesMapper(new SimpleAuthorityMapper());
      auth.authenticationProvider(keycloakAuthenticationProvider);
    }
  
    @Bean
    @Override
    protected SessionAuthenticationStrategy sessionAuthenticationStrategy() {   // 세션인증 전략 정의
      return new RegisterSessionAuthenticationStrategy(new SessionRegistryImpl());
    }
  
    @Bean
    public KeycloakConfigResolver KeycloakConfigResolver() {
      return new KeycloakSpringBootConfigResolver();
    }
}
````
- `KeycloakWebSecurityConfigurerAdapter` 클래스 메서드들 재정의
  - configure
  - configureGlobal
  - sessionAuthenticationStrategy
  - KeycloakConfigResolver

- 이장에서 실습할 자원 보호 항목
  - `인증된 사용자`만 사용자 서비스 URL에 접근
  - `특정 역할`을 가진 사용자만 서비스 URL에 접근

[인증된 사용자로 서비스 보호]
````java
    @Override
    protected void configure(HttpSecurity http) throws Exception { //Keycloak 인증제공재 등록
      super.configure(http);
      http.authorizeRequests()
              .anyRequest().authenticated();
      http.csrf().disable();
    }
````
- 인증되지 않은 사용자는 URL 접근 불가  
![img_1.png](images/ch09/img_8.png)  
- illary.huaylupo 토큰 발급    
![img_1.png](images/ch09/img_10.png)   
- 인증된 사용자 URL 접근     
![img_1.png](images/ch09/img_9.png)  

[특정 역할을 이용한 서비스 보호]
- john.carnell 키클록 openid-connect 토큰발급
![img_1.png](images/ch09/img_11.png)  
- USER 역할은 조직 삭제 불가
![img_1.png](images/ch09/img_12.png)  

### 9.4.4 액세스 토큰 전파
- 라이선싱 서비스 -> 조직 서비스를 호출
  - 액세스 토큰 전파 필요
![img_1.png](images/ch09/img_13.png)               
출처 : 길벗 - 스프링 마이크로서비스 코딩 공작소 개정2판  

[라이선싱 서비스 구성]  
- 액세스 토큰을 전파할 때 첫 번째 단계는 메이븐 의존성 추가
````xml
		<dependency>
		    <groupId>org.keycloak</groupId>
		    <artifactId>keycloak-spring-boot-starter</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>
        ....
        <dependency>
          <groupId>org.keycloak.bom</groupId>
          <artifactId>keycloak-adapter-bom</artifactId>
          <version>11.0.2</version>
          <type>pom</type>
          <scope>import</scope>
        </dependency>
````
- 스프링 시큐리티가 없다면 라이선싱 서비스에 유입되는 호출에서 라이선싱 서비스의 모든 아웃바운드 호출에 수작업으로 헤더를 추가해야함.
  - 키클록은 이러한 호출을 지원하는 새로운 REST 템플릿 클래스인 `KeycloakRestTemplate`을 제공
````java
@Configuration
@EnableWebSecurity
@ComponentScan(basePackageClasses = KeycloakSecurityComponents.class)
public class SecurityConfig extends KeycloakWebSecurityConfigurerAdapter {

	@Autowired
	public KeycloakClientRequestFactory keycloakClientRequestFactory;
    
    ...
  
	@Bean
	@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
	public KeycloakRestTemplate keycloakRestTemplate() {
		return new KeycloakRestTemplate(keycloakClientRequestFactory);
	}
}
````
````java
@Component
public class OrganizationRestTemplateClient {
    
    @Autowired
    private KeycloakRestTemplate restTemplate;

    public Organization getOrganization(String organizationId){
        ResponseEntity<Organization> restExchange = 
                restTemplate.exchange(
                   "http://localhost:8072/organization/v1/organization/{organizationId}",
                   HttpMethod.GET,
                   null, Organization.class, organizationId);

        return restExchange.getBody();
    }
}
````
![img_1.png](images/ch09/img_14.png)               
출처 : 길벗 - 스프링 마이크로서비스 코딩 공작소 개정2판  
### 9.4.5 JWT의 사용자 정의 필드 파싱
````java
	private String getUsername(HttpHeaders requestHeaders){
		String username = "";
		if (filterUtils.getAuthToken(requestHeaders)!=null){
			String authToken = filterUtils.getAuthToken(requestHeaders).replace("Bearer ","");
	        JSONObject jsonObj = decodeJWT(authToken);
	        try {
	        	username = jsonObj.getString("preferred_username");
	        }catch(Exception e) {logger.debug(e.getMessage());}
		}
		return username;
	}

	private JSONObject decodeJWT(String JWTToken) {
		String[] split_string = JWTToken.split("\\.");
		String base64EncodedBody = split_string[1];
		Base64 base64Url = new Base64(true);
		String body = new String(base64Url.decode(base64EncodedBody));
		JSONObject jsonObj = new JSONObject(body);
		return jsonObj;
	}
````
![img_1.png](images/ch09/img_15.png)  
## 9.5 마이크로서비스 보안을 마치며

![img_1.png](images/ch09/img_16.png)               
출처 : 길벗 - 스프링 마이크로서비스 코딩 공작소 개정2판  
### 9.5.1 모든 서비스 통신에 HTTPS/SSLS 사용하라
- 운영 환경에서 마이크로서비스는 HTTPS 및 SSL로 제공되는 암호화된 채널로만 통신
### 9.5.2 서비스 게이트웨이를 사용하여 마이크로서비스에 접근하라
- 서비스 게이트웨이가 서비스 호출을 위한 진입점 및 게이트키퍼 역할
- 서비스 게이트웨이의 트래픽만 허용
  - 서비스를 보호하고 감시하는 방식을 일관된 방식으로 유지할 수 있다.
### 9.5.3 공개 API 및 비공개 API 영역을 지정하라
- 보안은 접근 계층을 구축하고 **최소 권한 개념을 적용** 하는것이다.
- **사용자는 작업을 수행할 수 있는 최소한의 네트워크 접근과 권한**만 가져야 한다
- **비공개 영역**은 핵심 애플리케이션의 기능 및 데이터를 보호하는 장벽 역할을 한다.
### 9.5.4 불필요한 네트워크 포트를 차단해서 마이크로서비스 공격 지점을 제한하라
- 서비스에서 필요한 포트나 인프라스트럭처에 대한 인바운드와 아웃 바운드 액세스만 허용해야 한다.
## 9.6 요약
- OAuth2는 웹 서비스 호출을 보호하기 위해 다양한 메커니즘을 제공하는 토큰 기반의 인가 (권한 부여) 프레임워크 => 이러한 메커니즘을 **그랜트** 라고 칭함.
- 애플리케이션마다 키클록 애플리케이션의 고유 이름과 시크릿 키
- 운영 환경에서는 HTTPS를 사용하여 서비스 간 모든 호출을 암호화
- 서비스 게이트웨이를 사용하여 서비스에 도달할 수 있는 접근 지점을 줄여야 한다.
- 서비스가 실행되는 운영 체제의 인바운드 및 아웃바운드 포트 수를 제한해야 한다.