---
title: "[JAVA] Java 메모리 구조"
author: Seongje kim
layout: post
---

첫 포스트에 앞서 앞으로 대부분 'Java'와 'Spring'을 주로 다룰 예정입니다. 또한 모든 포스트의 주제와 내용은 개인적으로 공부한 것들을 정리하여 올리는 것이기 때문에 다소 미흡한 부분이 있을 수 있습니다.

첫 포스트의 주제는 Java 의 메모리 구조에 대한 기본적인 내용입니다.

## Java 의 메모리 구조 및 컴파일 과정
---

일반적인 프로그램 또는 프로세스는 OS(Operating System) 위의 메모리에서 동작한다. 하지만 Java 프로그램은 OS에 __독립적으로__실행된다. 그 이유는 __JVM(Java Virtual Machine)__이라는 특별한 가상 머신이 OS와 Java 프로그램 사이에 위치하고 OS에게 메모리 사용 권한을 부여받아 Java 프로그램을 호출하여 기계어로 해석해주는 역할을 하기 때문이다.

그렇다면 이와 같은 JVM의 장점은 무엇일까?

앞서 말했듯이 Java 프로그램은 JVM 위에서 OS에 독립적으로 실행된다. 이는 Java 프로그램이 OS에 종속적이지 않고 어떤 디바이스 또는 환경에서도 JVM 위에서 프로그램을 실행할 수 있다는 큰 장점이 된다.
하지만 OS으로부터 직접 제어 받는 방식보다 속도 면에서 느릴 수밖에 없다. (이를 보완하기 위해 특별한 컴파일러를 가진다.)

아래는 JVM의 전체 구조이다.

<center><img src="{{ 'assets/images/java/memory/java_memory_01.png' | relative_url }}" alt="" /></center>

JVM은 크게 Class Loader, Execution Engine, Runtime Data Area으로 구성되어 있다. 하나씩 살펴보자.

- ***Class Loader***

<center><img src="{{ 'assets/images/java/memory/java_memory_02.png' | relative_url }}" alt="" /></center>

우선 기본적으로 Java 개발 툴(ex. Eclipse)에서 작성된 Java 코드는 .java 파일로 저장된다. 그 후 Java 파일의 빌드가 이루어지면 Java 컴파일러는 javac라는 명령어를 통해 .class 파일을 생성한다.
.class 파일은 바이트코드(반기계어)이기 때문에 OS에서 바로 실행될 수 없다. 그래서 JVM은 OS가 해당 바이트코드를 이해할 수 있도록 해석해주는 역할을 한다.
이제 런타임 시점이 되면 Class Loader에 의해 JVM 내부에 로드된다.

- ***Execution Engine***

<center><img src="{{ 'assets/images/java/memory/java_memory_03.png' | relative_url }}" alt="" /></center>

그 다음 JVM 내 로드된 바이트코드는 Execution Engine에 의해 기계어로 해석되어 메모리 상(Runtime Data Area)에 배치된다. Execution Engine은 인터프리터와 JIT(Just In Time)컴파일러로 구성되어 있다.
앞서 말한 코드가 JVM을 통해 해석되기 때문에 OS으로부터 직접 제어 받는 방식보다 속도면에서 느리다는 단점을 보완하기 위해 JIT 컴파일러가 존재한다.
인터프리터는 바이트코드를 한줄씩 실행하기 때문에 속도가 느리다는 단점을 가진다. 이를 보완하기 위해 JIT 컴파일러는 바이트코드 전체를 어셈블러와 같은 네이티브코드로 컴파일하여 직접 실행하게 된다.
JIT 컴파일러에 의해 해석된 코드는 캐시에 보관되기 때문에 한번 컴파일 된 후에는 빠르게 수행된다. 하지만 이를 변환하는데 인터프리터 방식보다 훨씬 오래걸린다는 비용이 발생하게 된다.
이러한 이유로 Execution Engine은 인터프리터 방식으로 실행하다가 적절한 시점에 JIT 컴파일러 방식을 선택한다. 예를들어 한번만 실행되는 코드는 인터프리터 방식을 사용하는 것이 유리하다.

- ***Runtime Data Area***

<center><img src="{{ 'assets/images/java/memory/java_memory_08.png' | relative_url }}" alt="" /></center>
