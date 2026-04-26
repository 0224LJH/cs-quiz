# 자바, 스프링, JPA 관련

# 자바

- 클래스와 객체의 차이는 무엇인가요?
    
    <aside>
    
    클래스는 객체를 만들기 위한 설계도이고, 객체는 그 설계도를 기반으로 메모리에 실제로 생성된 실체입니다.
    
    예를 들어 User라는 클래스가 있다면, `new User()`로 생성한 각각의 인스턴스가 객체가 되는 거죠. 같은 클래스로 여러 객체를 만들 수 있고, 각 객체는 힙 메모리에 독립적으로 할당됩니다.
    
    </aside>
    
- Java는 인터프리터 언어인가요? 아니면 컴파일 언어인가요? 어떻게 구동되나요?
    
     Java는 "플랫폼 독립적"이고, JVM은 "플랫폼 종속적
    
    <aside>
    
    “Java는 전통적인 의미의 순수 인터프리터 언어도 아니고, C처럼 완전히 기계어로만 컴파일되는 언어도 아닙니다.
    
    먼저 `javac`가 `*.java` 소스 코드를 **플랫폼에 독립적인 바이트코드**인 `*.class` 파일로 컴파일합니다. 이 바이트코드는 실제 CPU 명령어가 아니라 **JVM이 이해하는 가상 명령어 집합**입니다.
    
    실행할 때는 `java` 명령으로 **HotSpot JVM** 같은 구현체가 올라오고, 이 JVM이 `*.class`에 있는 바이트코드를 읽어 실행합니다. HotSpot JVM은 처음에는 바이트코드를 **인터프리터로 한 줄씩 실행하면서 프로파일링**을 하고, 자주 호출되는 메서드나 루프 같은 ‘핫스팟’ 코드를 발견하면 그 부분을 **JIT(Just-In-Time) 컴파일러로 네이티브 기계어로 변환해서 캐시**합니다. 이후에는 해당 부분을 거의 C 코드처럼 **직접 기계어로 실행**하기 때문에 성능이 많이 올라갑니다.
    
    그래서 Java는 소스 단계에서는 **바이트코드로 컴파일**되고, 실행 단계에서는 JVM 위에서 **인터프리트 + JIT 컴파일**이 함께 동작하는 **하이브리드 방식으로 구동되는 언어**라고 이해하고 있습니다.”
    
    </aside>
    

- JVM 메모리 구조와, OS에서 배우는 프로세스 메모리 구조는 어떻게 다른가요?
    
    <aside>
    
    java를 실행하면 **OS 입장에서는 JVM도 그냥 하나의 네이티브 프로세스**입니다. 
    
    그 프로세스의 메모리 공간 안에서 JVM이 다시 자기만의 구조를 정의하는데, 여기서 Method Area와 Heap은 스레드들이 함께 공유하는 영역, Stack, PC Register, Native Method Stack은 각 스레드별로 따로 가지는 영역으로 나뉩니다. 
    
    그래서 큰 패턴만 보면 OS에서 배우는 “공유 힙 + 스레드별 스택” 구조와 상당히 비슷하지만, 중요한 차이는 Method Area나 Java Heap이 OS 레벨의 세그먼트가 아니라, JVM이 그 프로세스 안에서 소프트웨어적으로 논리 구역을 나눠서 관리하는 것이라는 점입니다.
    
    </aside>
    
- JVM의 메모리 구조에 대해 설명해주세요
    
    <aside>
    
    JVM의 Runtime Data Area는 크게 **Method Area, Heap, Stack, PC Register, Native Method Stack**으로 나뉩니다.
    
    **Method Area**는 클래스 정보와 static 변수가 저장되고, **Heap**은 new로 생성한 객체들이 저장됩니다. 이 두 영역은 모든 스레드가 공유합니다.
    
    **Stack**은 메서드 호출 시 지역변수와 매개변수가 저장되는데, 각 스레드마다 독립적으로 가지고 있습니다. 메서드가 끝나면 자동으로 제거되죠.
    
    실무에서 중요한 건 **Stack에는 참조변수(주소값)가, Heap에는 실제 객체가 저장된다**는 점입니다.
    
    </aside>
    
- JVM구조에서 Stack과 Heap의 차이는 무엇인가요?
    
    <aside>
    
    Stack은 컴파일 시점에 크기가 결정되고, 메서드 호출과 함께 생성되었다가 자동으로 사라집니다. 스레드마다 독립적이고요.
    
    반면 Heap은 런타임에 동적으로 할당되고, 모든 스레드가 공유하며, GC의 관리 대상입니다.
    
    정리하면 **Stack은 빠르지만 크기 제한이 있고, Heap은 크지만 GC 오버헤드**가 있습니다.
    
    </aside>
    
- JVM의 PC Registere와, OS에서의 PC를 비교설명해주세요,
    
    <aside>
    
    **JVM의 PC Register는 “각 Java 스레드가 현재 실행 중인 *바이트코드 위치*”를 나타내는 논리적인 카운터이고,**
    
    **OS/CPU의 PC(Program Counter)는 “현재 실행 중인 *기계어 명령의 메모리 주소*”를 가리키는 하드웨어 레지스터입니다.**
    
    둘 다 “다음에 실행할 명령이 어디냐”를 나타낸다는 공통점이 있지만,
    
    OS의 PC는 **CPU 레벨에서 네이티브 코드 주소**를 가리키고,
    
    JVM의 PC Register는 **JVM 내부에서 Java 바이트코드 오프셋**을 가리킨다는 점에서
    
    레벨(하드웨어 vs 가상 머신)이 다르다고 정리할 수 있습니다.
    
    </aside>
    

- GC는 무엇인가요?
    
    <aside>
    
    Garbage Collection은 **자바가 메모리를 자동으로 관리하는 메커니즘**입니다. Heap 영역에서 더 이상 사용되지 않는 객체를 자동으로 찾아서 제거해줍니다.
    
    왜 필요하냐면, C/C++처럼 개발자가 직접 malloc/free를 관리하면 실수로 메모리 누수나 댕글링 포인터 같은 문제가 발생하기 쉽습니다. GC는 이런 문제를 자동으로 해결해서 개발자가 비즈니스 로직에만 집중할 수 있게 해주죠.
    
    다만 GC가 실행될 때 **Stop-The-World**가 발생해서 애플리케이션이 잠깐 멈추는 단점이 있습니다. 그래서 실무에서는 GC 튜닝이 중요합니다.
    
    </aside>
    
- GC는 어떤 객체를 수거 대상으로 판단하나요?
    
    <aside>
    
    **Reachability**라는 개념으로 판단합니다. 쉽게 말하면 **루트에서 시작해서 참조 체인을 따라갈 수 없는 객체**가 GC 대상입니다.
    
    루트는 다음 세 가지입니다.
    
    - **Stack의 지역변수**
    - **Method Area의 static 변수**
    - **JNI로 생성된 객체**
    
    예를 들어, 메서드가 끝나면 지역변수가 사라지면서 그 변수가 참조하던 객체는 도달 불가능해집니다. 또는 `obj = null`처럼 명시적으로 참조를 끊어도 도달 불가능해지죠.
    
    흥미로운 건 **순환 참조**도 GC 대상이라는 겁니다. A가 B를 참조하고 B가 A를 참조하더라도, 루트에서 도달할 수 없으면 둘 다 제거됩니다. 자바는 Reference Counting이 아니라 Reachability 방식이라서 가능한 거죠
    
    </aside>
    
- JNI란?
    
    <aside>
    
    JNI는 Java와 C/C++ 같은 네이티브 코드를 연결해주는 인터페이스입니다.
    
    네이티브 코드에서 JNI를 통해 Java 객체를 참조하면, 그 참조도 GC Root로 취급돼서 GC에서 수거되지 않습니다
    
    </aside>
    
- Stop-The-World가 무엇이고, 왜 발생하나요?
    
    <aside>
    
    Stop-The-World는 **GC 실행을 위해 모든 애플리케이션 스레드가 일시 중지되는 현상**입니다. 줄여서 STW라고 합니다.
    
    왜 필요하냐면, GC가 실행되는 동안 객체의 참조 관계가 바뀌면 정확하게 청소할 수 없기 때문입니다. 마치 방 청소할 때 사람들이 계속 돌아다니면 청소가 안 되는 것과 비슷하죠.
    
    문제는 **STW 시간이 길어지면 애플리케이션 응답 시간이 느려진다**는 겁니다. 예를 들어 STW가 1초면, 사용자는 1초 동안 아무 응답도 못 받습니다.
    
    최신 GC의 경우  STW를 10ms 이하 수준으로 줄였다고 알고 있습니다.
    
    </aside>
    
- 메모리 누수가 발생하는 경우는?
    
    <aside>
    
    자바는 GC가 있지만 메모리 누수는 여전히 발생할 수 있습니다.
    
    대표적인 예가 **static 컬렉션에 계속 객체를 추가**하는 경우입니다. static은 GC 대상이 아니라서 프로그램이 끝날 때까지 메모리에 남죠.
    
    두 번째는 **이벤트 리스너나 콜백을 등록하고 해제 안 하는 경우**입니다. 리스너가 객체를 참조하고 있으면 GC가 못 지웁니다.
    
    세 번째는 **커넥션이나 스트림을 close() 안 하는 경우**입니다. try-with-resources를 사용하면 이 문제를 방지할 수 있습니다.
    
    실무에서는 Heap dump를 분석하거나 VisualVM 같은 프로파일러로 메모리 사용량을 모니터링합니다.
    
    </aside>
    

