#  Spring MVC 실행 흐름
- 요청 → DispatcherServlet → HandlerMapping → Controller → ViewResolver → View → 응답

----

### 1. 클라이언트 요청
- 사용자가 브라우저에서 GET /users/1 같은 요청을 보냄.

### 2. Front Controller: DispatcherServlet
- 모든 요청은 스프링의 중앙 관제탑인 DispatcherServlet이 먼저 받음.
- 웹.xml 또는 SpringBoot의 @SpringBootApplication 설정 덕분에 모든 URL이 이쪽으로 들어옴.

### 3. Handler Mapping
- 어떤 컨트롤러가 이 요청을 처리할지 찾음.
- 예: /users/1 → UserController.getUser() 매핑됨.

### 4. Handler Adapter
- 찾은 컨트롤러를 실행할 수 있도록 도와주는 어댑터 역할.
- @RequestMapping, @GetMapping 등의 애노테이션을 해석함.

### 5. Controller 실행
- 실제 사용자가 정의한 컨트롤러 메서드가 실행됨.
- DB에서 사용자 정보 가져오고, 로직 처리함.

### 6. Model과 View 반환
- 컨트롤러가 ModelAndView, 또는 String(view 이름) + Model 객체를 반환.
- ex: "user/profile" + {name: "슬기"}

### 7. View Resolver
- 반환된 view 이름을 가지고 실제 보여줄 JSP, HTML 파일 위치를 찾음.
- ex: "user/profile" → /WEB-INF/views/user/profile.jsp

### 8. View 렌더링
- 모델 데이터를 뷰에 전달해서 HTML을 완성.
- 완성된 HTML을 브라우저로 응답.

### 9. 클라이언트 응답
- 사용자의 브라우저에 최종 HTML 페이지가 도착함.

