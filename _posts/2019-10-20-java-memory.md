---
title: "[JAVA] Java 메모리 구조"
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

첫 포스트에 앞서 앞으로 대부분 'Java'와 'Spring'을 주로 다룰 예정입니다. 또한 모든 포스트의 주제와 내용은 개인적으로 공부한 것들을 정리하여 올리는 것이기 때문에 다소 미흡한 부분이 있을 수 있습니다.

첫 포스트의 주제는 Java 의 메모리 구조에 대한 기본적인 내용입니다.

## Java 의 메모리 구조 및 컴파일 과정
---

일반적인 프로그램 또는 프로세스는 OS(Operating System) 위의 메모리에서 동작한다. 하지만 Java 프로그램은 OS에 ***독립적으로*** 실행된다.
그 이유는 ***JVM(Java Virtual Machine)***이라는 특별한 가상 머신이 OS와 Java 프로그램 사이에 위치하고 OS에게 메모리 사용 권한을 부여받아 Java 프로그램을 호출하여 기계어로 해석해주는 역할을 하기 때문이다.

그렇다면 이와 같은 ***JVM***의 장점은 무엇일까?

앞서 말했듯이 Java 프로그램은 ***JVM*** 위에서 OS에 독립적으로 실행된다. 이는 Java 프로그램이 OS에 종속적이지 않고 어떤 디바이스 또는 환경에서도 ***JVM*** 위에서 프로그램을 실행할 수 있다는 큰 장점이 된다.
하지만 OS으로부터 직접 제어 받는 방식보다 속도 면에서 느릴 수밖에 없다. (이를 보완하기 위해 특별한 컴파일러를 가진다.)

아래는 ***JVM***의 전체 구조이다.

<img src="{{ 'assets/images/java/memory/java_memory_01.png' | relative_url }}" alt=""/>

***JVM***은 크게 ***Class Loader, Execution Engine, Runtime Data Area***으로 구성되어 있다. 차례로 하나씩 살펴보자.

- ***Class Loader***

<img src="{{ 'assets/images/java/memory/java_memory_02.png' | relative_url }}" alt=""/>

우선 기본적으로 Java 개발 툴(ex. Eclipse)에서 작성된 Java 코드는 .java 파일로 저장된다. 그 후 Java 파일의 빌드가 이루어지면 Java 컴파일러는 javac라는 명령어를 통해 .class 파일을 생성한다.
.class 파일은 바이트코드(Byte Code, 반기계어)이기 때문에 OS에서 바로 실행될 수 없다. 그래서 ***JVM***은 OS가 해당 바이트코드를 이해할 수 있도록 해석해주는 역할을 한다.
이제 런타임 시점이 되면 ***Class Loader***에 의해 ***JVM*** 내부에 로드된다.

- ***Execution Engine***

<img src="{{ 'assets/images/java/memory/java_memory_03.png' | relative_url }}" alt=""/>

그다음 ***JVM*** 내 로드된 바이트코드는 ***Execution Engine***에 의해 기계어로 해석되어 메모리 상(Runtime Data Area)에 배치된다. ***Execution Engine***은 인터프리터(Interpreter)와 ***JIT(Just In Time)***컴파일러(Compiler)로 구성되어 있다.
앞서 말한 코드가 ***JVM***을 통해 해석되기 때문에 OS으로부터 직접 제어 받는 방식보다 속도 면에서 느리다는 단점을 보완하기 위해 ***JIT*** 컴파일러가 존재한다.
인터프리터는 바이트코드를 한줄씩 실행하기 때문에 속도가 느리다는 단점을 가진다. 이를 보완하기 위해 ***JIT*** 컴파일러는 바이트코드 전체를 어셈블러와 같은 네이티브(Native) 코드로 컴파일하여 직접 실행하게 된다.
***JIT*** 컴파일러에 의해 해석된 코드는 캐시(Cache)에 보관되기 때문에 한번 컴파일 된 후에는 빠르게 수행된다. 하지만 이를 변환하는데 인터프리터 방식보다 훨씬 오래 걸린다는 비용이 발생하게 된다.
이러한 이유로 ***Execution Engine***은 인터프리터 방식으로 실행하다가 적절한 시점에 ***JIT*** 컴파일러 방식을 선택한다. 예를들어 한번만 실행되는 코드는 인터프리터 방식을 사용하는 것이 유리하다.

