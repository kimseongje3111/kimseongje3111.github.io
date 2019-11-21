---
title: "[JAVA] Java 함수 호출 및 인자 전달 방식"
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

이번 포스트의 주제는 Java의 함수 호출 및 인자 전달 방식에 대한 내용입니다.

## 함수 호출 및 인자 전달 방식
---

어떤 함수를 호출하면 메모리의 스택 영역에 호출된 함수에 대한 별도의 임시 프레임이 새롭게 추가되고, 해당 프레임 안에는 함수의 매개 변수, 로컬 변수, 리턴 값 등의 정보를 가진다.
이때, 함수를 호출한 ***Caller***는 호출 대상인 ***Callee***에게 인자(Argument)를 전달할 수 있다. 다양한 프로그래밍 언어에서 사용되는 인자 전달 방식은 대표적으로 아래 2가지가 있다.

***1. 값에 의한 전달 (Call By Value)***
***2. 참조에 의한 전달 (Call By Reference)***

## Call By Value 와 Call By Reference
---

- ***Call By Value***

'***Call By Value***'는 함수 호출 시 ***Caller***가 전달하려는 인자의 메모리에 저장되어 있는 값을 복사하여 ***Callee***에게 전달하는 방식이다.
이렇게 ***Callee***에게 전달된 인자는 복사된 값이기 때문에 원래의 변수와 별개의 변수가 된다. 결과적으로 ***Callee*** 내에서 인자의 값을 변경하여도 원래의 변수에 영향을 미치지 못한다.

<img src="{{ 'assets/images/java/function_call/function_call_01.png' | relative_url }}" alt=""/>

위 그림은 ***Call By Value*** 방식을 따르는 프로그래밍 언어에서 두 변수의 값을 교환하는 일반적인 ***Swap*** 함수에 대해 호출 전/후 메모리 및 변수의 모습을 보여준다.
간단히 설명하면 각각 10, 20이 저장된 a, b는 전달하려는 인자가 되고 ***'Swap(a, b)'***의 형태로 함수를 호출했을 때, ***Call By Value*** 방식을 따르기 때문에 ***Swap*** 함수 내부의 매개 변수 x, y는 인자의 값이 복사되어 값의 교환이 이루어진다.
그 결과, 위 그림과 같이 x, y의 값이 교환되었지만 원래의 변수 a, b의 값에는 영향을 미치지 않는 모습을 볼 수 있다.

- ***Call By Reference***

그와 반대로 '***Call By Reference***'는 ***Caller***가 전달하려는 인자의 메모리 주소를 복사하여 ***Callee***에게 전달하는 방식이다.
즉, 인자로 전달된 변수의 원래 주소를 전달하는 것이다. 따라서 ***Callee*** 내부에서의 인자 또한 원래의 변수 주소를 참조하기 때문에 함수 안에서 인자의 값을 변경하는 것은 원래의 변수 값을 변경하는 것과 동일하게 동작한다.

그렇다면 ***Call By Reference*** 방식을 따르는 프로그래밍 언어에서 이전과 동일하게 ***Swap*** 함수를 호출했을 때, 동작 결과 모습을 보자.

<img src="{{ 'assets/images/java/function_call/function_call_02.png' | relative_url }}" alt=""/>

***'Swap(a, b)'***으로 변수 a, b를 인자로 전달하게 되면 함수 내 매개 변수 x, y는 각각 a, b의 주소를 전달받는다.
위 그림에서 보듯 각각 a와 x, b와 y는 동일한 주소를 참조하며 참조된 주소에는 실제 값이 저장되어 있다.
결과적으로 ***Call By Reference*** 방식을 따르기 때문에 ***Swap*** 함수 내부에서 x, y의 값을 교환하는 것은 x, y가 참조하는 주소의 실제 값이 교환되는 것이다.
따라서 동일한 주소를 참조하는 원래의 변수 a, b의 값도 교환되는 결과를 보여준다.

## Java에서의 함수(메서드) 호출
---

Java는 기본적으로 ***Call By Value*** 방식을 따른다.
하지만 전달하려는 인자가 참조 타입의 변수일 경우 복사되는 값은 해당 변수가 참조하고 있는 주솟값이 된다.
이는 참조를 위한 특정 주솟값을 전달한다는 개념에서 ***Call By Reference*** 방식과 유사한 모습을 보여준다.
그래서 메서드 내에서의 매개 변수는 복사된 주솟값으로 원래의 변수가 참조하는 주소를 동일하게 참조한다.

그렇다면 Java는 ***Call By Value*** 방식만을 따른다고 말할 수 있을까?

결론부터 말하자면 Java는 ***Call By Value*** 방식을 따르는 게 맞다.
예외적으로 참조 타입의 변수를 인자로 전달하는 경우 메서드 내에서의 매개 변수가 원래의 변수와 같은 주소를 참조하며 값을 변경할 수 있다.
예를 들어 배열의 값, 객체의 필드 값 등을 메서드 내에서 변경이 가능하다.
이는 원래의 변수가 참조하는 값을 메서드 내에서 변경하여 영향을 준다는 점에서 ***Call By Reference*** 방식과 유사한 효과를 유발한다.

