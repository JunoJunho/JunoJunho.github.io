---
title: Kotlin - Gradle Groovy와 Gradle Kotlin DSL 
excerpt: 뭐로 시작해야할까?

toc: true
toc_sticky: true
tags: [Programming, Software Engineering, Kotlin]
categories:
- Software Engineering
last_modified_at: 2023-06-09T19:07:00
---


![Programming_Language_Pic](../../img/post/230517/pexels-sabrina-gelbart-249798.jpg)

TLDR;
------
어느것을 쓰든 정답은 없지만, 꼭 하나만 골라달라면 Kotlin Gradle을 쓰세요. 왜냐구요 ? 많이 사용하고 대규모 프로젝트에서도 많이 쓰거든요. 덤으로 코틀린도 익히세요.

Gradle?
-------
Gradle은 안드로이드 앱을 빌드하기 위한 오픈소스 빌드 자동화 시스템입니다. Gradle은 Groovy DSL과 Kotlin DSL을 사용하여 빌드 스크립트를 작성할 수 있습니다. 이를 통해 빌드 프로세스를 자동화하고 더욱 효율적으로 관리할 수 있습니다. 예전에는 Maven이 대세였는데 어느순간부터인가 Gradle이 그 자리를 꿰차고 있습니다. Maven과 Gradle의 선택은 정말 개인의 선호에 따라 갈리는 것 같습니다. 개인적으로는 Maven은 구조화된 XML을 사용하지만, 생각보다 Boilerplace가 많다고 생각하기 때문에 Gradle을 조금 더 선호합니다. 더 간결하고 Maven에 비하면 조금 더 직관적이거든요.

Gradle을 사용하여 의존성 관리(Dependency Management)를 하는 것은 Groovy 언어를 사용하는 방법과 Kotlin 언어를 사용하는 방법 두 가지가 있습니다. 이 포스트에서는 각각을 간단하게 살펴보고 무엇을 사용해야 할지 간단하게 생각해보겠습니다.

Kotlin Gradle
-----
코틀린은 JVM 위에서 동작하는 모던 언어 입니다. 강타입을 지원하고 가장 중요한 Null safety가 보장되는 언어이지요. 코틀린의 문법을 사용하여 Gradle 스크립트를 작성할 수 있습니다. 코틀린은 이해하기 쉽고 읽기도 자바를 할 줄 안다면 별도의 러닝 커브 없이 이해할 수 있기 때문에 Gradle 스크립트를 작성하는 방식에 있어서 이상적인 방법이라고 할 수 있습니다. 코틀린 Gradle은 DSL(도메인-Specific 언어)이며, Gradle 스크립트를 작성하는 방법 중 타입 세이프 한 방법이라고 할 수 있습니다. 즉, 컴파일 타임이나 스크립트를 작성하면서 문법적으로 틀릴 부분을 미리 바로 잡을 수 있어 시간이 절약됩니다.

Kotlin Gradle은 뒤에 이어서 설명할 Groovy보다 조금 더 간결하게 작성할 수 있기 때문에, IDE 환경에서 편집하기 용이하며, Groovy와 비교해서 조금 더 나은 성능을 보여줍니다. 특히 대규모에 프로젝트에서는 그렇습니다.

Kotlin Groovy
-------
Groovy를 사용하여 Gradle 스크립트를 작성하는 방식은 오래된 방식입니다. 그만큼 레퍼런스도 많고 사용예가 많아서 무언가 막혔을 경우, 쉽게 관련된 내용을 찾아서 트러블 슈팅을 할 수 있다는 장점이 있습니다. Groovy 언어 자체는 객체 지향 언어이며 역시나 JVM위에서 동작합니다. 자바와 비슷한 언어이지만, 조금더 간결한 문법을 지니고 있습니다. 역시나 Groovy Gradle DSL은 Groovy를 사용하여 Gradle 스크립트를 작성할 수 있는 언어입니다.

Groovy Gradle은 유연하고 이해하기 쉽습니다. 코틀린을 딱히 알지 못한다면 Groovy가 조금 더 러닝 커브가 쉽다고 할 수 있습니다. 본래 스크립트 언어이기 때문에 스크립팅이 조금 더 쉽고 직관적으로 작성할수 있다는 장점이 있습니다. XML과 JSON 파싱을 Kotlin Gradle보다 더 쉽게 할 수 있습니다.

그렇다면 무엇을 사용해야 할까?
-------
사실 어느것을 사용하느냐는 역시나 프로젝트의 요구사항에 달려 있습니다. 새로 합류한 팀에 이미 Groovy 혹은 Kotlin을 Gradle 스크립트를 작성하는 것에 사용하고 있다면 당연히 따라야겠지요. 만약 새로운 프로젝트를 시작한다면, 대규모 프로젝트를 작성하는 경우 Kotlin이 조금 더 나은 성능을 보여줄 수 있습니다. 작은 프로젝트나 개인 프로젝트는 Groovy를 선택하는 것도 나쁘지 않은 선택이 될 수 있습니다. 조금 더 수정이 유연하기 때문이지요. 하지만 그렇다고 해서 Kotlin Gradle이 유연성이 없다는 이야기는 아닙니다. 많은 개발자들은 요새 Groovy에서 코틀린으로 이동하는 추세입니다. 성능이 당연히 낫기 때문이고, 많은 경우 기업에서 사용하는 스킬셋을 본인의 개인 프로젝트에서도 동일하게 가져가고 싶기 때문에 Kotlin Gradle을 선택하는 빈도가 늘어난다고 합니다.

그렇다면 더더욱 Kotlin Gradle을 사용하지 않을 이유는 없어 보이네요.