---
title: 자바 동시성 - HashMap을 절대로 멀티스레드 이상 환경에서 쓰지 말아야 할 이유
excerpt: 그 큰 대기업에서도 HashMap으로 장애를 낸 적이 있습니다.

toc: true
toc_sticky: true
tags: [Programming, Software Engineering, Java]
categories:
- Software Engineering
last_modified_at: 2023-06-17T08:32:00
---


![Programming_Language_Pic](../../img/post/230617/firmbee-com-gcsNOsPEXfs-unsplash.jpg)

회사 업무에서 발견한 내용을 일반화 해서 설명드리겠지만, 자바로 프로그램을 구현하시는 분들이라면 가끔씩 할 수 있는 실수이기 때문에 공유합니다.

때는 바야흐로 1월경입니다. 저희 팀은 근래의 구조조정 바람이 불면서 Re-org (사내의 R&R을 다시 정리하는 거국적인 이벤트; 이 때 운이 없으면 팀이 갈려나가면서 해고가 될 수 있습니다.)를 맞이해서 인도에서 담당하던 기능을 저희 밴쿠버 팀이 받고, 저희 팀의 제품을 인도쪽으로 넘기게 되는 엄청나게 큰 행사를 맞이하게 되었습니다. 이전에도 제가 한번 소개해드렸지만, 인도에서 일하는 친구들은 (저의 개인적인 주관으로는) 꽤나 많이 코드를 `돌아가게만` 구현하는 경우가 많아서 힘든 상황이 왕왕 생깁니다. 

이번에도 그닥 다르지 않았습니다. 전체 도메인은 비슷했지만, 서로 다른 영역을 터치하는 제품을 개발하고 있어서인지, 인도에서 개발한 새로운 코드 베이스와 기존의 레거시를 인계받았고, 저희 팀에서 2년넘게 애지중지하면서 안정화시키고 있던 제품을 인도쪽에 넘겨 주었습니다. 그 과정에서 저희팀의 인도국적의 개발자 동료는 인도에 날아가서 2주간 양성 교육을 해야했지요.

뭐 서론은 이쯤하고, 본론으로 들어가서 인수한 제품 중에서 10년이 넘도록 유지보수 되고 있던 코드가 있었습니다. JSP + Servlet 와우 ㅎㅎㅎ 제가 대학생 때에도 이미 도서관에서 오래된 주제로 서가를 정리해본 기억이 있습니다. 아무튼 그 코드를 살펴보다보니 히스토리가 있는 코드를 발견했습니다. 그러다가 히스토리를 따라가 보았는데요. 

## HashMap을 Super user를 위해서 사용하다가 문제가 발생하였습니다.

HashMap은 맵기반 자료구조에서 가장 널리 사용되는 구조체라는 사실은 모두가 잘 알고 계실겁니다. 해싱을 사용하기 때문에 가의 대부분의 경우에 O(1)의 복잡도로 접근이 가능하고, 최악의 경우에도 O(N)의 복잡도로 구조체에 접근이 가능합니다. 문제는 해당 자료구조는 동시성을 지원하지 않는다는 점입니다.

[자바 공식 API](https://docs.oracle.com/javase/8/docs/api/java/util/HashMap.html) 를 살펴보시면 다음과 같은 문구를 확인해보실 수 있습니다.

> Note that this implementation is not synchronized. If multiple threads access a hash map concurrently, and at least one of the threads modifies the map structurally, it must be synchronized externally.

네, 정확하게 써있습니다. 자바에서 동시성의 구애를 받지 않도록 설계된 synchrnoized 키워드가 적용되어 있지 않습니다. 성능상으로 해당 키워드가 적용되어 있으면, JVM에서 의도적으로 동시에 한 스레드만 구조체에 접근하도록 하기때문에 느려질 가능성이 있습니다.

해당 코드는 이 싱글톤으로 선언된 HashMap으로 admin 사용자가 사용할 수 있는 권한을 맵 구조체로 사용하고 있었습니다. 하지만 온보딩한 비즈니스가 많고, 여러 개발자가 동시에 이 계정을 사용하려다보니, 하나의 서버 인스턴스에서 동시에 여러개의 이 구조체를 초기화 하려는 시도가 발생하였고, 하나가 실행되는 와중에 다른 스레드가 그 기록을 덮어쓰기 하면서 각종 이슈가 발생하기 시작했습니다.

## Java의 Multi Thread 프로그래밍에서는 HashMap을 사용하지 마세요.

웹서버에서도 일반적으로 1개의 Request당 1개의 Thread 가 해당 리퀘스트를 핸들링하는 것이 일반적입니다. 만약 어떤 데이터를 저장하는 것의 내부 구조체를 HashMap을 사용하고 있다면 매우 높은 확률로 요청이 몰리는 타이밍에 이슈가 발생할 가능성이 있습니다. 이는 여러 스레드가 HashMap에 동시에 액세스하면 충돌이 발생할 수 있기 때문입니다.

모든 멀티스레딩 환경이 동일합니다.

## 대신 ConcurrentHashMap을 사용하세요

Multi Thread 프로그래밍에서는 ConcurrentHashMap을 사용하는 것이 좋습니다. ConcurrentHashMap은 스레드 안전을 보장하기 위한 여러 기능을 갖추고 있습니다. 또한 성능도 HashMap에 비해 우수합니다. 많은 곳에서 설명하고 있는 해당 구조체에 대한 설명을 [참조](https://www.geeksforgeeks.org/concurrenthashmap-in-java/)해보시면, Java를 개발한 석박사의 힘이 동기화 이슈를 갖지 않도록 + 동시에 한 스레드만 접근이 가능하도록 하는 Locking 시간을 최소한으로 가져가서 성능상의 이슈를 최대한 배재하도록 설계하였습니다.

따라서 Multi Thread 환경에서는 항상 ConcurrentHashMap을 사용하도록 권장합니다.

어떻게 쓰냐구요?

Java에서 HashMap과 ConcurrentHashMap은 모두 Map 인터페이스를 구현하고 있습니다. 두 자료구조는 모두 Key-Value 형태의 자료구조로서, Key와 Value는 모두 Object 타입이어야 합니다.

### HashMap 사용 예제

```
Map<String, String> map = new HashMap<>();
map.put("key1", "value1");
map.put("key2", "value2");

String value = map.get("key1");
System.out.println(value); // output: "value1"

for (String key : map.keySet()) {
    String value = map.get(key);
    System.out.println(key + ": " + value);
}
```

### ConcurrentHashMap 사용 예제

```
ConcurrentMap<String, String> map = new ConcurrentHashMap<>();
map.put("key1", "value1");
map.put("key2", "value2");

String value = map.get("key1");
System.out.println(value); // output: "value1"

for (String key : map.keySet()) {
    String value = map.get(key);
    System.out.println(key + ": " + value);
}
```

HashMap과 ConcurrentHashMap의 사용법은 거의 동일합니다. 다만, ConcurrentHashMap은 동시성을 지원하기 때문에, 여러 스레드가 동시에 접근해도 안전합니다. 따라서, Multi Thread 환경에서는 항상 ConcurrentHashMap을 사용하는 것이 좋습니다.