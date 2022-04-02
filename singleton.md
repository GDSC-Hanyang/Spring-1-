# SingleTon
# 목차
- 1. 싱글톤의 의미
- 2. Spring에서의 SingleTon Pattern 구현
- 3. SingleTon의 장・단점
- 4. Spring Container

# 1. 싱글톤의 의미
싱글턴은 '특정 클래스의' 인스턴스가 오직 하나임을 보장하는 객체를 의미한다.   그리하여 요청횟수에 상관없이 해당 클래스에서 이미 생성된 같은 인스턴스를 반환한다.    그리하여 특정 클래스의 인스턴스가 하나만 존재하고, 이를 공유해서 사용하는 형태를 말한다.    

## 사용목적
만약 DI 컨테이너를 요청할때마다 새로운 객체를 생성하는데, 이때 트래픽이 큰 사이트에서 매번 새로운 객체를 생성하게 되면 메모리 낭비가 커지게 된다.


# Spring에서의 SingleTon Pattern 구현
```java
public class SingleTonService {
    private static final SingleTonTest instance = new SingleTonTest();
    public static SingleTonTest getInstance
    () {
        return instance;
    }
    // 2개 이상 객체 생성 불가능
    private SingleTonTest() {

    }
}    
//  ...
@Test
@DisplayName("싱글톤 패턴 적용 개체")
void TestSingleTon() {
    SingleTonTest instance_1 = SingleTonTest.getInstance;
    SingleTonTest instance_2 = SingleTonTest.getInstance;

    assertThat(instance_1).isSameAs(instance_2);
}
```    
해당 예시를 통해 같은 객체가 호출됨을 알 수 있다. 

# 3. SingleTon의 장・단점 
## 장점
- 1. 하나의 객체로 관리하기 때문에 해당 객체를 재활용하여 메모리 관리를 효율적으로 하는 디자인 패턴이다.

## 단점
- 1. SingleTon 설정으로 인해 추가되는 코드의 양이 많다. 
- 2. DIP(Dependency Inversion Principle)을 위반할 수 있고, 그에따라 OCP(Open Close Principle)도 위반할 수 있다. 
- 3. public으로 공유 인스턴스를 사용할 수 있기 때문에, 해당 인스턴스를 직접 참조할 수 있게 됩니다.
- 4. 유연성이 떨어진다.

# 4. Spring Container
스프링 컨테이너는 스프링의 많은 단점을 해결하며 객체 인스턴스를 싱글톤으로 관리한다.    
스프링 컨테이너는 객체 인스턴스를 스프링 빈으로 등록하여 객체 호출시, 컨테이너에 있는 빈을 호출해 싱글톤 처럼 사용된다. 이를 싱글톤 레지스트리라고 한다. (이는 요청때마다 객체를 재생성하는 것이 아닌, 이미 만들어진 객체를 공유하기에 효율적으로 사용이 가능하다.)     
## 구현방식
객체 인스턴스를 하나만 생성해서 공유하는 방식에선, 객체 상태를 stateful하게 설계해선 안된다. 특정 클라이언트에 의존적인 필드, 값 변경 필드등이 존재해서는 안된다.     
그리하여 대부분 read-only만 가능하도록 하며, 필드에 공유되지 않는 변수들을 사용한다. 

# Reference
[Apple developer document for SingleTon Design Pattern](https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/Singleton.html "SingleTon Document in Apple developver")     
https://velog.io/@jaeeunxo1/spring-singleton    
https://jhhan009.tistory.com/77     
https://catsbi.oopy.io/6c4846a1-130d-4aba-94ea-e630cc15056d     
https://jong99.tistory.com/126    