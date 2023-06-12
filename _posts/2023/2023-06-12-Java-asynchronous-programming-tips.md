---
title: Java - Some Tips for Asynchronous Programming
excerpt: 비동기는 어려워

toc: true
toc_sticky: true
tags: [Programming, Software Engineering, Java]
categories:
- Software Engineering
last_modified_at: 2023-06-12T14:06:00
---


![Programming_Language_Pic](../../img/post/230517/pexels-sabrina-gelbart-249798.jpg)

Java 비동기 프로그래밍이란?
--------
Java에서의 비동기 프로그래밍이란, 하나의 스레드가 작업을 수행하는 동안 다른 스레드가 대기하지 않고 병렬적으로 다른 작업을 수행할 수 있도록 하는 프로그래밍 방식입니다. 비동기 프로그래밍은 성능을 향상시키고, 자원을 효율적으로 활용하고, 사용자 경험을 개선하는 데 도움이 됩니다. 다만 잘 사용했을 때 말이죠.

구현하는 방법
--------

#### **스레드 생성**

Thread 클래스나 Runnable 인터페이스를 사용하여 새로운 스레드를 생성하고, start() 메서드를 호출하여 실행합니다. 이 방법은 간단하지만, 스레드의 수와 생명주기를 관리하기 어렵고, 예외 처리나 결과 반환 등에 대한 추가적인 작업이 필요합니다.


```
public class ThreadExample {
    public static void main(String[] args) {
        // 첫 번째 스레드 생성
        Thread thread1 = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("Hello");
            }
        });
        // 두 번째 스레드 생성
        Thread thread2 = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("World");
            }
        });
        // 스레드 실행
        thread1.start();
        thread2.start();
    }
}
```

#### **스레드 풀 사용**

ExecutorService 인터페이스나 Executors 클래스를 사용하여 스레드 풀을 생성하고, submit() 메서드로 작업을 전달합니다. 이 방법은 스레드의 수와 생명주기를 자동으로 관리하고, Future 객체를 통해 작업의 상태와 결과를 얻을 수 있습니다. 하지만 Future 객체는 블로킹 방식이므로, 작업이 완료될 때까지 기다려야 하고, 여러 작업의 조합이나 예외 처리 등에 대한 기능이 부족합니다.

```
import java.util.concurrent.*;

public class ThreadPoolExample {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        // 고정된 크기의 스레드 풀 생성
        ExecutorService executor = Executors.newFixedThreadPool(2);
        // 네 개의 작업을 스레드 풀에 제출
        Future<Integer> future1 = executor.submit(() -> 1 + 1); // 2를 반환하는 작업
        Future<Integer> future2 = executor.submit(() -> 2 + 2); // 4를 반환하는 작업
        Future<Integer> future3 = executor.submit(() -> 3 + 3); // 6을 반환하는 작업
        Future<Integer> future4 = executor.submit(() -> 4 + 4); // 8을 반환하는 작업
        // 각 작업의 결과를 출력
        System.out.println(future1.get());
        System.out.println(future2.get());
        System.out.println(future3.get());
        System.out.println(future4.get());
        // 스레드 풀 종료
        executor.shutdown();
    }
}
```

#### **CompletableFuture 사용**

CompletableFuture 클래스는 Future 인터페이스의 구현체로, 비동기 작업의 결과를 나타냅니다. CompletableFuture는 Future에 비해 다양한 기능을 제공합니다. 예를 들어, 작업이 완료되면 콜백을 실행하거나(thenApply(), thenAccept(), thenRun() 등), 여러 작업을 조합하거나(allOf(), anyOf(), thenCompose(), thenCombine() 등), 예외 처리를 할 수 있습니다(exceptionally(), handle() 등). 또한 CompletableFuture는 논블로킹 방식이므로, 작업이 완료될 때까지 기다리지 않고 다른 작업을 수행할 수 있습니다.

```
import java.util.concurrent.*;

public class CompletableFutureExample {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        // 첫 번째 비동기 작업 생성
        CompletableFuture<Integer> future1 = CompletableFuture.supplyAsync(() -> 1 + 1); // 2를 반환하는 작업
        // 두 번째 비동기 작업 생성 (첫 번째 작업의 결과에 의존)
        CompletableFuture<Integer> future2 = future1.thenApply(x -> x * x); // 제곱을 반환하는 작업
        // 두 번째 작업의 결과를 출력
        System.out.println(future2.get()); // 4
    }
}
```

CompletableFuture는 잘 사용하면 코드가 이해하기 쉽고 깔끔하지만, 잘못 구현할 경우 코드가 대략 난감해질 수도 있으니 주의가 필요합니다.

