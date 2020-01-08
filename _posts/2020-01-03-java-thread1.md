---
title: "[JAVA] Thread 1"
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

이번 포스트의 주제는 Java의 스레드(***Thread***)입니다.
Java의 스레드와 관련하여 2부에 걸쳐 포스팅할 예정입니다.

## 프로세스(***Process***)와 스레드(***Thread***)
---

Java의 스레드를 설명하기 앞서, 먼저 '스레드'가 무엇인지 운영체제 관점에서 기본적인 개념을 알아보자.

- 프로세스(***Process***)

프로세스란 '실행 중인 프로그램'을 말한다.
우리가 프로그래밍 언어로 작성한 하나의 프로그램은 보조 기억장치(하드 디스크 또는 SSD)에 저장되어 실행되기를 기다리는 명령어 및 데이터의 묶음이다.
이 프로그램을 실행시키면 할당받은 메모리에 관련된 명령어와 데이터가 적재되고 작성된 코드의 순서에 따라 해당 프로그램의 동작 및 종료를 수행된다.

- 스레드(***Thread***)

스레드란 '한 프로세스 내에서 실행되는 흐름의 단위'이다.
프로세스 내의 명령어 블록으로 할당받은 자원을 실제로 사용하며 시작점과 종료점을 가진다.
다시 말해 운영체제에 의해 관리되며 하나의 프로세스 내부에서 독립적으로 실행되는 하나의 작업 단위를 말한다.

프로세스는 보통 직렬적인 처리 루틴(***Routine***)으로 수행되지만 스레드를 사용하면 한 프로세스 내에서 병렬적으로 동시에 여러 동작을 수행할 수 있다.
이를 '멀티 스레드'라고 하며 기본적으로 프로세스는 최소 1개의 스레드(메인 스레드)를 가진다.

<img src="{{ 'assets/images/java/thread/java_thread_01.PNG' | relative_url }}" alt=""/>

다음 포스트에서 구체적으로 설명하겠지만 멀티 스레드를 사용했을 때의 장점은 강력하다.

컴퓨터의 CPU는 단순한 명령 처리기이기 때문에 하나의 CPU는 기본적으로 한순간에 하나의 프로세스만 실행할 수 있다.
하지만 현대의 CPU는 멀티 코어로써 코어의 수에 따라 동시에 여러 프로세스를 실행할 수 있다. 이를 '멀티 프로세싱'이라고 한다.
또한 각 코어는 '멀티 태스킹(***Tasking***)'을 통해 여러 프로세스들의 명령어를 쪼개어 빠르게 작업을 수행 및 교환함으로써 동시에 여러 프로세스들을 처리하는 것처럼 수행한다.

하지만 이 과정에서 프로세스 작업 교환 즉, '문맥 교환(***Context Switch***)'으로 인한 오버헤드가 발생한다.
그러나 이것을 스레드로 처리하게 된다면 문맥 교환과 프로세스 간 통신(***IPC***)에 대한 비용을 줄일 수 있고 효율적인 작업이 가능하다.
하나의 응용 프로그램을 여러 프로세스로 나누는 것보다 멀티 스레딩으로 처리했을 때 이와 같은 이점이 생긴다.

## Java의 스레드
---

Java의 스레드는 일반 스레드와 거의 차이가 없으며 ***JVM***이 운영체제 역할을 한다.

Java에서는 처음 프로그램 실행 시 발생하는 메인 프로세스를 제외한 다른 독립된 프로세스가 존재하지 않는다.
대신 스레드로 만 구성된 실행 단위 코드 블록들이 ***JVM***에 의해 관리된다.
또한 ***JVM***은 스레드에 대한 스케줄링을 포함해서 스레드의 개수, 상태, 우선순위 등의 스레드 관련 정보들을 모두 관리한다.

결과적으로 우리가 작성하는 Java 코드는 스레드 위에서 동작하는 코드인 것이다.
처음 Java 프로그램을 실행하면 ***JVM***에 의해 하나의 메인 프로세스가 발생하고 main() 안의 실행 문들이 하나의 스레드(메인)가 된다.
이렇게 스레드가 실행되고 종료될 때까지 생명을 부여받고 동작들이 수행된다.

