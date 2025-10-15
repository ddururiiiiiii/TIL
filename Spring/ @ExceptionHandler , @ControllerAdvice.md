#  @ExceptionHandler
- 컨트롤러에서 예외가 터졌을 때, 그 예외를 잡아서 처리해주는 메서드에 붙이는 어노테이션
- 보통 API 호출하다가 예외가 터지면 → 스프링이 500 에러랑 이상한 HTML 에러 페이지를 줌
- 이걸 @ExceptionHandler로 예외를 직접 잡고, 원하는 응답 형태로 만들어주는 일을 함.

## 예시
~~~ java
@RestController
public class UserController {

    @GetMapping("/users/{id}")
    public User getUser(@PathVariable Long id) {
        if (id == 0) {
            throw new IllegalArgumentException("ID는 0이 될 수 없습니다.");
        }
        return new User(id, "슬기");
    }

    // 👇 예외 처리 메서드
    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<String> handleIllegalArgument(IllegalArgumentException ex) {
        return ResponseEntity
                .badRequest()
                .body("에러 발생: " + ex.getMessage());
    }
}
~~~

----

# @ControllerAdvice
- 전역에서 예외 처리나 공통 로직을 담당하는 스프링 애노테이션
- 모든 컨트롤러에 적용되는 공통 기능을 따로 모아놓은 클래스에 붙임
- 전역 예외 처리 : 컨트롤러에서 발생하는 예외를 한 곳에서 처리하고 싶을 때.
- 전역 바인딩 처리 : 컨트롤러에 들어오는 파라미터 값을 공통으로 가공하거나 검증할 때.
- 전역 응답 변환 : @ResponseBody와 함께 JSON 응답을 일괄적으로 가공하고 싶을 때.

~~~ java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<String> handleIllegalArgument(IllegalArgumentException ex) {
        return ResponseEntity.badRequest().body("잘못된 요청이에요: " + ex.getMessage());
    }
}
~~~