#### **Reactor 사용**

Reactor는 Java에서 리액티브 프로그래밍을 구현하기 위한 라이브러리입니다. 리액티브 프로그래밍이란, 데이터 스트림과 변화를 기반으로 하는 비동기 프로그래밍 패러다임입니다. Reactor는 Flux와 Mono라는 두 가지 타입의 데이터 스트림을 제공합니다. Flux는 0개 이상의 데이터를 전달하는 스트림이고, Mono는 최대 1개의 데이터를 전달하는 스트림입니다. Reactor는 이러한 스트림에 대해 다양한 연산자를 제공하여 비동기 작업을 선언적으로 구성할 수 있습니다. 또한 Reactor는 스케줄러를 통해 작업을 병렬적으로 수행할 수 있습니다.

```
import reactor.core.publisher.*;
import reactor.core.scheduler.*;

public class ReactorExample {
    public static void main(String[] args) {
        // Flux 생성
        Flux<Integer> flux = Flux.just(1, 2, 3, 4);
        // 각 요소에 대해 비동기 작업 수행 (제곱을 반환하는 작업)
        flux = flux.flatMap(x -> Mono.fromCallable(() -> x * x)
                .subscribeOn(Schedulers.parallel()));
        // 결과를 출력
        flux.subscribe(System.out::println);
    }
}
```

Reactor와 관련하여서는 [라인의 기술 블로그 포스팅](https://engineering.linecorp.com/ko/blog/reactive-streams-with-armeria-1)이 정말 잘 다듬어져있다고 생각합니다.

장단점
--------
Java 비동기 프로그래밍의 **장점**은 다음과 같습니다.

1. 성능 향상: 비동기 프로그래밍은 작업을 병렬적으로 수행할 수 있으므로, 응답 시간을 줄이고, 처리량을 늘릴 수 있습니다. 예를 들어, 웹 서버에서 여러 요청을 동시에 처리할 수 있습니다.
2. 자원 활용: 비동기 프로그래밍은 작업이 완료될 때까지 대기하지 않고, 다른 작업을 수행할 수 있으므로, 자원을 낭비하지 않고 효율적으로 활용할 수 있습니다. 예를 들어, I/O 작업이 진행되는 동안 CPU 작업을 수행할 수 있습니다.
3. 사용자 경험: 비동기 프로그래밍은 작업이 오래 걸리더라도 사용자에게 빠른 응답을 제공할 수 있으므로, 사용자 경험을 개선할 수 있습니다. 예를 들어, GUI 애플리케이션에서 메인 스레드가 블로킹되지 않고, 사용자의 입력에 반응할 수 있습니다.

Java 비동기 프로그래밍의 **단점**은 다음과 같습니다.

1. 복잡도 증가: 비동기 프로그래밍은 동기 프로그래밍에 비해 코드의 가독성과 유지보수성이 떨어질 수 있습니다. 예를 들어, 콜백 지옥에 빠지거나, 데드락이나 경쟁 상태와 같은 동시성 문제에 직면할 수 있습니다.
2. 디버깅 어려움: 비동기 프로그래밍은 작업의 실행 순서와 타이밍이 예측하기 어렵고, 스택 트레이스가 제공되지 않거나 의미가 없을 수 있습니다. 따라서 디버깅이 어려울 수 있습니다. 
3. 테스트 어려움: 비동기 프로그래밍은 작업의 결과가 즉시 반환되지 않고, 다른 스레드에서 실행될 수 있으므로, 테스트 코드를 작성하고 실행하기 어려울 수 있습니다. 예를 들어, 시간 제한이나 동기화 기법 등을 사용해야 할 수 있습니다. 관련하여 제가 [이미 포스트](https://junojunho.github.io/learning/how-to-test-whenComplete-in-java/)를 작성한 적이 있으니 한번 참고해보시면 좋을 것 같습니다.

그래서 써요 말아요?
------
비동기 프로그래밍은 성능 향상, 자원 활용, 사용자 경험 개선 등의 장점이 있지만, 복잡도 증가, 디버깅 어려움, 테스트 어려움 등의 단점도 있습니다. 따라서 상황에 맞게 적절한 방법과 도구를 선택하고, 주의 깊게 설계하고 구현해야 합니다.

ㅎㅎ 말이 어렵죠? 단일 스레드보다 여러가지 일을 한번에 수행해야하는 환경에서는 비동기 프로그래밍 + 동시성 프로그래밍은 필수입니다. 하지만 기술을 잘 쓰까서 쓰지 않으면 오히려 성능에 해가 될 수 있으니 주의해야합니다.