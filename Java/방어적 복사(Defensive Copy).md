# 🛡 방어적 복사란?
- 외부에서 넘겨준 객체를 그대로 쓰지 않고, 복사해서 사용하는 것 → 원본이 바뀌는 걸 방지하기 위해!

## 📦 왜 필요할까?
- 자바에서 객체는 참조형이라서 외부에서 준 객체를 그대로 쓰면 내부 상태가 바뀔 위험이 있음


## ✅ 그래서 이렇게 방어한다
1. 복사해서 저장하기
~~~ java
public Person(Date birthDate) {
    this.birthDate = new Date(birthDate.getTime()); // ✅ 방어적 복사
}
~~~

2. getter에서도 복사해서 리턴
~~~ java
public Date getBirthDate() {
    return new Date(birthDate.getTime()); // ✅ 방어적 복사
}
~~~

### 💡언제 쓰면 좋냐면?
- Date, List, Map 같은 가변 객체를 필드로 가질 때
- 외부에서 값을 수정 못 하게 보호하고 싶을 때

