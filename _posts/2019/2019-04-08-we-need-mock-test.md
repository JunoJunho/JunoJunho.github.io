---
title: We need mock test!
excerpt: 개발자라면 알았으면 하는 Mock 테스트
toc: true
toc_sticky: true
categories:
  - Software Engineering
tags: [Software Engineering, Testing]
---

### Mock Test, 그 심오한 세계

Mock Test는 여러 클래스와의 관계로 얽혀있는 클래스를 테스트할 때 사용하는 테스팅 기법입니다.

왜 그래야 하냐구요?

특정 클래스 A를 테스트 할 때, 이 클래스의 로직만 테스트를 해야할 필요성이 있기 때문입니다. 예를 들어서, A라는 클래스가 DB 클래스로부터 결과를 얻어서 특정 연산을 하는 클래스라고 가정해 봅시다. 테스트 코드를 작성할 때 매번 DB와의 연산을 수행해야 할까요? 
 - 만약 그 DB 클래스가 (그럴리는 없겠지만) 실제 환경 DB와 connection을 생성하는 코드라면?
 - 실제로 테스트 코드로 실행하던 중에 실수로 클린업 코드에 모든 db table을 날려버리는 작업이 실행되었다면?

아 생각만 해도 장애 보고서를 써야한다는 감이 옵니다.

### Mock은 최대한 작성한 클래스 코드에만 집중하도록 해줍니다.

위의 예를 다시 한번 코드로 살펴봅시다. (저는 업이 Java를 사용하니 여기서도 Java를.. 쿨럭)

```
public class AppClass {

  private DB dbInstance;

  public AppClass (DB dbInstance) {
    this.dbInstance = dbInstance;
  }

  public int manipulateDb (int adder) {
    DBConnection conn = dbInstance.getConnection();
    final int dbVal = conn.getInt(1);
    return dbVal + adder;
  }
}
```

매우 매우 간단한 코드입니다. 코드를 살펴보면 db에서 커넥션을 얻어서 Integer 값을 얻은 다음, 해당 값에 adder를 더해서 돌려주는 코드입니다.

자 그러면, 우리는 이제 생각해봐야 합니다.

`저 manipulateDb를 어떻게 테스트 한담?`

### 우리에게는 Mock이 있다!

여러분의 집중도를 위해서 최대한 짧게 갑시다.

```
@JUnitTest
public class AppClassTest {

  @InjectMocks
  private AppClass app;

  @Mock
  private DB dbInstance;

  @Mock
  private DBConnection conn;

  @Before
  public void init() {
    when(dbInstance.getConnection()).thenReturn(conn);
  }

  public void testManipulateDb () {
    when(conn).getInt(anyInt()).thenReturn(0);
    final int realResult = app.manipulateDb(3);
    final int expectedResult = 3;
    assertEquals(expectedResult, realResult);
  }
}
```

대략 요런 형식입니다.
해당 클래스에서 의존 관계를 갖는 (특히 해당 메소드에서), 생성부터 테스트하고자 하는 메소드의 호출까지의 흐름에서 필요한 외부 클래스는 모두 Mock, 또는 그대로 사용하도록 설정하고 실제 검증하고자 하는 로직만 집중하도록 하였습니다.

#### 이제 장점만 나열해 봅니다.

네, 저는 Mock 테스트를 권장하는 사람이니깐, 좋은 점만 나열하겠습니다.

1. <b>검증하고자 하는 로직만 검증할 수 있습니다.</b> 제가 이전 직장 재직 시절, 저렇게 많은 dependency를 어떻게 해결하지 하고 고민했었는데, 이렇게 의존 관계를 모두 mocking 해버리면, 필요한 method 로직만 검증할 수 있습니다.
2. <b>코드가 자연스럽게 읽힙니다.</b> 이건 라이브러리의 장점일 수도 있지만, 문장을 읽듯이 자연스럽게 해결됩니다. `when(conn)` conn 객체에서, `.getInt(anyInt())` 임의의 Integer 값이 주어지는 getInt 메소드를 호출하면, `.thenReturn(0)` 0을 리턴해라. 그냥 쭉 글을 쓰듯이 테스트 시나리오를 작성할 수 있게 됩니다.
3. Mock 테스트 코드를 사용함으로써, 내 코드에는 문제가 없다는 것을 보증할 수 있게 됩니다. 즉, `라이브러리가 정상 동작하는 한 내 코드는 정상이다!` 라는 점을 확정할 수 있게 됩니다.

드디어 밀린 숙제를 해결한 느낌입니다. (~~마치 변비가 해결된 느낌~~)

다음에는 새로운 글로 찾아 뵙겠습니다. 그럼 20000 (_ _)