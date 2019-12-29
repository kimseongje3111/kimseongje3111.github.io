---
title: "[JAVA] HashMap"
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

이번 포스트의 주제는 Java HashMap의 동작 과정에 대한 내용입니다.

## 컬렉션 프레임워크 (Collection Framework)
---

***Collection Framework***란 Java에서 다수의 데이터를 쉽고 효율적으로 처리하기 위한 표준화된 방법을 제공하는 클래스들의 집합이다.
즉, Java에서 데이터를 저장하기 위한 자료 구조와 이를 처리하는 알고리즘을 구조화하여 구현된 클래스들을 말한다.

<img src="{{ 'assets/images/java/hashmap/hashmap_01.png' | relative_url }}" alt=""/>

Java에서 제공하는 ***Collection Framework***의 전체 구조는 위와 같고, 이들은 모두 인터페이스를 사용하여 구현된다.
다시 말해 우리가 실제로 사용하는 ***Collection Framework***의 자료 구조는 이처럼 상속 관계로 이루어진 인터페이스들의 최종 구현체(구현 클래스)이다.
***Collection Framework***이 제공하는 주요 인터페이스는 구조에 따라 크게 3가지로 나눌 수 있다.

1. ***List*** (순서가 있는 데이터, 중복 허용)  
2. ***Set*** (순서가 없는 데이터, 중복 불가)  
3. ***Map*** (순서가 없고 키와 값으로 이루어진 데이터 집합, 값만 중복 허용)

***Set***과 ***List***는 다시 이들의 공통된 부분을 정의하고 있는 ***Collection*** 인터페이스를 상속받는다.
하지만 구조적인 차이로 Map은 별도로 정의되어 있다.

이번 포스트의 주제인 ***HashMap*** 또한 ***Collection Framework***의 ***Map*** 인터페이스를 구현한 클래스이며 이를 ***컬렉션 클래스(Collection Class)***라고 한다.

## Map 인터페이스(Interface)
---

이제 ***HashMap***이 ***Map*** 인터페이스의 구현체인것을 알았다.
앞서 말했듯이 ***Map*** 인터페이스에 포함되는 데이터들은 키(Key)와 값(Value)의 쌍으로 이루어진 집합이다. 쉽게 말해 키와 값이 '맵핑(Mapping)' 된다.
또 다른 특징은 순서가 유지되지 않고 마지막에 저장한 값이 저장된다. 또한 키의 중복이 불가하며 값의 중복만 허용한다.

그래서 Java의 ***Map*** 인터페이스는 이러한 특징을 가지는 데이터들 즉, 키-값의 쌍을 처리하기 위해 내부적으로 ***Entry*** 인터페이스(***Map.Entry***)를 정의하고 있다.
밑의 코드는 ***Map.Entry*** 인터페이스를 implements하는 ***Entry*** 인터페이스의 일부이다.

```
transient Entry<K,V>[] table = (Entry<K,V>[]) EMPTY_TABLE;    // 해시 버킷 리스트(테이블)

static class Entry<K,V> implements Map.Entry<K,V> {
    final K key;        // 키
    V value;            // 값
    Entry<K,V> next;    // 다음 버킷
    int hash;           // 해시 값

    Entry(int h, K k, V v, Entry<K,V> n) {
        value = v;
        next = n;
        key = k;
        hash = h;
    }

    public final K getKey() { … }
    public final V getValue() { …}  
    public final V setValue(V newValue) { … }
    public final boolean equals(Object o) { … }
    public final int hashCode() {…}
    public final String toString() { …}
}
```

> transient로 선언된 이유는 직렬화(serializ)할 때 전체, table 배열 자체를 직렬화하는 것보다 키-값 쌍을 차례로 기록하는 것이 더 효율적이기 때문이다.

## HashMap
---

- ***HashMap***과 ***HashTable***

일종의 Java API인 ***HashMap***과 ***HashTable***은 모두 ***Map*** 인터페이스를 구현하고 있기 때문에 제공하는 기능들은 같다.
하지만 이 둘의 가장 큰 차이점은 ***HashMap***에서 사용되는 '보조 해시 함수'이다.

보조 해시 함수에 대해서는 밑에서 다시 자세하게 설명하고, 우선 보조 해시 함수를 사용하는 ***HashMap***은 ***HashTable***와 비교하여 해시 충돌(Hash Collision) 발생 가능성이 더 낮기 때문에 상대적으로 성능상 이점을 가진다.
하지만 최근 Java 버전에서의 ***HashTable***의 역할은 JRE 1.0, 1.1 환경을 대상으로 구현된 Java 애플리케이션의 동작에 하위 호환성을 제공하는 것이다. 따라서 이 둘 사이의 성능을 비교하는 것은 큰 의미가 없다.

