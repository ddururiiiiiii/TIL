# @Controller VS @Restcontroller
| 구분     | `@Controller`                 | `@RestController`       |
| ------ | ----------------------------- | ----------------------- |
| 목적     | 웹 페이지 반환                      | JSON 데이터 반환             |
| 내부적으로  | ViewResolver 동작               | HttpMessageConverter 동작 |
| 템플릿 사용 | O (JSP, Thymeleaf 등)          | X                       |
| API 응답 | X (직접 응답하려면 @ResponseBody 필요) | O (자동으로 바디에 담김)         |
- @RestController = @Controller + @ResponseBody

## @Controller 예시
~~~ java
@Controller
public class HelloController {
    @GetMapping("/hello")
    public String hello() {
        return "hello"; // → templates/hello.html 뷰 렌더링
    }
}
~~~
- 클라이언트한테 페이지(html) 를 보여줌


## @RestController 예시
~~~ java
@RestController
public class HelloApiController {
    @GetMapping("/hello")
    public String hello() {
        return "hello"; // → 응답 body에 문자열로 전송됨
    }
}
~~~