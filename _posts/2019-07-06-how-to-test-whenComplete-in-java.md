---
layout: post
title: 자바 비동기 - AsyncTaskExecutor 테스트하기
subtitle: whenCompleteAsync 콜백은 어떻게 테스트 해야할까
gh-repo: junojunho/JunoJunho.github.io
gh-badge: [star, fork, follow]
tags: [Software Testing, Async, Java]
---

비동기는 사용하기도, 테스트하기도 어렵습니다.
-------

기본적으로 비동기는 말 그대로 비동기로 실행되는 프로그램의 흐름이 언제 끝날지 알 수 없기 때문에, 구현을 진행하고서도 어떻게 돌아갈지 알수 없기 때문에 어렵고, 비동기 코드를 테스트 할 때에도 문제입니다. 

실제 비동기를 사용하는 경우에는 대다수는 HTTP Client의 사용, IO, Storage API 호출 등의 시간이 오래걸리는 작업을 비동기로 처리하기 때문에, `대충 잘 동작할꺼야~`라는 믿음으로 크게 테스트를 하지 않고 지나가는 경우가 많습니다.

이런 IO가 오래걸리는 작업을 비동기로 작업하는 것에서부터 요즈음 트래픽이 많은 환경에서는 모든 리퀘스트를 감당할 수 없기 떄문에, 리얼타임의 반응을 굳이 보여주지 않아도 되는 로직은 대부분 비동기로 전환하려는 노력이 많아지고 있습니다.


그렇다면 비동기, 어떻게 테스트 해야할까?
-------

> 우선 비동기에 대한 기본적인 사용방법을 아신다는 가정하에서 작성하였습니다.

이번 포스팅에서는 특정 작업을 다른 `asyncTaskExecutor`로 넘겨진 경우 해당 로직을 어떻게 테스트 하는지 알아보도록 하겠습니다.

우선 가장 기본적인 데이터 Entity 클래스 입니다.

```Java
package async;

import lombok.Data;

@Data
public class Polygon {

    private long numOfPoints;

}
```

매우 간단합니다. `Polyogon` 클래스는 내부에 `numOfPoints`, 꼭지점 개수인가요. lombok의 `@Data` 어노테이션을 사용해서 불필요한 보일러 플레이트 코드를 제거하였습니다.

```Java
package async;

import org.springframework.core.task.AsyncTaskExecutor;
import org.springframework.scheduling.concurrent.ConcurrentTaskExecutor;

import java.util.concurrent.CompletableFuture;

public class AsyncExecutor {

    private AsyncTaskExecutor taskExecutor;
    Polygon polygon;

    public AsyncExecutor() {
        taskExecutor = new ConcurrentTaskExecutor();
        polygon = new Polygon();
    }

    public CompletableFuture<Long> submitTask() throws Exception{
        return CompletableFuture.supplyAsync( () -> Math.round(Math.random() * 7 + 1) );
    }

    public void handleTask() throws Exception{
        this.submitTask()
                .whenCompleteAsync((numOfDigit, e) -> {
                    if (e != null) {
                        System.out.println(e);
                    } else {
                        System.out.println("From AsyncExecutor: " + handlePolygon(numOfDigit));
                    }
                }, taskExecutor);
    }

    public long handlePolygon(long digit) {
        polygon.setNumOfPoints(digit);
        return polygon.getNumOfPoints();
    }

}
```

비동기 작업을 수행하는 `AsyncExecutor` 클래스입니다. 내부 변수로 동시성을 가지고 태스크를 실행하는 `Executor`를 가지고 있으며, 간단히 테스트를 위해 `polygon`객체를 초기화 하였습니다.

태스크를 입력받는 `submitTask`에서는 임의의 꼭짓점을 반환하여 `CompletableFuture`로 반환하였습니다. (비동기로 태스크를 걸어주기 위함) 그리고 `handleTask` 메소드에서는 위의 `submitTask` 메소드를 호출하고, 이 메소드 호출이 끝날 때, 앞서 생성자에서 만들어 냈던 `taskExecutor`로 이하 콜백 메소드를 실행하게 태스크를 던졌습니다.

즉, 메인 스레드에서는 `submitTask`, `handleTask` 메소드의 `whenCompleteAsync` 메소드 체인 이 시작하기 이전까지만 동작하게 되는 것이지요. 이후 콜백은 앞서 말씀드렸듯이 비동기 스레드에서 실행을 하게 됩니다.

##### 잘 동작하는지 볼까요?