그럼 다시 한번 ***Call By Value*** 방식의 개념을 살펴보자.

***Call By Value*** 방식은 메서드 내에서 인자의 값을 변경하여도 원래의 변수에 영향을 미치지 못한다. 참조 타입의 변수를 인자로 전달하는 경우 처음 메서드 내의 매개 변수에 복사된 값은 주솟값이다.
Java는 ***Call By Value*** 방식을 따르기 때문에 메서드 내에서 매개 변수가 참조하는 주솟값을 변경하여도 원래의 변수가 참조하는 주솟값은 변경되지 않는다.
즉, 메서드 내의 매개 변수는 복사된 주솟값을 전달받았기 때문에 원래의 변수가 참조하는 주솟값을 변경할 수 없다. 쉽게 말해 주소 자체를 값으로 여기는 것이다.
다만 참조한 주소의 특정 객체의 필드 값 또는 배열의 값을 변경할 수 있다는 점 때문에 ***Call By Reference*** 방식과 혼동할 가능성이 있다.

아래 코드로 직접 확인해보았다.

```
public static void main(String[] args) {
    Animal dog = new Animal("Coco", "Male");
    Animal cat = new Animal("Momo", "Female");

    System.out.println("==========================================");
    System.out.println("Dog and Cat");
    System.out.println("==========================================");

    System.out.println("Dog's name is " + dog.name);
    System.out.println("Dog's sex is " + dog.sex);
    System.out.println("Cat's name is " + cat.name);
    System.out.println("Cat's sex is " + cat.sex);

    System.out.println("\n==========================================");
    System.out.println("Call By Value");
    System.out.println("==========================================");

    callByValue1(dog);
    callByValue2(cat);

    System.out.println("Dog's name is " + dog.name);
    System.out.println("Dog's sex is " + dog.sex);
    System.out.println("Cat's name is " + cat.name);
    System.out.println("Cat's sex is " + cat.sex);
}

private static void callByValue1(Animal animal) {
    animal.name = "Happy";
    animal.sex = "Female";
}

private static void callByValue2(Animal animal) {
    animal = new Animal("Teddy", "Male");
}
```

우선 이름과 성별을 필드 값으로 가지는 Animal 클래스가 존재한다.
각각 'dog'와 'cat'이라는 특정 이름과 성별을 가지는 Animal 객체를 생성하고 해당 객체(참조 타입 변수)를 매개 변수로 전달하여 메서드를 호출하는 간단한 예제다.
그리고 메서드 호출 전, 후의 결과 확인을 위한 출력문이 포함되어 있다.

Animal 클래스 타입의 매개 변수를 받는 메서드 callByValue1과 callByValue2는 각각 다른 동작을 수행한다.
첫 번째 callByValue1는 매개 변수 animal를 참조하여 해당 객체의 이름과 성별을 변경한다.
두 번째 callByValue2 또한 매개 변수 animal에 대해 이름과 성별을 변경하지만 new를 통해 객체를 재할당해준다. 이는 매개 변수 animal이 참조하는 주솟값을 변경하는 행위이다.

그렇다면 Animal 객체 'dog'와 'cat'을 인자로 전달하여 각각 위 두개의 메서드 callByValue1과 callByValue2를 호출했을 때 어떤 차이점이 존재할까?

앞서 설명한 개념을 생각하면 쉽게 예상할 수 있을 것이다.
Java에서는 메서드 내에서 매개 변수가 참조하는 주솟값을 변경하여도 원래의 변수가 참조하는 주솟값은 변경되지 않지만 참조한 주소의 특정 객체의 필드 값 또는 배열의 값은 변경할 수 있다.
따라서 결과적으로 메서드 호출 후 이름과 성별이 변경되는 객체는 'dog'가 된다.
그와 반대로 'cat'은 callByValue2 안에서 재할당하여 주솟값을 변경하였지만 원래의 'cat'이 참조하는 주솟값에는 영향을 주지 않기 때문에 메서드 호출 전, 후 결과가 동일하다.

아래는 코드 실행 결과이다.

<img src="{{ 'assets/images/java/function_call/function_call_03.PNG' | relative_url }}" alt=""/>

[전체 코드](https://github.com/kimseongje3111/ExampleCode/blob/master/Java/Post2_01.java)

이번 포스트에서는 Java의 함수 호출 및 인자 전달 방식에 대해 알아보았습니다.
사실 프로그래밍 언어의 함수 호출 및 인자 전달 방식을 구분하기 위해 'Java는 Call By Reference 방식이 아닌 Call By Value 방식을 따른다.'라는 결론을 내렸지만 포괄적으로 본다면 참조 타입 변수를 ***Call By Reference***으로 볼 수 있는 측면이 있을 수 있다고 생각합니다.
그래서 Java의 함수 호출 및 인자 전달 방식이 ***Call By Value*** 인지 ***Call By Reference*** 인지 구분하기보다 Java의 함수 호출 및 인자 전달 방식과 동작 그 자체를 이해하는 것이 더 중요하다고 생각됩니다.
이것으로 포스트를 마무리하겠습니다. 감사합니다.
