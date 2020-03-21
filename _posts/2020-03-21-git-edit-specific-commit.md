---
title: git rebase로 특정 커밋 내용 수정하기
excerpt: 일한지 3년만에 터득한 방법

toc: true
toc_sticky: true
tags: [git]
last_modified_at: 2020-03-21T22:58:00
---


### cherry-pick 하기

기업에서는 나의 똥 코드를 한번에 develop 이든, master branch 이든 한번에 반영하기 어렵습니다. (당신이 뭘 작성했을 줄 알고 넣어줍니까) 항상 pull-request (혹은 merge request)를 통해서 코드 리뷰를 거친 이후에 리뷰어들의 승인(approve)절차가 있고 난 다음에 코드 베이스에 반영을 할 수 있습니다.

보통 develop branch로 들어간 코드는 특정 테스트 등의 검증 과정을 통과하면 이제 실제 사용자들(고갱님들)에게 나의 똥을 뽐낼 기회를 얻는 릴리즈를 할 수 있게 됩니다. 문제는 항상 코드가 develop branch에 반영된 순서대로 나가는 법이 없기 때문에 cherry-pick (체리 고르기. 흑.)를 통해서 내보내고자 하는 변경과 연관된 커밋들만 릴리즈 커밋으로 뭉쳐서 내보내게 됩니다.

이 과정을

```
# git cherry-pick commitId
```

를 통해서 이루어지게 되죠. 이렇게 커밋이 하나 둘씩 쌓아서 pull-request를 작성하기 전에 cherry-pick으로 타워를 만들게 되죠.


### 문제는 중간에 하나가 틀어지면..

cherry-pick을 한다는 것은 나의 변경사항이 다른 사람의 변경사항과 섞여서 문제가 발생할 소지가 높다는 이야기입니다.

만약,

```
public static void main(String[] args) {
  int a = 0;
  int b = 1;

  return a + b + methodA();
}

int methodA() {
  return 3;
}
```

에서

저는
```
public static void main(String[] args) {
  int a = 0;
  int b = 1;

  return a + b + methodA().get();
}

CompletableFuture<Integer> methodA() {
  return CompletableFuture.completedFuture(3);
}
```

로 변경하고

다른 사람이

저는
```
public static void main(String[] args) {
  int a = 0;
  int b = 1;

  return a + b + methodA();
}

int methodA() {
  return 4;
}
```

로 변경했다고 생각해봅시다. (exception handling은 생략했습니다.)

그리고 다른 변경이 먼저 real에 나간 상황이라면, 저는 cherry-pick할 때 제대로 반영해서 해야하지만, 그렇지 못하고 commit 을 쌓아버렸다면 문제가 발생하겠죠.

그리고 보통 이런 일은 항상 급박하게 일을 처리하다 보면 발생합니다. (cherry-pick 다 해놓고 빌드 하고 있는데 테스트 코드에서 문제가 발생한다던지..)


### 요럴 때 rebase + edit을 사용합니다.

예를 들어,

```
commitA <-- HEAD
commitB
commitC
```

순으로 커밋이 있고, commitC를 수정해야 한다면,

```
# git rebase -i HEAD~3
```

을 통해서 `commitC` 까지 rebase를 한다음 해당 commit을 `e`, edit으로 변경하여 변경 내용을 반영하고, rebase를 지속하면 문제없기 해결할 수 있습니다.

글을 다 작성하고 나니, 위의 예제를 왜 만들었을까 하는 생각이 있네요..

이미 만들었으니 그냥 사용하겠습니다. (그냥 똑같은 곳을 변경하다보면 충돌한다 정도로..)
