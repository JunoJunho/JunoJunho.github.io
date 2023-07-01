---
title: 자바 예외 (Exception) - Checked, Unchecked Exceptions
excerpt: and why they do matter

toc: true
toc_sticky: true
tags: [Programming, Software Engineering, Java, Kotlin]
categories:
- Software Engineering
last_modified_at: 2023-07-01T13:46:00
---

![Exception_Pic](../../img/post/230701/pawn-ga34ac0b65_1280.jpg)

Intro
----
요새 회사에서는 코틀린을 사용하여 새로운 프로젝트를 진행하기 때문에, 잠시 휴가를 즐기고 있는 이 기간동안에 코틀린 강좌를 듣고 있습니다. 저는 자바언어를 배운적이 있고, 실제 업무에서도 활용한 경력이 있기 때문에 아예 맨땅에 해딩하는 것 보다는 [자바 개발자를 위한 코틀린 프로그래밍 강좌](https://www.coursera.org/learn/kotlin-for-java-developers)를 듣고 있습니다. 코세라에서 진행하기 때문에 무엇보다 플랫폼 자체가 깨끗하고, 중요한 것은 무료라는 사실! (물론 certificate를 받기 위해서는 일정금액을 지불해야합니다.) 게다가 코틀린을 만든 곳인 젯브레인에서 강좌를 진행하기 때문에 코틀린의 각 기능에 대해서 왜 이런 선택을 했는지에 대한 내용도 알아 볼 수 있습니다. 단점은 뭔가 발음이 익숙하지 않다는 사실.. (젯브레인이 영국 근처인가봐요 다들 발음이 미국영어에 익숙한 저로서는 알아듣기 힘드네요..)

아무튼 코틀린 강좌를 듣다보니 checked exception과 unchecked exception이 Kotlin에서 다루어 지는 방식이 다르고, 이 부분이 조금 중요하게 다루어지는 것 같아서 배경지식으로 여기서 한번 정리해보고자 합니다.

Exception
-----
자바 언어를 배워보신 분이라면 누구나 한번쯤 들어봤고, 업무상에서 많이 접하는 용어입니다. 프로그램 상에서는 크게 두 종류의 오류(?)가 있습니다. 첫 번쨰는 프로그램 실행중 원치않는 흐름에 의해서 프로그램 자체가 종료되버리는 에러 (Error)와 발생할 것으로 예상되는 오류인 예외 exception이 있습니다.

오류의 종류에는 JVM 자체의 에러와 같은 Virtual Machine Error, 해당 값이 꼭 검증되어야 할 때 검증 값이 달라서 프로그램 자체가 종료되는 Assertion Error 등이 있습니다. 이 경우는 흔히 말해서 Fatal한 케이스이므로 프로그램 입장에서도 타협하지 않고 바로 프로그램이 종료됩니다. 이와 반대로 예외 (Exception)은 프로그램 실행 흐름 상에서 발생할 것으로 예상되었기 때문에 Java에서 사용되는 `try-catch`구문을 사용해서 핸들링이 가능합니다.

Checked-UnChecked Exception
-----
마치 학교의 수업시간같네요. Exception에서는 다시 Checked와 Unchecked Exception으로 나누어 살펴볼 수 있습니다. Checked와 UnChecked의 가장 큰 차이점은 해당 예외가 컴파일 과정중에 체킹이 되었는지 아닌지 유무에 따라 나누어 볼 수 있습니다. 즉, checked는 컴파일 과정중에 해당 예외가 발생할 수 있다고 확인(checked)이 되었다는 의미이고, unchecked는 그렇지 않다는 것을 의미합니다. 그렇다면 의문점은 당연하게 어떻게 check를 하느냐로 갈 수 있습니다.

흔히 메소드에서 아래와 같은 구문을 보셨을 것이라 생각합니다.

```
void foo() throws IOException;
```

위의 메소드 `foo`는 `IOException`을 던질 가능성이 있다라고 의미하는 메소드 시그니쳐입니다. 즉, 컴파일 과정중에 컴파일러는 `IOException` family가 발생할 수 있다고 확인한 셈이지요. 그렇다면 모든 메소드에 `throws Exception`을 기재하면 모든 메소드가 checked Exception을 핸들링하는 것이냐 하고 궁금증을 가져볼 수 있는데요. 정답은 그렇지 않다입니다. 아무리 Exception을 기재해놓았다 하더라도 Exception의 child class 중에서 unchecked exception이 존재하기 때문입니다.

그렇다면 Unchecked Exception은 무엇인가 알아보겠습니다. 당연하게 그 의미는 컴파일 과정중에 확인이 되지 않은 예외군을 의미합니다. 여담이지만 C++에서는 모든 예외가 unchecked이며 이 예외들을 컴파일러에서 프로그램을 작성한 개발자에게 확인하도록 강제하지 않습니다. 자바에서는 Error와 RuntimeException 군이 unchecked exception으로 구분되며 그렇지 않는 모든 exception들은 checked exception으로 분류됩니다. 다시 말해서 프로그램을 실행해 봐야지만 알 수 있는 에러 (프로그램을 실행하기 전까지는 안에 값이 어떤 상태로 어떤 연산을 할 지 알수 없기 때문에 컴파일러가 앞서서 확인해 줄 수 없는 예외들)을 이 군으로 분류할 수 있습니다.

예를들어,

```
int x = 0;
int y = 10;
int z = y / x; // throws ArithmeticException
```

위의 구문은 컴파일러 상에서 볼 때 전혀 문제가 없습니다. 정수 값 두개를 받아서 이를 나누는 작업이니까요. 하지만 실제는 정수 값을 0으로 나누는 연산이기 떄문에 예외가 발생합니다. 짧게 정리해보면 unchecked exception은 컴파일 과정중에 걸러내기 어렵지만 런타임 과정중에 발생할 수 있는 예외이며, throws 구문을 굳이 설정하지 않아도 문제가 없다는 점입니다. 흔히 프로그래밍 에러들이라고 할 수 있습니다. 

뭐 결론은 다 잘 걸러내서 핸들링 하는 것이 개발자의 근본이라고 할 수 있겠죠?