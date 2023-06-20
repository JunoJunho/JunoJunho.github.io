---
title: 자바, 코틀린 동시성 - 자바의 멀티스레딩과 코틀린의 코루틴 비교해보기
excerpt: 둘다 좋습니다. 근데 코루틴도 좋네요?

toc: true
toc_sticky: true
tags: [Programming, Software Engineering, Java, Kotlin]
categories:
- Software Engineering
last_modified_at: 2023-06-19T20:32:00
---

![Programming_Language_Pic](../../img/post/230617/firmbee-com-gcsNOsPEXfs-unsplash.jpg)

Intro
----
개발자 업계에 몸을 담고 있는 사람이라면 멀티스레딩, 동시성 프로그래밍은 피할 수 없는 주제입니다. 언제나 면접때마다 우리를 괴롭히면서 힘들게 하지요. 하지만 제대로 사용할 수 있다면, 진정한 프로그래밍의 세계에서 활약할 수 있는 활로를 만들어 줄 것입니다. 한국은 역시나 자바 공화국입니다. 그 자바 공화국에서 무섭게 치고 올라오는 언어는 젯브레인에서 만들어낸 코틀린입니다. 코틀린은 JVM위에서 동작하기 때문에 자바와 쉽게 통합(Integrate)될 수 있고, 문법도 비슷하면서 몇가지 더 강력한 기능들을 제공하기 때문에 자바 개발자들이 코틀린으로 많이 넘어가는 추세입니다. 현재 제가 재직하고 있는 회사에서도 새롭게 시작하는 프로젝트는 코틀린을 사용하자는 움직임이 많습니다. 그만큼 코틀린이 레퍼런스도 많이 쌓이면서 잘 되고 있다는 뜻이겠죠?

코틀린의 코루틴과 자바의 멀티스레딩은 비동기 프로그래밍과 병렬 처리에 중요한 역할을 하는 기술입니다. 이번 포스트에서는 두 가지 접근 방식을 비교해보고, 각각의 장단점과 활용 사례를 살펴보겠습니다.

코틀린의 코루틴과 자바의 멀티스레딩을 간단하게 살펴보기
---
간단하게 데이터를 어딘가에서 가져와서 UI에 업데이트 하는 로직이 있다고 가정해보고 이를 코루틴과 멀티스레딩으로 각각 살펴보겠습니다.

먼저 코틀린의 코루틴입니다. 코루틴은 비동기 프로그래밍을 구현하기 위한 기법으로, 비선점형 스레드와 유사한 동작 방식을 가지고 있습니다. 아래는 간단한 코루틴의 예시 코드입니다:

```
import kotlinx.coroutines.GlobalScope
import kotlinx.coroutines.async
import kotlinx.coroutines.delay
import kotlinx.coroutines.launch
import kotlinx.coroutines.runBlocking

fun main() {
    // 코루틴을 사용한 비동기 작업 예시

    // 비동기 작업 1: 데이터 가져오기
    val fetchDataDeferred = GlobalScope.async {
        // 데이터 가져오는 로직
        fetchData()
    }

    // 비동기 작업 2: UI 업데이트
    GlobalScope.launch {
        val data = fetchDataDeferred.await()
        // UI 업데이트 로직
        updateUI(data)
    }

    runBlocking {
        // 모든 비동기 작업이 완료될 때까지 대기
        delay(2000)
    }
}

suspend fun fetchData(): String {
    // 데이터를 가져오는 시간이 오래 걸리는 작업이라고 가정
    delay(2000)
    return "Data"
}

fun updateUI(data: String) {
    // UI 업데이트 로직
    println("Updating UI with data: $data")
}
```

`GlobalScope.async` 함수를 사용하여 비동기 작업을 수행합니다. 첫 번째 Deferred인 fetchDataDeferred는 `fetchData()` 함수를 비동기적으로 실행하여 데이터를 가져오는 작업을 수행합니다. 그리고 `GlobalScope.launch` 함수를 사용하여 fetchDataDeferred.await()을 통해 데이터가 준비될 때까지 대기하고, 대기가 완료되면 데이터를 UI에 업데이트하는 updateUI() 함수를 비동기적으로 실행합니다.

`GlobalScope`에 관하여는 나중에 따로 포스팅을 할애해서 살펴보겠습니다.

다음은 자바의 멀티스레딩입니다. 역시 동일한 예제이니 차이점을 중점적으로 살펴보면 좋을것 같습니다.


```
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;

public class MultiThreadingExample {

    public static void main(String[] args) {
        // CompletableFuture를 사용한 비동기 작업 예시

        // 비동기 작업 1: 데이터 가져오기
        CompletableFuture<String> fetchDataFuture = CompletableFuture.supplyAsync(() -> {
            // 데이터 가져오는 로직
            String data = fetchData();
            return data;
        });

        // 비동기 작업 2: UI 업데이트
        CompletableFuture<Void> updateUIFuture = fetchDataFuture.thenAccept(data -> {
            // UI 업데이트 로직
            updateUI(data);
        });

        try {
            // 모든 비동기 작업이 완료될 때까지 대기
            updateUIFuture.get();
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
    }

    private static String fetchData() {
        // 데이터를 가져오는 시간이 오래 걸리는 작업이라고 가정
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "Data";
    }

    private static void updateUI(String data) {
        // UI 업데이트 로직
        System.out.println("Updating UI with data: " + data);
    }
}
```


