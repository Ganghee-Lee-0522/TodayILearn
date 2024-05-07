# [240408] Synchronous & Blocking

요즈음 진행하고 있는 프로젝트의 기능 구현 방식에 대해 고민하다가 `비동기 처리`에 꽂혔다. 프-백 상관없이 이런저런 정보를 찍먹하다가 이왕이면 성능 개선까지 실현해보자하는 생각에 스프링에서 비동기 처리를 구현하는 방식에 대해 본격적으로 공부해보려 한다.

원래 이번 주차에 @Async 어노테이션에 대해 정리해보려 했으나, 그보다도 근본적인 개념인 비동기-동기/블럭킹-논블럭킹에 대해 설명하고 가는 편이 좋을 것 같아 해당 주제를 먼저 정리해보았다.

최대한 공식 문서를 참고하여 작성한 글이지만 필자는 일개 학생이다보니 오류가 있다면 언제든 의견은 환영이다.

> Async vs Non-Blocking

항상 `구분이 애매하다`는 의견을 달고 다오는 두 개념이다. 필자가 과거 CS 면접 스터디를 진행했을 때도, 끊임없이 끌올되며 갑론을박을 펼쳤던 주제였다. 실제로 인터넷 상에는 오개념이 난무한다는 것을 조금만 구글링해보아도 알 수 있다. 스터디 당시에 어느정도 결론이 났었는데, 기억력이 별로인 필자는 다시 스터디 로그를 찾아보기도 했다. 아무튼 공신력있는 문서들과 믿을만한 개발자들이 작성한 칼럼을 바탕으로 글을 작성한다.



</br>

# 배경

이 용어의 쓰임에 대해 찾아보면, 다음의 [IBM 자료](https://developer.ibm.com/articles/l-async/)를 발견할 수 있을 것이다.

![](https://velog.velcdn.com/images/gangjjang5/post/9b245665-1502-44f3-95f5-5db74e2f9467/image.png)

무려  2006년에 작성된 글에 등장한 자료이다. 지금의 우리에게는 익숙하다만, 당시에는 굉장한 신기술이었던 AIO(Asynchronous I/O)에 대해 설명하는 글이다.

</br>

# Blocking / Non-blocking

먼저 살펴볼 개념은 Blocking과 Non-blocking이다. 이 두 개념 사이의 쟁점은 `return 시기가 언제인가` 라고 할 수 있다.

호출된 함수가 작업 수행과 관계없이 리턴값을 넘겨주면, 호출한 함수는 제어권을 가지고 다른 일을 할 수 있게 된다. 이것이 Non-blocking이다.

반대로, 호출된 함수가 작업을 모두 마친 후 리턴값을 넘겨주면, 호출한 함수는 그동안 대기해야만 한다. 리턴값을 받지 못했으니, 제어권이 없기 때문이다. 이러한 상황이 Blocking이다.

**호출된 함수의 리턴값**에 집중하면 쉽다.

</br>

# Synchronous / Asynchronous

이번엔 동기와 비동기이다. 이 두 개념 사이의 쟁점은 `callback의 실행` 에 있다고 할 수 있다.

호출된 함수에게 callback을 쥐어주고, 작업이 완료되면 callback을 실행하도록 하는 것이 비동기, 즉 Asynchronous이다.

반대로, 호출한 함수가 호출된 함수의 작업 완료를 스스로 계속 확인하면 동기, Synchronous이다.

**호출한 함수의 마음가짐**에 집중하면 쉽다.

</br>

# Matrix

중요한 것은 맨 처음 소개했던 매트릭스 상의 4가지 조합에 대해 이해하는 것이다.

</br>

## Synchronous blocking

![](https://velog.velcdn.com/images/gangjjang5/post/90b73993-96b5-4151-a6f3-268b57ba42db/image.png)

>호출한 함수는 호출된 함수에게 관심이 있다. 호출된 함수는 return을 바로 주지 않는다. 호출한 함수는 대기 상태이고 그 상태에서 다른 일을 할 수 없다.

카페에서 라떼를 시키는 상황을 가정하자. 주문을 하니 사장님이 거기 서서 잠시 기다리라고 한다. 서서 기다리며 라떼가 만들어지는 과정을 흥미롭게 지켜보며, 언제 음료가 나올지 궁금해한다.


</br>

## Synchronous non-blocking

![](https://velog.velcdn.com/images/gangjjang5/post/633c94ef-103c-4c40-9609-595c777ec3d5/image.png)


> 호출한 함수는 호출된 함수에게 관심이 있다. 호출된 함수는 return을 바로 준다. 호출한 함수는 제어권을 받아 다른 일을 할 수 있다. 그러나 계속 작업이 완료되었는지 신경 쓴다.

라떼를 주문하니 사장님이 빈 컵을 주셨다. 자리에 앉아 기다리지만 라떼가 만들어지고 있는 과정을 흥미롭게 지켜보며, 언제 음료가 나올지 궁금해한다. 됐나요? - 아뇨! 아직인가요? - 아직이에요! 계속 질문을 한다. 됐나요? - 됐습니다! 드디어 라떼가 완성되어 컵에 채워진다.

</br>

## Asynchronous blocking


![](https://velog.velcdn.com/images/gangjjang5/post/4b3d5e02-dca0-4f39-b57e-62f09fc7ecfd/image.png)

> 호출한 함수는 호출된 함수에게 관심이 없다. 호출된 함수는 return을 바로 주지 않는다. 호출한 함수는 대기 상태이고 그 상태에서 다른 일을 할 수 없다.

라떼를 주문하니 사장님이 기다리라고 한다. 그 자리에 서서 기다릴 수 밖에 없지만, 라떼가 만들어지는 과정에는 관심이 없다. 그치만 기다리라고 해서 아무것도 하지 못하고 우두커니 기다린다. 기다리다보니 라떼가 완성되어 받았다.

</br>

## Asynchronous non-blocking

![](https://velog.velcdn.com/images/gangjjang5/post/30b6039e-9e69-4963-a410-bb96d254a70c/image.png)


> 호출한 함수는 호출된 함수에게 관심이 없다. 호출된 함수는 return을 바로 준다. 호출한 함수는 제어권을 받아 다른 일을 할 수 있다. 호출된 함수의 작업이 완료되면 callback으로 호출한 함수에게 결과를 넘겨준다.

라떼를 주문하니 사장님이 빈 컵을 주셨다. 자리에 앉아 노트북을 켜고 작업을 시작한다. 작업에 한창 심취해있으니 라떼가 완성되었고, 사장님이 자리로 와 빈 컵에 따라주셨다.

---

# Q&A

## Synchronous non-blocking/Asynchronous blocking이 실제로 쓰이는 개념인지, 아니면 단순히 조합상 등장할 수 있다는 의미인지 궁금합니다.

## Synchronous non-blocking에서는 호출한 함수가 질의하지 않으면 결과값을 받을 수 없나요?
<style>
.callout {
  background-color: #f0f0f0;
  border: 1px solid #ddd;
  padding: 10px;
  border-radius: 5px;
  margin-bottom: 10px;
}
</style>

<div class="callout">
  이것은 Sync/Async를 호출된 함수의 종료를 호출한 함수가 처리하느냐, 호출된 함수가 처리하느냐로 이해하면 쉽습니다.
</div>


# 다음 주제 예고
사실상 이야기하고 싶었던 것은...</br>
https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/annotation/Async.html
https://www.baeldung.com/spring-async

</br></br>

`참고`
https://developer.ibm.com/articles/l-async/
</br>
`사진 출처`
https://developer.ibm.com/articles/l-async/