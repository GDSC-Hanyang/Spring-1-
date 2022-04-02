## IoC Container
Spring Container에 대해서 언급하기 전에, IoC Container에 대해서 알아야 한다.  

Spring Conatiner로 알려져 있는 `ApplicationContext`와 같은 개념은 사실 Spring에서 사용하는 IoC Container이기 때문이다.
> 실제로 [공식문서](https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/beans.html#beans-introduction)
> 에서 Spring Container가 IoC Container를 대표한다고 나와 있다.

IoC는 DI(Dependency Injection)라고도 알려져 있는데, **객체에 대한 제어나 프로그램의 일부분**을 **Container 혹은 Framework로 이전**하는 것을 의미한다.  
이러한 방식을 통해 객체가 가지고 있는 의존성에 대해서 제어권을 이전받은 **Container**가 직접 주입(_Inject_)을 함으로써 아래와 같은 장점을 가진다.

1. 작업에 대한 **실행과 그 구현을 분리**(_Decoupling_)한다.
2. 여러 **구현체에 대해서 쉽게 선택하고 변경**을 할 수 있게끔 한다.
3. 모듈화의 정도를 높인다.
4. 의존성을 낮춤으로써 단위 테스트를 쉽게 한다.

IoC는 일반적으로 **제어의 역전**이라고 하는데 그 이유는 종래에는 객체가 직접 의존성에 대해서 선택하지만, IoC를 통해서 그 의존관계에 대한 설정이 역전되었기(Container -> Object)때문에, 제어의 역전이라고 한다.

이렇게 제어의 역전을 수행하는 Container를 IoC Container라고 한다.

## `BeanFactory` vs `ApplicationContext`
위에서 말한 것처럼 Spring Container는 DI를 이용하여, Spring 어플리케이션 내부에서 사용되는 여러가지 빈들의 생명주기(Lifecycle)을 관리하며  
내부적인 서비스들의 execution을 관리한다.

<img src="https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/images/container-magic.png">

일반적으로 Container에 대해서 적절한 설정(XML 혹은 `@Configuration` 클래스)만 해주면, 우리는 빈에 대한 고민을 깊게 하지 않고 **그냥 사용하면 된다.**

Spring에서의 Container하면 보통 두 가지를 떠올린다. 첫번째는 `BeanFactory`이고, 두번째는 `ApplicationContext`이다.

먼저 `BeanFacotry`는 `interface`로 기본적인 타입의 IoC Container로 빈을 생성하고 의존관계를 설정하는 역할을 수행한다.

`ApplicationContext`는 `BeanFacotry`의 `sub-interface`로 일종의 확장된 버전이다. 추가된 기능은 아래와 같다.
* 환경변수 (properties 설정)
* AOP 기능에 대한 통합
* 응답메시지 i18n (국제화)
* 이벤트 발생 (`event publication`)
> 자세한 설명은 [공식문서](https://docs.spring.io/spring-framework/docs/3.0.0.M4/reference/html/ch03s13.html)참조
> 
> 혹은 [스택오버플로우 답변](https://stackoverflow.com/questions/243385/beanfactory-vs-applicationcontext)도 좋습니다.

이러한 것들도 중요하지만, 가장 중요하게 생각하는 점은 **Lazy Loading**과 **Eager Loading**이라고 생각한다.

### Lazy-Loading (`BeanFacotry`)
어떤 메소드나 클래스가 빈에 대한 요청을 받는 시점에 인스턴스를 만들고 로딩하는 방법이다.

예를 들어 한개의 메소드를 가지는 `Student`라는 클래스가 있다고 해보자.
```java
public class Student {
    public static boolean isBeanInstantiated = false;

    public void postConstruct() {
        setBeanInstantiated(true);
    }

    //standard setters and getters
}
```
이 때 이 `Student` 클래스를 빈으로 등록하고, 생명주기에 `postConstruct()` 라는 메소드를 매치시켜보자.
> postConstruct()는 빈 객체가 생성된 후에 실행되는 메소드를 의미한다. 일반적으로는 `@postConstruct`를 통해 적용한다.

필요에 의할때 로딩이 되는것이므로, 아래 테스트 코드로 검증해보자.
```java
@Test
public void BeanFactorty가_만들어질때_Student는_초기화X () {
    Resource res = new ClassPathResource("ioc-container-difference-example.xml");
    BeanFactory factory = new XmlBeanFactory(res);
    
    assertFalse(Student.isBeanInstantiated);
}
```
위의 코드는 성공한다. 다시 말해서 **`BeanFactory`가 만들어져도, Student는 초기화되지 않**은 것이다.  
그럼 언제 로드될까? **`getBean()` 메소드를 명시적으로 호출**했을 때이다. 

코드를 살짝 수정해서 아래와 같이 테스트해보자.
```java
@Test
public void BeanFactorty가_만들어지고_Student는_초기화O () {
    Resource res = new ClassPathResource("ioc-container-difference-example.xml");
    BeanFactory factory = new XmlBeanFactory(res);
    Student student = (Student) factory.getBean("student");

    assertTrue(Student.isBeanInstantiated());
}
```
성공한다. 즉, `BeanFactory`는 오직 특정한 빈이 필요할때만 로드한다는 것을 알 수 있다.

### Eager-Loading (`ApplicationContext`)
Lazy loading과는 다르게 모든 빈들과 설정파일들이 `ApplicationContext`에 의해 로드 요청이 될때 인스턴스로 만들어지고 로드된다.
> 다른 말로는 Pre-Loading이라고도 한다.

위의 예시를 그대로 사용해서 바로 테스트를 해보자.
```java
@Test
public void ApplicationContext가_만들어지고_Student는_초기화O () {
    ApplicationContext context = new ClassPathXmlApplicationContext("ioc-container-difference-example.xml");
    
    assertTrue(Student.isBeanInstantiated());
}
```
성공한다. 즉, **명시적으로 `getBean()`을 호출하지 않더라도 Student는 초기화된다.**

둘을 비교해보면, `BeanFactory`는 필요한 시점에 로드하고, `ApplicationContext`는 시작될때 모든 빈들을 로드한다.  
이런 Eager-loading 전략 때문에 `ApplicationContext`는 무거운 IoC Container로 여겨진다.  
반대로 `BeanFactory`는 비교적 가볍고 메모리 제약적(_memory-constrained_) 시스템에서 유용할 수 있다.
> Lazy-loading이 유리한 시점, 시스템이 다 구분되어 있다. 어느 것이 항상 유리하고 그렇지 않은 것은 없다. 
> 대표적인 예는 JPA에 있다. 예를 들어 1:N 구조의 모델에서의 `FetchType.Lazy`이다. 이 경우에는 프록시를 사용한다.

그럼에도 불구하고 공식문서에조차 대부분의 상황에서는 `ApplicationContext`를 쓰는 것을 권장한다.

## Singleton 레지스트리
Spring Container는 별다른 설정이 없으면, **빈 객체들을 모두 싱글톤으로 생성**한다.  

만약 빈 객체들이 싱글톤으로 생성되지 않는다고 해보자.  
음식을 주문하는 서비스를 구성하는데, 요청받은 주문을 처리하기 위한 서비스 객체가 매 요청마다 생성된다면 사람들이 몰려서 주문을 할때는 대처하지 못할것이다.  
> 비유를 하자면, **새로운 주문을 받을때마다 주문받을 사람을 새로 뽑는 것이다.**

특히나 Spring이 일반적으로 서버 환경에서 사용된다는 점을 고려했을때, 매 요청마다 객체를 생성한다면 아무리 GC 튜닝이 잘 되어 있어도 대처할 수 없다.  
> 특히 Java GC는 STW를 필연적으로 동반한다는 점을 생각해보자. 자세한 설명은 [Java의 알쏭달쏭한 GC](https://becomeweasel.me/java-gc/) 참조.

그렇기에 싱글톤 패턴과 유사한 방식으로 유일한 빈 객체를 생성하고 이를 공유해서 사용한다.  

그러나 안티패턴이라고 불리는 기존의 싱글톤 패턴을 그대로 사용하기에는 여러가지 문제점이 있다.
1. 생성자가 `private` 접근자이기에 **상속이 불가능**하다.
2. 단위 테스트가 너무 어렵다.
3. 서버 환경에서는 동시성 이슈가 무조건 생긴다. 그런데 `synchronized`는 매우 느리다..
4. OOP에서 전역적 객체는 권장되지 않는다. **캡슐화의 개념을 망치기 때문이다.**

그래서 Spring에서는 `Singleton registry`라는 개념을 통해서 이를 해결한다.

생각보다 간단한데, 애초에 빈 객체에 대한 **생성과 파괴의 책임이 Spring Container에게 있으니** `private`이나 `static` 같은 키워드 없이도 가능하다.  
다시 말해서 생성, 의존성 설정 같은 제어권 자체가 Container에게 역전되었으니, POJO 역시 싱글톤으로 관리될 수 있다는 것이다.

> 하지만 읽기 전용을 제외하고 무상태성을 가지게끔 빈을 구성해야 한다. 
> 
> 빈에게 필요한 정보가 있다면 쓰레드의 메모리 구조를 생각하며
> 독립적인 영역(Stack)에 배치되게끔 디자인해야 한다.

> 모든 빈이 싱글톤은 아니다. `@Scope("prototype")`을 통해 매번 새로운 객체가 생성되게끔 할 수 있다.

## 빈의 생성원리
이제 Spring Container가 무엇인지는 알았는데, 제일 중요한 빈을 어떻게 만든다는 걸까?  
그럼 실제로 Spring이 빈들을 어떻게 구성하는지 바로 보자.

빈을 등록할때 XML 안에 명시,Java 파일안에 빈 설정을 하거나 적절한 어노테이션을 이용하곤 한다.
> 거의 대부분 `@Controller` 같은 어노테이션을 이용하는 것 같아보여요.

<img src="https://media.vlpt.us/images/hyunmin/post/1767f0c2-837c-4129-8715-9f58690ba9a6/velog.002.jpeg">

위의 그림의 구조처럼 **`BeanDefinitionReader`가 XML,Java 설정,어노테이션들을 읽고 `BeanDefinition`으로 변환해준다.**  
그 정보와 POJO 클래스를 적절히 조합해서 Container가 빈을 생성한다.

요즘엔 대부분 어노테이션을 이용해서 빈을 등록하는데, 이건 어떻게 작동하는 걸까?

### 컴포넌트 스캔
**컴포넌트 스캔(Component Scan)** 이 해결해준다.

> 아래부터는 정확히는 Spring Boot 관련 내용입니다.

`@SpringBootApplication` 어노테이션에는 `@ComponentScan`이라고 명시되어 있는데, 이 어노테이션이 사용된 클래스의  
패키지를 `basePackage`로 정한다.

그 후에 `basePacakge`와 그 서브 패키지들을 검색 대상으로 두고, `@Component`와 그것을 메타 어노테이션으로 가진 어노테이션들이 붙은 클래스들을  
모두 찾아 `BeanDefinition`으로 변환 후 빈으로 등록시킨다. 또 `@Configuration 역시 스캔 대상이 된다.
>`@Repository`,`@Service`,`@Controller` 같은것이 `@Component`를 메타 어노테이션으로 가진 어노테이션들이다.

