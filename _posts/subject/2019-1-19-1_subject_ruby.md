---
layout: post
title: 1월_Ruby_Standalone Java Application에서 SpringFramework Annotation 적용해서 사용해보기
excerpt: "Standlone Java Application 환경에서 Java SpringFramework 의 Autowired Annotation 사용할 수 있도록 구현해보기"
categories: subject
tags: [new-post,java,javaspring]
comments: true
---

## 주제


- 내용 : Standlone java application 환경에서 Java SpringFramework 의 Autowired Annotation 사용할 수 있도록 구현해보기
- 계획 : 
  1. Java SpringFramework의 Bean 관리가 어떻게 이뤄지는 지 개념 이해하기
  2. Java SpringFramework Autowired기능을 구현하기 위해 필요한 Library 준비하기
  3. 실전! 프로젝트 만들어보기 
- 이슈사항 : Autowired Annotation을 우선적으로 구현해보겠습니다.

---

## 본격적으로 알아보자

### 1. Java SpirngFramework의 Bean 관리가 어떻게 이뤄지는 지 개념 이해하기

ApplicationContext는 javaspring에서 오브젝트에 대한 생성과 관계설정을 담당한다. IoC컨테이너라고 하기도 한다.
1. ApplicationContext는 앞의 @Configuration이 붙은 ContextLoader와 ServiceFactory를 설정정보로 등록하고, Bean 목록을 만든다. 
2. 클라이언트가 ApplicationContext의 getBean 메소드를 호출하면 Bean 목록에서 요청한 이름이 있는지 찾는다.
3. 있다면 Bean을 생성하는 메소드를 호출해서 오브젝트를 생성시킨 후 클라이언트에게 리턴해 준다.

### 2. Java SpringFramework Autowired기능을 구현하기 위해 필요한 Library 준비하기


<code>spring-core</code>,<code>spring-beans</code>,<code>spring-context</code> dependency를 pom.xml에 추가해준다.

```
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>3.1.1.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>3.1.1.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>3.1.1.RELEASE</version>
</dependency>
```


여기까지 dependency를 준비하면 아래와 같은 오류가 발생한다. 테스트 한 spring3버전에서는 cglib가 포함되어있지 않아서 발생하는 오류이다. 

```
java.lang.IllegalStateException: CGLIB is required to process @Configuration classes. Either add CGLIB to the classpath or remove the following @Configuration bean definitions: [serviceFactory]
	at org.springframework.context.annotation.ConfigurationClassPostProcessor.enhanceConfigurationClasses(ConfigurationClassPostProcessor.java:327)
	at org.springframework.context.annotation.ConfigurationClassPostProcessor.postProcessBeanFactory(ConfigurationClassPostProcessor.java:222)
	at org.springframework.context.support.AbstractApplicationContext.invokeBeanFactoryPostProcessors(AbstractApplicationContext.java:681)
	at org.springframework.context.support.AbstractApplicationContext.invokeBeanFactoryPostProcessors(AbstractApplicationContext.java:620)
	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:446)
	at org.springframework.context.annotation.AnnotationConfigApplicationContext.<init>(AnnotationConfigApplicationContext.java:73)
```


cglib 오류를 해결하기 위해서 <code>cglib</code> dependency를 추가해준다.

```
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>3.1</version>
</dependency>
```


### 3. 실전! 프로젝트 만들어보기

#### 개발환경

|환경|버전|
|:---:|:---:|
|Java|1.8.0_121|
|OS|Window 10|
|javaspring|3.1.1.RELEASE|

#### Configuration 클래스 설정



---

전체 소스가 궁금하다면 https://github.com/uunnaa/standalone-java-application-spring 에서 확인하면 됩니다.

---

## 참고 사이트 (reference)
- http://zetcode.com/articles/standalonespring/
- http://javaslave.tistory.com/48
- http://wonwoo.ml/index.php/post/1571