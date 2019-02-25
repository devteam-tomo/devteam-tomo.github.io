---
layout: post
title: 1월_Brad_자바로 배우는 핵심 자료구조와 알고리즘 리뷰
excerpt: ""
categories: subject
tags: [new-post]
comments: true
---
### Chapter 1. Interface 

![tds_book](/images/brad/tds_book.jpg){: .center}

- 내용 : <code>자바로 배우는 핵심 자료구조와 알고리즘</code> 내용 리뷰
  
* 목차
{:toc}

# 학습목표

앞으로 실습을 통해 다음의 3가지 학습 목표를 갖는다.

### 자료구조
{: .no_toc}

- 자바 컬렉션 프레임워크<sub>(Java Collection Framework, JFC)</sub> 구조로 시작하여, List, Map, 과 같은 자료구조를 사용과 동작 방법

### 알고리즘 분석
{: .no_toc}

- 코드를 분석하고 이 코드가 얼마나 빠른 동작을 하고 얼마나 많은 공간(메모리)를 필요한지 예측

### 정보 검색
{: .no_toc}

- 자료구조와 알고리즘을 활용한 간단한 웹 검색 엔진을 만들어 봄

---

# List Interface

앞으로 몇가지 실습을 통해 `List Interface`를 분석하고 학습하려 한다.

JFC<sub>Java Framework Collection</sub>는 List 라는 interface를 통해 ArrayList와 LinkedList라는 두 구현 클래스를 제공한다.

List interface를 구현하는 클래스는 약 20가지 메서드를 포함한 특정 메서드를 구현해야한다.

따라서 List Interface를 구현하는 ArrayList와 LinkedList는 모두 동일한 메서드를 제공하므로 상호 교환할 수 있다는 것이다.

그렇다면 이 두 클래스의 차이점은 무엇일까?

## ArrayList vs LinkedList