이제 ***HashMap***의 정의를 보자.

> 정의 : 키(Key)에 대한 해시(Hash) 값을 사용하여 값(Value)을 저장 및 조회하고, 키-값 쌍의 개수에 따라 동적으로 크기가 증가하는 'Associate Array' (Map, Dictionary, Symbol Table 등)

수학적으로 보면 정의역인 키의 집합과 공역인 값의 집합의 대응(Mapping) 관계에서 해시 함수를 이용하는 것이다.
우리가 주목해야 할 것은 역시 '해시(Hash)'이다.

- 해시 분포와 해시 충돌

우선 해시는 내부적으로 ***Hash Table***이라는 배열을 사용하여 빠른 검색 속도를 가지지만 ***Table***의 크기에 따라 성능 차이가 날 수있다.
***Hash Table***을 이용하여 데이터를 저장할 때, 해시 함수인 ***Object*** 클래스의 ***hashCode()***라는 메소드를 이용하여 해당 데이터의 인덱스가 될 고유한 숫자값인 ***Hash Code***를 만들어 사용한다.

Java에서는 어떻게 모든 객체에 대한 고유한 해시 값을 만들고 관리하는 것일까?
만일 그것이 가능하다면 '모든 객체 X, Y에 대해 X.equals(Y) == False 일 때, X.hashCode() != Y.hashCode()이다.'이라는 정의가 성립된다고 말할 수 있을 것이다.

하지만 위와 같은 완전한 해시 함수를 만드는 것은 매우 이상적이다. 그 이유는 키가 될 수 있는 모든 대상(객체)에 대한 통계적인 분포를 아는 것은 거의 불가능하기 때문이다.
예를 들어, ***Boolean***과 같이 서로 구별되는 종류가 적거나 ***Integer***, ***Long***, ***Double***과 같이 값 자체를 해시 값으로 사용할 수 있는 객체들은 완전 해시 함수의 대상이 될 수 있지만 ***String***이나 ***POJO***(Plain old java object:특정 규약에 종속되지 않는 자바 객체)에 대한 표현은 그 종류와 범위가 너무 크기 때문에 사실상 불가능하다.
또한 ***hashCode()***가 반환하는 값은 32비트 정수(int) 형이다.
하지만 논리적으로 생성 가능한 객체의 수가 2^32개 보다 많을 수밖에 없고, 만일 표현이 가능하다고 해도 ***HashMap***의 모든 객체에 대해 O(1)의 속도를 보장하기 위해서 모든 ***HashMap***이 원소가 2^32개인 배열을 가져야한다.
이는 완전한 해시 함수를 만드는것이 얼마나 이상적인 것인지 알 수 있다.

그렇다면 이제 실제 해시가 어떻게 동작하는지 알아보자.

```
int index = X.hashCode() % M;
```

위처럼 각 객체에 대한 해시 코드에 대해 M으로 나눈 나머지 값을 해시 버킷(Bucket)의 인덱스로 사용한다.
그 이유는 실제 해시 함수의 표현 정수 범위보다 작은 크기 M개의 배열만을 사용하여 메모리를 절약하기 위함이다.
그 결과, 서로 다른 해시 코드를 가지는 서로 다른 객체는 1/M의 확률로 같은 해시 버킷을 사용하게 된다.
이는 원래의 해시 함수가 충돌을 잘 회피하도록 구현되었더라도 이와는 관계 없이 발생할 수 있는 또 다른 종류의 해시 충돌이다.

먼저 배열의 크기 M을 소수로 만들면 충돌을 최소화 할 수 있다. 하지만 이 방법은 해결 방법이 아니다.
그래서 해시 충돌이 발생하더라도 데이터 저장 및 조회에 문제가 없도록 대표적으로 두 가지 방식이 사용된다.

1. 개방 주소법 (Open Addressing)  
2. 분리 연결법 (Seperate Chaining)

'개방 주소법'은 데이터 삽입시 해시 충돌이 일어난 경우 사용하지 않는 다른 해시 버킷에 해당 데이터를 삽입하는 방식이다.
다른 해시 버킷을 조회하기 위해 선형조사(Linear Probing), 제곱조사(Quadratic Probing), 이중해싱(Double Hasing) 등의 방법을 사용한다.