이 예시 코드에서는 `CompletableFuture.supplyAsync()` 메소드를 사용하여 비동기 작업을 수행합니다. 첫 번째 CompletableFuture인 fetchDataFuture는 fetchData() 메서드를 비동기적으로 실행하여 데이터를 가져오는 작업을 수행합니다. 그리고 `thenAccept` 메소드를 사용하여 fetchDataFuture 작업이 완료되면 데이터를 UI에 업데이트하는 updateUI() 메서드를 비동기적으로 실행합니다. updateUIFuture.get()을 호출하여 모든 비동기 작업이 완료될 때까지 대기합니다.

이렇게 CompletableFuture를 사용하여 멀티스레딩을 구현하면 병렬 처리와 비동기 작업을 쉽게 구현하고, 작업의 결과를 편리하게 핸들링할 수 있습니다.

그러면 무엇이 다를까요 ?
----

### 문법

- 코루틴: 코루틴은 suspend 함수를 사용하여 비동기 작업을 일시 중단하고 재개할 수 있습니다. 이는 코드의 가독성과 유지보수성을 향상시킵니다.
- Java 멀티스레딩: 멀티스레딩은 자바의 Thread 클래스나 스레드 풀, CompletableFuture을 사용하여 병렬 처리를 수행합니다.

### 가독성

- 코루틴: 코루틴은 비동기 작업의 흐름을 자연스럽게 표현할 수 있으며, 동기 코드와 유사한 구조를 갖추므로 가독성이 높습니다.
- 멀티스레딩: 스레드를 직접 다루는 멀티스레딩은 동기화 문제와 스레드 간 통신에 대한 추가적인 관리가 필요하여 코드의 복잡성이 증가할 수 있습니다.

### 성능

- 코루틴: 코루틴은 비동기 작업의 일부를 일시 중단하고 다른 작업으로 전환함으로써 효율적인 리소스 관리를 할 수 있습니다.
- 멀티스레딩: 멀티스레딩은 병렬 처리를 위해 여러 스레드를 사용하여 CPU 코어를 최대한 활용할 수 있습니다. 그러나 스레드 간의 컨텍스트 전환에 따른 오버헤드가 발생할 수도 있습니다.

### 안정성

- 코루틴: 코루틴은 try-catch 블록을 통해 예외 처리를 할 수 있으며, 비동기 작업 중에도 예외를 적절하게 처리할 수 있습니다.
- 멀티스레딩: 멀티스레딩은 스레드 간의 예외 처리가 복잡할 수 있으며, 스레드 상태를 올바르게 관리하지 않을 경우 예기치 않은 동작이 발생할 수 있습니다.

### 활용 예시

- 비동기 API 호출: 코루틴과 멀티스레딩을 사용하여 비동기 API 호출을 처리하는 방법을 실제 예시와 함께 살펴봅니다.
  - 코루틴: async와 await를 사용하여 비동기 작업을 간편하게 조합하고, 결과를 기다릴 수 있습니다.
  - 멀티스레딩: 스레드를 생성하여 API 호출을 병렬로 처리하고, 각 스레드의 결과를 취합할 수 있습니다.
- 병렬 처리: 대량의 데이터를 병렬로 처리하는 데 각각의 접근 방식이 어떻게 활용될 수 있는지 다양한 시나리오를 제시합니다.
  - 코루틴: 비동기 스트림을 통해 대량의 데이터를 효율적으로 처리하고, 병렬로 실행될 수 있는 작업들을 조율할 수 있습니다.
  - 멀티스레딩: 데이터를 분할하여 여러 스레드에서 병렬로 처리하고, 작업의 결과를 통합할 수 있습니다.

### 장단점
- 코루틴: 코루틴의 장점은 가독성과 코드 이해의 용이성, 효율적인 리소스 관리입니다. 그러나 코루틴은 단일 스레드에서 실행되므로 CPU 바운드 작업에는 적합하지 않을 수 있습니다.
- 멀티스레딩: 멀티스레딩은 병렬 처리를 효과적으로 수행할 수 있지만, 스레드 간의 동기화와 상태 관리에 주의해야 합니다. 또한, 스레드 생성과 컨텍스트 전환에 따른 오버헤드가 발생할 수 있습니다.

그렇다면 어떻게 해야할까요?
---

(진리의) 프로젝트 요구사항과 개발 환경에 따라 최적의 선택을 하는 데 도움이 되는 지침을 제시합니다. 코틀린이라면 코루틴을 사용하지 않을 이유가 없고 (Language-native support), Java를 사용한다면 ~~채신~~ 기술인 CompletableFuture를 사용하지 않을 이유가 없습니다. 네, 각 언어마다 꼭 사용하세요 두번 사용하세요. 대신 잘 사용하세요. 잘 사용하는 방법에 대하여도 나중에 한번 따로 다루어 보도록 하겠습니다.