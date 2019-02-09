---
layout: post
title: 1월_Ruby_Standalone Java Application에서 SpringFramework Annotation 적용해서 사용해보기
excerpt: "Standlone Java Application 환경에서 Java SpringFramework 의 Autowired Annotation 사용할 수 있도록 구현해보기"
categories: subject
tags: [new-post,java,javaspring]
comments: true
---


* 목차
{:toc}


## 주제


- 내용 : Standlone java application 환경에서 Java SpringFramework 의 Autowired Annotation 사용할 수 있도록 구현해보기
- 계획 : 
  1. Java SpringFramework의 Bean 관리가 어떻게 이뤄지는 지 개념 이해하기
  2. Java SpringFramework Autowired기능을 구현하기 위해 필요한 Library 준비하기
  3. 실전! 프로젝트 만들어보기 
- 이슈사항 : Autowired Annotation을 우선적으로 구현해보겠습니다.

---

## 본격적으로 알아보자

### Chapter 1) Java SpirngFramework의 Bean 관리가 어떻게 이뤄지는 지 개념 이해하기

ApplicationContext는 javaspring에서 오브젝트에 대한 생성과 관계설정을 담당한다. IoC컨테이너라고 하기도 한다.
  1. ApplicationContext는 앞의 @Configuration이 붙은 ContextLoader와 ServiceFactory를 설정정보로 등록하고, Bean 목록을 만든다. 
  2. 클라이언트가 ApplicationContext의 getBean 메소드를 호출하면 Bean 목록에서 요청한 이름이 있는지 찾는다.
  3. 있다면 Bean을 생성하는 메소드를 호출해서 오브젝트를 생성시킨 후 클라이언트에게 리턴해 준다.

---

### Chapter 2) Java SpringFramework Autowired기능을 구현하기 위해 필요한 Library 준비하기


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

---

### Chapter 3) 실전! 프로젝트 만들어보기


#### 1. 개발환경

|환경|버전|
|:---:|:---:|
|Java|1.8.0_121|
|OS|Window 10|
|javaspring|3.1.1.RELEASE|


#### 2. Configuration 클래스 설정

Bean객체의 설정 정보를 컨트롤 하는 Configuration 클래스 생성하자.

<code>tomo.config.ContextLoader.java</code>

<code>@Configuration</code> 어노테이션을 추가하여 Application이 수행될 때 가장 먼저 호출하여 Bean 오브젝트에 대한 설정 정보를 읽어올 수 있도록 한다.
```
@Configuration
public class ContextLoader {
  ...
}
```

<code>ApplicationContext</code>를 생성해서 Bean 객체에 대한 정보를 인터페이스에 담아야 하는데, 자바 <code>@Configuration</code> 어노테이션이 붙은 클래스를 설정 정보 이용하기 위해서 <code>AnnotationConfigApplicationContext</code> 구현체를 활용한다. AnnotationConfigApplicationContext의 파라미터로 등록된 Bean을 알려준다.

```
...
    private static ApplicationContext context;
	
    static{
        try {
            if( context == null ) {
                context = new AnnotationConfigApplicationContext(ServiceFactory.class);
            }
        } catch (Exception e) { }
    }
...
```

<code>context.getBean(..)</code>은 ApplicationContext가 컨트롤하는 객체를 요청하는 메서드. 등록된 Bean의 이름을 파라미터로 넘기면 해당 객체를 리턴해서 넘겨준다.

```
...
    public static Object getBean(String beanName) {
        ApplicationContext context = getContext();
        return (context == null) ? null : context.getBean(beanName);
    }
...
```

#### 3. Bean 정보를 가진 Factory클래스

<code>tomo.config.factory.ServiceFactory.java</code>

AnnotationConfigApplicationContext 클래스로 JavaConfig를 사용하기 위해서는 <code>@Configuration</code> 어노테이션을 붙여줘야 한다. <code>@Bean</code>어노테이션으로 Bean을 하나씩 생성하는 방법도 있지만, <code>@ComponentScan</code>어노테이션을 활용해서 패키지로 Bean객체를 읽어오도록 설정해준다.

```
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = "tomo.app")
public class ServiceFactory {

}

```

#### 4. tomo.app 패키지 하위에 <code>@Component</code>, <code>@Service</code> 등의 어노테이션을 가진 클래스를 생성한다.

<code>tomo.app.user.service.UserServiceImpl.java</code>

DeptService 클래스를 @Autowired 시켜 멤버 변수로 가진 UserServiceImpl을 작성한다.
```
package tomo.app.user.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import tomo.app.dept.service.DeptService;

@Service
public class UserServiceImpl implements UserService {

	@Autowired
	private DeptService deptService;
	
	public void printUserName() {
		String username = "Ruby";
		System.out.println( String.format("My name is %s.", username) );
		
		deptService.printDeptName(username);
	}

}
```

#### 5. Main클래스 작성하기

<code>tomo.config.MainApp.java</code>

ApplicationContext로 생성된 Bean이 아니면 <code>@Autowired</code>어노테이션을 이용하여 자동 주입시킬 수 없다. 이럴 때는 ContextLoader의 context를 이용해서 직접 Bean 객체를 가져와야 한다. ApplicationContext에 등록되어 있는 Bean객체에서는 미리 생성된 Bean 객체가 자동 주입되므로 UserService, DeptService에서는 어노테이션 사용이 가능하다. 

```
public class MainApp {

	private static UserService userService = ContextLoader.getContext().getBean(UserService.class);
	
	public static void main(String[] args) {
		System.out.println("[--------- main start! ----------]");
		userService.printUserName();
		System.out.println("[---------- main end! -----------]");
	}

}
```

## 마무리

이 예제는 web application이 아닌, java application 환경에서 javaspring의 어노테이션 기능을 어떻게 사용할 수 있는 지 간략한 예제를 다뤄본 글입니다. ApplicationContext가 javaspring framework에서 Bean의 생성과 관계연결 어떻게 하는지 이해할 있었던 경험이었습니다.

저는 java, javaspring을 꾸준히 배우고 있는 주니어 개발자입니다. 혹시 틀린 점이나 보완해야할 부분이 있다면 언제든지 코멘트 남겨주세요!

---

전체 소스가 궁금하다면 [https://github.com/uunnaa/standalone-java-application-spring](https://github.com/uunnaa/standalone-java-application-spring) 에서 확인하면 됩니다.

---

## 참고 사이트 (reference)
- [Standalone Spring applications](http://zetcode.com/articles/standalonespring/)
- [Spring Bean Life Cycle (빈 생명주기 관리)](http://javaslave.tistory.com/48)
- [ApplicationContext와 BeanFactory](http://wonwoo.ml/index.php/post/1571)