'분리 연결법'에서의 배열은 인덱스가 같은 해시 버킷들을 연결한 연결 리스트(Linked List)의 첫 부분(Head)이다.
즉, 충돌이 발생하는 데이터를 해당 인덱스의 연결리스트에 다음 노드로 추가하는 것이다.

<img src="{{ 'assets/images/java/hashmap/hashmap_02.png' | relative_url }}" alt=""/>

두가지 방법 모두 O(M)이다.
개방 주소법은 연속된 배열 공간에 데이터를 저장하기 때문에 캐시 효율이 높다.
하지만 배열의 크기가 커질 수록 L1, L2캐시 적중률이 낮아지기 때문에 장점이 되기 어렵다.
또한 분리 연결법과 비교하여 데이터 삭제시 데이터가 많아질수록 해시 버킷을 채운 밀도가 높아져 ***Worst Case*** 발생 빈도가 더욱 높아진다.
반면에 분리 연결법은 해시 충돌이 잘 발생하지 않도록 조정(보조 해시 함수)할 수 있다면 ***Worst Case***를 줄일 수 있다.
때문에 Java에서는 일반적으로 더 빠르고 효율적인 분리 연결법을 사용한다.

- 해시 버킷의 동적 확장

일정한 크기의 테이블에 많은 데이터가 입력된다면 충돌이 많이 발생할 것이다.
해시 버킷의 개수가 적다면 메모리 사용을 아낄 수 있지만 해시 충돌로 인해 성능 상 손실이 발생한다.
때문에 성능 저하를 방지하기 위해 테이블의 크기의 최대 용량까지 ***Threshold***를 넘을때마다 테이블의 크기를 ***Doubling***하고 모든 데이터에 대해 '재해싱'한다.

> 배열의 크기(M)는 항상 2의 제곱수, 기본적으로 ***Threshold*** = ***load factor***[0.75] * M

하지만 버킷 개수가 두 배로 증가할 때마다 모든 키-값 쌍의 데이터를 읽어 재해싱 해야 하는 비용이 발생한다.
또한 기본 생성자로 생성한 ***HashMap***을 이용하여 많은 양의 데이터를 삽입할 때, 데이터 접근에 대해 수행 시간이 길어질 수 있다.
만일 해당 ***HashMap***에 저장될 데이터 개수를 예측하고 최적의 처음 생성 시 해시 버킷 개수를 지정할 수 있다면 불필요하게 재구성하는 것을 막고 성능을 향상시킬 수 있을 것이다. 따라서 처음 ***HashMap*** 생성 시 적정한 해시 버킷 개수를 지정하는 것도 고려할 대상이다.

- 보조 해시 함수

하지만 ***Doubling***에는 결정적인 문제가 있다.
해시 버킷의 개수(M)가 항상 2의 제곱수이기 때문에 'X.hashcode() % M'를 계산할 때 X.hashCode()의 32비트 중 하위 2의 승수개의 비트만 사용하게 되어 해시 충돌이 자주 발생할 가능성이 있다. 이는 보조 해시 함수가 필요한 이유이다.

앞서 말했듯이 M이 소수이면 인덱스의 분포가 가장 균등하다.
하지만 M이 소수가 아니기 때문에 해시 충돌을 최소화하기 위해서는 다른 방법을 사용하여 인덱스 값의 분포를 균등하게 만들어야 한다.
결과적으로 보조 해시 함수의 목적은 키의 해시 값을 변형하여 인덱스 값의 분포를 균등하게 만드는 방법으로 이러한 해시 충돌 가능성을 줄이는 것이다.

Java 8에서는 Java 7보다 개선되고 단순한 형태의 보조 해시 함수를 가진다. 하지만 구체적인 구현 내용은 생략하도록 하겠다.
다만 최근의 해시는 트리를 사용하기 때문에 충돌 시 발생하는 비용 문제가 완화되었고, 해시 함수 자체가 인덱스의 분포의 균등 분포를 이루도록 잘 구현되어 더 단순한 형태의 보조 해시 함수를 사용하더라도 성능 상의 손해가 없다.

- HashMap 내부 구현 및 동작

아래 코드는 Java 7의 put() 메서드이다.

