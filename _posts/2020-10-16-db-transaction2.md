---
title: "[Database] Transaction 2"
author: Seongje kim
layout: post
categories: [Database]
tags: [Database]
---
<style>
    blockquote {
        font-size:12pt;
		padding-bottom:0.1px;
        margin-bottom:40px;
    }

	img {
		margin-left:15px;
		margin-right:30px;
		max-width:100%;
		heght:auto;
	}
</style>

데이터베이스 트랜잭션에 대한 내용입니다.

## 트랜잭션 전파(Propagation)
---

트랜잭션 전파란 새롭게 트랜잭션을 시작하거나 진행되고 있는 트랜잭션에서 다른 트랜잭션을 호출할 때, 호출된 다른 트랜잭션이 기존 트랜잭션에 참여하는 방법 또는 어떻게 처리할지 결정하는 속성이다.

이는 선언적 트랜잭션의 경계 설정을 통해 여러 트랜잭션의 적용 범위를 하나로 묶어 커다란 트랜잭션 경계를 만들 수 있다는 장점을 가진다.
따라서 특정 트랜잭션 경계의 시작 지점에서 트랜잭션 전파 속성을 참조하여 해당 범위의 트랜잭션을 어떻게 처리할 것인지 결정할 수 있다.

### ***Spring***에서의 트랜잭션 전파  

***Spring***에서 사용하는 애노테이션인 ***@Transactional***은 해당 메서드를 하나의 트랜잭션 안에서 진행할 수 있도록 만들어주는 역할을 한다.
이때, 트랜잭션 간의 경계 및 처리 설정을 위해 ***@Transactional***의 옵션인 ***propagation*** 지정을 통한 트랜잭션 전파 속성을 설정할 수 있다.

아래의 트랜잭션 전파 속성들은 ***Spring***에서 지원하는 속성들이다.

## 트랜잭션 전파 속성 및 특징
---

### ***REQUIRED*** (기본 값)  

***propagation*** 옵션을 설정하지 않은 경우에 기본적으로 적용되는 속성이다.
모든 트랜잭션 매니저가 지원하며 일반적인 경우에 해당한다.

미리 시작된(부모) 트랜잭션이 있다면 참여하고, 없다면 새로 트랜잭션을 시작하는 방식이다.
하나의 트랜잭션이 시작된 후에 다른 트랜잭션 경계가 설정된 메서드를 호출하게 된다면 자연스럽게 같은 트랜잭션으로 묶인다.
그러므로 중간에 롤백이 발생한다면 전체 트랜잭션이 모두 롤백된다.

### ***REQUIRES_NEW***

항상 새로운 트랜잭션을 시작함으로써 각각의 트랜잭션에 서로 영향을 주지 않는 방식이다.
이미 진행 중인 부모 트랜잭션이 있다면 해당 트랜잭션을 잠시 보류한다.

### ***MANDATORY***  

REQUIRED 속성과 비슷하게 이미 시작된 부모 트랜잭션이 있다면 합류하지만 시작된 트랜잭션이 없다면 새로 시작하는 대신 예외를 발생시킨다.
예를 들어 독립적으로 진행되면 안되는 트랜잭션에 이 속성을 부여한다.

### ***SUPPORTS***  

부모 트랜잭션이 있는 경우에만 합류하고, 트랜잭션을 새로 만들지 않는 방식이다.
하지만 해당 경계 안에서 부모 트랜잭션의 Connection 또는 하이버네이트 Session 등을 공유할 수 있다.

### ***NOT_SUPPORTED***  

트랜잭션을 사용하지 않게하는 방식이다.
트랜잭션을 생성하지 않으며, 부모 트랜잭션이 진행 중이라면 보류시킨다.

### ***NEVER***  

트랜잭션을 사용하지 않도록 강제하는 방식이다.
트랜잭션을 생성하지 않을 뿐만 아니라 진행 중인 부모 트랜잭션이 있다면 예외를 발생시킨다.

### ***NESTED***  

부모 트랜잭션이 없다면 새로운 트랜잭션을 생성하고, 이미 진행 중인 트랜잭션이 있다면 중첩 트랜잭션을 시작하는 방식이다.
중첩 트랜잭션이란 트랜잭션 안에 다시 트랜잭션을 만드는 것인데, 이는 ***REQUIRES_NEW*** 속성처럼 독립적인 트랜잭션을 새로 시작하는 것과는 차이가 있다.

중첩된 트랜잭션은 먼저 시작된 부모 트랜잭션의 커밋 및 롤백에는 영향을 받지만 자신의 커밋과 롤백은 부모 트랜잭션에게 영향을 주지 않는다.
만일 중첩된 트랜잭션 내부에서 롤백이 발생한다면 해당 트랜잭션의 시작 지점까지만 롤백이 되며, 부모 트랜잭션이 커밋될 때 같이 커밋된다.

