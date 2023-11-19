---
title: 코틀린 프로그래밍과 로깅 with SoC
excerpt: Separation of Concerns이 적용되는 코틀린 프로그래밍과 로깅

toc: true
toc_sticky: true
tags: [Software Engineering, Kotlin, 코틀린]
categories:
- Software Engineering
last_modified_at: 2023-11-17T23:12:00
---

![ab819adc-5ed9-4027-a286-f1cc83d3cd47.png](../../img/post/231118/42749f6d-b732-45f6-a4b4-967dac9f4c2e.png)

놀라지 마세요, 위의 제목으로 이미지를 생성한겁니다. ㅋㅋㅋㅋㅋ

저는 현업에서 약 5년간 자바로 프로그래밍을 하였습니다. 그렇기 때문에 선언형 (Imperative) 프로그래밍에 굉장히 최적화 되어 있는 코딩 스타일을 가지고 있죠. 물론 객체 지향 (OOP)도 조금은 합니다만, 일반적으로 두뇌의 흐름대로 코딩을 하는 것에 더 익숙합니다.

코틀린도 이러한 선언형 + 객체지향 프로그래밍 패러다임을 받아들였지만, 간략한 함수형 프로그래밍을 조금 더 많이 강조하고 있습니다. 하나의 스트림 객체에서 `.filter, .map` 등의 함수를 사용해서 다양한 요구사항을 충족할 수 있죠.

```kotlin
val filteredSomeMap = someMap.filter { it.someAttribute = anotherAttribute }
														 .map { it.theOtherAttribute }
                             .any { it.someCondition() }
                             ?: throw AttributeNotFoundException()
```

이에 더해서 오늘의 주제인 로깅 관련된 내용도 손쉽게 적용할 수 있습니다.

[코틀린 로깅](https://www.baeldung.com/kotlin/kotlin-logging-library) (Kotlin logging)이라는 라이브러리를 사용해서 기존 코틀린 언어와 매우 유사한 구조로 로깅을 할 수 있습니다.

```kotlin
logger.info { "This is info log" }
```

그리고 해당 로그는 기존 log4j와 동일하게 timestamp, 실행 스레드, 실행 line number등을 추가해서 컨텍스트를 추가할 수도 있습니다.

## Separation of Concerns

스프링을 살펴보신적이 있다면, 혹은 MVC 아키텍처를 공부해보신 적이 있다면, Separation of Concern 이라는 용어를 들어보신적이 있을 겁니다. 관련된 내용을 하나의 클래스 혹은 하나의 로직에 모두 집어넣지 않고, 관련된 내용들만 따로 구성하는 것을 말하죠. OOP와 비슷한 개념입니다만 겹치지 않는 Orthogonal 한 내용이다 라고 할 수도 있겠네요.

쉽게 생각해서 MVC 에서는 데이터에 관련된 내용을 모델 클래스, 라우팅 관련된 내용을 컨트롤러에, 화면에 디스플레이 하는 내용을 뷰에 넣는 작업과 비슷하다고 할 수 있죠.

이를 코틀린에 적용해보면, 다음의 자바코드를, 

```kotlin
public String applyEscapeInQueryString(String sqlQuery) {
	logger.info("Start to process " + sqlQuery);
	final String escapedQuery = sqlQuery.replace("\"", "\\\"");
	logger.info("Escaped: " + escapedQuery);
  return escapedQuery;
}
```

요렇게 코틀린 에서는 코딩 할 수 있습니다.

(아시겠지만 이건 최적화된 코드라고 볼 수 없습니다.)

```kotlin
public fun String.applyEscapeInQueryString() = {
	logger.info { "Start to process $this" }
	this.replace { "\"", "\\\"" }
      .also { logger.info { "Escaped : $this" } }
}
```

매우 간단한 코드이기 때문에 이게 뭐 ? 라고 볼 수 있지만, 함수 체이닝이 많은 구조에서는 해당 코드가 조금 더 효과적으로 비즈니스 로직과 로깅 과정을 분리할 수 있습니다. 매 스텝마다 로깅 과정을 추가해서 이후 디버깅 과정에서 적절하게 트러블슈팅도 할 수 있죠.

꼭 `also` 메소드를 사용하지 않더라도, `apply` 등의 메소드를 사용해서 동일한 기능을 할 수 있습니다. 적절한 상황에 적절한 메소드를 선택하는 것이 더 중요하겠죵.