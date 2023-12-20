---
title: 코틀린 exception handling 아름답게 하기
excerpt: 조금 더 읽기 쉬워요

toc: true
toc_sticky: true
tags: [Software Engineering, Kotlin, 코틀린]
categories:
- Software Engineering
last_modified_at: 2023-12-20T01:07:00
---

자바에서는 흔히 exception handling을 try-catch 구문을 사용합니다.

```kotlin
try {
	somethingDangerous();
} catch (Exception e) {
	logger.err("Hey ! there is something wrong in here! " + e);
}
```

조금 더 resource 관리 측면에서 구현해보면 아래처럼도 될 수 있겠군요

```kotlin
try (InputStream in = someInputStreamOpen()) {
	doSomethingDangerous(in);
} catch (Exception e) {
	logger.err("Hey ! there is something wrong in here! " + e);
}
```

위도 나름 신경써서 만든 구현입니다. (지금은 코드 네이밍이나 이런 논의는 하지 않기로 합시다.)

자칫 낭비가 될 수 있는 혹은 스트림을 열고 닫지 않아서 발생할 수 있는 이슈들을 JVM에게 맡겨서 해결해버리도록 했습니다.

코틀린에서도 위와 같은 방식으로 처리할 수 있지만, 코틀린은 기본적으로 다음처럼 성공 실패 케이스에 대해서 체인을 구성할 수 있습니다.

```kotlin
runCatching { doSomethingDangerous() }
	.onSuccess { logger.info { "Hey this works!" } }
	.onFailure { logger.err(e) { "Dang! this is wrong!" } }
	.takeIf { someConditionmet() }
// and blah-blah
```

기존의 자바에서 보았던 try-catch 구문과의 차이점은 이 방법으로는 예외 핸들링 작업과 성공했을 경우의 작업을 간략하게 체인으로 구성해서 한눈에 보기 쉽게 만든다는 점입니다.

문제가 발생하지 않으면 `onSucess` 블록으로 연결되고, 문제가 생겼으면 `onFailure` 블록으로 넘어가게 되죠. 코드가 조금 더 읽기 쉬워졌고 (제 눈에는 그래요. ㅎㅎ.), 처리로직이 조금더 간략해졌습니다.

사실 try-catch 구문으로도 위의 내용을 구현할 수 있지만, 요즘 구 시스템을 신규 시스템으로 이전하는 작업을 하면서 이전 자바코드를 코틀린으로 다시 짜고 있는데, 코드가 간결해지고 직관적으로 구현하게 되어서 좋습니다.

물론 모던 자바에서는 위와 같은 체이닝도 엇비슷하게 구현이 가능하지만, 역시 언어 네이티브에서 지원하는 점과 설계시점부터 이러한 점들을 고려했다는 점에서 코틀린에게 조금 더 높은 점수를 주고 싶네요 🙂