- 기본형과 참조형의 차이는?
    
    <aside>
    
    기본형은 시작이 소문자인 8개의 타입으로, stack에 실제 값이 저장되며, 변수를 복사하면 값 자체가 복사됩니다.
    
    참조형은 시작이 클래스,배열,인터페이스 등으로, Stack에 주소값만 저장되고, 실제 객체는 Heap에 있습니다. 복사할 경우 주소가 복사되어 같은 객체를 참조하게 됩니다.
    
    중요한 점은 자바는 항상 Call By Value라는 점입니다. 참조형도 주소값을 복사하는 것이지, 참조 자체를 전달하는 것이 아닙니다.
    
    </aside>
    
- String은 왜 특별한가요?
    
    <aside>
    
    첫째, **불변(Immutable)** 객체라서 한 번 생성되면 내용을 바꿀 수 없습니다. 수정하면 새 객체가 생성되죠.
    
    둘째, **String Pool**이라는 특수한 메모리 영역을 사용합니다. 리터럴로 생성하면 같은 값은 같은 주소를 참조해서 메모리를 절약합니다.
    
    셋째, 불변이라서 **멀티스레드 환경에서 안전**하고, HashMap의 키로 사용하기에도 적합합니다.
    
    실무에서는 문자열 연결이 많을 때 StringBuilder를 사용하는데, String은 매번 새 객체를 만들어서 비효율적이기 때문입니다.
    
    </aside>
    
- String을 + 연산자로 계속 연결하면 왜 성능이 안 좋나요?
    
    <aside>
    
    String은 불변 객체라서 + 연산을 할 때마다 **새로운 String 객체가 생성**되기 때문입니다.
    
    예를 들어 루프에서 100번 문자열을 연결하면 100개의 임시 객체가 생성되고, 이게 모두 GC 대상이 됩니다. 시간복잡도도 O(n²)이 되죠.
    
    반면 **StringBuilder**는 가변 객체라서 내부 버퍼에 문자를 추가만 하고, 필요할 때 한 번에 String으로 변환합니다. O(n)의 시간복잡도에 객체 생성도 최소화되죠.
    
    실무에서는 반복문 안에서 문자열 연결할 때는 무조건 StringBuilder를, 멀티스레드 환경에서는 StringBuffer를 사용합니다.
    
    </aside>
    
- ==과 equals()의 차이는?
    
    <aside>
    
    `==`는 **주소값 비교**이고, `equals()`는 **내용 비교**입니다.
    그렇기에 객체 비교를 할 때는 항상 equals를 사용해야합니다. 특히 String이나 사용자 정의 객체를 비교할 때 주의해야합니다.
    
    </aside>
    

- static은 언제 사용하며, 단점은 무엇인가요?
    
    <aside>
    
    static은 **클래스 레벨에서 공유**해야 할 때 사용합니다. 객체 생성 없이 `클래스명.메서드()`로 바로 호출할 수 있고, 모든 인스턴스가 같은 값을 공유하죠.
    
    주로 유틸 클래스나 싱글톤 패턴에서 사용합니다. 예를 들어 `Math.max()` 같은 경우요.
    
    단점은 세 가지입니다. 첫째, **GC 대상이 아니라** 프로그램 종료까지 메모리에 남아서 메모리 낭비가 생길 수 있습니다. 둘째, 멀티스레드 환경에서 동시성 이슈가 있고, 셋째, 테스트하기 어렵습니다. 전역 상태라서요.
    
    </aside>
    
- final 키워드는 어떻게 사용하나요?
    
    <aside>
    
    final은 위치에 따라 의미가 다릅니다.
    
    **변수**에 붙으면 재할당이 불가능한 상수가 되고, **메서드**에 붙으면 자식 클래스에서 오버라이딩을 못하고, **클래스**에 붙으면 상속 자체가 불가능합니다.
    
    실무에서는 **불변성을 보장**하기 위해 많이 씁니다. 특히 Spring에서 생성자 주입할 때 필드를 final로 선언하면 불변 객체가 되어 안전하죠.
    
    주의할 점은 final이 참조형 변수에 붙으면 주소는 못 바꾸지만, 객체 내부의 값은 바꿀 수 있다는 겁니다
    
    </aside>
    
- 접근 제어자 4가지를 설명해주세요.
    
    <aside>
    
    **private**는 같은 클래스 내에서만, **default**는 같은 패키지 내에서만, **protected**는 같은 패키지 + 다른 패키지의 자식 클래스에서, **public**은 어디서든 접근 가능합니다.
    
    실무에서는 **캡슐화를 위해** 필드는 기본적으로 private으로 두고 getter/setter로 접근합니다. 이렇게 하면 나중에 검증 로직을 추가하거나 내부 구현을 바꾸기 쉽죠.
    
    생성자를 private으로 만들면 외부에서 객체 생성을 막을 수 있어서 싱글톤 패턴이나 정적 팩토리 메서드 패턴에서 사용합니다
    
    </aside>
    
- Checked Exception과 Uncheked Exception의 차이는?
    
    <aside>
    
    **Checked Exception**은 컴파일 시점에 체크되어서 반드시 try-catch나 throws로 처리해야 합니다. IOException, SQLException 같은 것들이죠. **복구 가능한 상황**에 사용합니다.
    
    **Unchecked Exception**은 RuntimeException을 상속해서 처리가 선택적입니다. NullPointerException, IllegalArgumentException 같은 것들로, **프로그래밍 오류**를 나타냅니다.
    
    실무에서는 비즈니스 예외를 만들 때 복구 가능하면 Checked, 프로그래밍 오류면 Unchecked로 만듭니다. 하지만 요즘은 Unchecked를 선호하는 경향이 있는데, API 사용자가 예외 처리를 강제받지 않아서 편하기 때문입니다.
    
    Spring의 @Transactional도 기본적으로 Unchecked Exception에만 롤백되니까 주의해야 합니다.
    
    </aside>
    
- try-with-resources란?
    
    <aside>
    
     Java 7에서 추가된 문법으로, **AutoCloseable을 구현한 자원을 자동으로 닫아주는** 기능입니다.
    
    장점은 세 가지입니다. 
    
    첫째, finally 블록 없이도 자원을 해제할 수 있어서 코드가 간결해집니다. 
    
    둘째, 예외가 발생해도 자원이 확실히 닫힙니다. 
    
    셋째, 예외 억제(suppressed exception) 메커니즘으로 원본 예외를 잃어버리지 않습니다.
    
    실무에서 JDBC Connection, Stream, Scanner 등을 사용할 때 필수로 써야 합니다.
    
    </aside>
    
- PriorityQueue의 내부 구조는?
    
    <aside>
    
    배열 기반의 **이진힙 (binary heap)구조.**
    
    i번 노드의 부모노드는 i/2번 노드. 왼쪽/오른쪽 자식 노드는 2*i / 2*i+1 번 노드.
    
    </aside>
    

- 제네릭(Generic)은 왜 사용하나요?
    
    <aside>
    
    제네릭은 **컴파일 타임에 타입 안정성을 보장**하기 위해 사용합니다.
    
    제네릭 없이 List를 쓰면 Object를 저장하고 꺼낼 때마다 형변환해야 하는데, 이러면 런타임에 ClassCastException이 발생할 수 있습니다. 제네릭을 쓰면 컴파일 시점에 타입을 체크해서 이런 오류를 방지하죠.
    
    한 가지 주의할 점은 **타입 소거(Type Erasure)** 때문에 런타임에는 제네릭 정보가 사라진다는 겁니다. 하위 호환성을 위해서인데, 그래서 `new T[]` 같은 건 할 수 없습니다.
    
    실무에서는 컬렉션뿐만 아니라 DAO나 서비스 레이어에서도 제네릭을 많이 사용해서 코드 재사용성을 높입니다
    
    </aside>
    
- <?>와 <T>에 대해 설명해주세요
    
    <aside>
    
    <?>은 어떤 타입인지는 모르겠지만, 하나의 타입이 올 수 있다는 뜻의 와일드 카드입니다.
    
    - `<T>`
        - **내가 정의하는 쪽**에서 타입 파라미터를 선언하는 것
        - 클래스나 메서드를 제네릭으로 만들 때:
            
            ```java
            class Box<T> { ... }
            <T> void print(T value) { ... }
            
            ```
            
        - 내부에서 `T`를 **이름 붙여서 적극적으로 쓰고 싶을 때** 사용
    - `<?>`
        - **이미 만들어진 제네릭 타입을 받아 쓸 때**,
            
            “여기 타입이 뭐인지는 중요하지 않고, **그냥 아무거나**”라는 의미
            
            ```java
            void printList(List<?> list) { ... }
            
            ```
            
    </aside>
    
- 람다와 스트림은 무엇인가요?
    
    <aside>
    
    람다(Lambda)는 **익명 함수를 간결하게 표현**하는 방법입니다. 기존에는 인터페이스 구현을 위해 익명 클래스를 만들어야 했는데, 람다를 사용하면 훨씬 간단해집니다.
    
    ```java
    // 기존 방식
    List<String> names = Arrays.asList("김철수", "이영희", "박민수");
    Collections.sort(names, new Comparator<String>() {
        public int compare(String a, String b) {
            return a.compareTo(b);
        }
    });
    
    // 람다 사용
    Collections.sort(names, (a, b) -> a.compareTo(b));
    ```
    
    스트림은 컬렉션 데이터를 선언적으로 처리하는 API입니다. 데이터의 흐름을 파이프라인처럼 연결해서 처리할 수 있습니다.
    
    ```java
    List<String> result = names.stream()
        .filter(name -> name.startsWith("김"))
        .map(String::toUpperCase)
        .collect(Collectors.toList());
    ```
    
    핵심은 **람다는 "어떻게"를 간결하게 표현하는 문법**이고, **스트림은 "무엇을"을 선언하는 API**라는 점입니다.
    
    </aside>
    
