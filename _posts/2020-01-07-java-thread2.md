---
title: "[JAVA] Thread 2"
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

지난 포스트에서 이어서 Java의 스레드에 대한 내용입니다. 아래는 스레드 1부 포스트입니다.  
[Thread 1](https://kimseongje3111.github.io/2020/01/03/java-thread1.html)

## 멀티 스레드
---

지난 포스트에서 스레드란 한 프로세스 내에서 실행되는 여러 작업의 단위라고 설명하였다.
또한 '멀티 스레드'는 한 프로세스에서 2개 이상의 작업(스레드)를 동시에 처리하는 것이다.

<img src="{{ 'assets/images/java/thread/java_thread_01.PNG' | relative_url }}" alt=""/>

위 그림과 같이 한 프로세스에 생성된 스레드들은 각각 자신만의 ***Stack*** 영역을 할당받고 ***Code, Data, Heap*** 영역은 공유한다.
다른 메모리에 직접 접근할 수 없는 프로세스와 반대로 스레드끼리 같은 메모리 주소 공간 또는 자원을 공유하는 것이다.

### 멀티 스레드의 활용  

프로그래밍 시 멀티 스레드를 활용한다면 많은 장점을 가질 수 있다.

- 멀티 프로세서의 활용

현대의 컴퓨터는 기본적으로 멀티 코어 이상의 CPU를 사용하며 프로세서 스케줄링의 기본 단위가 스레드이다.
때문에 멀티 스레드를 활용하면 CPU의 사용률을 향상시킬 수 있다.
만일 특정 프로그램을 실행에 대한 전체 프로세서 사용률을 100%라고 가정했을 때, 멀티 스레드와 비교하여 단일 스레드인 프로그램은 CPU 자원의 50%만 사용하는 것과 같은 것이다.

- 효율적인 자원 사용 및 관리

프로세스를 생성하여 자원을 할당하는 '시스템 콜(***System Call***)'이 줄어들어 효율적으로 자원을 관리할 수 있다.
또한 프로세스 대신 스레드를 사용함으로써 데이터 통신과 문맥 교환에 대한 비용을 절감할 수 있는 이점을 가진다.

- 더 빨리 반응하는 사용자 인터페이스

서비스 제공 프로그램이 멀티 스레드로 동작한다면 사용자 이벤트 처리 작업에 별도의 스레드를 할당하여 대기 시간을 줄이고 빠른 이벤트 처리가 가능해진다.
또한 다수의 클라이언트 프로그램으로부터 이벤트를 받는 서버 프로그램이 단일 스레드라면 클라이언트 요청이 증가할수록 응답에 대한 대기 시간이 그만큼 증가할 것이다.
때문에 여러 스레드를 할당하여 각자의 클라이언트 요청을 담당하는 것이 더 효율적이다.

단, CPU 코어의 수가 적을 경우 그만큼 처리할 수 있는 스레드의 개수가 제한되기 떄문에 눈에 띄는 속도 향상을 기대할 수 없다.
또한 처리하는 데이터 양이 적을 경우에도 멀티 스레딩의 효율을 기대하기 어렵다.

### 멀티 스레드의 위험  

하지만 멀티 스레드가 장점만 가지는 것은 아니다.
여러 스레드가 한 프로세스 내에서 자원을 공유하면서 작업을 하기 때문에 주의 깊은 설계가 필요하다.

- 안전성 위해 요소

잘못 설계된 멀티 스레드 프로그램은 타이밍에 따라서 정확한 동작을 보장하지 못한다.
스레드는 서로 같은 메모리 주소 공간과 자원을 공유하면서 실행되기 때문에 예상치 못한 문제가 발생할 수 있다.
예를 들어 경우에 따라 다른 스레드가 사용 중인 변수를 읽거나 수정할 수 있다.
이처럼 멀티 스레딩으로 인해 프로그램의 동작이 부정확하다면 안정성에 위해가 될 수 있는 요소가 된다.

- 활동성 위험

또한 잘못 설계된 멀티 스레드 프로그램은 데드락(***Dead Lock***), 소모 상태 등의 문제을 일으킬 수 있다.

- 성능 위험

잘 설계된 병렬 프로그램은 멀티 스레드를 사용함으로써 성능 향상을 기대할 수 있지만 그렇지 않은 경우 동기화 수단 및 스레드 간 문맥 교환에 따른 과부하가 발생할 수 있다.

## 스레드 안정성(Thread-Safe)
---

잘 동작하는 프로그램은 '정확성'이 바탕이 된다.
프로그램은 어떤 경우에도 자신이 의도한 대로 정확하게 동작해야 한다.
이처럼 멀티 스레드 환경의 프로그램에서 어떤 경우라도 의도한대로 정확한 동작을 보장하는 것이 '스레드 안정성'이다.
다시 말해 멀티 스레드 프로그램이 잘 동작하기 위해서는 스레드 안정성을 보장해야 한다.

멀티 코어 환경에서는 동시에 두 스레드가 수행될 수 있기 때문에 작업이 겹칠 가능성이 있다.
또한 매 순간 상황에 따라 프로세스와 스레드에게 할당되는 실행 시간이 일정하지 않는 불확실성을 가진다.
때문에 두 스레드가 서로 같은 자원을 공유하는 작업의 경우 주의 깊은 설계가 필요하다고 설명하였다.

스레드 안정성을 보장하기 위해서 멀티 스레드 환경의 두 스레드가 서로 같은 자원을 공유할때 생길 수 있는 문제, 이른바 '경쟁 상태(***Race Condition***)' 문제를 발생시키지 않아야 한다.
경쟁 상태는 상대적인 시점이나 ***JVM***의 스레드 스케줄링에 따른 교차 상황에 따라 계산의 정확성이 달라질 때를 말한다.
즉, 스레드의 타이밍에 따라 결과가 달라질 가능성이 있는 것이다.

그래서 스레드 간 경쟁 상태를 유발할 가능성이 있는 공유 자원을 쓰는 영역 즉, 임계 영역(***Critical Section***)은 상호 배타적으로 접근 및 사용되어야 한다.
이를 해결하기 위해 대표적으로 세마포어(***Semaphore***), 상호 배제(***Mutex***) 등의 개념이 있고 이들은 근본적으로 공유되고 변경할 수 있는 상태에 대한 접근을 관리한다.
위에서 말하는 상태란 클래스 변수, ***static*** 변수 같은 상태 변수에 저장된 데이터 등을 말한다.

결과적으로 스레드 안정성을 위한 설계 방법은 아래와 같이 정리할 수 있다.

1. 문제가 될 수 있는 상태 변수를 스레드 간에 공유하지 않는다.  
2. 문제가 될 수 있는 상태 변수를 변경할 수 없도록 만든다.  
3. 문제가 될 수 있는 상태 변수에 접근할 땐 '동기화'를 사용한다.

## 스레드 풀(***Thread-Pool***)
---

또한 멀티 스레드 사용 시 추가적으로 고려할 점이 존재한다.
아래 그림과 같은 웹서버 시스템이 있다고 생각해보자.

<img src="{{ 'assets/images/java/thread/java_thread_04.png' | relative_url }}" alt=""/>

위 그림에서 보듯이 웹서버는 각 클라이언트의 서비스를 위해 클라이언트 당 스레드를 생성한다.
이는 멀티 스레드의 장점을 잘 활용한 예로써 볼 수 있다.
하지만 이처럼 각 클라이언트마다 스레드를 생성하는데 개수에 제한을 두지 않았다면 아마 서버는 클라이언트 수가 증가함에 따라 병렬 작업 처리가 많아지고 극단적으로는 메모리를 다 차지할 만큼의 스레드를 만들어 버릴 것이다.
그렇지 않다 하더라도 새로운 스레드를 생성하고 작업이 종료되면 수거하는 행위에 대한 비용을 무시할 수 없다.
결과적으로 스레드 생성과 스케줄링으로 인해 CPU가 바빠져 메모리 사용량이 늘어나게 될 수 있다.
이는 프로그램의 성능 저하와 연관된다.

'스레드 풀'을 이용하면 생성할 수 있는 스레드의 수를 제한함으로써 이와 같은 프로그램의 성능 저하를 방지할 수 있다.
미리 일정 수의 스레드를 만들어 놓는다면 매번 발생되는 작업을 병렬 처리하기 위한 스레드 생성 및 수거 비용의 부담을 줄일 수 있다.
특히 서비스가 다수의 사용자 요청을 처리해야 한다면 더욱 효과적일 것이다.

아래는 스레드 풀의 동작 원리을 표현한 그림이다.

<img src="{{ 'assets/images/java/thread/java_thread_05.png' | relative_url }}" alt=""/>

서비스 애플리케이션에 발생한 사용자 요청에 대한 작업을 큐에 넣고 스레드 풀이 큐에 쌓인 작업들을 미리 생성해놓은 스레드들에게 할당한다.
병렬 처리 작업들을 하나씩 스레드가 맡아 처리하며 작업이 완료된 스레드는 다시 애플리케이션에게 결과 값을 리턴한다.
결과적으로 스레드 풀을 사용함으로써 스레드의 재사용성을 높이고 작업 요청 폭증으로 인한 성능 저하도 방지할 수 있다.

### 스레드 풀 생성  

Java에서는 스레드 풀을 사용을 위해 ***java.util.concurrent Package***의 ***ExecutorService*** 인터페이스와 ***Executors*** 클래스를 제공한다.
***Executors*** 클래스의 다양한 정적 메서드를 통해 ***ExecutorService*** 구현 객체를 만들어서 사용한다.

```
ExecutorService executorService1 = Executors.newFixedThreadPool(Runtime.getRuntime().availableProcessors());
ExecutorService executorService2 = Executors.newCachedThreadPool();
```

|메서드||초기 스레드 수||코어 스레드 수||최대 스레드 수|
|-----||-----||-----||-----|
|newFixedThreadPool(int nThreads)||0||nThreads||nThreads|
|newCachedThreadPool()||0||0||Integer.MAX_VALUE|

1. 초기 스레드 수 : 기본적으로 생성되는 스레드 수  
2. 코어 스레드 수 : 최소한으로 유지해야 할 스레드 수  
3. 최대 스레드 수 : 스레드풀에서 관리하는 최대 스레드 수

- ***newFixedThreadPool(int nThreads)***

최대 지정한 개수만큼의 스레드를 가질 수 있는 스레드 풀을 생성한다.  
항상 일정한 스레드 개수를 유지하며 스레드가 유휴 상태라도 제거하지 않고 유지한다.

- ***newCachedThreadPool()***

필요할 때마다 스레드를 생성하는 스레드 풀을 생성한다.
이미 생성된 스레드의 경우 재사용된다.
다만 일정 시간(60초) 동안 사용하지 않는(***Idle***) 스레드는 종료된다.
필요 없는 스레드를 제거하므로 메모리는 적게 사용하지만, 스레드 생성과 삭제를 반복하므로 작업 부하가 불규칙적인 경우 비효율적이다.

### 작업 생성  

```
Runnable task = new Runnable() {
    @Override
    public void run(){
        // 작업 내용
    }
}

Callable<T> task = new Callable<T>() {
    @Override
    public T call() throws Exception{
        // 작업 내용
        return T;
    }
}
```

하나의 작업은 ***Runnable*** 혹은 ***Callable*** 인터페이스로 표현한다.
두 인터페이스의 차이는 작업 처리 완료 후 리턴 값 유무의 차이다.
***call()*** 메서드는 리턴 값이 존재하며 리턴 타입은 ***Callable*** 인터페이스를 구현한 클래스에서 지정한 T 타입이다.
스레드 풀의 스레드는 작업 큐에서 할당받은 ***Runnable*** 또는 ***Callable*** 객체를 가져와 ***run()***과 ***call()*** 메서드를 실행한다.

### 작업 처리 요청  

작업 처리 요청이란 ***ExecutorService***의 작업 큐에 ***Runnable*** 또는 ***Callable*** 객체를 넣는 행위를 말한다.  
***ExecutorService*** 작업을 처리하기위해 아래 두가지 메서드를 제공한다.

|메서드||리턴 타입||설명|
|-----||-----||-----|
|execute(Runnable task)||void||작업 처리 결과를 받지 못함|
|submit(Runnable(or Callable) task)||Future||리턴된 Future를 통해 작업 처리 결과를 얻을 수 있음|

***execute()***는 작업 처리 결과를 받지 못하지만 ***submit()***은 작업 처리 결과를 받을 수 있도록 Future 객체를 리턴한다.
***execute()***는 작업 처리 도중 예외가 발생하면 해당 스레드가 종료되고 스레드 풀에서 제거된다. (다른 작업을 하기 위해선 새로운 스레드 생성)  
반대로 ***submit()***은 작업 처리 도중 예외가 발생하여도 종료되지 않고 재사용된다.
때문에 스레드 생성에 대한 오버헤드를 줄이기 위해 ***submit()***을 사용하는 것이 바람직하다.

### 스레드 풀 종료  

스레드 풀에 속한 스레드는 기본적으로 데몬 스레드가 아니기 때문에 메인 스레드가 종료되어도 작업을 처리하기 위해 계속 실행 상태로 남아있다.
즉 ***main()*** 메서드의 실행이 끝나도 프로세스는 종료되지 않는다.
때문에 프로세스를 종료하기 위해서 스레드 풀을 강제로 종료시켜 스레드를 해제시켜야 한다.

- ***ExecutorService.shutdown()***

작업 큐에 남아있는 작업까지 모두 마무리 후 종료한다.  
오버헤드를 줄이기 위해 일반적으로 많이 사용한다.

- ***ExecutorService.shoutdownNow()***

작업 큐에 남아 있는 작업량과 상관없이 작업 중인 스레드를 ***interrupt()***하여 중지 시키고 스레드 풀을 강제 종료한다.  
리턴 값은 미처리된 작업 목록이다.

- ***ExecutorService.awaitTermination(long timeout, TimeUnit unit)***

모든 작업 처리를 ***timeout*** 시간 안에 처리하면 ***true***, 처리하지 못하면 작업 스레드들을 즉시 ***interrupt()*** 시키고 ***false*** 리턴한다.

### Code  

예제 코드를 통해서 스레드 풀의 동작 과정을 살펴보고 추가적으로 스레드 생성 방법에 따른 차이점을 살펴보자.  
예제의 내용은 아래와 같다.

1. 테스트 : Facebook과 Twitter의 타임라인 데이터들을 동시에 요청하여 두 타임라인 데이터를 날짜별로 정렬하여 병합한다.  
2. 전제 : 두 스레드의 공유 데이터는 동기화되며, 데이터 요청과 병합은 ***sleep()***으로 대체한다.

우선 스레드 생성 방법에 따른 차이를 비교하기 위해 먼저 Thread 클래스 상속 방법을 보자.

```
public class fbThread extends Thread {

    @Override
    public void run() {
        try {
            for (int i = 0; i < 300; i++) {     // 전체 응답 시간 = (0.05 * 300) 초
                sleep(50);
            }

        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("(" + System.currentTimeMillis() + ") [FaceBook] Response Complete.");
    }
}
```

Facebook 타임라인 데이터들을 요청하는 fbThread 클래스는 ***Thread*** 클래스를 상속받아 구현하였다.  
위 구현 방법과 동일하게 Twitter 타임라인 데이터들을 요청하는 twThread 클래스를 생성한다.

Facebook은 데이터 한개에 대한 응답이 0.05 초이며, 총 300개를 요청한다고 가정한다.  
Twitter는 데이터 한개에 대한 응답이 0.03 초이며, 총 500개를 요청한다고 가정한다.

```
public class Main {

    public static void main(String[] args) {    // 출력문 생략 ...
        Thread fbthread = new fbThread();
        Thread twthread = new twThread();

        fbthread.start();
        twthread.start();

        try {
            sleep(1000);       // 합병 시간 = 1초
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

메인에서 Facebook과 Twitter 데이터 요청을 위한 각각의 스레드를 생성하고 실행하며 이후 두 데이터를 병합한다.  
하지만 위 방법은 문제점이 존재한다.

<img src="{{ 'assets/images/java/thread/java_thread_06.PNG' | relative_url }}" alt=""/>

메인 스레드는 각 스레드를 생성하고 ***start()*** 후 그냥 나머지 처리를 진행하게 된다.
결국 양쪽 또는 한쪽의 데이터가 모두 모이지 않은 상태에서 원하지 않은 병합이 실행되거나, 또는 처리 속도가 너무 빨라서 양쪽의 데이터가 모두 도착하지 않았는데도 병합이 완료되었다고 하는 경우가 발생할 수 있다.

이를 보완하기 위해서는 공용 메모리나 파이프 등을 이용하여 하나의 스레드가 다른 한쪽의 처리 완료 여부를 지속적으로 확인해야 한다.
물론 이러한 코드는 ***while()*** 등으로 구현될 것이므로 자원 낭비, 처리율 저하 등의 문제를 야기할 수 있다.

위 방법과 비교하여 개선된 코드를 보자.

```
public class fbCallable implements Callable<List<String>> {

    @Override
    public List<String> call() throws Exception {
        List<String> list = new ArrayList<>();

        for (int i = 0; i < 300; i++) {
            System.out.println(Thread.currentThread().getName() + " : getFaceBookTimeline ... (Line " + i + ")");

            Thread.sleep(50);
            list.add("FaceBook TimeLine : " + i);
        }

        return list;
    }
}
```

조건은 동일하며 Facebook, Twitter 데이터 요청 작업을 위한 스레드는 ***Callable*** 인터페이스로 구현된다.  
리턴 값은 리스트 형태의 전체 타임라인 데이터이다.

```
public static void main(String[] args) {    // 출력문 생략 ...
    try {
        // 스레드 풀 생성
        ExecutorService executorService = Executors.newCachedThreadPool();

        // 작업 생성 및 처리 요청
        List<String> responseFb;
        List<String> responseTw;

        Future<List<String>> futureFb = executorService.submit(new fbCallable());
        Future<List<String>> futureTw = executorService.submit(new twCallable());

        // 작업 종료 응답 대기 : 블록됨
        // 만일 트위터 응답이 더 빨라도 메인 스레드는 페이스북 응답이 완료될 때까지 블록됨
        responseFb = futureFb.get();
        responseTw = futureTw.get();

        // 확인 및 병합
        if (futureFb.isDone() && futureTw.isDone()) {
            sleep(1000);

            for (String line : responseFb) System.out.println(line);
            for (String line : responseTw) System.out.println(line);
        }

        // 스레드 풀 종료
        executorService.shutdown();

        try {
            if (!executorService.awaitTermination(5, TimeUnit.MINUTES)) {
                executorService.shutdownNow();
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
            executorService.shutdownNow();
        }

    } catch (InterruptedException | ExecutionException e) {
        e.printStackTrace();
    }
}
```

이전의 방법과 비교하여 각 스레드는 ***Callable*** 인터페이스를 구현하여 작업을 처리하며 앞서 설명한 동작 과정에 따라 스레드 풀을 사용하였다.
위와 같은 방법으로 처리한다면 메인 스레드는 병합 전에 자동적으로 ***Blocking***되어 다른 스레드 처리가 모두 완료될 때까지 멈추게 된다.
그러므로 모든 스레드 처리가 완료된 후 병합 처리를 진행할 수 있을 것이다.

조금 더 구체적으로 설명하자면 ***Future*** 클래스의 ***get()*** 메서드는 해당 객체가 담당하는 ***Callable*** 객체가 처리를 마치고 값을 반환하기 전까지 ***Future.get()***을 실행한 라인에서 ***Blocking*** 된다.

<img src="{{ 'assets/images/java/thread/java_thread_07.PNG' | relative_url }}" alt=""/>

위 결과를 보면 두 스레드가 동시에 데이터 요청을 하며 메인에서는 이 두 작업을 모두 기다린 후 병합을 시작하는 모습을 볼 수 있다.

[예제 코드](https://github.com/kimseongje3111/ExampleCode/tree/master/Java/Java_05)

### 정리  

하지만 스레드 풀의 활용은 프로그램 또는 작업의 특성과 환경을 잘 파악해야 한다.
만일 많은 병렬 처리 작업을 예상하여 스레드를 많이 만들어 놓았지만 실제로 활용되는 스레드가 일부라면 놀고 있는 나머지 스레드는 메모리 낭비일 뿐이다.
또한 스레드 수가 적절하더라도 스레드 스케줄링 타이밍에 따라 일부의 스레드만 사용되는 문제가 발생할 수 있다.
이와 관련하여 Java 7 이상 부터는 ***forkJoinPool*** 방식을 지원한다.

정리하자면 스레드 풀의 사용은 프로그램 성능과 연관되는 요소이다.
여러 조건과 환경에 맞춰 적절하게 튜닝하여 프로그램의 성능을 최적화하는 것이 목적이다.
이와 관련한 더 자세한 내용은 이번 포스트에서 다루지 못했지만 아래와 같은 사이트들을 참고하면 좋을 것 같다.

[ForkJoinPool](https://hamait.tistory.com/612)  
[스레드 풀 튜닝 및 Executor 고급 활용](https://12bme.tistory.com/368)

***

예상보다 포스트의 내용이 많아 이것으로 이번 포스트를 마치고 스레드 3부 포스트를 통해 마무리 하겠습니다.
감사합니다.

- 그림/내용 참조  
[사이트 1](https://limkydev.tistory.com/55)  
[사이트 2](https://cornswrold.tistory.com/197)  
[사이트 3](https://dailyworker.github.io/java-thread/)  
[사이트 4](https://soulduse.tistory.com/14)
