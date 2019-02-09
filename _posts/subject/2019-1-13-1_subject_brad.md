---
layout: post
title: 1월_Bread_자바로 배우는 핵심 자료구조와 알고리즘 리뷰
excerpt: ""
categories: subject
tags: [new-post]
comments: true
---

### 주제
![book](/images/bread/B1619666323_l.jpg)

- 내용 : <code>자바로 배우는 핵심 자료구조와 알고리즘</code> 내용 리뷰

---

### Chapter 1. Interface 

## ArrayList vs LinkedList
Java Collection Framework(JCF)는 List라는 interface를 통해 ArrayList와 LinkedList를 제공한다.

![jfc](http://www.nextree.co.kr/content/images/2016/09/jdchoi_20140220_JCF.png)

두 클래스의 차이를 자세히 살펴보기 위해선 다음의 링크를 참고하자 [ArrayList와 LinkedList 차이](http://www.nextree.co.kr/p6506/)

정말 간단하게만 둘의 차이를 요약하자면, 

 > ArrayList는 데이터들이 순서대로 쭉 늘어선 배열의 형식을 취하고 있는 반면 LinkedList는 순서대로 늘어선 것이 아니라 자료의 주소 값으로 서로 연결되어 있는 구조를 하고 있다.

 다시 말해,

<code>ArrayList</code>는 이름(Array)에서 볼 수 있듯이, index를 담고 있어서 특정 인덱스를 참고하고자 할 때 검색이나 접근에 용이하다.

반면 리스트 중간의 값이 제거/삽입 되는 경우 중복되거나 비어있는 인덱스를 허용하지 않기 때문에 모든 배열 값들의 이동이 일어난다. 따라서 빈번한 **삽입/제거**가 발생하는 경우에는 비효율적인 구조라 볼 수 있다.

<code>LinkedList</code>는 역시 이름(Linked)에서 볼 수 있듯이, 줄줄이 노드가 연결된 구조이다.

LinkedList는 배열의 index값이 아닌, 각 객체의 주소값으로 줄줄이 연결되어 있는 구조이기 때문에 **검색**(외부에서 특정 객체에 대한 접근)은 효율적이지 못하다.

하지만 이벤트가 발생한 노드의 주소지만 알고 있다면, 그 위치에 **삽입/제거**만 진행할 뿐 전체 노드의 구조는 변함이 없기 때문에 매우 빠른 처리를 할 수 있다.

![list_compare](http://i.stack.imgur.com/skN72.png)


#### 검색
데이터 검색 시에는 ArrayList는 LinkedList에 비해 굉장히 빠르다. ArrayList는 인덱스 기반의 자료 구조이며 get(int index) 를 통해 O(1) 의 시간 복잡도를 가진다. 그에 비해 LinkedList는 검색 시 모든 요소를 탐색해야 하기 때문에 최악의 경우에는 O(N)의 시간 복잡도를 가진다.

#### 삽입, 삭제

LinkedList에서의 데이터의 삽입, 삭제 시에는 ArrayList와 비교해 굉장히 빠른데, LinkedList는 이전 노드와 다음 노드를 참조하는 상태만 변경하면 되기 때문이다. 삽입, 삭제가 일어날 때 O(1)의 시작 복잡도를 가진다. 반면 ArrayList의 경우 삽입, 삭제 이후 다른 데이터를 복사해야 하기 때문에 최악의 경우 O(N) 의 성능을 내게 된다.


>단순하게 생각하면 쉬운 것 같다.<br/>
우리는 귀찮거나 반복되거나 하는 비생산적인 일을 싫어하는 프로그래머다.<br/>
극도의 효율을 중시하는 프로그래머가 사용하고, 만든 언어인 만큼 이 두 개념 역시 서로의 부족한 부분에 대한 효율을 채우기 위하여 구분된 클래스이다.<br/>
따라서 한가지 개념만 완벽하게 이해한다면, 그와 비슷한 기능을 하는 다른 클래스/객체에 대한 이해는 덤으로 딸려 올 것이다.<br/>

---

다시 본론으로 돌아와 List Interface를 살펴보자.

## List Interface
ArrayList와 LinkedList는 <code>List</code>를 베이스로 하여 동작하는 클래스이다.

따라서 다음과 같은 구조로 사용할 수 있다.

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

## @SuppressWarnings Annotaion

1. all : 모든 경고를 억제
2. cast : 캐스트 연산자 관련 경고 억제
3. dep-ann : 사용하지 말아야 할 주석 관련 경고 억제
4. deprecation : 사용하지 말아야 할 메소드 관련 경고 억제
5. fallthrough : switch문에서의 break 누락 관련 경고 억제
6. finally : 반환하지 않는 finally 블럭 관련 경고 억제
7. null : null 분석 관련 경고 억제
8. rawtypes : 제네릭(데이터 형식에 의존하지 않고, 하나의 값이 여러 다른 데이터 타입들을 가질 수 있음)을 사용하는 클래스 매개 변수가 불특정일 때의 경고 억제
9. unchecked : 검증되지 않은 연산자 관련 경고 억제
10. unused : 사용하지 않는 코드 관련 경고 억제
11. incomplete-switch : switch문 case에 누락 관련 경고 제외

---

이 클래스의 구성은 유용하진 않지만, List를 캡슐화하는 클래스의 필수 요소를 가지고 있다.

위 클래스는 생성자를 통해서만 LinkedList를 초기화하고, getList를 통해 List객체애 대한 참조를 반환한다.

경우에 따라 ArrayList를 사용하고자 한다면, 생성자를 통한 초기화만 바꾸어주면 된다.

이와 같은 방식을 **인터페이스 프로그래밍**이라고 하는데, 필요한 경우가 아니라면 LinkedList나 ArrayList를 직접 구현하지 않고 생성자를 통해 구현함으로써, 필요에 따라 생성자의 초기화만 변경하여 유연한 대처를 할 수 있다. 

> 여기서 말하는 '인터페이스'는  자바의 인터페이스가 아닌, 일반적인 의미의 인터페이스를 말한다.


## [생각해보기]
1. 앞선 <code>ListClientExample</code> 클래스 생성자에서 ArrayList객체를 List 인터페이스로 교체하면 어떻게 될까?
    - 생성자에서 인스턴스를 생성하지 않고, 인터페이스로만 선언한다면, getter로 쓰이고 있는 함수에서 반환할 수 있는 list의 실체(인스턴스)가 없으므로 컴파일이 제대로 되지 않는다.

2. 왜 List 인터페이스로는 인스턴스가 생성되지 않을까?
    - 어려운 질문이네요....


[참고]
- https://www.holaxprogramming.com/2014/02/12/java-list-interface/
- http://www.nextree.co.kr/p6506/