## Runtime Data Area
---

***JVM***이 프로그램을 실행하기 위해 OS로부터 할당받은 메모리 공간으로 크게 5가지로 나뉜다.

<img src="{{ 'assets/images/java/memory/java_memory_04.png' | relative_url }}" alt=""/>

***1. Method Area***

***JVM*** 시작 시 생성되고 모든 스레드(Thread)가 공유하는 영역이다. ***JVM*** 구동 중 사용될 클래스 파일을 로드하고 클래스 별로 런타임 상수풀(Runtime Constant Pool), 필드(Field), 메서드(Method), 생성자(Constructor) 등 클래스와 인터페이스와 관련된 데이터들을 분류하고 저장한다. 또한 정적(Static) 영역의 데이터는 프로그램 종료 시까지 공유되며 메모리에 남아있게 된다.
> 상수풀(Constant Pool) : Type에서 사용된 상수들을 저장하는 곳이다. 상수의 중복이 생기면 기존의 상수를 사용한다.
예를 들면 String Type의 상수에 대해 직접 new를 이용하여 객체를 생성하지 않고 "" 으로 생성한 두 변수의 상수값이 같다면 같은 주솟값을 가진다.
그 외 final class 변수들도 상수 풀에 값을 복사한다.    
(Ex. String s1 = "hello",  String s2 = "hello"  ---->  s1과 s2는 같은 주솟값을 가진다.)

***2. Stack Area***

각 스레드마다 하나씩 존재하며 스레드가 시작될 때 할당된다. 만일 추가적인 스레드 생성이 없었다면 메인 스레드에 대한 한 개의 스택만 할당된다. 클래스 내의 메서드에서 사용되는 정보들이 저장되는 공간으로 메서드 호출 시 해당 메서드에 대한 프레임을 추가하고 종료되면 프레임을 제거한다. 각 프레임은 LIFO(Last In First Out) 방식으로 실행 순서에 따라 생성되고 소멸되며 메서드의 매개변수, 로컬 변수, 리턴 값 등의 정보를 가진다. 특히 프레임 내부의 로컬 변수 스택에서는 각 블록마다 기본 타입(Primitive) 또는 참조 타입(Reference) 변수가 추가/제거된다. 로컬 변수 스택에 변수에 대한 블록이 생성되는 시점은 초기화가 될 때, 즉 최초로 변수에 값이 저장될 때이다. 이는 Java에서 변수 초기화를 해야 하는 이유를 알 수 있다.
> 기본 타입 변수는 스택 영역에 실제 값을 가지지만 참조 타입 변수는 실제 값이 아닌 히프 영역이나 메서드 영역의 객체들에 대한 주솟값을 가진다.

***3. Heap Area***

주로 긴 생명주기를 가지는 데이터들이 저장된다. 힙 영역에는 클래스들에 대한 객체 또는 배열 등의 참조 타입 변수 정보가 저장된다. 힙 영역에 생성된 객체는 ***JVM*** 스택 영역의 변수나 다른 클래스의 필드에서 참조된다. 쉽게 말해 실제 값이 저장된 힙 영역의 주솟값을 저장하는 것이다. 만일 이를 참조하는 다른 변수나 필드가 없다면 쓰레기로 취급되어 GC(Garbage Collector)에 의해 제거된다.

***4. PC Register Area***

스레드마다 하나씩 생성하며 ***JVM*** 명령의 주솟값이 저장된다.

***5. Native Method Stack Area***

Java 외 다른 언어의 함수 호출(예: C/C++의 메서드)를 위해 할당되는 영역이다.

- 그림 참조

<img src="{{ 'assets/images/java/memory/java_memory_05.png' | relative_url }}" alt=""/>

## Code
---
이제 직접 코드를 통해서 Stack과 Heap의 개념과 추가적으로 상수플, 불변 객체(Inmmutable Object)에 대해 확인해보자. 아래는 직접 작성한 코드이다.

```
public static void main(String[] args) {
    Integer inmmutable1 = 10;

    System.out.println("Before Function Call (Value): " + inmmutable1);
    System.out.println("Before Function Call (Address): " + inmmutable1.hashCode());
    System.out.println("--------------In Function--------------");

    IncreaseIntegerValue(inmmutable1);

    System.out.println("--------------Out Function--------------");
    System.out.println("After Function Call (Value): " + inmmutable1);
    System.out.println("After Function Call (Address): " + inmmutable1.hashCode());
}

private static void IncreaseIntegerValue(Integer parm) {
    System.out.println("Before Calculation (Parmeter Address):" + parm.hashCode());

    parm += 20;

    System.out.println("After Calculation (Parmeter Address):" + parm.hashCode());
}
```

