# 프로세스 (Process)
- 컴퓨터에서 실행 중인 하나의 프로그램
- 운영체제로부터 독립적인 메모리 공간을 할당 받음. (다른 프로세스와 메모리를 공유하지 않음)
- 한 프로세스가 비정상적으로 종료되어도 다른 프로세스에 영향을 미치지 않음.
- 쓰레드보다 생성비용이 많이 들고 생성 속도도 느림.
- 프로세스 간의 병렬 처리가 가능 (= 멀티프로세싱)
- ex. 각각의 독립된 집

<br>

# 쓰레드 (Thread)
- 프로세스 내에서 실행되는 더 작은 단위
- 프로세스가 할당 받은 자원을 공유하여 실행됨.
- 프로세스 내의 쓰레드는 같은 메모리 공간을 공유함.
- 한 쓰레드가 비정상적으로 종료되면 다른 쓰레드에도 영향을 미칠 수 있음.
- 프로세스보다 생성비용이 적고, 생성 속도도 빠름.
- 아무런 쓰레드를 생성하지 않아도 JVM을 관리하기 위해서는 여러 쓰레드가 존재함(GC쓰레드 등)
- 쓰레드 간의 병렬 처리가 가능하다 (= 멀티쓰레딩)
- ex. 한 집 안에 있는 여러 방.


## 쓰레드를 사용하는 이유
- 프로세스를 실행하는 데는 많은 자원이 필요한데 쓰레드는 프로세스보다 보다 적은 자원으로 실행이 가능함 -> CPU자원을 효율적으로 사용할 수 있고, 동시에 여러 작업을 수행할 수 있음. 
- '경량 프로세스'라고 불리기도 함.