```Java
package async;

public class AsyncExecutorTester {

    private Polygon polygon;

    public AsyncExecutorTester() {
        polygon = new Polygon();
        polygon.setNumOfPoints(3);
    }

    public void handleTest() {
        System.out.println("Before:" + polygon.getNumOfPoints());

        AsyncExecutor asyncExecutor = new AsyncExecutor();
        try {
            asyncExecutor.handleTask();
            System.out.println("Accept Executor");
        } catch (Exception e) {
            System.out.println("Exception ! " + e);
        }
    }

    public static void main(String[] args) {

        new AsyncExecutorTester().handleTest();

        System.exit(0);
    }
}

// Output
// Before:3
// Accept Executor
// From AsyncExecutor: 6
```

잘 동작하는 군요. 그럼 이제 테스트로 넘어가 봅시다.

#### 어떻게 테스트 할까?

고민입니다. 비동기 태스크가 (물론 99.99999%의 확률로) 넘어가자마자 실행되겠지만, 모종의 이유로 실행이 되지 않는다면, 정상적인 동작임에도 불구하고 테스트 코드가 실패하는 경우를 맞이할 수도 있습니다. 즉, `Thread.sleep`과 같은 방식으로 메인 스레드를 잠시 멈추는 작업으로는 일관된 코드의 행위를 보장할 수 없게 됩니다.

그럼 어떻게 해야할까요? 긴말 안하고 코드부터 보시죠.

```Java
package async;

import org.junit.Rule;
import org.junit.Test;
import org.mockito.InjectMocks;
import org.mockito.Spy;
import org.mockito.junit.MockitoJUnit;
import org.mockito.junit.MockitoRule;
import org.springframework.core.task.AsyncTaskExecutor;
import org.springframework.core.task.SimpleAsyncTaskExecutor;

import java.util.concurrent.CompletableFuture;

import static org.awaitility.Awaitility.await;
import static org.mockito.Mockito.spy;
import static org.mockito.Mockito.when;

public class AsyncExecutorTesterTest {

    @Rule
    public MockitoRule rule = MockitoJUnit.rule();

    @InjectMocks
    private AsyncExecutor executor = spy(new AsyncExecutor());

    @Spy
    private AsyncTaskExecutor taskExecutor = new SimpleAsyncTaskExecutor();

    @Test
    public void testHandleTest() throws Exception {
        when(executor.submitTask()).thenReturn(CompletableFuture.completedFuture(10L));

        executor.handleTask();
        await().until(() -> executor.polygon.getNumOfPoints() == 10L);
    }

}
```

`taskExecutor`를 새로 만들어 주고, `Await`이라는 패키지를 사용해서 `await().until(() -> executor.polygon.getNumOfPoints() == 10L);` 로 정말 의도한대로 동작하는지 (앞서서 `submitTask`가 무조건 10을 리턴하게 한 뒤,) 확인해보았습니다.

즉, 위의 테스트 코드는 
> `executor`의 `polygon` 객체의 내부 변수 (`numOfPoints`)가 10이 될 때까지 (`executor`)가 올바르게 동작할 때 까지 기다려라. 

라는 의미로 해석 할 수 있습니다.

##### 무한정 기다릴 수도 있지 않아요?

아닙니다!

내부 구현을 잠시만 들여다 봅시다.

```Java
// org.awaitability.core.ConditionFactory.java
public void until(Callable<Boolean> conditionEvaluator) {
      until(new CallableCondition(conditionEvaluator, generateConditionSettings()));
  }

private ConditionSettings generateConditionSettings() {
        Duration actualPollDelay = definePollDelay(pollDelay, pollInterval);

        if (actualPollDelay.isForever()) {
            throw new IllegalArgumentException("Cannot delay polling forever");
        }

        Duration timeout = timeoutConstraint.getMaxWaitTime(); // 얼마나 기다릴 것인가?
        final long timeoutInMS = timeout.getValueInMS(); // 최대로 대기할 시간 받아오기
        
        ...
        
        // timeoutConstraint를 대기할 condition으로 제공
        return new ConditionSettings(alias, catchUncaughtExceptions, timeoutConstraint, pollInterval, actualPollDelay,
                conditionEvaluationListener, exceptionsIgnorer, executorLifecycle);
    }

private static volatile WaitConstraint defaultWaitConstraint = AtMostWaitConstraint.TEN_SECONDS; // defaultWaitConstrait, 10초    
```

즉, 10초를 기달려도 해당 스레드가 안끝난다면 fail을 발생시키게 됩니다.

Timeout의 값은 필요에 따라서 적절한 조정이 필요하겠죠.? ㅎㅎ

그럼 오늘은 여기서 마무리 짓도록 하겠습니다~