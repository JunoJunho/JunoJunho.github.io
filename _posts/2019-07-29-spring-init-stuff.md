---
title: Spring에서의 bean-init, static, constructor, @PostConstruct
excerpt: 미묘한 Ordering
toc: true
toc_sticky: true
tags: [Java, Spring]
---

하루 종일 삽질했습니다.
-------

제목의 4가지 녀석들의 미묘한 관계 때문에 오늘 하루종일 삽질 했습니다.

회의가 많으 날에는 가뜩이나 일을 많이 못하긴 하지만, 오늘 같이 기술적 난이도가 높지 않은 일에 대해서 삽질을 오래하게 되면 개인적으로 지치게 됩니다. 어찌저찌 집에 가기 전까지는 문제를 해결하였으므로 블로그에 포스팅을 남겨놓음으로써 후대에 똑같은 일이 발생했을 때 오랜 시간을 쓰는 것을 미연에 방지하고자 합니다.

스프링 초기화 방식에는 크게 4가지 키워드가 있습니다.
-----------

#### Bean init, Constructor, static, @PostConstruct

저는 현재 회사에서 xml 관련 설정들을 type checking을 더 강하게 할 수 있는 Java-based Configuration으로 바꾸는 작업을 하고 있는데요. (왜 바꾸는 것이 더 좋은지에 대한 내용 자세한 건 [이곳](https://stackoverflow.com/questions/29162278/benefits-of-javaconfig-over-xml-configurations-in-spring) 에서..) xml이 Java code based로 바뀌다 보니 동시성 이슈를 비롯된 초기화 관련 이슈가 발생하였습니다.

기존에는 spring 컨테이너가 알아서 넣어주던 것을 이제 생성자 혹은 다른 방식으로 초기화를 진행하도록 내부 리팩토링을 진행하면서 문제가 생긴 것인데요.

결론은 bean을 생성할 때 lazy하게 관련 빈을 생성하는 것이 아니라 후에 동시성 이슈가 발생하지 않도록 먼저 생성하도록 해결책을 만들고 코드를 수정하기 시작했는데요.

빌드 테스트부터 시작해서 매 Step마다 `Null Pointer Exception`이 발생하기 시작했습니다.

문제를 직접 적을 수는 없으니 (당연히 회사 업무 기밀..ㅎㅎ 이죠..), 간략하게 요약하면 초기화 순서를 제대로 알지 못해서 발생한 삽질이었습니다.

스프링에서는 `@Resource` 또는 `@Autowired` 태그를 통해서 하나의 빈에 다른 빈들을 주입할 수 있는데요. 위에 언급한 키워드 들과 연관성은 다음과 같이 할 수 있습니다.

1. static keyword로 정의된 변수 초기화
2. Constructor 실행
3. Dependency가 있는 빈 초기화 (Injection)
4. @PostConstruct 수행

제가 삽질한 부분은 1-3번만 삥삥 돌다가, Injection된 녀석들이 계속 `Null`값을 나타내게 되면서 끝없는 삽질을 하게 되었습니다. 

결국 static 이었던 녀석들을 instance variable으로 끌어 내리고, Constructor에는 property만, 이후 DI가 끝나면 별도의 `init` 메소드를 `@PostConstruct` annotation을 사용하여 초기화 하는 방식을 택했습니다.

문제를 알고 나니 깔끔하게 테스트 및 정상적으로 빈이 생성되는 것까지 확인할 수 있었습니다.

맨땅에 헤딩 보다는 문제를 파악하는 것에 집중하자
-------------

이번에 얻은 교훈입니다. `금방 끝날꺼야`라는 생각을 가지고 헤딩을 시작하니 도저히 풀리지 않더군요. 문제를 정확하게 정의하고 접근하는 방식을 취하도록 노력해야할 것 같습니다.

할일도 많은데,, 흐흐 그럼 오늘은 이만 총총