- 람다와 스트림의 장단점은?
    
    <aside>
    
    **람다**는 함수형 인터페이스를 간결하게 구현할 수 있어서 코드가 깔끔해집니다. 특히 컬렉션 처리할 때 가독성이 좋아지죠.
    
    **스트림**은 선언적으로 데이터를 처리할 수 있고, 중간 연산이 lazy하게 동작해서 필요한 만큼만 실행됩니다. parallelStream()으로 병렬 처리도 쉽게 할 수 있고요.
    
    하지만 단점도 있습니다. **작은 데이터셋에서는 for문이 더 빠를 수 있고**, 병렬 스트림은 오버헤드가 있어서 항상 빠른 건 아닙니다. 디버깅도 어렵고, 스트림은 일회용이라 재사용이 안 됩니다.
    
    실무에서는 **가독성과 성능을 고려해서** 적절히 선택하는 게 중요합니다. CPU-intensive한 작업에 큰 데이터셋이면 병렬 스트림이 효과적이지만, I/O 작업이나 작은 데이터는 일반 반복문이 나을 수 있습니다.
    
    </aside>
    
- 함수형 인터페이스는 무엇인가요?
    
    <aside>
    
    함수형 인터페이스는 **추상 메서드가 딱 하나만 있는 인터페이스**입니다. 람다 표현식의 타입으로 사용되죠.
    
    **@FunctionalInterface** 어노테이션을 붙여서 명시적으로 표현할 수 있고, 이렇게 하면 컴파일러가 추상 메서드가 하나만 있는지 체크해줍니다.
    
    대표적인 함수형 인터페이스는 다음과 같습니다.
    
    ![image.png](%EC%9E%90%EB%B0%94,%20%EC%8A%A4%ED%94%84%EB%A7%81,%20JPA%20%EA%B4%80%EB%A0%A8/image.png)
    
    주의할 점은 **default 메서드나 static 메서드는 개수 제한이 없다**는 겁니다. 추상 메서드만 하나면 됩니다.
    
    </aside>
    
- 스트림 중간 연산과 최종 연산의 차이는?
    
    <aside>
    
    중간 연산(Intermediate Operation)은 스트림을 반환해서 **체이닝이 가능**하고, **지연 평가(Lazy Evaluation)** 방식으로 동작합니다. 최종 연산이 호출되기 전까지는 실제로 실행되지 않죠.
    
    대표적인 중간 연산:
    
    - **filter()**: 조건에 맞는 요소만 선택
    - **map()**: 요소를 다른 형태로 변환
    - **sorted()**: 정렬
    - **distinct()**: 중복 제거
    - **limit()**, **skip()**: 개수 제한
    - 최종 연산(Terminal Operation)은 실제로 **결과를 생성**하고, 스트림을 소비해서 **재사용이 불가능**합니다.
    
    대표적인 최종 연산:
    
    - **collect()**: 리스트, 맵 등으로 수집
    - **forEach()**: 각 요소에 작업 수행
    - **count()**, **sum()**, **average()**: 집계
    - **anyMatch()**, **allMatch()**, **noneMatch()**: 조건 검사
    - **findFirst()**, **findAny()**: 요소 찾기
    
    **실무 예시**:
    
    ```java
    list.stream()
        .filter(x -> x > 10)    *// 중간: 아직 실행 안 됨*
        .map(x -> x * 2)        *// 중간: 아직 실행 안 됨*
        .collect(Collectors.toList());  *// 최종: 이제 실행!*
    ```
    
    이런 지연 평가 덕분에 불필요한 연산을 줄일 수 있습니다. 예를 들어 `findFirst()`로 첫 번째 요소만 찾으면, 그 이후 요소는 처리하지 않죠.
    
    </aside>
    
- 병렬 스트림은 언제 사용하나요?
    
    <aside>
    
    병렬 스트림은 `parallelStream()`으로 만들 수 있고, **멀티코어 CPU를 활용해서 작업을 병렬 처리**합니다. 내부적으로 Fork/Join 프레임워크를 사용하죠.
    
    **사용하면 좋은 경우**:
    
    1. **데이터가 충분히 많을 때** (최소 수천 개 이상)
    2. **각 요소의 처리가 독립적일 때** (순서가 중요하지 않음)
    3. **CPU-intensive한 작업일 때** (계산이 복잡함)
    
    **사용하면 안 좋은 경우**:
    
    1. **데이터가 적을 때**: 오버헤드가 더 큼
    2. **I/O 작업이 많을 때**: 스레드 블로킹으로 효율 저하
    3. **순서가 중요할 때**: 결과가 매번 다를 수 있음
    4. **상태를 공유할 때**: 동시성 이슈 발생
    
    **실무 경험**:
    이전 프로젝트에서 수백만 건의 로그 데이터를 분석할 때 병렬 스트림을 사용했습니다. 각 로그는 독립적으로 처리 가능했고, CPU 연산이 많아서 순차 처리 대비 3~4배 빨라졌습니다.
    
    하지만 작은 데이터셋(1000건 미만)에서는 오히려 느려져서, **항상 성능 테스트를 해보고 결정**하는 게 중요합니다.
    
    </aside>
    
- 람다에서 외부 변수를 사용할 때 주의할 점은?
    
    <aside>
    
    람다는 **effectively final** 변수만 사용할 수 있습니다. 명시적으로 final을 붙이지 않아도, 값이 변경되지 않으면 사용 가능합니다.
    
    **왜 이런 제약이 있을까요?**
    
    람다는 **외부 변수를 캡처(capture)할 때 값을 복사**합니다. 만약 값이 변경된다면 람다 내부와 외부의 값이 달라져서 혼란스럽죠. 특히 병렬 스트림에서는 동시성 문제가 발생할 수 있습니다.
    
    실무에서는 가능한 **스트림 API의 집계 메서드를 사용**하는 게 안전하고 명확합니다.
    
    </aside>
    
- Thread-Safe란? 이를 만들려면?
    
    <aside>
    
    자바에서 스레드 세이프란 여러 스레드가 동시에 접근해도 객체의 `불변 조건(invariant)`이 깨지지 않도록 불변 객체, 공유 제한, 동기화, 동시성 컬렉션·원자 클래스를 적절히 조합해서 **공유 가변 상태를 안전하게 관리하는 것**을 의미합니다.
    
    스레드 세이프하게 만들려면 먼저 **여러 스레드가 공유하는 가변 상태를 최대한 줄이는 것**부터 시작합니다. 가능한 경우에는 객체를 **불변으로 만들거나, 스레드 내부에만 국한해서 공유 자체를 없애고요.
    
    공유하면서 수정까지 필요하다면, `synchronized`나 `ReentrantLock`으로 **복합 연산 구간을 원자적으로 보호**하고, 가시성이 중요할 땐 `volatile`이나 `AtomicInteger` 같은 원자 클래스, `ConcurrentHashMap` 같은 동시성 컬렉션을 사용해서 이미 검증된 패턴을 재사용합니다.
    
    결국 핵심은 **공유 가변 상태를 최소화하고, 불가피할 때만 락과 동시성 유틸로 일관성 있는 방식으로 보호하는 설계**라고 생각합니다
    
    </aside>
    