```
public V put(K key, V value) { if (table == EMPTY_TABLE) { inflateTable(threshold); } if (key == null) return putForNullKey(value);
        int hash = hash(key);                   // 보조 해시 함수
        int i = indexFor(hash, table.length);   // 해시 버킷의 인덱스

        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }

        modCount++;                       // ConcurrentModificationException
        addEntry(hash, key, value, i);    // 새로운 데이터

        return null;
    }
```

1. ***put()***  
보조 해시 함수를 이용한 새로운 해시 값에 대해 인덱스를 찾아 연결 리스트 형태로 연결하고 만일 키가 이미 존재하면 값만 대체한다.

2. ***doubling()***  
해시 버킷의 동적 확장을 말한다.

3. ***indexFor()***  
계산된 해시 값을 이용하여 테이블의 인덱스를 구하는 함수이다. 해시 값과 [테이블의 크기(M) - 1]의 '& 연산' 결과를 리턴한다.
2의 제곱수인 테이블의 크기(M)에 -1를 하게 되면 모든 비트가 1로 채워진 수가 되며, 이 수는 ***doubling***의 수행마다 상위 1비트가 추가된다.
그 결과 기존 인덱스의 범위와 같거나 새롭게 추가된 비트 범위의 인덱스만을 가지게 되며 좀 더 효율적인 데이터의 분산을 기대할 수 있다.
이는 해시 충돌 시 발생하는 비용과 ***Trade-off*** 관계가 될 수 있다.

4. ***ConcurrentModificationExceiption()***  
***HashMap***은 ***Thread-safe***한 구조가 아니다.
그래서 ***Multi-thread*** 환경에서 ***modCount***라는 변수를 이용하여 문제 발생시 ***Exception***을 발생시킨다.

5. ***get()***  
테이블의 연결 리스트에서 equals()와 키를 이용하여 값을 찾는다.

<img src="{{ 'assets/images/java/hashmap/hashmap_03.png' | relative_url }}" alt=""/>

- JAVA 8 HashMap

만일 객체들의 해시 값이 균등 분포 상태라고 한다면 ***get()*** 메서드의 호출에 대한 기댓값은 ‘***E(N/M)***’이다.
그러나 Java 8에서는 이보다 더 나은 ‘***E(log(N/M))***’을 보장한다.
그 이유는 데이터의 개수가 많아진다면 연결 리스트 대신 트리를 사용하기 때문이다.
실제 해시 값은 균등 분포가 아니며 혹여 균등 분포를 따른다고 해도 일부 해시 버킷 몇 개에 데이터가 집중될 우려가 있다.
그래서 데이터의 개수가 특정 기준 이상이라면 트리를 사용하는 것이 성능상 이점을 가진다.

아래와 같이 하나의 해시 버킷에 할당된 키-값 쌍의 개수를 기준으로 연결 리스트와 트리 중 어떤 것을 사용할지 결정된다.

```
static final int TREEIFY_THRESHOLD = 8;
static final int UNTREEIFY_THRESHOLD = 6;
```

상수 형태로 기준을 정한다.
하나의 해시 버킷에 키-값 쌍이 8개 이상이라면 연결 리스트를 트리로, 6개 이하가 된다면 다시 트리를 연결 리스트 형태로 변경한다.
2만큼의 차이는 한 개의 키-값 쌍에 대해 반복된 삽입/삭제 시 불필요한 변경으로 인한 성능 저하를 방지하기 위함이다.
트리는 연결 리스트보다 메모리 사용량이 많기 때문에 데이터의 개수가 적을 때 성능적으로 두 가지를 비교하는 것은 의미가 없다.

Java 8에서는 ***Entry*** 클래스와 내용이 같은 ***Node*** 클래스를 사용하지만 트리 사용을 위해 하위 클래스에 ***TreeNode***를 포함한다.
이때 사용되는 트리는 ***Red-Black Tree***이며 해시 값을 대소 판단의 기준으로 삼는다.

이번 포스트에서는 Java에서 우리가 많이 활용하는 HashMap의 동작 과정에서 대해 알아보았습니다.
추가적으로 설명하면 String 객체에 대해서는 별도의 해시 함수가 존재합니다.
위 내용에서 다루지 않았지만 해당 내용에 대한 자세한 설명을 원하신다면 아래 참조 사이트와 Horner’s method를 참고하시면 좋을 것 같습니다.

이것으로 이번 포스트를 마무리하겠습니다. 감사합니다.

- 그림/내용 참조  
[사이트 1](https://d2.naver.com/helloworld/831311)  
[사이트 2](https://www.opentutorials.org/course/1223/6446)