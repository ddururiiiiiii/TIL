### AOP (관점 지향 프로그래밍)

- AOP는 핵심 로직과 공통 관심사를 분리하는 프로그래밍 패러다임
- 핵심 비즈니스 로직과 공통 로직을 분리할 수 있어 코드의 가독성과 유지보수성이 높아짐.
- 로깅, 트랜잭션 관리, 권한 검증 등 여러 곳에서 반복적으로 실행되는 코드가 있을 때 사용.
    - 트랜잭션 관리 : 데이터베이스 작업이 포함된 모든 서비스 메서드에서 트랜잭션을 자동으로 시작하고, 성공/실패에 따라 커밋 또는 롤백.
    - 로깅 : 특정 패키지에 있는 모든 메서드 호출 시 메서드 이름, 파라미터, 반환값을 기록.
    - 메서드 실행 시간 측정 : 성능 모니터링을 위해 특정 메서드가 실행되는 데 걸린 시간을 측정.

```java
@Aspect
@Component
public class LoggingAspect {
  
    @Before("execution(* com.example.service.*.*(..))")
    public void logBeforeMethod(JoinPoint joinPoint) {
        System.out.println("Before method: " + joinPoint.getSignature());
    }
}

```

### 인터셉터 (HandlerInterceptor)

- **스프링 MVC**에서 제공하는 기술
    - 스프링 MVC 구조에 특화된 필터기능과 같은 것이라 MVC를 사용하고 특별히 필터를 사용하지 않는다면 인터셉터를 사용하는 것이 더 편리함.
- HTTP 요청과 응답을 가로채 특정 작업을 수행할 수 있게 해주는 기능
- 주로 **컨트롤러(Controller)**에 접근하기 전, 또는 뷰(View)를 렌더링하기 전/후에 특정 작업을 수행.
- 동작 과정 : 클라이언트 요청 → 디스패처 서블릿 → **인터셉터** → 컨트롤러.
- 사용자 권한 체크, API 요청 검증, 응답 데이터 가공 (특정 경로에만 로직을 추가)
    - 권한 체크 : 특정 API 요청에서 사용자가 적절한 권한을 가졌는지 확인, 로그인한 사용자의 권한을 확인하고, 권한이 없으면 요청을 중단.
    - API 요청 데이터 검증 : `/api/v1/orders` 요청에서 필수 파라미터 `orderId`가 있는지 확인.
    - 사용자 세션 처리 : 사용자 요청 전에 세션 유효성을 확인하고, 유효하지 않으면 로그인 페이지로 리다이렉트.

```java
public class MyInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        System.out.println("Pre-handle: 요청 시작");
        return true; // true이면 컨트롤러로 진행
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) {
        System.out.println("Post-handle: 컨트롤러 처리 후");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        System.out.println("After-completion: 뷰 렌더링 후");
    }
}

```

### 왜 필터보다 인터셉터가 더 편리할까?

- 필터는 서블릿 스펙에서 제공하는 기능으로 스프링에서 관리하는 빈에 접근하려면 추가적인 작업이 필요함.
- 필터는 **모든 HTTP 요청**에 대해 동작하며, 요청이 어떤 컨트롤러나 핸들러로 전달될지에 대한 정보가 없습니다. 따라서 요청 경로에 따라 세부적으로 작업을 분리하려면 조건문이나 경로 매칭 로직을 직접 구현하는데, 인터셉터는 **스프링 MVC의 핸들러 매핑(Handler Mapping)** 이후에 동작하기 때문에 어떤 컨트롤러와 메서드가 호출될지에 대한 정보가 포함되어 있어 더 세부적인 제어가 가능함.
- 필터는 **스프링 컨트롤러 외부**에서 동작하며, 서블릿 스펙상 요청과 응답의 전후를 처 리하다보니스프링 MVC의 특정 요청 흐름 단계(예: 컨트롤러 호출 전, View 렌더링 후)를 다루기가 어려움.

### 필터 (Filter)

- **서블릿 스펙**에서 제공하는 기능으로, 요청과 응답을 **서블릿 컨테이너 레벨**에서 처리. → 스프링에 의존하지 않고 **웹 애플리케이션 레벨**에서 동작
- 요청이 **서블릿 컨테이너(**DispatcherServlet**)**에 도달하기 전에 동작하여 전처리 및 후처리를 수행함.
- 정적 리소스로 처리가 가능함 → 이미지 파일(.jpg, .png), 스타일시트(.css), JavaScript 파일(.js) 같은 파일들도 필터의 적용범위에 포함됨.
- 필터는 **체인 구조**를 통해 여러 개의 필터를 연결할 수 있음
- 동작과정 : 클라이언트 요청 → 필터 → 디스패처 서블릿(DispatcherServlet) → 컨트롤러
- **리퀘스트 변조가 필요한 작업**은 **필터**에서 처리.
- 주로 인증 토큰 검증, CORS 설정, 데이터 압축, 공통 로깅과 같은 **전역적인** 요청/응답 처리 작업에 사용
    - 로깅 : 모든 요청의 URI, HTTP 메서드, 응답 상태를 기록해야 한다면.
    - CORS 설정 : 클라이언트가 다른 도메인에서 요청할 때 허용 여부를 처리
    - **CSRF (Cross-Site Request Forgery)** 공격 방어 : 스프링은 CSRF 공격을 방지하기 위해 기본적으로 **CSRF 토큰을 자동으로 검증**하는 기능을 제공하며, 이를 처리하는 것은 **스프링의 필터.** 이 필터는 HTTP 요청에 포함된 CSRF 토큰을 자동으로 확인하고, 유효하지 않으면 요청을 거부함.

- **CSRF 공격**
    - **사용자가 인증된 상태**에서 악의적인 웹사이트가 **사용자의 인증 정보를 이용**해 서버에 원하지 않는 요청을 보내는 공격
        - ex. 사용자가 이미 로그인된 상태에서 공격자가 만든 페이지를 방문하면, 공격자는 사용자가 의도하지 않은 상태로 서버에 요청을 보냄.

```java
@WebFilter("/*") // 모든 요청에 대해 필터 적용
public class MyFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        System.out.println("Before processing request in Filter");
        chain.doFilter(request, response); // 다음 필터로 넘기거나 서블릿으로 이동
        System.out.println("After processing response in Filter");
    }
}

```

## **정리: AOP, 인터셉터, 필터의 차이점**

### 4. **세 가지의 비교**

| 구분 | 필터(Filter) | 인터셉터(Interceptor) | AOP |
| --- | --- | --- | --- |
| **적용 범위** | 서블릿 컨테이너 레벨 | 스프링 MVC 컨트롤러 레벨 | 메서드 실행 레벨 |
| **대상** | 모든 요청 (정적 리소스 포함) | 컨트롤러 매핑된 요청만 처리 | 특정 클래스/메서드의 비즈니스 로직 |
| **종속성** | 서블릿 API | 스프링 MVC | 스프링 AOP |
| **주요 사용 목적** | 인증, 로깅, CORS, 데이터 압축 | 권한 체크, 요청 데이터 검증 | 트랜잭션, 로깅, 권한 체크 |
| **처리 위치** | 디스패처 서블릿 전에 실행 | 디스패처 서블릿 이후 컨트롤러 전에 실행 | 메서드 호출 전후, 반환 또는 예외 처리 |
| **등록 방법** | 서블릿 필터 등록 | `WebMvcConfigurer`에서 설정 | `@Aspect`로 정의 |

---

### **실행 순서**

1. **필터(서블릿 레벨)** → 2. **인터셉터** → 3. **AOP (비즈니스 로직 실행 시점)**