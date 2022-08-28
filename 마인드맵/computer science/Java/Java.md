---

mindmap-plugin: basic

---

# Java

## JDK : java Development Kit
- Java 개발 목적
- 특징
   - JRE를 포함하고 있음 (JDK ⊃ JRE)
   - javac가 있다
- 구성
   - JVM
   - 자바 클래스 라이브러리
   - 자바 명령

## JRE : Java Runtime Environment
- Java 실행 목적
- 특징
   - Java용 SDK(Software Development Kit)

## JVM ^afca81fa-0cb1-26b9
- 메모리 구조
- JVM이란?
   - 자바와 운영체제 사이에서 중개자 역할을 수행
   - 운영체제에 구애 받지 않고 프로그램을 `실행`할 수 있도록 도와줌 (단, JVM은 운영체제에 종속적)
   - 가비지 컬렉터를 사용한 메모리 관리 자동 수행
   - 다른 하드웨어와 다르게 레지스터 기반이 아닌 스택 기반으로 동작
- 구조
   - Class Loader ^6f801167-65a9-8884
      - 런타임 시에 동적으로 클래스를 로드
      - JVM 내로 클래스 파일을 로드하고, 링크를 통해 배치하는 작업을 수행하는 모듈
   - Execution Engine
      - 클래스 로더를 통해 JVM 내의 Runtime Data Area에 배치된 바이트 코드들을 명렁어 단위로 읽어서 실행
      - 인터프리터 방식 → (일정 기준이 넘어가면) → JIT컴파일러 방식
   - GC
      - 힙 메모리 영역에 생성된 객체들 중에서 참조되지 않은 객체들을 탐색 후 제거하는 역할
   - Runtime Data Area
      - JVM의 메모리 영역으로 자바 애플리케이션을 실행할 때 사용되는 데이터들을 적재하는 영역
         - Method Area
            - 모든 쓰레드가 공유하는 메모리 영역
            - 클래스, 인터페이스, 메소드, 필드, Static 변수 등의 바이트 코드를 보관
         - Heap Area
            - 모든 쓰레드가 공유하며, new 키워드로 생성된 객체와 배열이 생성되는 영역
            - 메소드 영역에 로드된 클래스만 생성이 가능
            - Garbage Collector가 참조되지 않는 메모리를 확인하고 제거하는 영역
         - Stack Area
         - PC register
            - 현재 수행중인 JVM 명령의 주소를 갖는다
            - 쓰레드가 시작될 때 생성
            - 생성될 때마다 생성되는 공간으로 쓰레드마다 하나씩 존재
            - 쓰레드가 어떤 부분을 무슨 명령으로 실행해야할 지에 대한 기록을 하는 부분
         - Native Method Stack
            - 자바 외 언어로 작성된 네이티브 코드를 위한 메모리 영역
      - 참고 : https://steady-coding.tistory.com/305

## GC (Garbage Collector) ^a51c5b6a-3884-76d0
- GC란?
   - 힙 메모리 영역에 생성된 객체들 중에서 참조되지 않은 객체들을 탐색 후 제거하는 역할
   - Java의 GC는 2가지 가정에 기반해 설계됨 (weak generational hypothesis)
      - 대부분의 객체는 금방 unreachable 상태가 된다
      - 오래된 객체에서 최근 생긴 객체로의 참조는 거의 없다.
   - 2가지 가정에 의해 설계된 Heap 메모리 구조는 Young/Old 영역으로 나뉨
      - Young
         - 새로운 객체가 생성되는 위치
         - Young 영역에서 Old 영역으로 객체가 이동하는 것을 `Promotion` 되었다고 표현
      - Old
         - Young 영역에서 임계치 보다 오래 살아남은 객체들이 넘어오는 영역
         - 이 영역에서 일어나는 GC를 `Major GC` 또는 `Full GC`라고 한다.
            그럴 경우에는 STW(=Stop The World)가 발생해 JVM이 GC 스레드 빼고 모두 일시적으로 멈추는 현상 발생
         - 일반적으로 GC 튜닝을 한다는 것은 Old 영역에서 발생하는 STW 시간을 단축시키는 것을 의미
         - 512 바이트 chunk의 card table 존재
            - old 객체에서 young 객체로의 참조 정보를 담고 있어
               Minor GC 실행시  메모리 회수 대상인지 아닌지를 빠르게 판단
               - 약간의 overhead가 발생하지만 전반적인 GC 시간을 줄여준다
      - Permanent Generation
         - Old 영역에서도 GC에서 오래 살아남은 객체들이 이동
            - Major GC로 unreachable한 객체를 회수
            - Major GC이기 때문에 STW가 발생
- 동작과정
   - Minor GC 동작
      - Copy & Scavenge 알고리즘
         - 속도가 빠르고 작은 크키의 메모리의 GC에 효과적
      - Young 영역에 있는 객체가 GC에서 살아 남을 때마다
         `Tenuring Threshold` 값이 1씩 증가하다가 살아 남은 횟수가 Max 값에 도달하면 Old 영역으로 Promotion되는 것
         - 기본 Max 값은 31이고, 이는 MaxTenuringThreshold 옵션을 통해 변경 가능
   - Major GC(Full GC)
      - Major GC를 수행할 때 어디에 초점을 맞추느냐에 따라 2가지 방식으로 구분할 수 있다
         - Throughput Collector
            - GC를 수행할 때 모든 리소스를 투입해서 GC를 빨리 끝내자. STW 시간을 단축시키는 것이 목적
            - Serial GC, Parallel GC, Parallel Old GC
         - Low Pause(Latency) Collector
            - STW가 발생을 분산시켜 체감 pause time을 줄이자. GC 수행과 동시에 작업도 수행할 수 있다.
            - CMS, G1 GC
