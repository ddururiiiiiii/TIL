# 1. @ResponseBody 있는 경우
~~~ java
@GetMapping("/hello")
@ResponseBody
public String hello() {
    return "Hello, 슬기!";
}
- 📌 결과 : 클라이언트에 "Hello, 슬기!" 라는 텍스트가 그대로 응답됨


✅ 2. @ResponseBody 없는 경우
~~~ java
@GetMapping("/hello")
public String hello() {
    return "hello";
}
- 📌 결과 : resources/templates/hello.html 뷰 파일을 찾아서 렌더링함
- (📦 스프링 MVC의 ViewResolver가 동작함)


✅ ResponseEntity<T>는 뭐냐면?
~~~ java
@GetMapping("/user")
public ResponseEntity<UserDto> getUser() {
    return ResponseEntity.ok(new UserDto("슬기", 28));
}
- 응답 상태 코드 + 헤더 + 바디 모두 제어 가능한 형태
- 예를 들어 404 Not Found, 201 Created, 헤더 설정 같은 것도 가능



----

- @ResponseBody나 ResponseEntity<T>가 있으면 → "그대로 응답을 내려주는" REST 방식
- 없으면 → "뷰 이름으로 처리해서 템플릿 페이지 보여주는" 웹 방식