우선 코드를 보면 Integer 타입의 객체를 매개변수로 받고 값을 20 증가시키는 메서드 IncreaseIntegerValue를 호출하는 간단한 코드이다.
그리고 Integer 타입의 변수 inmmutable1에 대해 메서드를 호출하기 전, 실행 중, 실행 후의 값과 주솟값을 확인하기 위한 출력문이 포함되어 있다.
설명에 앞서 기본적으로 Java의 hashCode()는 객체의 주솟값을 이용하여 해시코드를 생성한다.

처음에 Integer는 Object 타입이므로 힙 영역에 값 10이 할당되고 그 값을 참조하는 변수 inmmutable1가 스택 영역에 할당된다.
메서드를 호출하게 되면 매개변수 parm에게 복사된 참조 변수(주솟값)를 넘겨주고 스택 영역에 해당 메서드의 새로운 프레임이 추가된다.
그다음 변수 parm은 스택 영역에 할당되어 변수 inmmutable1이 참조하는 주소를 똑같이 참조하게 된다.
이제 연산에 의해 변수 parm의 값을 20 증가시키면 inmmutable1의 값이 변하는가? 답은 당연히 변하지 않는다.

그 이유는 무엇일까? 이 질문을 답하기 위해서는 불변 객체의 개념을 알아야 한다.
불변 객체는 불변 클래스로부터 생성된다. 불변 클래스들은 참조 타입이기 때문에 그 객체들은 힙 영역에 생성된다.
불변 객체란 말 그대로 변경이 불가한 객체를 말한다. 힙 영역에 할당된 객체를 변경할 수 없다는 말과 같다.
대표적으로 불변 클래스들에는 String, Boolean, Integer, Float, Long 등 타입 관련 클래스들이 있다.

그렇지만 우리는 불변 객체인 변수 inmmutable1, parm의 값을 변경하거나 연산할 수 있는것을 알고있다.
하지만 이는 변경되는것 처럼 보이더라도 실제 메모리(힙 영역)에 새로운 객체가 할당되는것이며 기존의 값은 변경되는 않는다.
이제 위 코드 결과의 이유를 설명할 수 있을것이다. 변수 parm을 증가시키면 힙 영역에 새로운 값 30이 할당되고 기존 스택 영역에 할당되었던 변수 parm은 그 새로운 값을 참조한다.
그 결과 inmmutable1의 값과 주솟값은 그대로 유지될 수 있는 것이다.

그 후 메서드가 종료되면 해당 프레임이 제거되면서 스택에 있었던 parm은 더 이상 힙 영역의 30을 참조하지 않는 상태가 된다. 이것은 Garbage로 분류된다.

아래는 코드 실행 결과이다.

<img src="{{ 'assets/images/java/memory/java_memory_08.png' | relative_url }}" alt=""/>

추가적으로 상수풀의 존재도 확인해보았다.

```
public static void main(String[] args) {
    String inmmutable2 = "hello";
    String constantPool = "hello";

    System.out.println("Before Change Value (Address): " + inmmutable2.hashCode());
    System.out.println("Before Change Value (Constant): " + constantPool.hashCode());

    inmmutable2 = "bye";
    constantPool = "bye";

    System.out.println("After Change Value (Address): " + inmmutable2.hashCode());
    System.out.println("Before Change Value (Constant): " + constantPool.hashCode());
}
```

앞서 설명했듯이 String 객체는 불변 객체이므로 변수 inmmutable2의 값을 hello에서 bye로 변경하면 새로운 객체가 할당된다.
때문에 당연히 변경 전과 후의 변수 inmmutable2의 주솟값은 다르게 나올 것이다.
또한 상수풀을 통해 상수의 중복이 생기면 기존의 상수를 사용한다고 설명하였다.
그렇다면 변수 constantPool의 주솟값을 쉽게 예상해 볼 수 있을것이다.

아래는 코드 실행 결과이다.

<img src="{{ 'assets/images/java/memory/java_memory_09.png' | relative_url }}" alt=""/>

## Garbage Collector
---