예를 들어 어떤 중요한 작업을 진행하는 중에 작업 로그를 데이터베이스에 저장하는 처리가 필요하다고 가정했을 때, 로그를 저장하는 트랜잭션이 실패하더라도 기존 트랜잭션까지 롤백되는 것을 방지해야 한다.
반대로 해당 작업을 로그를 기록한 후에 원래 메인 작업에서 예외가 발생한다면 해당 로그를 지워야 하는 상황이 될 것이다.
이런 경우 중첩 트랜잭션을 사용한다면 문제를 해결할 수 있다.

## 트랜잭션 격리 수준(Isolation Level)
---

트랜잭션 격리 수준이란 동시에 여러 트랜잭션이 진행될 때, 한 트랜잭션의 작업 결과를 다른 트랜잭션에게 어떻게 노출할 것인지를 결정하는 기준으로써 트랜잭션 내부의 일관성 없는 데이터에 대한 허용 수준을 말한다.

여러 트랜잭션의 병렬 처리와 함께 ***ACID*** 특성을 유지하기 위해 ***Locking***을 통한 동시성 제어가 이루지지만 무조건적인 ***Locking***으로 동시에 수행되는 많은 트랜잭션들을 순차적으로 처리한다면 데이터베이스의 성능에 악영향을 미칠 우려가 있다.
따라서 트랜잭션의 특성을 유지하면서 최대한 응답성을 높일 수 있는 효율적인 ***Locking*** 방법이 필요하다.

## 트랜잭션 격리 수준의 종류 및 특징
---

### ***READ_UNCOMMITTED (Level 0)***  

가장 낮은 단계의 격리 수준이다.
하나의 트랜잭션이 커밋되기 전에 다른 트랜잭션의 읽기를 허용(***shared_lock***이 걸리지 않음)하기 때문에 아직 완료되지 않은 트랜잭션의 일관성 없는 공유 데이터가 그대로 노출되는 위험이 존재한다.
하지만 가장 빠르기 때문에 데이터의 일관성이 조금 떨어지더라도 성능을 극대화하려 할 때, 의도적으로 사용될 수 있다.

### ***READ_COMMITTED (Level 1)***  

일반적으로 많이 사용되는 격리 수준으로써 다른 트랜잭션이 커밋하지 않은 데이터는 읽을 수 없다.
하지만 하나의 트랜잭션이 읽은 로우를 다른 트랜잭션이 수정할 수 있기 때문에 처음 트랜잭션이 같은 로우를 읽을 경우 다른 값이 발견 될 수 있다.

### ***REPEATABLE_READ (Level 2)***  

하나의 트랜잭션이 읽은 로우를 다른 트랜잭션이 수정하는 것을 막는다.
따라서 트랜잭션이 범위 내에서 조회한 데이터의 내용이 항상 동일함을 보장한다.
하지만 새로운 로우를 추가하는 것은 제한하지 않기 때문에 조건에 맞는 로우를 조회할 경우 새롭게 추가된 로우가 발견될 수 있다.

### ***SERIALIZABLE (Level 3)***  

가장 강력한 트랜잭션 격리 수준이다.
트랜잭션을 순차적으로 진행시키기 때문에 여러 트랜잭션이 동시에 같은 테이블의 정보를 접근할 수 없다.
가장 안전하면서 완전한 읽기 일관성을 보장하는 대신 성능면에서 가장 뒤떨어지게 된다.

## 트랜잭션 격리 수준에 따른 트랜잭션의 상태
---

결과적으로 트랜잭션의 격리 수준과 데이터베이스의 성능은 ***Trade-off*** 관계를 형성한다.
트랜잭션의 격리 수준이 낮을수록 동시성이 증가하지만 데이터 무결성에 대한 문제가 발생할 수 있고, 높을수록 안전하지만 성능에 대한 비용이 증가한다.

|ISOLATION LEVEL|DIRTY READ|NON-REPEATABLE READ|PHANTOM READ|
|:---:|:---:|:---:|:---:|
|READ_UNCOMMITTED|O|O|O|
|READ_COMMITTED|-|O|O|
|REPEATABLE_READ|-|-|O|
|SERIALIZABLE|-|-|-|

1. ***DIRTY READ*** : 아직 완료하지 않은 한 트랜잭션에 의한 변경 사항을 다른 트랜잭션에서 읽는 경우
2. ***NON-REPEATABLE READ*** : 한 트랜잭션 내에서 다른 트랜잭션의 수정/삭제에 의해 데이터의 일관성이 깨지는 경우
3. ***PHANTOM READ*** : 한 트랜잭션 내에서 다른 트랜잭션의 삽입에 의해 조회 데이터의 개수가 상이한 경우

***

- 그림/내용 참조  
[Transaction - Rednics Blog](https://springsource.tistory.com/136)
