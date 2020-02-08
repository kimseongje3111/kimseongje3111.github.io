---
title: "[JAVA] Exception"
author: Seongje kim
layout: post
---
<style>
    blockquote {
        font-size:13pt;
		padding-bottom:0.1px;
        margin-bottom:30px;
    }

	img {
		margin-left:15px;
		margin-right:30px;
		max-width:100%;
		heght:auto;
	}

	h3 {
		margin-bottom:15px;
	}
</style>

이번 포스트 주제는 Java의 예외 처리에 대한 내용입니다.
예외 처리는 프로그램 코드 작성 시 가장 신경써야 할 요소이며 보다 안전하고 유연한 프로그래밍을 위해 필수적으로 활용되어야 한다고 생각합니다.
따라서 이번 포스트를 통해 예외 처리에 대한 중요성를 이해하고 올바르게 적용하는 것이 목표입니다.

## 예외 (Exception)
---

우선 '예외(***Exception***)' 는 '오류(***Error***)' 와 구분되는 개념이다.

- 오류 : 
시스템의 비정상적 상황에 발생한다.
이는 시스템 레벨에서 발생하는 문제이기 때문에 비교적 심각한 수준이다.
따라서 개발자가 코드 작성시 미리 예측할 수 없는 문제이며, 애플리케이션 레벨에서 이에 대한 처리가 필요하지 않다.

- 예외 :
오류와 달리 애플리케이션 레벨에서 개발자가 구현한 로직에서 발생하며 프로그램의 오작동을 막는다.
따라서 개발자의 입장에서 예외가 발생할 수 있는 상황을 미리 예측하여 처리할 수 있다.
그러므로 개발자는 코드 작성시 특정 상황에 따른 예외들을 구분하고, 이들을 어떻게 처리를 해야하는지 고려하여 적절히 적용하는 것이 필요하다.

결과적으로 우리가 해결해야 할 것은 바로 '예외' 이다.

### 예외 클래스 (Exception Class)  

<img src="{{ 'assets/images/java/exception/java_exception_01.png' | relative_url }}" alt=""/>

위 그림은 예외 클래스의 구조이다.
***Trowable*** 클래스를 상속받는 클래스는 ***Error*** 와 ***Exception*** 클래스로 나뉜다.
개발자는 애플리케이션 레벨에서 발생 가능한 문제를 이 ***Exception*** 클래스를 이용하여 처리할 수 있다.
예외가 발생할 수 있는 상황이 다양한 만큼 ***Exception*** 클래스는 수많은 자식 클래스를 가진다.

***Exception*** 클래스는 다시 ***RuntimeException*** 클래스와 이외의 클래스들로 나뉜다.
***RuntimeException*** 클래스는 ***Exception*** 클래스를 ***Checked Exception*** 과 ***Unchecked Exception*** 으로 나누는 기준이 된다.
즉, ***RuntimeException*** 클래스와 그 자식 클래스들은 ***Unchecked Exception***, 이들을 제외한 ***Exception*** 클래스의 모든 자식 클래스들은 ***Checked Exception*** 이다.

### Checked vs Unchecked(Runtime)  

***Exception*** 을 ***Checked*** 와 ***Unchecked*** 로 구분하는 기준은 '반드시 처리가 필요한가' 이다.

***Unchecked Exception*** 은 말 그대로 확인되지 않은 예외 즉, 컴파일 단계에서 확인되지 않고 실행 단계에서 발생하는 예외이다.
주로 개발자의 부주의로 발생하는 경우가 대부분이며, 컴파일 시 문제가 되지 않기 때문에 굳이 명시적인 처리가 필요하지 않다.
즉, 개발자는 실행 단계에서 예외가 발생하지 않도록 코드 작성시 주의를 기울이는 것이 중요하다.
대표적으로 ***IndexOutOfBound, NullPointer Exception*** 등이 있다.

***Checked Exception*** 는 이미 예측 가능한 예외들로써 해당 예외가 발생할 수 있는 로직에는 반드시 명시적인 처리를 해야한다.
이 예외들은 컴파일 단계에서 명확한 확인이 가능하기 때문에 만일 예외를 처리하지 않는다면 컴파일 시 오류가 발생한다.
대표적으로 ***I/O, SQL Exception*** 등이 있다.

또한 두 예외는 '예외 발생시 트랜잭션(***Transaction***)의 ***Roll-back*** 여부' 에 대해서도 차이가 있다.
'트랜잭션' 이란 하나의 작업 단위를 뜻한다.
이 트랜잭션을 처리하는 과정에서 예외가 발생했을 때, 작업의 모든 단계를 취소하고 작업을 초기화하는 것이 바로 '***Roll-back***' 이다.
기본적으로 ***Checked Exception*** 은 예외가 발생하면 트랜잭션을 ***Roll-back*** 하지 않고 예외를 던져준다.
하지만 ***Unchecked Exception*** 은 예외 발생 시 트랜잭션을 ***Roll-back*** 한다는 점에서 차이가 있다.

추가적으로 트랜잭션을 어떻게 묶어놓는가 즉, 전파 방식(***Propagation Behavior***)에 따라 ***Checked Exception*** 과 ***Unchecked Exception*** 의 차이가 미치는 영향을 알아야한다.
결과적으로 트랜잭션의 전파 방식과 예외에 따라 작업이 ***Roll-back*** 되는 범위가 달라지기 때문에 개발자가 이를 인지하지 못한다면 실행 결과가 맞지 않거나 예상치 못한 예외가 발생할 수 있다.
그러므로 이를 인지하고 트랜잭션을 적용시킬 때, 전파 방식 및 ***Roll-back*** 규칙을 적절히 사용하면 더욱 효율적이고 안전한 애플리케이션을 구현할 수 있을 것이다.

|구분||Checked Exception||UnChecked Exception|
|:-------:||-------||-------|
|예외 처리 여부||반드시 예외 처리가 필요||명시적인 예외 처리를 강제하지 않음|
|확인 시점||컴파일 단계||실행 단계|
|트랜잭션 처리||Roll-back : O||Roll-back : X|
|대표 예시||IOException, SQLException||NullPointerException, IllegalArgumentException, IndexOutOfBoundException, SystemException|

## 예외 처리 방법
---