- 종류 ^6063678a-85ed-85ef
   - Serial GC
      - Minor GC & Major GC를 하나의 스레드로 작업
   - Parallel GC
      - Minor GC는 멀티 스레드로 작업하고 Major GC는 하나의 스레드로 작업
   - Parallel Old GC
      - Minor GC & Major GC 모두 멀티 스레드로 작업한다.
         - Multi CPU에 유리
         - Old GC의 처리량이 증가
   - CMS GC (Concurrent Mark-Sweep)
      - Low Latency GC로 Compaction을 기본적으로 수행하지 않다가 단편화가 심해지면 수행
   - G1 GC
      - Java 9 버전부터 기본 GC 방식으로 채택
      - CMS의 문제점을 개선하기 위해 만들어진 GC로 위의 4가지 방법과 구조가 다르다.
         - 물리적 Generation 구분을 없애고
            전체 Heap을 1~32MB 단위의 Region들로 재편 (약 2048개의 Regions들로 나눌수있음)
            - 각 Region은 상태에 따라 역할이 동적으로 부여
      - 역할
         - Humongous 추가
            - Region 크기의 50%가 넘는 큰 객체를 저장하기 위한 영역
            - GC 동작이 최적으로 동작하지 않는다
         - Available / Unused 추가
            - 아직 사용되지 않는 영역
   - 참고 : https://s2choco.tistory.com/14

## Java의 컴파일 과정
- 과정 ^8feb6dd4-2470-fca8
   - java 파일 작성 → .java
   - 자바 컴파일러가 .java 컴파일 → .class
   - JVM 클래스로더에게 전달
   - 클래스 로더는 동적로딩을 통해, 필요한 클래스들을 로딩 및 링크하여 런타임 데이터 영역 (JVM의 메모리) 에 올린다
      - 클래스 로더 세부 동작
         - 로드 - 클래스 파일을 가져와 JVM 메모리에 로드
         - 검증 - 자바언어 명세 및 JVM 명세에 명시된 대로 구성되어 있는지 검사
         - 준비 - 클래스가 필요로 하는 메모리 할당 (필드, 메서드, 인터페이스 등)
         - 분석 - 클래스의 상수 풀 내 모든 심볼릭 레퍼런스를 다이렉트 레퍼런스로 변경
         - 초기화 - 클래스 변수들을 적절한 값으로 초기화 (static 필드)
   - 실행엔진(java)은 JVM 메모리에 올라온 바이트 코드들을 명령어 단위로 하나씩 실행, 이때, 실행 엔진은 두가지 방식으로 변경??
      - 인터프리터
         - 바이트 코드 명령어를 하나씩 읽어서 해석하고 실행
         - 하나하나의 실행은 빠르나, 전체적인 실행 속도가 느리다
      - JIT 컴파일러 (just-in-time compiler)
         - 인터프리터의 단점을 보완하기 위해 도입된 방식
         - 바이트 코드 전체를 컴파일하여 바이너리 코드로 변경하고 이후에는 해당 메서드를 더이상 인터프리팅 하지 않고, 바이너리 코드로 직접 실행
         - 하나씩 인터프리팅하여 실행x 바이트 코드 전체가 컴파일된 바이너리 코드를 실행 o
         - 따라서 전체적인 실행속도는 인터프리팅 방식보다 빠름
   - 질문
      - 인터프리터와 JIT 컴파일러는 둘 다 실행되는건지 택 1인지?
   - 참고
      - https://gyoogle.dev/blog/computer-language/Java/컴파일%20과정.html

## 컴파일 언어 vs 인터프리터 언어
- 컴파일언어
   - 정의
   - 특징
      - 컴파일 단계와 실행 단계가 분리
      - 런타임 상황에서는 이미 기계어로 모든 소스코드가 변환되어 있기 때문에 빠르게 실행할 수 있다
      - 실행 파일 생성
   - 예시
      - C/C++, Go
- 인터프리터 언어
   - 정의
      - 원시코드(소스코드)를 기계어로 변환하는 과정없이 한줄 한줄 해석하여 바로 명령어를 실행하는 언어
   - 특징
      - 런타임에 인터프리터가 코드 한 줄씩 중간 코드인 바이트코드로 변환 후 즉시 실행
      - 기계어로 변환하지 않기 때문에 빌드 시간이 없다
      - Runtime 상황에서는 한 줄씩 실시간으로 읽어서 실행하기 때문에 컴파일 언어에 비해 속도가 느리다
   - 예시
      - Python, Javascript, Ruby
- 자바는 컴파일언어일까 인터프리터 언어일까?
   - 자바 컴파일러가 .java를 자바 바이트 코드 .class로 번역한다
   - 자바 바이트코드는 자바 인터프리터를 기계어로 번역되어 한줄씩 실행된다
   - 처음에는 인터프리터 언어로써 사용되다가 성능향상을 위해 JIT 컴파일러를 추가하여 컴파일 언어의 장점을 가져왔다고 한다

## Java LTS 버전 ^037e2495-b631-4e82
- Java 8
   - 함수형프로그래밍 지원
      - 람다표현식
      - 함수형 인터페이스
      - Stream
   - Default Method
   - Optional
      - NPE 처리에 효과적
   - 날짜 관련 클래스 추가
   - String 메서드 추가
- Java 11
   - var
   - HTTP Client
   - 모듈
   - 프로파일링 및 진단
   - 가비지 수집
      - Java 11의 기본 GC는 G1GC(가비지 우선 가비지 수집기)
      - CMS(Concurrent Mark and Sweep) GC는 사용할 수 있지만 Java 9 이후 사용X
   - 성능 향상
- Java 17
   - 텍스트 블록
   - record
   - sealed class
   - instance of 변수
   - 스위치 문법 람다