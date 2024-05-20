# [240520] Exception Custom

Exception Handler를 써서 common exception을 멋지게 처리하고 싶었으나.. 그러기엔 기본기가 부족하여 JAVA부터 제대로 정리해보았다.
</br>

# 배경
예외란 개발자가 해결할 수 있는 오류다. Error와 Exception의 구분이 어려우면..간단히 검색해본 후, 다시 이 글을 읽어보길 권한다.

</br></br>

# 예외 전가
try-catch라는 짜치는 방법 말고 멋지게 예외를 처리할 수 없을까? 답은 `예외 전가`에 있다. 예외 전가란 예외 처리의 의무를 호출한 메서드에게 떠넘기는 것이다. 아래와 같이 예외를 던져버리면 된다.

```java
int funcEx(int example) throws exceptionEX {
// 예외 발생 코드
}
```

</br>

>##### 모든 메서드가 예외를 전가하기만 한다면?
`main()`메서드마저 예외를 전가하면, JVM이 직접 예외를 처리한다. 여기서 말하는 처리란, 발생한 예외의 정보를 화면에 출력하고 프로그램을 강제 종료하는 것이다.

</br></br>

# Exception Custom

아무튼 필자가 원하는 것은 정확히 말해 `사용자 정의 예외 클래스`라고 할 수 있다. 자바가 다양한 예외 클래스를 제공하고 있지만, 이외에도 따로 예외를 처리해주어야 하는 기능들이 존재한다. 공동 작업의 상황에서, 약속된 방식의 예외 처리를 구현하고 싶을 때도 있다.

사용자 정의 예외 클래스의 생성 방법은 다음의 3단계를 수행함으로써 구현 가능하다.
1. 예외 클래스 직접 정의
2. 작성한 예외 클래스를 이용해 객체 생성
3. 고려하는 예외 상황에서 객체 throw

</br>

## 1. 예외 클래스 직접 정의
사용자 예외 클래스를 정의하는 방법은 두 가지로 나눌 수 있다.
1. Exception을 상송해 일반 예외 클래스로 만들기
2. RuntimeException을 상속해 실행 예외 클래스로 만들기

두 가지 모두 기본 생성자와 문자열을 입력받는 생성자를 추가한다. 문자열을 입력받는 생성자는 예외 메세지를 전달받아 예외 객체를 생성하는 생성자이다. 내부에서는 부모 클래스의 생성자를 호출해 사용할 것이다.

```java
1. 일반 예외

class My Exception extends Exception {
	MyException() {
    }
    MyException(String s) {
    	super(s); // 부모 생성자 호출
    }
}

2. 실행 예외

class MyRTException extends RuntimeException {
	MyRTException() {
    }
    MyRTException(String s) {
    	super(s); // 부모 생성자 호출
    }
}
```
</br>

## 2. 작성한 예외 클래스를 이용해 객체 생성

아래와 같이 객체를 생성한다.

```java
1. 일반 예외 객체

MyException me1 = new MyException();
MyException me2 = new MyException("에외 메세지");

2. 실행 예외 객체

MyRTException mre1 = new MyRTException();
MyRTException mre2 = new MyRTException("에외 메세지");
```
</br>

## 3. 고려하는 예외 상황에서 객체 throw
여기서의 throw는 `실제 JVM에게 예외 객체를 만들어 전달한다` 정도로 해석하면 된다. 예외 객체를 던지면 곧바로 예외가 발생한다. 이때의 `throw`는 예외를 전가하는 `throws`와는 다르기 때문에 구분해야 한다.

```java
1. 일반 예외

throw me1;
throw me2;
throw new MyException();
throw new MyException("예외메세지");

2. 실행 예외

throw mre1;
throw mre2;
throw new MyRTException();
throw new MyRTException("예외메세지");
```
이때의 플로우는 다음과 같다.
예외 객체가 던져진다
-> 던져진 예외 객체가 JVM으로 전달된다
-> JVM은 해당 예외 객체를 처리할 catch(){} 블록을 찾는다(따라서 throw 이후에 예외를 직접 처리하거나 예외를 전가하는 구문을 반드시 작성해야 한다)

</br></br>

`참고`
Do it! 자바 완전 정복 (김동형 지음)