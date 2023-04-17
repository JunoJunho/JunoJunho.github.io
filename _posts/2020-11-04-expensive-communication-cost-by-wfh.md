---
title: Very Very Expensive Communication Cost
excerpt: WFH이기 때문에 발생하는 불필요한 비용

toc: true
toc_sticky: true
tags: [Amazon, Software Engineering]
categories:
  - Software Engineering
last_modified_at: 2020-11-04T22:35:00
---

아 이걸 다르게 어떻게 설명해?
--------

오늘 말로만 듣던 언어의 장벽을 느꼈습니다. 저는 토종 한국인 (그 유명한 어학 연수도 안가보고 한국에서 영어를 배우고 자라서 본토 발음이라고는 영화에서만 접해본 그 한국인!)이기 때문에 영어는 저에게 철저히 Second Language입니다. 사실 작년에 이 회사에 합격하고 캐나다에 랜딩하기 이전까지만 해도 저는 개인적으로 영어를 조금 잘 하는 사람인줄 알았습니다. 하지만 세상은 그렇게 만만하지 않더군요. 첫날부터 정말 택시기사 아저씨, 임시 숙소 매니저, 컨시어지에서 하는 말을 절반은 알아듣고 절반은 못알아 듣겠더군요. 그리고 일주일 뒤에 바로 회사 업무를 시작했으니 회사에서는 아직도 개그나 일상 이야기는 거의 회의 시간에 나오는 70% 정도의 저에 해당하지 않는 이야기는 흘려 듣고 있습니다. (이러지 않기로 이 회사에 입사하면서 다짐 했었는데 참..)

요새 저는 사내 업무에 필요한 분산 애플리케이션 스크립트를 작성하고 있습니다. 워낙 데이터가 큰 업무와 정합성을 요구하는 업무이다보니, 같이 일하는 co-worker들과 커뮤니케이션을 해야할 일이 굉장히 많이 생깁니다. 한번 작업 돌아가면 길면 몇시간도 돌아가는데 요구사항 잘못 파악해서 긴 시간의 작업량을 날려버릴 수도 있고, 제가 분산 자원을 사용하는 동안에는 엄연히 다른 잡들도 돌아가지 못하니깐요. (이해가 안가시죠? ㅎㅎ 하지만 Amazon의 Leadership Principle중 Frugality를 참조하시면 이해가 되실 겁니다.)

오늘은 점심을 먹고 가볍게 운동을 하고 난 뒤에 하루 종일 커뮤니케이션에만 매달리게 되었습니다. 잡의 태스크 중에 어떤 기능에 대한 해석을 두고 논의가 계속 이어졌습니다. 저와 이야기하는 상대방은 해당 기능의 동작이 태스크를 끝난 이후 결과에 적용이 되는 기능이다라고 이야기를 했고, 저는 아무리 문서를 읽어보고 테스트를 돌려보아도 작업 도중에 특정한 조건 안에서만 동작하기 때문에 결과 값과는 아무런 관련이 없다라고 이해하게 되었습니다.

`네, 아시겠지만 끝없는 평행선이죠.`

애플리케이션 작성자인 제가 결국 해당 애플리케이션 프레임워크 팀과 이야기 해보기로 했고, 여기서도 다시 처음 왜 이 문제에 접근하게 되었는지, 어떤 컨텍스트인지, 그래서 내가 이해하는 기능은 이런 방식인데 이렇게 동작하는 것이 맞는지, 예제를 포함해서 다시 이걸 처음부터 설명할 생각을 하니, 머리가 깜깜해졌습니다. 한 30분 정도 들여서 설명을 하고나니, 일단 팀원은 왜 그래야하는지, 왜 설명이 이렇게 흘러가는지, 엄연히 따져보면 해당 기능은 그런 것이 아니다라고 이야기 하고 생각보다 커뮤니케이션이 길어지게 되었습니다.

그리고 다시 원래 요구사항을 뿜어내는 사람에게 돌아와서 결과를 이야기해주고 (결론은 내가 한 말이 맞다. 요 결론을 얻기 위해서 2시간을..) 정성것 설명해 주었습니다.

`Hey Junho, I still did not clearly understand what you said. Can you rephrase in another way?`

응? 지금 내가 무언갈 잘못 보았나.. 싶었습니다. 이 긴 내용을 다시 다른 언어로 설명해 달라고? 난 저기에 설명할 수 있는 단어가 저거밖에 없는데? 헐..

왠지 길어질 거 같으니깐 티켓으로 따서 따로 태스크 포인트를 할당하고 내가 쿼리로 가능한지 확인해볼께
----------

정말 이 말을 듣는 순간 읭? 했습니다. 사실 처음부터 데이터는 s3 bucket에 잘 적재되고 있었기 때문에 단순한 필터링 작업을 위해서 필요한 기능이었거든요. 요구사항을 전달해준 사람 입장에서도 이게 그렇게 오래걸릴 일이 아닌데, 이 이야기 저 이야기 mismatch 되는 부분을 다시 맞추어 나가면서 일을 진행하다보니 본인도 1시간이면 해결될 일인거 같았는데 이게 하루 종일 걸릴줄은 예상을 전혀 못했던 것이죠. 결국 저도 저의 오늘 예상되어있던 일정을 다 미루고 due가 있던 태스크는 다음 스프린트까지로 미루어야 했습니다.

왜 이런일이 발생한 걸까요?

사실 이러한 사실 관계 확인, 그 이후에 해결책을 찾아서 누가 작업을 할지 정하는 등의 커뮤니케이션들은 오프라인일 경우 대개 30분 내외로, 설명이 아무리 길어진다고 해도 1시간 내외로 끝나는 이야기입니다. 게다가 이러한 내용을 잘 알고 있는 팀원이 한명 더 있으면 그 사람이 정리해주면 되니까 더 간단하게 해결될 일이죠. 하지만 지금은 팬대믹 시대에 재택으로 하다보니 모든 것이 text-base 로 의사소통이 이루어집니다. 말로 하면 3초면 날아가는 일들이, 글로 다듬어서 제대로 말해야하니 몇배로 늘어납니다. 게다가 채팅은 동기라고 생각하지만 비동기 의사소통이기 때문에 전달되어도 응답을 받기까지는 빠르면 즉각적으로, 늦으면 몇분이 걸리게 됩니다. 30분이면 될일이 최소 1시간, 길어지면 오늘 저의 경우처럼 하루 종일 걸리는 거죠.

그러면 어떻게 해결해야 할까?
------

네, 그걸 알면 제가 오늘 이 고생을 안했겠죠... 적당히 생각해보면 이렇게 정리해볼 수 있습니다.

- 이슈를 정확히 파악하고, 이슈를 clear하게 기술해 낼 수 있어야 한다.
- 딴길로 새지 않고 이슈만 다루고, 내용을 서술해준다.
- 필요한 경우, 적절히 다른 단어를 써가면서 설명을 할 수 있어야 한다.

정리하고 나니, 회사에서 괜히 Leadership Principle을 배우는 것이 아니었습니다. 다 연결되어 있더군요.. 제길.. 사실 영어를 제가 잘 했으면 이럴 일도 없었겠죠. 다들 영어 공부 합시다. 흑... IELTS도 드릅게 어렵던데... 