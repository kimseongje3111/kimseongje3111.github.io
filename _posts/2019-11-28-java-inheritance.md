---
title: "[JAVA] Inheritance"
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

이번 포스트의 주제는 Java 상속(Inheritance)에 대한 내용입니다. 이다음 포스트에서 다룰 추상 클래스와 인터페이스에 대한 이해를 돕기 위한 내용입니다.

## 상속(Inheritance)의 개념 및 특징
---

설명에 앞서 먼저 일상에서 쓰이는 상속의 의미를 생각해보자.
상속이란 말 그대로 부모님의 재산을 자식들이 물려받는 행위이다.
다시 말해 상속인은 부모, 피상속인은 자식, 상속 대상은 재산이다.
Java에서 쓰이는 상속의 의미도 비슷하다. 자식(클래스)가 부모(클래스)로부터 무언가를 물려받는 것이다.

자식 클래스는 하위 클래스 또는 서브(Sub) 클래스라고도 불린다.
그와 반대로 부모 클래스는 상위 클래스 또는 슈퍼(Super) 클래스로 표현 될 수 있다.

그렇다면 Java에서 상속의 대상은 무엇일까?

Java에서 상속의 대상은 상위 클래스의 모든 멤버 즉, 상위 클래스의 필드와 메서드가 된다.
단, 접근 제어자가 private, default인 멤버는 제외한다. Java에서의 상속은 아래와 같이 추가적으로 몇 가지 특징을 가진다.

1. 하위 클래스는 상위 클래스를 선택해서 상속받는다.  
2. 다중 상속을 지원하지 않는다. (단일 상속)  
3. 상속의 횟수의 제한이 없다. (한 상위 클래스는 여러 하위 클래스에게 상속이 가능하다.)  
4. 궁극적으로 Object 클래스를 제외한 모든 클래스는 Java의 클래스 계층 구조에 따라 상위 클래스를 가진다. (최상위 클래스 : java.lang.Object)

Java가 다중 상속을 지원하지 않는 이유는 이른바 ‘다이아몬드 문제’ 때문이다.
만일 어떤 하위 클래스가 2개 이상의 상위 클래스를 상속 받았을 때, 하위 클래스는 상위 클래스 간의 멤버 이름 중복에서 발생할 수 있는 충돌 문제에 직면하게 된다.
이에 Java는 내부적으로 다중 상속 구현이 불가하도록 설계되어 있다. 반면에 같은 객체 지향 언어인 C++은 다중 상속을 막지 않고 이와 같은 문제를 개발자에게 맡겨두고 있다.
이러한 점을 미루어보아 비슷하지만 다른 두 언어의 성격을 볼 수 있다.

<img src="{{ 'assets/images/java/inheritance/java_inheritance_01.png' | relative_url }}" alt=""/>

위 그림과 같이 java.lang 패키지에 정의된 Object 클래스는 작성하는 클래스를 포함하여 모든 클래스에 공통적인 동작을 정의하고 구현한다.
Java에서 많은 클래스들은 Object에서 직접 파생되거나 그 하위 클래스들 중 일부에서 파생되며 결과적으로 클래스 계층 구조를 형성한다.
결과적으로 이를 통해 계층적으로 클래스들을 분류하고 관리할 수 있다.

Java에서 상속을 사용하여 얻는 이점은 이뿐만이 아니다.

Java 레퍼런스 문서는 이렇게 설명하고 있다.
새로운 클래스를 작성하려 할 때, 원하는 코드가 포함된 클래스가 이미 있는 경우 기존 클래스에서 새로운 클래스를 파생시킬 수 있고 필드와 메서드를 직접 작성 또는 디버깅할 필요 없이 재사용 할 수 있다.
이는 간단하지만 강력한 아이디어다.

이처럼 Java는 상속을 사용함으로써 중복 코드를 줄이고, 유지 보수를 편리하게 한다.
이를 정리하면 Java의 상속은 코드의 재사용성과 간결성을 향상시키는 이점을 가진다.

## 상속의 구현
---

```
// 하위 클래스 : B, 상위 클래스 : A
class B extends A {

}
```