### Java 스레드의 생명주기  

아래 그림과 같이 스레드는 생명을 가지고 동작을 수행하는 동안 여러 상태를 가진다.

<img src="{{ 'assets/images/java/thread/java_thread_02.png' | relative_url }}" alt=""/>

1. NEW (생성) : 스레드가 생성되었지만 아직 실행할 준비가 되지 않았음  
2. RUNNABLE (준비) : 스레드가 실행 준비되어 스케줄링을 기다리는 상태  
3. RUNNING (실행 중) : ***JVM*** 및 스케줄러에 의해 결정된 스레드가 run() 명령을 받아 실행 중인 상태  
4. WATING (대기) : 다른 스레드의 notify(), notifyAll() 명령을 기다리고 있는 상태(동기화)  
5. TIMED_WAITING (시간 대기) : 스레드가 sleep() 호출로 인해 일시적으로 대기하고 있는 상태  
6. BLOCK (봉쇄) : 스레드가 I/O 작업을 요청하면 완료 시까지 BLOCK 상태로 대기  
7. TERMINATE (종료) : 스레드가 종료한 상태

### 데몬(Daemon) 스레드  

데몬 스레드란 다른 일반 스레드의 작업을 돕는 보조적인 역할을 수행하는 스레드이다.
다른 일반 스레드를 서비스 해주면서 다른 일반 스레드가 모두 종료되면 자신도 종료된다.
데몬 스레드의 예로는 가비지 컬렉터(***GC***), 메인 스레드 등이 있다.

## Java 스레드의 구현
---

Java에서 스레드를 구현하는 방법은 2가지이다.

1. ***Thread*** 클래스 상속  
2. ***Runnable*** 인터페이스 구현

일반적으로 ***Thread*** 클래스를 상속받는 방법은 다른 클래스를 상속 받을 수 없기때문에 ***Runnable*** 인터페이스를 구현한다.

아래 예제 코드를 통해 기본적인 스레드 구현과 동작을 보자.

```
public class Main {

    public static void main(String[] args) {
        Thread myThread1 = new MyThread1();
        Thread myThread2 = new Thread(new MyThread2());   // 인자 : Target(Runnable)

        startThread(myThread1);
        startThread(myThread2);
    }

    private static void startThread(Thread thread) {
        thread.start();
    }
}

class MyThread1 extends Thread {
    @Override
    public void run() {
        for (int i = 1; i <= 5; i++) {
            System.out.println(getName() + " : " + i);
        }
    }
}

class MyThread2 implements Runnable {
    @Override
    public void run() {
        for (int i = 1; i <= 5; i++) {
            System.out.println(Thread.currentThread().getName() + " : " + i);
        }
    }
}
```

MyThread1 클래스는 ***Thread*** 클래스를 상속하였고, MyThread2 클래스는 ***Runnable*** 인터페이스를 구현한 모습이다.
두 스레드는 동일하게 현재 실행 중인 스레드의 이름와 횟수를 총 5번 출력한다.

<img src="{{ 'assets/images/java/thread/java_thread_03.png' | relative_url }}" alt=""/>

위 결과는 동일한 코드를 2번 실행한 결과이다.
각 2번의 실행마다 스케줄링 차이에 의한 다른 출력 결과와 각 스레드들이 서로 병렬적으로 실행되는 모습을 볼 수 있다.

[예제 코드](https://github.com/kimseongje3111/ExampleCode/tree/master/Java/Java_04)  

***

이것으로 Java 스레드 1부 포스트를 마치겠습니다. 감사합니다.

- 그림/내용 참조  
[사이트 1](https://dailyworker.github.io/java-thread/)  
[사이트 2](https://raccoonjy.tistory.com/15)  
[사이트 3](https://gmlwjd9405.github.io/2018/09/14/process-vs-thread.html)
