# Container

## 1. Spring Container란?

### 1.1 정의 : 자바 객체의 생명 주기를 관리하며, 생성된 자바 객체들에게 추가적 기능을 제공하는 역할을 함
	- 여기서 말하는 자바 객체를 Spring에서는 빈(Bean)이라고 부름
	- IoC와 DI의 원리가 이에 적용됨
	- 일반적으로 개발자의 역할인 new 연산자, 인터페이스 호출, 팩토리 호출 방식으로 객체를 생성, 소멸시키는 것을 Spring Container가 대신 해줌
	- 예시) Servlet을 실행해주는 WAS는 Servlet 컨테이너를 가지고 있다고 말함
		○ WAS는 웹 브라우저로부터 서블릿 URL에 해당하는 요청을 받으면, 서블릿을 메모리에 올린 후 실행함
		○ 개발자가 서블릿 클래스를 작성했지만, 실제로 메모리에 올리고 실행하는 것은 WAS가 가지고 있는 Servlet 컨테이너임
		○ Servlet컨테이너는 동일한 서블릿에 해당하는 요청을 받으면, 또 메모리에 올리지 않고 기존에 메모리에 올라간 서블릿을 실행하여 그 결과를 웹 브라우저에게 전달함
		○ => 이런 식으로, 이러한 역할을 해주는 것이 Container
<br/> <br/>

### 1.2 종류
	- BeanFactory
		○ Bean(빈)을 관리하는 역할(등록, 생성, 조회, 돌리기)
		○ IoC/DI에 대한 기본 기능을 가지고 있음 (매우 간단한 것만 가짐)
	- ApplicationContext
		○ BeanFactory의 모든 기능을 포함하며, 일반적으로 BeanFactory보다 추천됨(이걸 주로 사용함)
		○ 트랜잭션처리, AOP등에 대한 처리를 할 수 있음
		○ BeanPostProcessor, BeanFactoryPostProcessor등을 자동으로 등록하고, 국제화 처리(텍스트 메시지 등), 어플리케이션 이벤트 등을 처리할 수 있음
			• BeanPostProcessor : 컨테이너의 기본로직을 오버라이딩하여 인스턴스화 와 의존성 처리 로직 등을 개발자가 원하는 대로 구현 할 수 있도록 함
BeanFactoryPostProcessor : 설정된 메타 데이터를 커스터마이징 할 수 있음

#### BeanFactory와 ApplicationContext의 차이점?
	- AplicationContext가 BeanFactory의 기능을 물려받음
	- BeanFactory는 처음으로 getBean() 메소드가 호출된 시점에서 해당 빈을 생성
	- ApplicationContext는 Context 초기화 시점에 모든 싱글톤 빈을 미리 로드한 후 애플리케이션 가동 후에는 빈을 지연 없이 받을 수 있음
	- => 부가 기능과 빈을 지연 없이 받을 수 있다는 장점 때문에 ApplicationContext를 실제 개발에서 주로 사용함



## 2. 관련 개념
### 2.1 IoC(Inversion of Control)
	- 컨테이너가 코드 대신 오브젝트의 제어권을 갖고 있어 IoC(제어의 역전)이라 함
	- 예시) 서블릿 클래스는 개발자가 만들지만, 그 서블릿의 메소드를 알맞게 호출하는 것은 WAS입니다.
    - => 이렇게 개발자가 만든 어떤 클래스나 메소드를 다른 프로그램이 대신 실행해주는 것을 제어의 역전이라고 합니다.![image](https://user-images.githubusercontent.com/67469315/161408529-b8603830-e6fb-46ec-8b21-3d6fcea45e13.png)


  

### 2.2 DI(Dependency Injection)
	- 정의 : 의존성 주입
    - 클래스 사이의 의존 관계를 빈(Bean) 설정 정보를 바탕으로 컨테이너가 자동으로 연결해주는 것을 말합니다.

#### DI 적용되지 않은 예
개발자가 직접 인스턴스를 생성합니다.
```java
class엔진 {
}
class자동차 {
	엔진 v5 =new엔진();
}
```
![image](https://user-images.githubusercontent.com/67469315/161408643-c9192362-48b5-4f2c-bf45-47971bfeef93.png)


#### DI 적용된 예
엔진 type의 v5변수에 아직 인스턴스가 할당되지 않았습니다.<br/>
컨테이너가 v5변수에 인스턴스를 할당해주게 됩니다.
```java
@Component
class엔진 {}
@Component
class자동차 {
	@Autowired
	엔진 v5;
}
```
![image](https://user-images.githubusercontent.com/67469315/161408578-2ac30f34-738b-42b4-9259-ce104be90899.png)

## Reference
https://steady-coding.tistory.com/459><br/> 
https://www.boostcourse.org/web326/lecture/58970?isDesc=false><br/>
