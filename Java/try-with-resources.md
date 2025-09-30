# try-with-resources
- 자동으로 자원(Resource)을 닫아주는 try문
- 파일, DB 연결, 스트림 등은 쓰고 나면 꼭 close() 해줘야 하는데 그걸 자동으로 해주는 문법

## 장점
| 전통적인 try-finally | try-with-resources |
| ---------------- | ------------------ |
| `close()` 직접 호출  | 자동으로 닫아줌           |
| 실수로 안 닫을 수도 있음   | 안전하게 자원 회수 가능      |
| 코드 길고 지저분함       | 코드 간결함             |


## 보통 자원 사용할 때 try-catch문
~~~ java
BufferedReader br = null;
try {
    br = new BufferedReader(new FileReader("hello.txt"));
    String line = br.readLine();
} catch (IOException e) {
    e.printStackTrace();
} finally {
    try {
        if (br != null) br.close(); // 직접 닫아줘야 해
    } catch (IOException e) {
        e.printStackTrace();
    }
}
~~~
- br은 try문 밖에 선언됨
- finally에서 직접 close() 해야 함
- 깜빡하거나 예외 나면 자원 누수 발생 가능


### try-with-resources 사용시
~~~ java
try (BufferedReader br = new BufferedReader(new FileReader("hello.txt"))) {
    String line = br.readLine();
} catch (IOException e) {
    e.printStackTrace();
}
// 자동으로 br.close() 호출됨!!
~~~
- br은 try 괄호 안에 선언됨
- try 끝나면 자동으로 close() 호출됨 (JVM이 finally 안 써도 해줌!)
- 예외 발생해도 무조건 닫힘!