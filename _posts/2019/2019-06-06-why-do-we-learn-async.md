---
title: 개발자가 비동기를 배워야 하는 이유
excerpt: 어렵지만 매력적이고 필수인 비동기
toc: true
toc_sticky: true
categories:
  - Software Engineering
tags: [Software Engineering, Async, Java]
---

## 이 문서는 Java 8을 기준으로 하고 있습니다.
-------

학부시절 우리는 코딩을 하면 항상 동기 프로그래밍 (Sync programming)을 하거나 스레드 몇개를 더 생성해서 프로그램을 짜보는 동시성 프로그래밍(Concurrent Programming)의 맛을 보는 정도만 진행합니다. 현업에서는 이런 동기 프로그래밍을 수행하는 경우는 거의 없고, 제가 일하고 있는 백엔드 서버에서는 거의 99%의 확률로 비동기 프로그래밍을 사용한다는 점입니다.

메인 줄기의 비지니스 로직은 메인 스레드에서 처리하는 경우가 많지만, 시간이 걸리는 작업, 혹은 외부 API를 사용하거나 해당 태스크를 수행하는 것 때문에 메인 스레드 풀을 점유하게 된다면, 전체 서버 성능은 저하되고, 자원을 낭비하게 됩니다.

#### 비동기 프로그래밍?

비동기 프로그래밍 (Async Programming)을 알아보기 이전에 동기 프로그래밍을 알고 있으면 비동기의 이해가 더 쉬울 것입니다.

동기 프로그래밍은 프로그램의 모든 statement 한개 한개를 시작부터 끝까지 하나의 스레드가 다 따라가는 것을 의미합니다. 예를 들어서, 사용자로 부터 입력을 받아서 연산을 수행하는 프로그램이 있다고 생각해봅시다. 우리는 아마 이렇게 프로그램을 작성할 거에요.

```java
Scanner sc = new Scanner(System.in);
int left = sc.nextInt();
int right = sc.nextInt();

System.out.println(left + right);
```

대략 이렇게 되지 않을까 생각하네요. 이떄 `sc.nextInt()`로 각각의 입력을 받아들이는 부분은 사용자가 입력을 제공하기 이전까지는 저 구문에서 실행 흐름이 멈춰있는 것이죠. 

그렇다면 비동기는 어떨까요? 비동기는 간단하게 말해서 실행 흐름 (실행 스레드)에서 사용자의 입력을 기다리지 않는 경우입니다. 아 물론 위에서 봤던 계산기와 같은 사용자와 상호작용을 해야하는 경우라면 굳이 비동기를 선택할 이유는 없겠죠? 그럼 비동기가 필요한 예제를 살펴보겠습니다.

```java
HttpClient client = /* create client */
HttpRequest request = /* create request */

// Part A
CompletableFuture<HttpResponse<String>> responseFuture = client.sendAsync(request, BodyHandler.asString());

// Part B
responseFuture.thenAccept( response -> {
  if(response.statusCode == 200) {
    // onSuccess
  } else if (response.statusCode > 400) {
    // error handling
  }
})
```

음.. 갑자기 뭔가 이해하기 난해한 구문들이 나왔습니다. 괜찮습니다. 겁먹지 않아도 되요. 무언가 어려운게 나오면 항상 우리는 겁을 먹고 이게 뭐시여 하는 버릇이 있는데, 그렇지 않아도 됩니다. 대게 이런 포스팅을 하게 되면 다 설명해야하거든요.. ㅎㅎㅎㅎ

자 그럼 비동기부터 시작해보겠습니다. 위에서 동기는 실행 흐름이 사용자 입력 또는 API 호출이 끝날때까지 기다린다고 말씀을 드렸었는데요. 비동기는 당연히 이와는 반대로 실행 결과를 메인 스레드에서 기다리지 않습니다. 내부적으로 핸들러를 등록하고, 멀리 원정을 보낸 메소드 콜이 결과 값을 가지고 돌아오면, 미리 등록했던 핸들러에서 결과를 처리하게 되는 것입니다.

위에서 살펴봤던 예제에서는 `Part A`에서 `sendAsync` 메소드를 호출하고 결과가 나오면 `Part B`에서 `thenAccept` 메소드 내부에 등록되어 있는 Consumer가 수행되는 것입니다. 

### FYI
 `CompletableFuture`는 Java8에서 소개된 `Future` 인터페이스를 구현하고 있는 구현체입니다. 기존의 `Future`에서는 비동기 프로그래밍을 지원하기는 했지만, 결과를 얻끼 위해서는 `.get()`메소드를 사용하는 등의 결론적으로는 동기 프로그래밍을 어느정도 강제하는 포지션이어서, 진정한 비동기 프로그래밍을 지원하기 위해서(?) 소개된 패키지 입니다. (~~이럴꺼면 그냥 처음부터 잘하지~~)

