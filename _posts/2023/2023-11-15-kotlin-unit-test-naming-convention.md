---
title: 코틀린 테스트 코드 네이밍 컨벤션
excerpt: 이라 쓰고 내가 쓰는 건 그 끝에 한줄만.. ㅎㅎ

toc: true
toc_sticky: true
tags: [Software Engineering, Testing]
categories:
- Software Engineering
last_modified_at: 2023-11-15T23:24:00
---

![ab819adc-5ed9-4027-a286-f1cc83d3cd47.png](../../img/post/231115/ab819adc-5ed9-4027-a286-f1cc83d3cd47.png)

요즘 회사에서는 새로운 프로젝트를 킥오프 할 때 자바를 사용하기 보다는 불가피한 제약사항이 존재하지 않는 한 코틀린을 사용하는 것이 대세아닌 대세로 자리잡았습니다. 여러모로 장점이 있고 무엇보다 JVM 위에서 동작하기 때문에 기존 자바 코드와의 호환성 문제도 크게 존재하지 않기 때문이죠. 게다가 제가 현재 재직중인 아마존은 모든 서비스를 마이크로 서비스로 만드는 것이 기본 개념이다 보니 이런 서비스간 의존성 문제에서 조금 더 자유로운 분위기에서 새로운 언어의 도입이 어려운 것은 아닙니다. 기존 서비스에 새로운 코드를 추가한다고 할지라도 코틀린으로 작성해도 결국에는 자바 코드로 변환되고, 다시 바이트코드로 변환되기 때문에 내부적으로 어떻게 변환되는지를 특별히 신경써야하는 로직이 아닌 이상 크게 문제될 점은 없습니다.

어찌되었든 코틀린을 많이 사용하다보니 코틀린 자체에 대한 기능은 둘째치고서라도 여러가지 네이밍 컨벤션부터 다시 적응해야하는 문제가 생기게 되었습니다.

예를 들어, 코틀린에서는 기존의 클래스에서 확장 가능한 커스텀 메소드를 구현할 수 있도록 Extension 메소드를 지원합니다. 

```kotlin
private fun String.isValidEmail() = this.someMethodWithRegex()

// Usage
inputEmail: String = getInput()
if(inputEmail.isValidEmail()) print("Valid!") else throw Exception()
```

뭐 작성하고 하는 것에는 크게 문제가 있지는 않는데, 이것을 어디에 구현해야하는가? 는 다른 문제입니다 ㅎㅎ 일례로 같이 개발하는 동료와 이 메소드들을 따로 Extension 패키지를 구현하여 그 안에다 구현해야하느냐, 아니면 직접 비지니스 로직이 사용되는 클래스 내부에 구현해야하느냐에 대하여 의견 다툼을 한적도 있습니다. 

그래서 저는 그때마다 [코틀린 공식 도큐먼트](https://kotlinlang.org/docs/coding-conventions.html#class-layout)를 참조해서 제 주장의 논거로 제시하고는 하였죠. (물론 이 논쟁에서도 제 의견이 채택이 되었습니다 🙂)

팀에서는 새로운 프로젝트가 대략 3개가 올해 준비되고 있습니다. 문제는 모든 팀원들이 골고루 각 프로젝트에 기여할 기회를 얻지 않는다는 것에 있죠. 그렇다는 것은 기여하는 사람만 해당 프로젝트에 기여한다는 점입니다. 그렇게 될 수록 하는 사람들 끼리의 컨벤션으로 코드 스타일이 굳혀져 갑니다. 저희 프로젝트도 마찬가지였는데요. 어느날 다른 패키지의 코드 리뷰를 보다가 (구경하다가) 코딩 컨벤션에 대한 이야기가 나왔고, 테스트 코드쪽에 관련해서는 제가 관리하고 있던 패키지에서도 딱히 컨벤션이 없었기에 부랴부랴 찾아보게 되었습니다.

역시나 [공식 문서](https://kotlinlang.org/docs/coding-conventions.html#names-for-test-methods)를 확인해 봤는데, 별다른 내용이 없었습니다. 코틀린의 조상격인 자바는 테스팅 네이밍을 `When-Then-ExpectedBehaviour` 로 기술하는 방식과 `Should_ExpectedOne_When_State` 로 기술하는 방식 혹은 JUnit의 기본 네이밍 룰인 `testSomething` 로도 구분할 수 있습니다.

### 무엇이 베스트일까?

사실 정답은 언제나 그랬듯이 없습니다. 가장 중요한 것은 팀내에서의 통일성입니다. 누구는 스타일 A 를 따라가고 누구는 스타일 B를 따라간다면, 전체적으로 코드의 스타일이 달라지기 때문에 나중 사람이 코드를 이해하기 어려워집니다.

그렇다면 제가 사용하는 방식은 무엇인지 간단하게 살펴보면..

```kotlin
@Test
fun `Failed Athena Query Status should throw DependencyException`() {
	// Blah Blah
}
```

네 저는 간략하게 `When - Should - Expected Behaviour` 순으로 기재합니다. 앞에는 조건을 기술하고 `should` 를 분기점으로 기대하는 결과에 대해서 기술하고 있습니다.