- Future와 CompletableFuture는 무엇인가요?
    
    <aside>
    
    둘 다 **비동기 작업의 결과를 나타내는 객체**인데, CompletableFuture가 훨씬 강력합니다.
    
    **Future**는 Java 5부터 있던 인터페이스로, **비동기 작업의 완료를 기다리고 결과를 받을 수 있습니다**.
    
    ```java
    ExecutorService executor = Executors.newSingleThreadExecutor();
    Future<String> future = executor.submit(() -> {
        Thread.sleep(1000);
        return "결과";
    });
    
    String result = future.get();  *// 블로킹! 결과 나올 때까지 대기*`
    ```
    
    **Future의 문제점**:
    
    1. **get()이 블로킹**: 결과를 기다리는 동안 스레드가 멈춤
    2. **콜백 불가능**: 완료되면 알림 받을 방법이 없음
    3. **조합 불가능**: 여러 Future를 연결할 수 없음
    4. **예외 처리 어려움**: 예외를 다루기 복잡함
    
    **CompletableFuture**는 Java 8에서 추가되어 이런 문제들을 해결했습니다.
    
    ```java
    CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
        *// 비동기 작업*
        return "결과";
    });
    
    future.thenApply(result -> result.toUpperCase())  *// 변환*
          .thenAccept(System.out::println)            *// 소비*
          .exceptionally(ex -> {                       *// 예외 처리*
              System.err.println("에러: " + ex);
              return null;
          });
    ```
    
    핵심은 **Future는 단순한 비동기 결과 컨테이너**이고, **CompletableFuture는 비동기 작업을 조합하고 연결하는 도구**라는 점입니다.
    
    </aside>
    

- Java의 Call By Value를 설명해주세요
    
    <aside>
    
    자바는 **항상 Call by Value**입니다. 헷갈리는 이유는 참조형 때문인데, 참조형도 사실은 주소값을 복사하는 거라서 Call by Value입니다.
    
    기본형은 값 자체가 복사되어서 메서드 안에서 바꿔도 원본에 영향이 없습니다.
    
    참조형은 주소값이 복사되는데, 그 주소로 접근해서 객체 내부를 바꾸면 원본도 바뀝니다. 하지만 참조 자체를 바꾸면 원본에는 영향이 없죠.
    
    </aside>
    
- Spring에서 싱글톤 빈이 thread-safe인가요?
    
    <aside>
    
    싱글톤 빈 자체는 **thread-safe하지 않습니다**. 왜냐하면 모든 스레드가 같은 인스턴스를 공유하기 때문이죠.
    
    만약 빈이 **상태(필드)를 가지고 있으면** 멀티스레드 환경에서 문제가 됩니다. 예를 들어 카운터 변수 같은 걸 필드로 두면 여러 스레드가 동시에 변경해서 값이 꼬일 수 있습니다.
    
    그래서 Spring 빈은 **Stateless로 설계**하는 게 원칙입니다. 상태를 가져야 하면 ThreadLocal을 쓰거나, 메서드 파라미터로 전달하거나, Prototype 스코프를 사용해야 합니다.
    
    실무에서는 Service, Repository 같은 빈들은 로직만 있고 상태가 없어서 문제없지만, 만약 상태를 가진 빈을 만들어야 하면 동시성 처리를 해야 합니다.
    
    </aside>
    

---

# 스프링

- IoC(Inversion of Control, 제어의 역전)이 무엇이고, 왜 필요한가요?
    
    <aside>
    
    IoC는 객체의 생성과 생명주기 관리를 개발자가 아닌 프레임워크(스프링 컨테이너)가 담당하는 것을 의미합니다. 전통적인 프로그래밍에서는 개발자가 직접 객체를 생성하고 관리했지만, IoC에서는 이 제어권이 컨테이너로 역전됩니다.
    
    필요한 이유는 객체 간의 결합도를 낮추고, 코드의 재사용성과 테스트 용이성을 높이기 위해서입니다.
    
    </aside>
    
- IoC 컨테이너의 역할은 구체적으로 무엇인가요?
    
    <aside>
    
    IoC 컨테이너는 빈(Bean) 객체의 생성, 초기화, 의존관계 주입, 소멸까지의 전체 생명주기를 관리합니다. ApplicationContext나 BeanFactory가 대표적인 IoC 컨테이너입니다. 컨테이너는 설정 메타데이터(XML, 어노테이션, Java Config)를 읽어 빈을 생성하고 관리합니다
    
    </aside>
    
- DI가 무엇이며, 어떤 방식들이 존재하나요??
    
    <aside>
    
    DI는 객체가 필요로 하는 의존객체를 외부에서 주입받는 것을 말합니다. 메인 모듈이 직접 하위 모듈을 생성하지 않고, 중간에 의존성 주입자(Dependency Injector)가 간접적으로 의존성을 주입합니다.
    
    주입 방식은 크게 세 가지 존재합니다.
    
    - 생성자를 통해 의존성을 주입받는 생성자 주입.
    - setter 메서드를 통해 주입받는 세터 주입
    - @Autowired를 필드에 직접 적용하는 필드주입.
    
    이 중 생성자 주입이 가장 권장됩니다.
    
    </aside>
    
- 왜 생성자 주입이 권장되나요?
    
    <aside>
    
    - **불변성 보장**: final 키워드를 사용해 의존객체를 불변으로 만들 수 있습니다
    - **순환 참조 방지**: 컴파일 시점이나 애플리케이션 로딩 시점에 순환 참조를 감지할 수 있습니다
    - **테스트 용이성**: 순수 자바 코드로 테스트가 가능하며, 컨테이너 없이도 객체 생성이 가능합니다
    - **NPE 방지**: 객체 생성 시점에 모든 의존성이 주입되므로 NullPointerException을 방지할 수 있습니다
    </aside>
    
- 필드 주입의 문제점은?
    
    <aside>
    
    필드 주입은 간편하지만 여러 문제가 있습니다. 테스트 시 스프링 컨테이너가 필요하고, 불변 객체를 만들 수 없으며, 순환 참조가 런타임에만 발견됩니다. 또한 DI 프레임워크에 강하게 결합되어 순수 자바 코드로 테스트하기 어렵습니다.
    
    </aside>
    
- DIP( Dependency Inversion Principle, 의존관계 역전 원칙)이 무엇이고, DI와 어떤 관계?
    
    <aside>
    
    DIP는 SOLID 원칙 중 하나로, 두 가지 규칙을 지키는 상태를 말합니다:
    
    1. 상위 모듈은 하위 모듈에 의존해서는 안 됩니다. 둘 다 추상화에 의존해야 합니다
    2. 추상화는 세부사항에 의존해서는 안 됩니다. 세부 사항은 추상화에 따라 달라져야 합니다
    
    DI를 올바르게 적용하면 자연스럽게 DIP를 준수하게 됩니다. 구체적인 클래스가 아닌 인터페이스(추상화)에 의존하게 만드는 것이 핵심입니다.
    
    </aside>
    
- DIP를 준수하지 않은 DI도 있나요?
    
    <aside>
    
    네, 있습니다. 예를 들어 생성자를 통해 구체적인 클래스 인스턴스를 직접 주입받는 경우입니다. 이는 생성자 주입이라는 형태는 갖추고 있지만, 구체 클래스에 직접 의존하므로 DIP를 준수하지 않습니다. 진정한 DI는 인터페이스나 추상 클래스 같은 추상화에 의존해야 합니다
    
    </aside>
    

- 스프링 빈이란 무엇이고, 어떻게 등록하나요?
    
    <aside>
    
    스프링 빈은 스프링 IoC 컨테이너가 관리하는 자바 객체입니다. 일반적인 자바 객체(POJO)와 다르게 스프링 컨테이너에 의해 생성되고 관리됩니다.
    
    빈 등록 방법:
    
    - **어노테이션 기반**: @Component, @Service, @Repository, @Controller
    - **Java Config**: @Configuration과 @Bean 어노테이션 사용
    - **XML 설정**: <bean> 태그 사용 (레거시)
    </aside>
    
- 빈의 스코프(Scope)에는 어떤 것이 있나요?
    
    <aside>
    
    - **Singleton (기본)**: 스프링 컨테이너당 하나의 인스턴스만 생성됩니다
    - **Prototype**: 요청할 때마다 새로운 인스턴스를 생성합니다
    - **Request**: HTTP 요청당 하나의 인스턴스 (웹 환경)
    - **Session**: HTTP 세션당 하나의 인스턴스 (웹 환경)
    - **Application**: ServletContext당 하나의 인스턴스
    - **WebSocket**: WebSocket 세션당 하나의 인스턴스
    </aside>
    
- 싱글톤 빈 사용 시 주의할 점은?
    
    <aside>
    
    싱글톤 빈은 상태를 공유하므로 멀티스레드 환경에서 동시성 문제가 발생할 수 있습니다.
    
    따라서 싱글톤 빈은 무상태(Stateless)로 설계해야 하며, 공유 필드 사용을 지양하고 지역 변수나 메서드 파라미터를 활용해야 합니다. 
    
    상태 유지가 필요하면 ThreadLocal이나 프로토타입 스코프를 고려해야 합니다.
    
    </aside>
    

- AOP (Aspect-Oriented Programming, 관점 지향 프로그래밍)이란? 왜 사용하나요?
    
    <aside>
    
    AOP는 횡단 관심사(Cross-cutting Concerns)를 분리하여 모듈화하는 프로그래밍 기법입니다. 여러 모듈에서 공통적으로 나타나는 기능(로깅, 트랜잭션, 보안, 캐싱 등)을 핵심 비즈니스 로직에서 분리하여 관리합니다.
    
    사용 이유:
    
    - **코드 중복 제거**: 공통 기능을 한 곳에서 관리
    - **관심사 분리**: 비즈니스 로직과 부가 기능을 분리
    - **유지보수성 향상**: 부가 기능 수정 시 한 곳만 변경하면 됨
    </aside>
    
- AOP의 주요 용어들을 설명해주세요.
    
    <aside>
    
    - **Aspect**: 횡단 관심사를 모듈화한 것
    - **Join Point**: Aspect를 적용할 수 있는 지점 (메서드 실행, 예외 발생 등)
    - **Advice**: 실제로 실행되는 부가 기능 코드 (@Before, @After, @Around 등)
    - **Pointcut**: Join Point 중에서 Advice가 적용될 위치를 선별하는 표현식
    - **Target**: Advice가 적용되는 대상 객체
    - **Weaving**: Aspect와 Target을 연결하여 Proxy 객체를 생성하는 과정
    </aside>
    
- @Transactional은 AOP와 어떤 관계인가요?
    
    <aside>
    
    @Transactional은 AOP의 대표적인 활용 예시입니다. 스프링은 @Transactional이 붙은 메서드에 대해 AOP를 통해 프록시 객체를 생성하고, 메서드 실행 전후로 트랜잭션 시작/커밋/롤백 로직을 자동으로 적용합니다. 이를 통해 개발자는 비즈니스 로직에만 집중할 수 있습니다.
    
    </aside>
    

- Spring MVC의 동작과정을 설명해주세요.
    
    <aside>
    
    Spring MVC는 MVC 패턴을 웹 애플리케이션에 적용한 프레임워크입니다. DispatcherServlet이 Front Controller 역할을 수행하며 다음과 같이 동작합니다:
    
    1. 클라이언트 요청을 DispatcherServlet이 받습니다
    2. HandlerMapping을 참고하여 적절한 Controller를 찾습니다 (@RequestMapping 등 참고)
    3. Controller가 비즈니스 로직을 수행하고 Model을 생성합니다
    4. ViewResolver를 참고하여 적절한 View를 찾습니다
    5. View가 Model 데이터를 활용하여 렌더링합니다
    6. 최종 응답을 클라이언트에게 전송합니다
    </aside>
    
- DispatcherServlet이 Front Controller 패턴인 이유는?
    
    <aside>
    
     Front Controller 패턴은 모든 요청을 하나의 진입점에서 받아 처리하는 패턴입니다. DispatcherServlet은 모든 HTTP 요청을 중앙에서 받아 적절한 핸들러로 라우팅하고, 공통 처리(보안, 인코딩, 에러 처리 등)를 수행합니다. 이를 통해 각 Controller는 비즈니스 로직에만 집중할 수 있습니다.
    
    </aside>
    
- @RestController와 @Controller의 차이는?
    
    <aside>
    
    @Controller는 주로 View를 반환할 때 사용하며, 메서드의 반환값이 ViewResolver를 통해 뷰로 연결됩니다. @RestController는 @Controller + @ResponseBody로, 메서드의 반환값이 HTTP Response Body에 직접 작성됩니다. REST API 개발 시 JSON/XML 응답을 반환할 때 사용합니다.
    
    </aside>
    

- Spring Boot의 장점은?
    
    <aside>
    
    Spring Boot는 스프링 기반 애플리케이션 개발을 간소화한 프레임워크입니다:
    
    - **자동 설정(Auto Configuration)**: 일반적인 설정을 자동으로 구성해줍니다
    - **내장 서버**: Tomcat, Jetty 등을 내장하여 별도 서버 설치 불필요
    - **의존성 관리 간소화**: spring-boot-starter 패키지로 관련 라이브러리를 일괄 관리
    - **독립 실행 가능**: JAR 파일로 패키징하여 어디서든 실행 가능
    - **프로덕션 준비 기능**: Actuator를 통한 모니터링, 헬스체크 등 제공
    </aside>
    
- @SpringBootApplication 어노테이션은 무엇을 포함하나요?
    
    <aside>
    
    - **@SpringBootConfiguration**: 스프링 부트 설정을 나타냄
    - **@EnableAutoConfiguration**: 자동 설정 활성화
    - **@ComponentScan**: 현재 패키지와 하위 패키지에서 컴포넌트를 스캔
    </aside>
    

- PSA (Portable Service Abstraction)가 무엇인가요?
    
    <aside>
    
    PSA는 환경과 세부 기술의 변경에 관계없이 일관된 방식으로 기술에 접근할 수 있게 해주는 추상화 구조입니다. 스프링은 특정 기술에 종속되지 않도록 추상화 계층을 제공합니다.
    
    예시:
    
    - **트랜잭션**: @Transactional만 사용하면 JDBC, JPA, Hibernate 등 어떤 기술을 쓰든 동일하게 동작
    - **캐싱**: @Cacheable로 Redis, EhCache 등을 동일한 방식으로 사용
    - **메시징**: JMS, Kafka 등을 추상화하여 동일한 인터페이스로 접근
    </aside>
    
- PSA의 장점은 무엇인가요?
    
    <aside>
    
    기술 스택 변경 시 코드 수정을 최소화할 수 있습니다. 예를 들어 캐시를 EhCache에서 Redis로 변경해도 애플리케이션 코드는 변경하지 않고 설정만 바꾸면 됩니다. 이는 결합도를 낮추고 유연성을 높입니다.
    
    </aside>
    

- Filter, Interceptor, AOP의 차이점을 설명해주세요
    
    <aside>
    
    **Filter (서블릿 필터)**:
    
    - 서블릿 스펙에 정의된 기능으로, DispatcherServlet 이전에 동작
    - 웹 애플리케이션 전체에 적용 (스프링 컨텍스트 외부)
    - 인코딩, XSS 방어, 로깅 등에 사용
    - 실행 순서: 요청 → Filter → DispatcherServlet
    
    **Interceptor (스프링 인터셉터)**:
    
    - 스프링이 제공하는 기능으로, DispatcherServlet과 Controller 사이에서 동작
    - 스프링 빈 사용 가능
    - 인증/인가, 로깅, Controller 공통 처리에 사용
    - 실행 순서: DispatcherServlet → Interceptor → Controller
    
    **AOP**:
    
    - 메서드 단위로 동작하며, 가장 세밀한 제어 가능
    - 비즈니스 로직의 특정 지점에 적용
    - 트랜잭션, 캐싱, 메서드 실행 시간 측정 등에 사용
    
    ```java
    HTTP 요청
        ↓
    Filter (Servlet Filter)
        ↓
    Interceptor (preHandle)
        ↓
    AOP (Before Advice)
        ↓
    Controller Method 실행
        ↓
    AOP (After/AfterReturning Advice)
        ↓
    Interceptor (postHandle)
        ↓
    View Rendering
        ↓
    Interceptor (afterCompletion)
        ↓
    Filter
        ↓
    HTTP 응답
    ```
    
    </aside>
    
- 어떤 상황에 무엇을 사용해야 하나요?
    
    <aside>
    
    - **요청/응답 전체, 서블릿 단에서 처리해야 할 일**
        
        → **Filter**
        
    - **특정 컨트롤러 요청 전후에서만 처리하면 되는 일**
        
        → **Interceptor**
        
    - **웹 여부와 상관없이, 서비스·레포지토리까지 포함한 공통 메서드 로직**
        
        → **AOP**
        
    </aside>
    

# SOLID

- SRP(Single Responsibility Principle, 단일 책임 원칙)이 무엇이고, 왜 중요한가요?
    
    <aside>
    
    SRP는 하나의 클래스는 하나의 책임만 가져야 한다는 원칙입니다. 여기서 책임이란 "변경의 이유"를 의미합니다. 즉, 클래스가 변경되어야 하는 이유는 단 하나여야 합니다.
    
    예를 들어 사용자 관리 클래스가 있다면, 사용자 정보를 다루는 책임만 가져야 하고, 로깅이나 이메일 전송 같은 다른 책임은 분리해야 합니다.
    
    중요한 이유:
    
    - **유지보수성**: 한 가지 이유로만 변경되므로 수정 시 영향 범위가 제한됨
    - **가독성**: 클래스의 목적이 명확해짐
    - **재사용성**: 책임이 분리되어 있어 다른 곳에서도 사용 가능
    - **테스트 용이성**: 단일 책임만 테스트하면 되므로 테스트가 간단해짐
    </aside>
    
- 스프링에서 SRP는 어떻게 적용되나요?
    
    <aside>
    
    스프링은 계층화 아키텍처로 SRP를 자연스럽게 적용합니다:
    
    - **@Controller**: HTTP 요청/응답 처리 책임만
    - **@Service**: 비즈니스 로직 처리 책임만
    - **@Repository**: 데이터 접근 책임만
    - **@Component**: 특정 기능의 책임만
    
    각 계층이 자신의 책임에만 집중하도록 설계되어 있습니다.
    
    **연결 개념**: 관심사의 분리(Separation of Concerns), 응집도(Cohesion
    
    </aside>
    

- OCP (Open-Closed Principle, 개방-폐쇄 원칙)가 무엇이며, 어떻게 달성하나요?
    
    <aside>
    
    OCP는 소프트웨어 엔티티(클래스, 모듈, 함수 등)는 확장에는 열려 있어야 하고, 수정에는 닫혀 있어야 한다는 원칙입니다.
    
    즉, 기존 코드를 변경하지 않으면서도 새로운 기능을 추가할 수 있어야 합니다. 이는 주로 **추상화**와 **다형성**을 통해 달성합니다.
    
    달성 방법:
    
    - 인터페이스나 추상 클래스를 정의
    - 구체적인 구현은 하위 클래스에서 수행
    - 새로운 기능 추가 시 새로운 구현 클래스만 추가
    </aside>
    
- 스프링에서 OCP는 어떻게 나타나나요?
    
    <aside>
    
    1. **전략 패턴**: 프로젝트 자료에서 본 것처럼 PaymentStrategy 인터페이스와 다양한 구현체
    2. **템플릿 메서드 패턴**: JdbcTemplate 등에서 공통 로직은 그대로, 변하는 부분만 확장
    3. **AOP**: 기존 코드 수정 없이 횡단 관심사를 추가
    4. **PSA**: 추상화 계층을 통해 구현 기술 변경 가능 (예: EhCache → Redis로 변경해도 애플리케이션 코드는 그대로)
    
    **연결 개념**: 전략 패턴, 템플릿 메서드 패턴, 다형성, 추상화
    
    </aside>
    

- LSP (Liskov Substitution Principle, 리스코프 치환 원칙)가 무엇인가요?
    
    <aside>
    
    LSP는 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다는 원칙입니다.
    
    쉽게 말해, **부모 클래스 타입으로 동작하는 곳에 자식 클래스 인스턴스를 넣어도 프로그램이 정상적으로 동작해야 합니다**.
    
    핵심은:
    
    - 하위 타입은 상위 타입의 계약(contract)을 지켜야 함
    - 상위 타입에서 정의한 동작을 하위 타입이 깨트리면 안 됨
    - 클라이언트는 상위 타입만 알아도 하위 타입을 사용할 수 있어야 함
    </aside>
    
- 스프링에서 LSP는 어떻게 적용되나요?
    
    <aside>
    
    1. **인터페이스 기반 설계**: DataSource 인터페이스를 구현하는 모든 구현체(HikariCP, Apache DBCP 등)는 LSP를 준수하여 호환 가능
    2. **템플릿 콜백 패턴**: JdbcTemplate의 RowMapper 구현체들은 어떤 것을 사용해도 정상 동작
    3. **BeanPostProcessor**: 모든 구현체가 동일한 계약을 지킴
    
    **연결 개념**: 상속, 다형성, 인터페이스 설계, 계약에 의한 설계
    
    </aside>
    

- ISP (Interface Segregation Principle, 인터페이스 분리 원칙)가 무엇이며, 왜 필요한가요?
    
    <aside>
    
    ISP는 클라이언트는 자신이 사용하지 않는 메서드에 의존하지 않아야 한다는 원칙입니다. 즉, **범용 인터페이스 하나보다는 특정 클라이언트를 위한 여러 개의 인터페이스가 낫다**는 의미입니다.
    
    필요한 이유:
    
    - **불필요한 의존성 제거**: 사용하지 않는 메서드 변경에 영향받지 않음
    - **응집도 향상**: 관련된 기능만 모아놓음
    - **유연성**: 필요한 인터페이스만 구현 가능
    - **가독성**: 인터페이스의 목적이 명확해짐
    </aside>
    
- 스프링에서 ISP는 어떻게 나타나나요?
    
    <aside>
    
    1. **세분화된 인터페이스**: InitializingBean, DisposableBean 등 특정 생명주기만 필요한 경우 해당 인터페이스만 구현
    2. **Aware 인터페이스들**: ApplicationContextAware, BeanNameAware 등 필요한 것만 구현
    3. **JPA Repository**: CrudRepository → PagingAndSortingRepository → JpaRepository로 기능별 분리
    
    **연결 개념**: SRP, 응집도, 인터페이스 설계
    
    </aside>
    

- DIP (Dependency Inversion Principle, 의존관계역전원칙)가 무엇이고, DI와의 관계는?
    
    <aside>
    
    DIP는 두 가지 규칙을 지키는 상태를 말합니다:
    
    1. **상위 모듈은 하위 모듈에 의존해서는 안 됩니다. 둘 다 추상화에 의존해야 합니다**
    2. **추상화는 세부사항에 의존해서는 안 됩니다. 세부 사항은 추상화에 따라 달라져야 합니다**
    
    전통적인 의존 방향:
    
    `상위 모듈(비즈니스 로직) → 하위 모듈(구현체)`
    
    DIP 적용 후:
    
    `상위 모듈 → 추상화(인터페이스) ← 하위 모듈`
    
    의존성 주입(DI)을 올바르게 적용하면 자연스럽게 DIP가 준수됩니다.
    
    </aside>
    
- DIP가 "역전"이라는 이름이 붙은 이유는?
    
    <aside>
    
    전통적인 절차지향 프로그래밍에서는 상위 수준의 모듈이 하위 수준의 모듈에 의존했습니다(아래 방향으로 의존). 
    
    하지만 DIP에서는 이 의존 방향이 **역전**되어, 하위 모듈이 상위 모듈에서 정의한 추상화에 의존하게 됩니다(위 방향으로 의존).
    
    자료에 나온 것처럼, 의존적인 화살표가 "역전"된 것을 볼 수 있습니다.
    
    </aside>
    
- 스프링은 어떻게 DIP를 지원하나요?
    
    <aside>
    
    - **IoC 컨테이너**: 구체적인 객체 생성과 주입을 프레임워크가 담당
    - **인터페이스 기반 프로그래밍**: Service → Repository 인터페이스에 의존
    - **추상화된 API**: @Transactional, @Cacheable 등 추상화된 어노테이션 제공
    - **자동 의존성 해결**: @Autowired로 인터페이스 타입으로 주입받으면, 컨테이너가 적절한 구현체를 찾아서 주입
    </aside>
    
- SOLID원칙들이 서로 어떻게 연결되나요?
    
    <aside>
    
    A: SOLID 원칙들은 유기적으로 연결되어 있습니다:
    
    - **SRP + ISP**: 하나의 책임만 가지려면 인터페이스도 분리되어야 함
    - **OCP + DIP**: 확장에 열려있으려면 추상화에 의존해야 함
    - **LSP + OCP**: 치환 가능해야 확장이 안전하게 가능함
    - **DIP + DI**: 의존관계를 역전시키려면 의존성 주입이 필요함
    
    결국 모든 원칙은 **변경에 유연하고 확장 가능한 설계**를 만들기 위한 것입니다.
    
    **꼬리질문: 스프링이 SOLID를 잘 지키는 프레임워크인 이유는?**
    
    A:
    
    1. **계층화 아키텍처**: Controller-Service-Repository로 책임 분리 (SRP)
    2. **인터페이스 기반**: 추상화를 통한 확장성 (OCP, DIP)
    3. **템플릿/전략 패턴**: JdbcTemplate, RestTemplate 등 (OCP, LSP)
    4. **세분화된 인터페이스**: InitializingBean, Aware 계열 (ISP)
    5. **IoC/DI 컨테이너**: 의존관계 역전의 자동화 (DIP)
    
    **꼬리질문: 실무에서 SOLID를 지키기 어려운 경우는?**
    
    A:
    
    1. **과도한 추상화**: 간단한 기능에 너무 많은 인터페이스를 만들면 오히려 복잡도 증가
    2. **성능 요구사항**: 때로는 직접 결합이 성능상 유리할 수 있음
    3. **레거시 시스템**: 기존 코드를 리팩토링하기 어려운 경우
    4. **시간 압박**: 빠른 개발이 필요한 경우 원칙을 지키기 어려움
    
    중요한 것은 **원칙을 맹목적으로 따르기보다는, 각 원칙의 목적을 이해하고 상황에 맞게 적용하는 것**입니다.
    
    **연결 개념**: 디자인 패턴, 클린 아키텍처, 테스트 주도 개발(TDD)
    
    </aside>
    

# JPA

## 1. JPA와 ORM

- **JPA가 무엇이고, ORM은 무엇인가요?**
    
    <aside>
    
    JPA는 Java Persistence API의 약자로, 자바 애플리케이션에서 관계형 데이터베이스를 사용하는 방식을 정의한 인터페이스(명세)입니다. JPA 자체는 구현체가 아니라 표준 스펙입니다
    
    ORM(Object-Relational Mapping)은 객체와 관계형 데이터베이스의 테이블을 매핑하는 기술입니다. 객체 지향 프로그래밍의 객체와 관계형 데이터베이스의 테이블 간의 패러다임 불일치를 해결합니다.
    
    **프로젝트 자료 연결**: 자료에서 본 "엔터티"는 데이터베이스에서 실제 세계의 객체를 모델링한 것이며, JPA에서는 이 엔터티를 Java 클래스로 표현합니다.
    
    </aside>
    
- **JPA의 구현체는 무엇이 있나요?**
    
    <aside>
    
    - **Hibernate**: 가장 널리 사용되는 JPA 구현체
    - **EclipseLink**: 이클립스 재단의 구현체
    - **DataNucleus**: Apache 재단의 구현체
    
    실무에서는 Hibernate를 가장 많이 사용하며, Spring Data JPA는 JPA를 더 쉽게 사용하도록 추상화한 모듈입니다.
    
    </aside>
    
- **JPA를 사용하는 이유는 무엇인가요?**
    
    <aside>
    
    1. **생산성**: SQL을 직접 작성하지 않아도 CRUD 작업이 가능
    2. **유지보수성**: 필드 추가 시 SQL을 모두 수정할 필요 없음
    3. **패러다임 불일치 해결**: 상속, 연관관계, 객체 그래프 탐색 등을 자연스럽게 처리
    4. **데이터베이스 독립성**: 특정 DB에 종속되지 않음 (방언, Dialect 지원)
    5. **성능 최적화**: 1차 캐시, 지연 로딩, 쓰기 지연 등 다양한 최적화 기능 제공
    
    **연결 개념**: 데이터베이스 엔터티, 릴레이션, OOP
    
    </aside>
    

## 2. 영속성 컨텍스트 (Persistence Context)

- **영속성 컨텍스트가 무엇이고, 왜 중요한가요?**
    
    <aside>
    
    영속성 컨텍스트는 **엔티티를 영구 저장을 관리하는 환경**입니다. 애플리케이션과 데이터베이스 사이에서 엔티티를 보관하는 가상의 공간으로, EntityManager를 통해 접근합니다.
    
    중요한 이유:
    
    - **1차 캐시**: 같은 트랜잭션 내에서 같은 엔티티 조회 시 DB 접근 없이 캐시에서 반환
    - **동일성 보장**: 같은 엔티티는 항상 같은 인스턴스 (== 비교 가능)
    - **트랜잭션을 지원하는 쓰기 지연**: SQL을 모아서 한 번에 실행
    - **변경 감지(Dirty Checking)**: 엔티티 변경 시 자동으로 UPDATE 쿼리 생성
    - **지연 로딩**: 실제 사용 시점에 데이터 로딩
    </aside>
    
- **1차 캐시는 어떻게 동작하나요?**
    
    <aside>
    
    1차 캐시는 영속성 컨텍스트 내부에 있는 Map 구조입니다. Key는 @Id로 매핑한 식별자이고, Value는 엔티티 인스턴스입니다.
    
    ```
    // 첫 번째 조회: DB에서 조회 후 1차 캐시에 저장
    Member member1 = em.find(Member.class, 1L);
    
    // 두 번째 조회: 1차 캐시에서 반환, DB 조회 안 함
    Member member2 = em.find(Member.class, 1L);
    
    // member1 == member2 는 true (동일성 보장)
    
    ```
    
    단, 1차 캐시는 트랜잭션 단위로 생성되고 소멸되므로 애플리케이션 전체에서 공유되지 않습니다.
    
    </aside>
    
- **2차 캐시도 있나요?**
    
    <aside>
    
    네, 2차 캐시는 애플리케이션 범위의 캐시로, 여러 트랜잭션에서 공유됩니다. Hibernate의 경우 EhCache, Redis 등을 2차 캐시로 사용할 수 있습니다. 1차 캐시는 트랜잭션 단위, 2차 캐시는 애플리케이션 단위입니다.
    
    **연결 개념**: 트랜잭션, 캐시, 데이터베이스 접근
    
    </aside>
    

## 3. 엔티티 생명주기

- **JPA 엔티티의 생명주기를 설명해주세요.**
    
    <aside>
    
     JPA 엔티티는 4가지 상태를 가집니다:
    
    **1. 비영속 (New/Transient)**
    
    - 영속성 컨텍스트와 전혀 관계없는 새로운 상태
    - 단순히 객체를 생성한 상태
    
    ```
    Member member = new Member(); // 비영속 상태
    member.setName("회원1");
    
    ```
    
    **2. 영속 (Managed)**
    
    - 영속성 컨텍스트에 관리되는 상태
    - em.persist() 또는 em.find()로 조회한 엔티티
    
    ```
    em.persist(member); // 영속 상태
    
    ```
    
    **3. 준영속 (Detached)**
    
    - 영속성 컨텍스트에 저장되었다가 분리된 상태
    - em.detach(), em.clear(), em.close()로 전환
    
    ```
    em.detach(member); // 준영속 상태
    
    ```
    
    **4. 삭제 (Removed)**
    
    - 삭제된 상태
    
    ```
    em.remove(member); // 삭제 상태
    
    ```
    
    </aside>
    
- **준영속 상태의 엔티티를 다시 영속 상태로 만들 수 있나요?**
    
    <aside>
    
    네, em.merge(entity)를 사용하면 준영속 상태의 엔티티를 다시 영속 상태로 만들 수 있습니다. 하지만 merge()는 새로운 영속 엔티티를 반환하므로, 반환된 엔티티를 사용해야 합니다.
    
    ```
    Member mergedMember = em.merge(detachedMember);
    // mergedMember가 영속 상태, detachedMember는 여전히 준영속
    
    ```
    
    **연결 개념**: 영속성 컨텍스트, 트랜잭션
    
    </aside>
    

## 4. 즉시 로딩(EAGER)과 지연 로딩(LAZY)

- **Q: 즉시 로딩과 지연 로딩의 차이는 무엇인가요?**
    
    <aside>
    
    **즉시 로딩(EAGER Loading)**:
    
    - 엔티티를 조회할 때 연관된 엔티티도 함께 조회
    - @ManyToOne, @OneToOne의 기본 전략
    - 실무에서는 권장되지 않음
    
    **지연 로딩(LAZY Loading)**:
    
    - 연관된 엔티티를 실제 사용할 때 조회
    - @OneToMany, @ManyToMany의 기본 전략
    - 프록시 객체를 사용하여 구현
    - **실무에서는 모든 연관관계를 LAZY로 설정하는 것이 권장됨**
    
    ```
    @Entity
    public class Member {
        @ManyToOne(fetch = FetchType.LAZY) // 지연 로딩
        private Team team;
    }
    
    // 사용
    Member member = em.find(Member.class, 1L); // Member만 조회
    String teamName = member.getTeam().getName(); // 이 시점에 Team 조회
    
    ```
    
    </aside>
    
- **왜 실무에서는 지연 로딩을 권장하나요?**
    
    <aside>
    
    1. **N+1 문제 방지**: 즉시 로딩 시 예상치 못한 SQL이 대량 발생할 수 있음
    2. **성능 최적화**: 필요한 데이터만 조회하여 네트워크 비용 절감
    3. **명시적 제어**: Fetch Join이나 @EntityGraph로 필요한 경우에만 함께 조회
    4. **유연성**: 상황에 따라 선택적으로 데이터 로딩 가능
    
    **연결 개념**: 프록시 패턴, N+1 문제, 성능 최적화
    
    </aside>
    

## 5. N+1 문제

- **N+1 문제가 무엇이고, 어떻게 해결하나요?**
    
    <aside>
    
    N+1 문제는 연관관계가 설정된 엔티티를 조회할 때, 첫 번째 쿼리(1)로 N개의 엔티티를 조회한 후, 각 엔티티마다 연관된 데이터를 조회하는 추가 쿼리(N)가 발생하는 문제입니다.
    
    예시:
    
    ```
    // Member 리스트 조회 (1번의 쿼리)
    List<Member> members = em.createQuery("select m from Member m").getResultList();
    
    // 각 Member의 Team 조회 (N번의 쿼리)
    for (Member member : members) {
        System.out.println(member.getTeam().getName()); // 각각 쿼리 발생
    }
    // 총 1 + N번의 쿼리 발생!
    
    ```
    
    **해결 방법**:
    
    **1. Fetch Join 사용 (가장 일반적)**
    
    ```
    // JPQL에서 join fetch 사용
    select m from Member m join fetch m.team
    
    ```
    
    - 한 번의 쿼리로 연관된 엔티티까지 함께 조회
    - 실무에서 가장 많이 사용
    
    **2. @EntityGraph 사용**
    
    ```
    @EntityGraph(attributePaths = {"team"})
    @Query("select m from Member m")
    List<Member> findAllWithTeam();
    
    ```
    
    **3. Batch Size 설정**
    
    ```
    @BatchSize(size = 100)
    @OneToMany
    private List<Order> orders;
    
    ```
    
    - IN 절을 사용하여 지정된 크기만큼 한 번에 조회
    </aside>
    
- @EntityGraph와 Fetch Join의 차이점은?
    
    <aside>
    
    가장 큰 차이는 유연성입니다. 
    
    Fetch Join은 JPQL을 직접 작성하기 때문에 where 조건, 서브쿼리, 복잡한 조인 조건 등을 자유롭게 구성할 수 있습니다. 예를 들어 특정 상태의 주문만 함께 조회한다거나, INNER JOIN과 LEFT JOIN을 선택적으로 사용할 수 있죠. 
    
    반면 @EntityGraph는 간단한 연관관계 로딩에 특화되어 있어서 복잡한 조건을 추가하기 어렵습니다.
    
    또 다른 차이는 생성되는 SQL입니다. 
    
    @EntityGraph는 기본적으로 LEFT OUTER JOIN을 사용하는 반면, Fetch Join은 명시적으로 INNER JOIN이나 LEFT JOIN을 선택할 수 있습니다. 
    
    따라서 NULL이 없는 필수 연관관계라면 Fetch Join으로 INNER JOIN을 사용하는 것이 성능상 유리할 수 있습니다.
    
    재사용성 측면에서는 @EntityGraph가 유리합니다. NamedEntityGraph로 한 번 정의해두면 여러 Repository 메서드에서 재사용할 수 있고, Spring Data JPA의 메서드명 쿼리와도 자연스럽게 통합됩니다. 
    
    Fetch Join은 각 쿼리마다 join fetch를 반복해서 작성해야 하죠.
    
    실무에서는 간단한 연관관계 로딩은 @EntityGraph로 처리하고, 복잡한 조건이 필요하거나 동적 쿼리를 구성해야 할 때는 Fetch Join이나 QueryDSL을 사용하는 것이 일반적입니다.
    
    </aside>
    
- **Fetch Join 사용 시 주의할 점은?**
    
    <aside>
    
    1. **페이징 불가**: 컬렉션 페치 조인 시 페이징 API 사용 불가 (메모리에서 페이징)
    2. **둘 이상의 컬렉션 페치 조인 불가**: MultipleBagFetchException 발생
    3. **컬렉션 페치 조인 시 중복 제거**: distinct 사용 필요
    
    **연결 개념**: 프로젝트 자료의 조인 알고리즘(중첩루프조인, 해시조인 등)
    
    </aside>
    

## 6. 영속성 전이(Cascade)와 고아 객체

- **영속성 전이(Cascade)가 무엇인가요?**
    
    <aside>
    
    영속성 전이는 특정 엔티티의 영속 상태 변화를 연관된 엔티티에도 함께 적용하는 것입니다. 부모 엔티티를 저장/삭제할 때 자식 엔티티도 함께 저장/삭제됩니다
    
    ```
    @Entity
    public class Parent {
        @OneToMany(mappedBy = "parent", cascade = CascadeType.ALL)
        private List<Child> children = new ArrayList<>();
    }
    
    // 사용
    Parent parent = new Parent();
    Child child1 = new Child();
    Child child2 = new Child();
    
    parent.addChild(child1);
    parent.addChild(child2);
    
    em.persist(parent); // parent만 저장해도 child들도 함께 저장됨
    
    ```
    
    **Cascade 옵션**:
    
    - **ALL**: 모든 영속 상태 변화 전이
    - **PERSIST**: 저장 시에만 전이
    - **REMOVE**: 삭제 시에만 전이
    - **MERGE**: 병합 시에만 전이
    - **REFRESH**: 새로고침 시에만 전이
    - **DETACH**: 준영속 상태 전환 시 전이
    </aside>
    
- **고아 객체(Orphan) 제거는 무엇인가요?**
    
    <aside>
    
    A: 부모 엔티티와 연관관계가 끊어진 자식 엔티티를 자동으로 삭제하는 기능입니다.
    
    ```
    @OneToMany(mappedBy = "parent", orphanRemoval = true)
    private List<Child> children;
    
    // 사용
    Parent parent = em.find(Parent.class, 1L);
    parent.getChildren().remove(0); // 컬렉션에서 제거
    // commit 시점에 DELETE 쿼리 자동 실행
    
    ```
    
    **주의사항**:
    
    - 참조하는 곳이 하나일 때만 사용해야 함
    - @OneToOne, @OneToMany에서만 사용 가능
    - CascadeType.REMOVE와 유사하지만, 고아 객체는 컬렉션에서 제거만 해도 삭제됨
    
    **연결 개념**: 부모-자식 관계, 생명주기 관리
    
    </aside>
    

## 7. 더티 체킹(Dirty Checking)

- **더티 체킹이 무엇이고, 어떻게 동작하나요?**
    
    <aside>
    
    더티 체킹은 영속성 컨텍스트가 관리하는 엔티티의 변경사항을 자동으로 감지하여 데이터베이스에 반영하는 기능입니다. 개발자가 명시적으로 update() 메서드를 호출하지 않아도 됩니다.
    
    **동작 방식**:
    
    1. 엔티티를 영속 상태로 만들 때 **스냅샷**(최초 상태)을 저장
    2. 트랜잭션 커밋 시점에 스냅샷과 현재 엔티티 상태를 비교
    3. 변경사항이 있으면 UPDATE 쿼리를 자동 생성하여 실행
    
    ```
    // 조회
    Member member = em.find(Member.class, 1L);
    
    // 변경 - setter만 호출
    member.setName("변경된 이름");
    
    // em.update(member) 같은 코드 필요 없음!
    // 트랜잭션 커밋 시점에 자동으로 UPDATE 쿼리 실행
    
    ```
    
    </aside>
    
- **모든 필드를 UPDATE 하나요?**
    
    <aside>
    
    기본적으로 JPA는 모든 필드를 UPDATE합니다. 이유는:
    
    1. **수정 쿼리가 항상 동일**: 애플리케이션 로딩 시점에 쿼리 생성 가능
    2. **데이터베이스 재사용성**: 같은 쿼리를 보내므로 DB의 쿼리 캐시 활용 가능
    
    하지만 필드가 30개 이상이면 @DynamicUpdate를 사용하여 변경된 필드만 UPDATE할 수 있습니다.
    
    **꼬리질문: 준영속 상태에서는 더티 체킹이 동작하나요?**
    
    A: 아니요. 더티 체킹은 영속 상태의 엔티티에만 적용됩니다. 준영속 상태에서는 변경해도 데이터베이스에 반영되지 않습니다.
    
    **연결 개념**: 영속성 컨텍스트, 트랜잭션, 스냅샷
    
    </aside>
    

## 8. JPQL과 QueryDSL

- **JPQL이 무엇이고, SQL과 어떻게 다른가요?**
    
    <aside>
    
    JPQL(Java Persistence Query Language)은 **엔티티 객체를 대상으로 하는 객체 지향 쿼리 언어**입니다.
    
    **SQL과의 차이**:
    
    - **SQL**: 데이터베이스 테이블을 대상으로 쿼리
    - **JPQL**: 엔티티 객체를 대상으로 쿼리
    
    ```
    // JPQL - 엔티티와 필드명 사용
    String jpql = "select m from Member m where m.name = :name";
    
    // SQL - 테이블과 컬럼명 사용
    String sql = "select * from member where name = ?";
    
    ```
    
    **JPQL의 장점**:
    
    - 데이터베이스 방언에 독립적
    - 객체 지향적 쿼리 작성 가능
    - 타입 안정성은 부족 (문자열 기반)
    </aside>
    
- **QueryDSL을 사용하는 이유는?**
    
    <aside>
    
    QueryDSL은 JPQL의 단점을 보완한 타입 안전한 쿼리 빌더입니다.
    
    **QueryDSL의 장점**:
    
    1. **컴파일 시점 오류 감지**: 문자열이 아닌 자바 코드로 작성
    2. **IDE 자동완성**: 코드 작성이 편리
    3. **동적 쿼리 작성 용이**: 조건에 따라 쿼리를 쉽게 조합
    4. **재사용성**: 쿼리 로직을 메서드로 분리 가능
    
    ```
    // QueryDSL
    QMember member = QMember.member;
    List<Member> result = queryFactory
        .selectFrom(member)
        .where(member.name.eq("회원1"))
        .fetch();
    
    ```
    
    **연결 개념**: SQL, 동적 쿼리, 타입 안정성
    
    </aside>
    

## 9. 트랜잭션과 락

- **JPA에서 트랜잭션은 어떻게 관리되나요?**
    
    <aside>
    
    JPA는 트랜잭션 단위로 동작하며, EntityManager는 데이터 변경 시 반드시 트랜잭션 안에서 실행되어야 합니다.
    
    **프로젝트 자료 연결**: 자료에서 본 **트랜잭션의 ACID 속성**이 JPA에서도 그대로 적용됩니다:
    
    - **원자성(Atomicity)**: JPA의 flush() 시점에 모든 변경사항이 한 번에 적용되거나 롤백
    - **일관성(Consistency)**: 엔티티 검증과 제약조건 확인
    - **격리성(Isolation)**: 트랜잭션 격리 수준 설정 가능
    - **지속성(Durability)**: 커밋된 변경사항은 영구 저장
    
    ```
    @Transactional // 스프링에서 트랜잭션 관리
    public void businessLogic() {
        Member member = memberRepository.findById(1L);
        member.setName("변경");
        // 메서드 종료 시점에 자동 커밋
    }
    
    ```
    
    </aside>
    
- **JPA에서 낙관적 락과 비관적 락의 차이는?**
    
    <aside>
    
    **낙관적 락 (Optimistic Lock)**:
    
    - 트랜잭션 충돌이 거의 없을 것으로 가정
    - @Version 어노테이션으로 버전 관리
    - 충돌 시 OptimisticLockException 발생
    - **애플리케이션 레벨의 락**
    
    ```
    @Entity
    public class Member {
        @Version
        private Long version;
    }
    
    ```
    
    **비관적 락 (Pessimistic Lock)**:
    
    - 트랜잭션 충돌이 발생할 것으로 가정
    - 데이터베이스의 SELECT FOR UPDATE 사용
    - **데이터베이스 레벨의 락**
    
    ```
    Member member = em.find(Member.class, 1L, LockModeType.PESSIMISTIC_WRITE);
    
    ```
    
    **프로젝트 자료 연결**: 자료의 **트랜잭션 격리수준**(SERIALIZABLE, REPEATABLE_READ 등)과 연결되며, 격리수준이 낮을수록 동시성은 높지만 더티 리드, 팬텀 리드 등의 문제가 발생할 수 있습니다.
    
    **연결 개념**: 프로젝트 자료의 트랜잭션 ACID, 격리수준, 동시성 제어
    
    </aside>
    

## 10. 연관관계 매핑

- **연관관계 매핑의 종류를 설명해주세요.**
    
    <aside>
    
    JPA의 연관관계 매핑은 4가지가 있습니다:
    
    **1. @ManyToOne (N:1)**
    
    - 가장 많이 사용
    - 외래키가 있는 쪽이 연관관계의 주인
    
    ```
    @Entity
    public class Member {
        @ManyToOne(fetch = FetchType.LAZY)
        @JoinColumn(name = "team_id")
        private Team team;
    }
    
    ```
    
    **2. @OneToMany (1:N)**
    
    - 컬렉션으로 표현
    - mappedBy로 연관관계의 주인을 지정
    
    ```
    @Entity
    public class Team {
        @OneToMany(mappedBy = "team")
        private List<Member> members = new ArrayList<>();
    }
    
    ```
    
    **3. @OneToOne (1:1)**
    
    - 대상 테이블에 외래키가 있는 구조 권장
    
    ```
    @Entity
    public class Member {
        @OneToOne
        @JoinColumn(name = "locker_id")
        private Locker locker;
    }
    
    ```
    
    **4. @ManyToMany (N:M)**
    
    - 실무에서는 사용하지 않음
    - 중간 테이블을 엔티티로 승격시켜 @ManyToOne, @OneToMany로 풀어서 사용
    </aside>
    
- **연관관계의 주인이 무엇인가요?**
    
    <aside>
    
    연관관계의 주인은 **외래키를 관리하는 쪽**입니다. 양방향 연관관계에서 둘 중 하나를 주인으로 정해야 하며, 주인이 아닌 쪽은 mappedBy로 주인을 지정합니다.
    
    **원칙**:
    
    - 외래키가 있는 곳을 주인으로 정함
    - 주인만이 외래키를 등록, 수정 가능
    - 주인이 아닌 쪽은 읽기만 가능
    
    **프로젝트 자료 연결**: 자료의 **엔터티 관계**와 **정규화** 개념이 JPA 연관관계 매핑의 기초가 됩니다.
    
    **연결 개념**: 데이터베이스 정규화, 외래키, 조인
    
    </aside>
    

## 종합 질문

- **JPA 사용 시 주의할 점은 무엇인가요?**
    
    <aside>
    
    1. **영속성 컨텍스트 범위**: 트랜잭션 단위로 생성/소멸되므로, 트랜잭션 밖에서는 준영속 상태
    2. **지연 로딩 전략**: 모든 연관관계는 LAZY로 설정하고, 필요 시 Fetch Join 사용
    3. **N+1 문제**: 항상 주의하고 Fetch Join이나 Batch Size로 해결
    4. **양방향 연관관계**: 연관관계 편의 메서드를 작성하여 양쪽 모두 설정
    5. **엔티티 직접 노출 금지**: DTO로 변환하여 반환
    6. **영속성 전이**: 참조하는 곳이 하나일 때만 사용
    </aside>
    
- **프로젝트 자료의 데이터베이스 개념과 JPA는 어떻게 연결되나요?**
    
    <aside>
    
    - **엔터티** → JPA의 @Entity 클래스
    - **릴레이션/테이블** → 엔티티가 매핑되는 테이블
    - **속성** → 엔티티의 필드
    - **기본키** → @Id 어노테이션
    - **외래키** → @ManyToOne, @JoinColumn
    - **조인** → JPQL의 join, Fetch Join
    - **인덱스** → @Index, @Table(indexes = ...)
    - **트랜잭션** → @Transactional, EntityTransaction
    - **정규화** → 엔티티 설계와 연관관계 매핑의 기초
    
    ---
    
    </aside>
    

JPA는 객체와 관계형 데이터베이스를 연결하는 핵심 기술로, 실무에서 매우 중요합니다. 면접에서는 단순히 기능을 아는 것뿐만 아니라, **왜 그렇게 설계되었는지**, **어떤 상황에서 사용해야 하는지**, **어떤 문제점이 있고 어떻게 해결하는지**를 설명할 수 있어야 합니다.