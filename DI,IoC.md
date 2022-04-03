# DI(Dependency Injection) & IoC (Inversion of Control)

## 1. 의존성 (Dependency)

### 1.1 객체 지향 언어에서의 의존성
의존성이란 두 클래스 간의 관계를 나타낸다. 객체 지향 언어에서 다른 객체를 사용하는 것을 '다른 객체에 의존한다'라고 표현한다.<br/> <br/>

### 1.2 의존성(Dependency) 코드 예시

```java
class ExamScore {
    private EnglishScore EnglishScore;

    public ExamScore() {
        EnglishScore = new EnglishScore();
    }
}
```
위 코드에서는 ExamScore이라는 클래스 안에서 EnglishScore을 생성했다. 이때 EnglishScore이 바뀌면 ExamScore에도 영향을 미치기 때문에 EnglishScore은 ExamScore의 의존 대상이고, 이 둘은 의존관계이다. <br/> <br/>

## 2. IoC(Inversion of Control)와 DI(Dependency Injection)

### 2.1 DI(Dependency Injection) - 의존성 주입이란
어떤 객체에서 다른 객체를 사용하기 위해서는 1.2의 코드 예시와 같이 (1) 그 객체를 직접 생성하거나 (2) 외부에 생성되어있어야 하는데, (1)처럼 직접 생성하여 사용할 수 있고 (2) 이미 만들어져 있는 객체를 할당받아 사용할 수 있다. DI는 (2)와 같이 객체 간 의존관계를 외부에서 결정하고 주입하는 것을 말한다.

   <img src="https://user-images.githubusercontent.com/77472513/161405627-50dcfe20-04f7-475a-8367-d533b092e552.png" width="600px" alt="week1_1" > </img>

왼쪽 그림에서는 A 객체 안에 B, C 객체를 직접 생성하고 있다. 이 경우 객체 생성부터 메모리 관리를 위한 해당 객체의 라이프 사이클 관리를 모두 개발자가 하게 되어 의존성이 강해, 강한 결합이라고 한다. <br/>
오른쪽 그림에서는 B, C 객체가 외부에 생성되어 A객체에 주입되고 있는데, 이를 **'의존성 주입'** 이라고 한다. A가 B, C의 기능이 필요하여 외부에 요청하면 B, C의 기능을 주입시킨다는 의미이다. 왼쪽과 달리 약한/느슨한 결합이라고 한다. <br/> <br/>


### 2.2 IoC(Inversion of Control) - 제어의 역전이란
<img src="https://user-images.githubusercontent.com/77472513/161405916-25aeccc9-18c9-4253-a2a1-0946852c8701.png" width="650px" alt="week1_2" > </img>

1.3에서 본 오른쪽 그림을 확대해서 보자. 외부에서 B,C 객체 생성과 라이프 사이클, 객체 의존 주입까지 관리하는 주체를 컨테이너라고 한다. 우리가 앞으로 배울 스프링이 이 컨테이너의 역할을 한다. 이와 같이 오브젝트 관레에 대한 제어권을 프레임워크의 컨테이너에게 넘기는 것을 IoC(제어의 역전)라고 하고, IoC 방식 중 가장 대표적인 방식이 앞서 본 DI이다. 개발자나 애플리케이션이 아닌 컨테이너에게 제어권이 넘어갔기 때문에 개발자가 관리할 부분이 적어진다는 점에서 이용의 장점이 있다. <br/> <br/>

### 2.3 IoC를 사용하는 이유
SOLID 중 S에 해당하는 SRP(Single Responsibility Principle)는 단일책임의 원칙으로, 한 클래스는 단 하나의 책임만을 가져야 한다. IoC를 통해 객체를 관리하는 담당과 코드를 구현하는 담당을 분리해 응집도를 향상할 수 있다. 만약 method 전체가 static으로 되어 있다면 유틸리티 클래스들은 객체를 instance로 잡을 필요가 없어서 대부분 클래스를 만들 때 인스턴스화를 막기 위해 생성자를 private으로 설정해놓는다. 이 경우, IoC로 약한 결합을 만들면 코드 수정이 유리하다. <br/> <br/>

### 2.4 DI에 대한 코드 예시

```java
class Student {
    String student_name;
    String class_name;
    String grade;
    public Student() {}
}
```
위와 같은 속성을 지닌 Student 클래스를 만들었다. 이 Student 클래스를 DI를 사용할 때와 사용하지 않을 때 두 가지로 나누어보면 아래와 같다.

```java
public static void main(String[] args) {
    // 강한 결합
    public static void Student1(){
        Student s1 = new Student();
    }
}

// 느슨한 결합 - DI(Dependency Injection)
public static void Student2(Student s) {
    Student s2 = s;
}
```

기존 API나 프레임워크에서 제공하는 라이브러리도 비슷한 방법으로 약한 결합과 강한 결합을 만들 수 있다. <br/> <br/>


## Reference
https://beststar-1.tistory.com/33 <br/>
https://velog.io/@gillog/Spring-DIDependency-Injection <br/>
https://tecoble.techcourse.co.kr/post/2021-04-27-dependency-injection/ <br/>
https://leveloper.tistory.com/33 <br/>
https://youtu.be/fGOU7JqNHyE <br/>
