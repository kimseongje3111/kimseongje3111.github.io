---
title: [java] Java 메모리 구조
author: Seongje kim
layout: post
---

첫 포스트에 앞서 앞으로 대부분 'Java'와 'Spring'을 주로 다룰 예정입니다.
또한 모든 포스트의 주제와 내용은 개인적으로 공부한 것들을 정리하여 올리는 것이기 때문에 다소 미흡한 부분이 있을 수 있습니다. 첫 포스트의 주제는 Java 의 메모리 구조에 대한 기본적인 내용입니다.

## Java 의 메모리 구조 및 컴파일 과정

일반적인 프로그램 또는 프로세스는 OS(Operating System)위의 메모리에서 동작한다. 하지만 Java 프로그램은 OS에 **독립적**으로 실행된다. 그 이유는 **JVM(Java Virtual Machine)**이라는 특별한 가상 머신이 OS와 Java 프로그램 사이에 위치하고 OS에게 메모리 사용 권한을 부여받아 Java 프로그램을 호출하여 기계어로 해석해주는 역할을 하기 떄문이다.

그렇다면 이와 같은 JVM의 장점은 무엇일까?

앞서 말했듯이 Java 프로그램은 JVM 위에서 OS에 독립적으로 실행된다. 이는 Java 프로그램이 OS에 종속적이지 않고 어떤 디바이스 또는 환경에서도 JVM 위에서 프로그램을 실행할 수 있다는 큰 장점이 된다.
하지만 OS에 직접 제어 받는 방식보다 속도 면에서 느릴 수밖에 없다. (이를 해결하기 위해 특별한 컴파일러를 가진다)

아래 사진은 JVM의 전체 구조를 보여준다.
<span><img src="{{ 'assets/images/java/memory/java_memory_01.png' | relative_url }}" alt="" /></span>