상속의 구현은 간단하다. 위 결과 하위 클래스인 B는 상위 클래스 A의 필드와 메서드를 상속받는다.
앞서 말했듯이 private, default 접근 제어자는 제외하며 물론 static 멤버도 대상이 된다.
Java에서는 이와 같은 상속 관계를 ***IS-A*** 관계라고도 말한다. B는 A의 하위 개념으로서 'B는 A이다'라고 표현한다.
쉽게 예를 들면 개를 동물이라는 큰 범위 아래의 하위 개념이라고 생각했을 때, '개는 동물이다'라고 표현할 수 있다.

아래 코드를 통해서 몇 가지 경우를 더 살펴보자.

- *Employee.java*  
```
public class Employee {

    public static int totalEmployee = 0;  // 전체 사원수

    private String employeeNo;            // 사번
    private String name;                  // 이름
    private String part;                  // 부서

    public Employee(String employeeNo, String name, String part) {
        this.employeeNo = employeeNo;
        this.name = name;
        this.part = part;
    }

    // Getter & Setter 생략 ..

    @Override
    public String toString() {
       return "EmployeeNumber [" + employeeNo + "]" +
               "\nName : " + name +
               "\nPart : " + part + "\n";
    }
}
```

우선 Employee 클래스는 사원의 기본 정보(사번, 이름, 부서)와 전체 사원수에 대한 변수를 가진다.
전체 사원수를 의미하는 변수 totalEmployee는 static으로 선언되어 있고 0으로 초기화하였다.
또한 생성자와 toString 메서드를 포함한다.

- *Manager.java*  
```
public class Manager extends Employee {

    private int workingYears;   // 근무 연차

    public Manager(String employeeNo, String name, String part, int workingYears) {
        super(employeeNo, name, part);
        this.workingYears = workingYears;
        totalEmployee++;
    }

    @Override
    public String toString() {
        return super.toString() + "WorkingYears : " + workingYears;
    }
}
```

- *Intern.java*  
```
public class Intern extends Employee {

    private int contractPeriod;   // 계약 기간

    public Intern(String employeeNo, String name, String part, int contractPeriod) {
        super(employeeNo, name, part);
        this.contractPeriod = contractPeriod;
        totalEmployee++;
    }

    @Override
    public String toString() {
        return super.toString() +
                "Details : Internship\n" +
                "ContractPeriod : " + contractPeriod;
    }
}
```

Manager와 Intern 클래스는 Employee를 상속받고 각각 추가적으로 근무 연차, 계약 기간에 대한 변수를 가진다.
만약 동일한 이름의 변수가 상위 클래스와 하위 클래스에 존재한다면 상위 클래스의 변수는 가려진다.
코드를 보면 Employee 클래스의 사원 정보 변수는 private으로 선언되어 있기 때문에 상속의 대상이 되는 직접 사용 가능한 멤버는 전체 사원수 변수와 생성자를 포함한 메서드이다.

하지만 이것은 문제가 되지 않는다. 생성자 또는 Setter와 같은 사용 가능한 메서드를 통해 간접적으로 접근이 가능하기 때문이다.
대표적으로 하위 클래스에서 상위 클래스 객체를 가리키는 포인터 키워드인 ***super***를 사용한다.
위 코드에서도 하위 클래스들의 생성자에서 ***super*** 키워드를 이용해 상위 클래스인 Employee의 생성자를 호출하여 사원 정보 변수를 초기화하는 것을 볼 수 있다.
만일 상위 클래스와 하위 클래스 모두 동일한 메서드 또는 변수가 있을 때 상위 클래스 멤버를 가리킨다.
위 코드의 하위 클래스들의 toString 메서드에서 확인할 수 있다. 결과적으로 하위 클래스의 객체를 생성했다 하더라도 메모리에는 상위 클래스의 객체 또한 할당되는 것이다.

- *Main.java*  
```
public class Main {

    public static void main(String[] args) {

        Manager manager1 = new Manager("A001", "Kim", "Management", 15);
        Intern intern1 = new Intern("C001", "Park", "Management", 6);

        System.out.println("________________________________________");
        System.out.println(manager1);
        System.out.println("________________________________________");
        System.out.println(intern1);
        System.out.println("________________________________________");
        System.out.println("Total Employees : " + Employee.getTotalEmployee());

    }
}
```

아래는 코드 결과 화면이다.

<img src="{{ 'assets/images/java/inheritance/java_inheritance_02.PNG' | relative_url }}" alt=""/>

## 다형성 (Polymorphism)
---