![jfc](http://www.nextree.co.kr/content/images/2016/09/jdchoi_20140220_JCF.png)

두 클래스의 차이는 다음의 링크에 자세히 소개되고 있다. [ArrayList와 LinkedList 차이](http://www.nextree.co.kr/p6506/)

쉽게 정리하자면 다음과 같다.

![list](/images/brad/2_list.png)
<figcaption>생활코딩의 리스트 비교</figcaption>

하나의 **건물(메모리)**에 **회사(List의 요소)**가 입주해있다. 

#### ArrayList
{: .no_toc}

<code>Array List</code>는 그림과 같이 건물에 나란히 회사가 위치해있는 형태이다. 회사의 사무실이 이렇게 나란히 붙어있기 때문에 사무실의 위치를 찾기가 굉장히 수월하다.

하지만 이러한 방식은 빈 공간을 허용하지 않기 때문에, 중간의 하나의 사무실에 공실이 발생할 경우 전체 사무실이 빈 공간을 채우려 이동해야 한다.

#### LinkedList
{: .no_toc}

이와달리 <code>Linked List</code>는 사무실이 **산발적으로 연결(Linked)**된 방식이다.

우리는 사무실의 위치를 찾기 위해 각각의 사무실을(예: 201호 - 304호 - 408호 등) 방문하여 다음 사무실이 어디인지를 물어봐야한다.

예를들면, **201호**를 먼저 방문하여 직원에게 다음 사무실이 어딘지 물어봐야 다음 사무실이 **304호** 라는 것을 알 수 있다는 것이다.

어떤가, 벌써 찾아가기가 번거롭다.

하지만 이 방식은 나란히 있는 것이 아니기 때문에 중간에 사무실에 공실이 발생하거나 추가되어도 전혀 상관없다. 어차피 우리는 각자의 위치(주소)를 갖고 있기 때문이다.

따라서 다음의 특징을 갖는다.

![List성능비교](https://s3.ap-northeast-2.amazonaws.com/opentutorials-user-file/module/1335/2885.png)

---

# 실습1

본론으로 돌아와 List Interface를 살펴보자.

## List Interface 사용하기

ArrayList와 LinkedList는 <code>List</code>를 구현(implements)하여 사용하는 클래스이다.

따라서 다음과 같은 구조로 사용할 수 있다.

## 실습 코드

~~~java
public class ListClientExample {

    @SuppressWarnings("rawtypes")
    private List list;

    @SuppressWarnings("rawtypes")
    public ListClientExample() {
        list = new LinkedList();
    }

    @SuppressWarnings("rawtypes")
    public List getList() {
        return list;
    }

    public static void main(String[] args) {
        ListClientExample lce = new ListClientExample();
        @SuppressWarnings("rawtypes")
        List list = lce.getList();
        System.out.println(list);
    }
}
~~~

## 테스트 코드
~~~java
public class ListClientExampleTest {
	/**
	 * Test method for {@link ListClientExample}.
	 */
	@Test
	public void testListClientExample() {
		ListClientExample lce = new ListClientExample();
		@SuppressWarnings("rawtypes")
		List list = lce.getList();
		assertThat(list, instanceOf(ArrayList.class) );
	}
}
~~~

### 테스트 실행결과
{: .no_toc}

![failed_test](/images/brad/1_tds_ch1_failed_test.png)

첫번째 빨간불을 맛보았다.

오류 내용을 살펴보면, ArrayList의 instance를 기대했는데, **But** LinkedList 라는 것이다.

`getList()`로 부터 획득하는 리스트의 인스턴스가 문제가 있는 모양인데, 거슬러 올라가면 getList()의 리스트 인스턴스는 

~~~java
    @SuppressWarnings("rawtypes")
    public ListClientExample() {
        list = new LinkedList();
    }
~~~

이처럼 LinkedList()로 초기화된 모습이다.

테스트를 통과시키기 위해선 `ArrayList`가 필요하다. 이 초기화 작업만 바꾸어 주면 될 것 같다.

~~~java
    @SuppressWarnings("rawtypes")
    public ListClientExample() {
        list = new ArrayList();
    }
~~~

![success_test](/images/brad/1_tds_ch1_success.png)

이 클래스의 구성은 유용하진 않지만, List를 캡슐화하는 클래스의 필수 요소를 가지고 있다.

위 클래스는 생성자를 통해서만 LinkedList를 초기화하고, getList를 통해 List객체애 대한 참조를 반환한다.

경우에 따라 ArrayList를 사용하고자 한다면, 생성자를 통한 초기화만 바꾸어주면 된다.

이와 같은 방식을 **인터페이스 프로그래밍**이라고 하는데, 필요한 경우가 아니라면 LinkedList나 ArrayList를 직접 구현하지 않고 생성자를 통해 구현함으로써, 필요에 따라 생성자의 초기화만 변경하여 유연한 대처를 할 수 있다. 

> 여기서 말하는 '인터페이스'는  자바의 인터페이스가 아닌, 일반적인 의미의 인터페이스를 말한다.

첫 실습은 비교적 싱겁게(?) 끝났지만, 앞으로 다루게 될 List Interface에 대한 내용을 다루어 보았다.

이 장에서 기억해야 할 것은,
- ArrayList와 LinkedList가 분명하게 컨셉이 다르다는 것
- interface에 대한 개념(어떤 클래스를 초기화하느냐에 따라 유연하게 사용 가능)

그러면 다음 장에서 본격적으로 List에 대해서 파해쳐보자서

# 생각해보기

1. 앞선 <code>ListClientExample</code> 클래스 생성자에서 ArrayList객체를 List 인터페이스로 교체하면 어떻게 될까?
    - 생성자에서 인스턴스를 생성하지 않고, 인터페이스로만 선언한다면 List의 실체(인스턴스)가 없으므로 컴파일이 제대로 되지 않는다.

2. 왜 List 인터페이스로는 인스턴스가 생성되지 않을까?
   - 자바의 interface는 인스턴스를 생성하는 객체로 사용되지 않는다. interface를 사용해서 어떠한 클래스를 디자인할 것인지 명세(또는 목차)만을 제공한다.

   따라서 List가 interface로 설계된 이유 역시, List라는 전체 구조를 필요애 따라 구현(ArrayList or LinkedList)할 수 있도록 가이드라인만 제공해준 것이 아닐까.

[참고]
- https://www.holaxprogramming.com/2014/02/12/java-list-interface/
- http://www.nextree.co.kr/p6506/
- https://opentutorials.org/module/1335/8821