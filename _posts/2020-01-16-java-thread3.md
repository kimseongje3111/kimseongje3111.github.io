---
title: "[JAVA] Thread 3"
author: Seongje kim
layout: post
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

지난 포스트에서 이어서 Java의 스레드에 대한 마지막 내용입니다. 아래는 스레드 1,2부 포스트입니다.  
[Thread 1](https://kimseongje3111.github.io/2020/01/03/java-thread1.html)  
[Thread 2](https://kimseongje3111.github.io/2020/01/07/java-thread2.html)

## 스레드 동기화(Synchronization)
---

앞서 스레드 2부에서 설명한 '스레드 안정성(***Thread-Safe***)'이라는 개념을 기억할 것이다.
스레드 안정성의 키워드는 '정확성'이다. 구체적으로 멀티 스레드로 설계된 프로그램의 정확한 동작을 보장하는 것이다.

멀티 스레드 내부에서 발생할 수 있는 예측 불가한 결과는 공유 자원을 사용하는 스레드 간의 '경쟁 상태(***Race Condition***)'로부터 발생한다. 즉, 스레드 안정성을 위협하는 요소이다.

결과적으로 경쟁 상태를 유발할 수 있는 공유 자원에 대한 관리가 필요하며 이를 해결하기 위해 Java에서는 기본적으로 ***synchronized*** 라는 키워드를 통해 동기화(***Synchronization***)를 제공한다.

이제 아래 경쟁 상태의 예시와 코드들을 보며 Java의 스레드 동기화에 대해 알아보자.

먼저 '단일 연산'이란 중단되지 않는 연산을 말한다.
또한 운영체제 개념에서는 원자성(***Atomic***)을 가지는 연산으로 표현된다.
이를 스레드 관점으로 표현한다면 작업 A를 실행 중인 스레드 입장에서 작업 B를 실행 중인 다른 스레드를 볼 때 작업 B가 모두 수행됐거나 전혀 수행되지 않은 상태로 파악된다면 작업 B는 단일 연산이라고 말한다. 즉, 어떤 작업이 단일 연산을 보장한다면 해당 작업을 수행하는 동안 동시에 다른 스레드의 접근이 불가함을 뜻한다.

### 경쟁 상태 예시 (1) : 스레드의 타이밍에 따라 결과가 달라지는 경우  
</br>
```
public class UnsafeSequence {

    private int value;

    public int getNext() {
        return value++;
    }

    public int getPrev() {
        return value--;
    }
}
```

getNext(),getPrev() 메서드가 각각 value 값을 증가, 감소시켜 반환하는 것은 사실 3개의 명령으로 이루어진다.

1. value 값 읽기  
2. 값 연산  
3. value에 저장

<img src="{{ 'assets/images/java/thread/java_thread_08.png' | relative_url }}" alt=""/>

하지만 위 그림의 예시와 같이 value가 공유 변수일 경우 문제가 발생할 수 있다.
각 스레드의 작업인 Task A, Task B의 수행을 통해 의도한 동작 결과는 value 값이 9가 되어야 하지만 스레드의 접근 시점에 따라 예상과는 다른 결과를 유발할 수 있다.
따라서 스레드의 타이밍에 따라 예측할 수 없는 결과를 발생시킬 위험을 해결하기 위해 각 스레드가 변수에 접근하는 시점을 적절하게 조율해야 한다. 즉, 공유 자원을 사용하는 임계 영역(***Critical Section***)은 상호 배타적으로 수행되어야 한다.

```
public class SafeSequence {
	
    @GuardedBy("this")	// 해당 필드 또는 메서드를 사용하려면 반드시 지정된 락(Lock)을 확보한 상태에서 사용해야 함을 의미
	private int value;	

    public synchronized int getNext() {
        return value++;
    }

    public synchronized int getPrev() {
        return value--;
    }
}
```

위와 같이 코드를 작성하면 멀티 스레드 환경에서 데이터 공유 문제를 해결할 수 있다.
Java에서 제공하는 ***synchronized*** 키워드는 해당 블록에 대해 단일 연산의 특성을 보장하기 위한 '락(***Lock***)'을 제공한다.
***synchronized***는 락으로 사용될 객체의 참조 값과 해당 락으로 보호하려는 코드 블록으로 구성된다.

```
synchronized (lock) {	// lock은 보호할 객체의 참조 값
	/*
		단일 연산으로 만드려는 코드 블록
	*/
}
```

> 메서드 선언부에 지정된 ***synchronized*** 키워드는 해당 메서드 내부의 전체 코드를 포함하며 이 메서드를 포함한 클래스의 인스턴스를 락으로 사용함을 의미

Java에서 락은 상호 배제(***Mutex***)를 기반으로 동작하기 때문에 순간에 한 스레드만이 특정 락을 소유할 수 있다.
다시 말해 하나의 스레드만이 특정 ***lock***으로 보호된 ***synchronized*** 블록으로 들어갈 수 있다.
결과적으로 ***synchronized*** 키워드를 통한 동기화를 적용하여 공유 자원 접근에 대한 관리와 정확한 동작 결과를 보장할 수 있다.

### 경쟁 상태 예시 (2) : 상태가 없는 경우  
</br>
```
public class StatelessFactorizer implements Servlet {
	
	public void service(ServletRequest req, ServletResponse resp) {
		BigInteger i = extractFromRequest(req);
		BigInteger[] factors = factor(i);
		encodeIntoResponse(resp, factors);
	}
}
```

위의 서블릿(***Servlet***)은 공유하고 있는 상태가 존재하지 않는다.
메서드 안의 일시적인 상태는 해당 스레드의 스택(***Stack***)에 저장되기 때문에 다른 스레드가 접근할 수 없다.
그러므로 위 메서드를 실행하는 스레드는 서로 상태를 공유하지 않으며, 상태가 없는 객체는 항상 스레드 안정성을 보장한다.

### 경쟁 상태 예시 (3) : 상태가 1개인 경우 (Atomic 클래스를 통한 해결)
</br>
```
public class CountingFactorizer implements Servlet {
	private final AtomicLong count = new AtomicLong(0);
	
	public long getCount() { return count.get(0); }

	public void service (ServletRequest req, ServletResponse resp) {
		BigInteger i = extractFromRequest(req);
		BigInteger[] factors = factor(i);
		count.incrementAndGet();
		encodeIntoResponse(resp, factors);
	}
}
```

***java.util.concurrent.atomic*** 패키지는 숫자나 객체 참조 값에 대해 상태를 단일 연산으로 변경할 수 있는 클래스들을 제공한다.
위처럼 AtomicLong 타입으로 선언된 count 변수는 모두 단일 연산으로 처리되기 때문에 스레드 안전하다.

그렇다면 상태가 여러 개인 경우에도 Atomic 클래스를 이용해 해결할 수 있을까?

아니다. 상태가 여러 개라면 일관성을 유지하기 위해 관련 변수들을 하나의 단일 연산으로 갱신해야 한다.  
이때 필요한 것이 바로 '***synchronized***'이다.

```
public class UnsafeCachingFactorizer implements Servlet {

	private final AtomicReference<BigInteger> lastNumber = new AtomicReference<BigInteger>();
	private final AtomicReference<BigInteger> lastFactors = new AtomicReference<BigInteger>();

	public void service(ServletRequest req, ServletResponse resp) {
		BigInteger i = extractFromRequest(req);

		if (i.equals(lastNumber.get()) encodeIntoResponse(resp, lastFactors.get());
		else {
			BigInteger[] factors = factor(i);
			lastNumber.set(i);
			lastFactors.set(factors);
			encodeIntoResponse(resp, factors);
		}
	}
}

```

위의 코드는 스레드 안전하지 않다.

```
lastNumber.set(i);
lastFactors.set(factors);
```

이 값을 갱신하는 연산들이 전부 완료되기 전에 다른 스레드가 메서드를 실행시키면 결과 값이 정확하게 나오지 않을 가능성이 있다.
그러므로 상태를 일관성 있게 유지하려면 ***synchronized*** 키워드를 추가하여 위 연산들을 하나의 단일 연산으로 만들어야 한다.

```
public synchronized void service(ServletRequest req, ServletResponse resp) {...};
```

이 방법은 메서드 선언부에 ***synchronized*** 키워드를 추가하여 해결한 모습이다.
하지만 보호해야 할 코드 이외의 부분까지 전부 동기화되었기 때문에 성능이 떨어질 수 있는 문제가 있다.
또한 동기화 영역이 커지면 스케줄러와 우선순위에 따라 해당 스레드의 재진입 가능성이 증가하면서 다른 스레드의 활동성이 떨어질 수 있다.

> 재진입성 : 특정 스레드가 자기가 이미 획득한 락을 다시 확보할 수 있다. (스레드 단위로 락을 얻는다는 것을 의미)

```
synchronized (this) {
	lastNumber.set(i);
	lastFactors.set(factors);
}
```

위 코드는 보호해야 할 코드 블록에만 ***synchronized*** 키워드를 추가한 모습이다.
이와 같이 동기화할 경우 스레드 안정성을 보장하면서 메서드 전체를 동기화했을 때보다 성능을 개선할 수 있다.

### 정리  

***synchronized*** 블록의 범위를 크게 잡으면 코드도 보기 단순해지고, 확실하게 안전성을 보장받는 장점이 있지만 성능이 느려질 수 있다.

따라서 스레드 안전성을 먼저 확보한 다음에 성능이 너무 느려질 수 있는 부분은 락을 잡지 않는 것을 고려해보아야 한다.
예를 들어 복잡하고 오래 걸리는 계산 작업, 네트워크(***Network***) 작업, 사용자 입출력(I/O) 작업 등의 빠르게 처리가 안될 수도 있는 부분은 동기화 영역에서 제외한다.

## 스레드 스케줄링(Scheduling)
---

***JVM***의 스케줄링 규칙은 철저하게 '우선순위(***Priority***)'에 기반한다.
가장 높은 우선순위를 갖는 스레드가 먼저 스케줄링된다.

> 만일 두 스레드가 동일한 우선순위를 가지고 있다면 '라운드 로빈(***Round Robin***)' 방법이 적용된다.

Java에서는 이러한 우선순위 속성을 이용해서 스레드가 수행하는 작업의 중요도에 따라 우선순위를 서로 다르게 지정하여 특정 스레드가 더 많은 작업 시간을 갖도록 할 수 있다.

```
public static final int MAX_PRI = 10	// 최대 우선순위
public static final int MIN_PRI = 1		// 최소 우선순위
public static final int AVG_PRI = 5		// 보통 우선순위

// 제공 메서드
void setPriority(int newPriority);
int getPriority();
```

스레드의 우선순위는 1 ~ 10의 정숫값 을 지정할 수 있으며 숫자가 높을수록 우선순위가 높다는 것을 뜻한다.
기본적으로 설정된 메인 스레드의 우선순위는 5이다.

또한 스레드의 우선순위는 직접 설정하지 않는다면 해당 스레드를 생성한 스레드(같은 '스레드 그룹')로부터 상속받는다.
예를 들어 ***main()***에서 생성한 스레드의 우선순위는 자동적으로 5가 되는 것이다.

### 스레드 그룹(Group)  

우선순위의 부여는 스레드 그룹 단위로도 가능하다.

스레드 그룹은 서로 관련된 스레드를 그룹으로 다루기 위한 것이며 특정 메서드들을 제공한다.
자신이 속한 스레드 그룹이나 하위 스레드 그룹은 변경할 수 있지만 다른 스레드 그룹의 스레드는 변경하지 못하게 하기 위함이다.
이는 보완성과 관련이 있다.

스레드를 스레드 그룹에 포함시키려면 ***Thread***의 생성자를 이용해야 한다.
단, 모든 스레드는 반드시 스레드 그룹에 포함되어야 하기 때문에 스레드 그룹을 지정하는 생성자를 사용하지 않은 스레드는 기본적으로 자신을 생성한 스레드와 같은 스레드 그룹에 속하게 된다.

### 스레드 실행 제어  

앞서 설명했듯이 우선순위를 주어 스레드 간의 스케줄링이 가능하지만 이 방법은 제한적인 면이 있다.
따라서 Java에서는 추가적으로 스케줄링과 관련된 메서드들을 제공하며 이를 이용하여 더욱 구체적인 스레드 스케줄링 설계가 가능하다.

우선순위와 함께 다양한 스레드 실행 제어 메서드들과 스레드 생명 주기를 잘 파악하여 적절한 스케줄링 설계를 한다면 이에 따라 프로그램 성능 향상을 꾀할 수 있을 것이다.

[Thread (Java Platform SE 8) - Oracle Help Center](https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html)

## 메모리 가시성(Visibility)과 메모리 장벽(Barrier)
---

마지막으로는 Java 메모리 모델과 병렬 프로그래밍과 관련하여 다소 심화된 내용을 다룰 것이다.


