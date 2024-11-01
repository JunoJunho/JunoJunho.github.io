---
title: Kotlin - Mockk and Mockito
excerpt: Mockk는 코틀린에서의 Mockito의 대체재일까?

toc: true
toc_sticky: true
tags: [Programming, Software Engineering, Kotlin]
categories:
  - Software Engineering
last_modified_at: 2023-06-03T13:32:00
---


![Programming_Language_Pic](../../img/post/230517/pexels-sabrina-gelbart-249798.jpg)

TLDR;
------
There is no silver bullet! 언제나 정답은 똑같습니다. 모든 경우에 딱 알맞는 프레임워크는 없습니다. 코틀린은 JVM위에서 동작하는 나름 모던 랭기지라고 할 수 있기 때문에, 코틀린을 사용하는 경우 Mockk를 통해서 가장 코틀린 스러운 모킹을 통해 유닛 테스트를 할 수 있다는 점이고, Mockito는 자바에서 가장 많이 사용되는 프레임워크 답게 별도의 러닝 커브 없이 쭉 코딩 스타일을 유지할 수 있습니다.

Mockk ? Mockito ?
-----
코틀린에서 테스트를 작성할 때 모의 객체를 사용하는 것은 매우 일반적입니다. 모의 객체 프레임워크를 사용하면 실제 객체의 동작을 제어하고, 메서드 호출을 검증하며, 테스트 환경에서 예외 상황을 시뮬레이션할 수 있습니다. 요번 포스트에서는 코틀린에서 모의 객체 프레임워크중에서 가장 널리 사용되고, 유명한 Mockk와 Mockito를 비교해보고자 합니다.

이 포스트에서는 Mockk와 Mockito의 주요 차이점을 살펴보고, 각각의 특징과 장단점, 그리고 어떤 시나리오에서 어떤 프레임워크가 더 적합한지에 대해 알아보겠습니다. 

차이는 있을까?
-------
코틀린에서 Mockk와 Mockito를 비교하는 블로그 포스트를 작성하는 것은 좋은 아이디어입니다. 이 두 가지 프레임워크는 모두 테스트 더블(Mock, Stub 등)을 만들고 모의 객체(Mock Object)를 사용하는 데 도움을 주는 유명한 라이브러리입니다. 그러나 두 프레임워크는 몇 가지 다른 특징과 사용법을 가지고 있습니다.

1. 문법: Mockk는 코틀린 언어의 특성을 최대한 활용하여 문법을 제공합니다. 예를 들어, 코틀린의 널 안전성을 고려하여 nullable한 객체에 대한 모의 객체를 생성하고 동작을 지정할 수 있습니다. 반면에 Mockito는 자바 문법에 기반을 두고 있으며, 코틀린에서 사용할 수 있지만 일부 코틀린 특징을 활용하지 못할 수도 있습니다.

2. 코루틴 지원: Mockk는 코틀린의 코루틴을 지원하고, 코루틴 기반의 비동기 코드의 테스트를 쉽게 할 수 있습니다. Mockito는 코루틴에 대한 공식적인 지원이 없으며, 비동기 코드의 테스트를 위해 별도의 라이브러리를 사용해야 할 수도 있습니다.

3. Spy 객체: Mockk는 실제 객체의 메서드를 호출하고 반환 값을 조작하는 "spy" 객체를 생성하는 기능을 제공합니다. 이를 통해 실제 객체의 부분적인 동작을 유지하면서 일부 동작을 변경하거나 감시할 수 있습니다. Mockito는 이와 유사한 기능을 제공하지 않습니다.

4. 메서드 호출 순서 검증: Mockito는 메서드 호출 순서를 검증하는 기능을 제공합니다. 이를 통해 특정 메서드가 지정된 순서대로 호출되었는지 확인할 수 있습니다. Mockk는 이와 같은 순서 검증 기능을 기본으로 제공하지 않지만, verifyOrder 함수를 사용하여 비슷한 동작을 수행할 수 있습니다.

5. Kotlin DSL: Mockk는 코틀린의 DSL(Domain Specific Language)을 활용하여 테스트 코드를 작성할 수 있는 기능을 제공합니다. 이를 통해 가독성이 높은 테스트 코드를 작성할 수 있습니다. Mockito는 이와 같은 Kotlin DSL을 기본적으로 제공하지 않습니다.

위의 간략한 차이를 바탕으로 볼 때, 코틀린의 Mockk는 Mockito의 대부분의 사용 사례 (Use Case)를 커버할 수 있으면서 코틀린 스타일의 코드를 작성할 수 있는 매력이 있습니다. 하지만 위의 차이점은 Mockk와 Mockito의 주요 차이점 중 일부에 불과합니다. 실제로 두 프레임워크를 사용해보고 개발 환경에 따라 어떤 것이 더 적합한지 결정하는 것이 가장 좋습니다.



예시는..?
------

```
// Mockk의 사용을 위해 의존성을 추가해야 합니다.
// build.gradle 파일에 아래 코드를 추가하세요.
// 2023-06-03 기준 최신 릴리즈는 1.13.5 입니다.
// testImplementation("io.mockk:mockk:1.13.5")

// Mockk를 사용하여 Mock 객체 생성
val mockedList = mockk<List<String>>()

// 동작 지정
every { mockedList.size } returns 5

// 메서드 호출 확인
mockedList.size
verify { mockedList.size }
```

같은 내용을 Mockito로 작성하면 아래와 같습니다.

```
// Mockito의 사용을 위해 의존성을 추가해야 합니다.
// build.gradle 파일에 아래 코드를 추가하세요.
// 2023-06-03 기준 최신 릴리즈는 5.3.1
// testImplementation("org.mockito:mockito-core:5.3.1")

// Mockito를 사용하여 Mock 객체 생성
val mockedList = mock(List::class.java) as List<String>

// 동작 지정
whenever(mockedList.size).thenReturn(5)

// 메서드 호출 확인
mockedList.size
verify(mockedList).size
```

위의 예시는 Mockk와 Mockito를 사용하여 List의 Mock 객체를 생성하고 간단하게 메소드를 호출 한 뒤, 마지막으로 메서드 호출이 예상대로 이루어졌는지를 확인하기 위해 verify 함수를 사용하고 있습니다.

위의 예시는 간단한 동작을 보여주기 위한 것이며, 실제로 Mockk와 Mockito는 다양한 기능과 유연성을 제공합니다. 테스트 코드에서 실제 객체의 동작을 제어하고, 메서드 호출을 검증하며, 예외 상황을 시뮬레이션하는 등 다양한 작업을 수행할 수 있습니다.

그러면 언제 무얼 써야할까?
------
Mockk와 Mockito의 적합한 시나리오는 다양한 상황에 따라 달라질 수 있습니다. 그러나 몇 가지 일반적인 시나리오를 예로 들어보겠습니다:

1. Kotlin 프로젝트: Mockk는 코틀린 언어와의 통합이 우수하며, 널 안전성과 코루틴 지원 등 코틀린의 특징을 잘 활용합니다. 따라서 Kotlin으로 개발된 프로젝트에서는 Mockk가 더욱 적합할 수 있습니다.

2. Java 프로젝트: 자바로 개발된 프로젝트에는 Mockito가 더 적합할 수 있습니다.

3. 코루틴 기반 비동기 코드: Mockk는 코루틴을 지원하여 비동기 코드의 테스트를 용이하게 합니다. 코루틴을 적극적으로 활용하는 경우에는 Mockk를 사용하는 것이 유리할 수 있습니다.

4. 부분적인 동작 변경 및 감시: Mockk는 실제 객체의 일부 동작을 변경하거나 감시하기 위한 `spy` 객체를 생성할 수 있습니다. 이를 통해 특정 메서드의 동작을 유지하면서 일부 동작을 조작하거나 확인할 수 있습니다. 이런 유연성이 필요한 경우 Mockk를 고려할 수 있습니다.

5. Kotlin DSL 사용: Mockk는 Kotlin의 DSL을 지원하여 가독성이 높은 테스트 코드 작성을 도와줍니다. Kotlin DSL을 활용하고 싶은 경우 Mockk가 더욱 적합할 수 있습니다.

물논, 프로젝트의 특정 요구사항과 개발 환경에 따라 선택이 달라질 수 있습니다. Mockk와 Mockito는 모두 좋은 프레임워크이며, 가장 중요한(?) 개발자의 선호도를 고려하여 적절한 프레임워크를 선택하는 것이 중요합니다.

그래서 저는 요즘 회사에서 팀의 방향이 새로운 걸 시도하는 방향으로 가고 있어서, 코틀린과 Mockk를 사용하여 개발을 하고 있습니다. 뭘 개발하고 있냐구요? 몰?루?