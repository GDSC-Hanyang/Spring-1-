# <center>SOLID Principles</center>



[TOC]


## Overview

| Principle Name     | What is says?                      |
| ----------------------------------- | :----------------------------------------------------------: |
| **Single Responsibility Principle**   | One class should have one resposibility                     |
| **Open Closed Principle**           | Software components should be open for extension, but closed for modification |
| **Liskov's Substitution Principle** | Derived types must be completely substitutable for their base types |
| **Interface Segregation Principle** | Clients should not forced to implement unnecessary methods which they will not use |
| **Dependency Inversion Principle**  | Depend on abstractions, not con concretions                  |



## SRP(Single Responsibility Principle)

- 단일책임의 원칙

- 한 클래스는 단 하나의 책임만을 가져야 한다.

- 클래스를 변경하는 이유는 단 한 개여야 한다.

- 응집도를 향상 시키는 원리이다.

- 책임이란 변경 이유이다.

  - 책임이 많다는 것은 변경될 여지가 많다는 의미이다.
  - 책임을 많이 질수록 클래스 내부에서 서로 다른 역할을 수행하는 코드끼리 강하게 결합될 가능성이 높아진다.
- SRP 위반의 악취

  - 여러 원인에 의한 변경

    - 한 클래스를 여러 가지 다른 이유로 고칠 필요가 있을 때 발생
    - 즉, 하나의 클래스에 여러 책임이 혼재하고 있어 하나의 책임의 변화가 다른 책임에게 영향을 준다.

  - Shotgun Surgery

    <img src="https://upload.wikimedia.org/wikipedia/commons/9/98/Shotgun_Surgery.png" alt="shotgun" style="zoom:60%;" />

    - 하나의 책임이 여러 클래스에 분산되어 있기 때문에 발생한다.
    - 어떤 변경이 있을 경우 여러 클래스를 수정해야하는 증상이다.
    - 즉, 어떤 변경의 대상이 여러 곳에 분포되어 많은 노동 비용이 따르게 된다.

  - 한 클래스가 너무 많은 책임을 맡고 있어도 곤란하지만, 책임을 식별하지 못해 이를 담당할 클래스를 만들지 않고 여러 클래스에 흩뿌려 놓는 것 또한 문제가 있다.

    - 즉 프로그램의 전체 책임을 올바로 분담하지 못한 것이다.

### Example

- HTTP 프로토콜을 이용해서 데이터를 읽어와 화면에 보여주는 기능

 ```java
 public class DataViewer{
   public void display(){
     String data = loadHtml();
     updateGui(data);
   }
   public String loadHtml(){
     HttpClient client = new HttpClient();
     client.connect(url);
     return client.getResponse();
   }
   private void updateGui(String data){
     GuiData guiModel = parseDataToGuiData(data);
     tableUI.chageData(guiModel); 
   }
   private GuiData parseDataToGuiData(String data){
     // parsing...
   }
 }
 ```
- 데이터를 제공하는 서버가 HTTP protocol에서 socket 기반의 protocol로 변경되었을 경우
 ```java
 // 변경이 발생한 경우
 public class DataViewer{
   public void display(){
     byte[] data = loadHtml();
     updateGui(data);
   }
   // 읽어온 데이터의 구조 변화 String -> byte[]
   public byte[] loadHtml(){
     HttpClient client = new SocketClient();
     client.connect(server, port);
     return client.read();
   }
   // updateGui() 파라미터 타입 변화 String -> byte[]
   private void updateGui(byte[] data){
     GuiData guiModel = parseDataToGuiData(data);
     tableUI.chageData(guiModel); 
   }
   private GuiData parseDataToGuiData(byte[] data){
     // parsing...
   }
 }
 ```

- 책임을 분리하면 변경의 여파를 줄일 수 있다.

    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FlREdg%2FbtryeyX3luU%2FsWk2tYq1bOrZ0uF23YqTX1%2Fimg.png" style="zoom:50%;" />

    - 데이터 읽기와 데이터를 화면에 보여주는 책임을 두 개의 클래스로 분리하고 둘 간에 주고 받을 데이터를 저수준의 String이 아닌 알맞게 추상화된 타입을 사용하면, 데이터를 읽어 오는 부분의 변경 때문에 화면에 보여주는 부분의 코드가 변경되는 상황을 막을 수 있다.
    
- 책임이 분리되지 않을 경우

    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F0Z8J5%2Fbtrydzikw3N%2FiwvrKkMRrMD6J5yKsDxEvk%2Fimg.png" style="zoom:50%;" />

    - 필요하지 않은 패키지까지 필요하다.
    
- 단일 책임의 원칙은 재사용성을 높일 수 있다.

    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcjY4p9%2FbtrycksJbMO%2FshcbtdAPsvvbz8075NfOYk%2Fimg.png" style="zoom:50%;" />

- 책임이란 변화에 관한 것이다.

  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FpqEEy%2Fbtryf0zQ9U6%2F2r1r8FKXYYazRK3L4lkdFk%2Fimg.png" style="zoom:50%;" />

  - 클래스 사용자들이 서로 다른 메소드들을 사용한다면 그들 메소드는 각각 다른 책임에 속할 가능성이 높고 따라서 책임 분리 후보가 될 수 있다.

  


## OCP(Open-Closed Principle)

- 개방폐쇄원칙

- 변경에는 닫혀있고 확장에는 열려있다.

- 기존의 코드를 변경하지 않으면서(Closed) 기능을 추가(Open)할 수 있도록 설계가 되어야 한다.

- 단순 상속이 아니라 유지보수성에 대한 것이다.
  - 캡슐화와 추상화의 조합을 이루어졌다.
  - 부모 클래스를 추상화하여 변경을 못하게 한다.
  - 자식 클래스에서 확장을 통해 변경한다.
### Example

- Enemy Character의 움직이는 경로가 몇 가지 패턴으로 정해져있고, 정해진 패턴에 따라 경로를 이동하는 코드

  - ```java
    public class Enemy extends Character{
      private int pathPattern;
      public Enemy(int pathPattern){
        this.pathPattern = pathPattern;
      }
      public void draw(){
        if (pathPattern == 1 ){ 
          x += 4;
        } else if (pathPattern == 2){
          y += 10;
        } else if (pathPattern == 4 ){
          x += 4;
          y += 10;
        }
      }
    }
    ```

  - <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fpgh4y%2Fbtrygu1L932%2FLkEofJFoA19My9X6PsT3rk%2Fimg.png" style="zoom:50%;" />

  - 새로운 이동 패턴이 생길 경우 `draw()` method를 변경해야 한다.

- 이동 경로 계산에 OCP 원칙을 적용한 결과

  - ```java
    public class Enemy extends Character{
      private PathPattern pathPattern;
      public Enemy(PathPattern pathPattern){
        this.pathPattern = pathPattern;
      }
      public void draw(){
        int x = pathPattern.nextX();
        int y = pathPattern.nextY();
      }
    }
    ```

  - <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbWWmGb%2Fbtryd9Ejxze%2FM5N3vvkTFHfmacKCHDKVAk%2Fimg.png" style="zoom:50%;" />

  - 새로운 이동 패턴이 생기더라도 `Enemy` 클래스의 `draw()` method는 변경되지 않으며 새로운 타입의 `PathPattern` 구현 클래스를 추가해주기만 하면 된다.





## LSP(Liskov's Substitution Principle)

- 서브 타입은 언제나 기반 타입을 대체할 수 있어야 한다.
  - 서브 타입은 기반 타입이 약속한 규약(public interface, method가 던지는 exception도 포함)을 지켜야 한다.
- LSP를 만족하면 프로그램에서 부모 클래스의 인스턴스 대신에 자식 클래스의 인스턴스로 대체해도 프로그램의 의미는 변화되지 않고 프로그램은 정상적으로 동작해야 한다.
- LSP는 기능의 명세(계약, 규약)에 대한 내용이다.
- LSP 위반 사례
  - 명시된 명세에서 벗어난 값을 리턴한다.
  - 명시된 명세에서 벗어난 예외를 발생한다.
  - 명시된 명세에섯 벗어난 기능을 수행한다.
- LSP는 확장에 대한 것이다.
  - LSP를 어기면 OCP를 지킬 수 없다.
  - LSP를 바탕으로 OCP는 확장하는 부분에 다형성을 제공해 변화(확장)에 열려있는 프로그램을 만들 수 있도록 해준다.
### Example

- 상위 타입에서 지정한 리턴값의 범위에 해당하지 않는 값을 리턴하는 경우

  ```java
  public class BadInputStream implements InputStream { 
    public int read( byte[] data ) {
      return 0; //데이터가 없을 때 0을 리턴하도록 구현
    }
  }
  
  InputStream is = new BadInputStream(someData);
  OutputStream out = new FileOutputStream(filePath);
  CopyUtil.copy(is, out);
  
  // InputStream의 read() 메소드는 스트림의 끝에 도달해서 더 이상 데이터를 읽어 올 수 없는 경우 -1을 리턴한다고 정의되어 있고, CopyUtil.copy() 메소드는 이 규칙에 따라 -1이 아닐 때 까지 반복해서 데이터를 읽어와 out에 씀
  public class CopyUtil {
    public static void copy(InputStream is, OutputStream out) {
      byte[] data = new byte[512];
      int len = -1;
      
      // InputStream.read() 메소드는 스트림의 끝에 도달하면 -1을 리턴
      // is가 BadInputStream인 경우 read() method는 -1을 리턴하지 않으므로, 아래 코드는 무한루프에 빠진다.
      while((len = is.read(data)) != -1) {
        out.write(data, 0, len);
      }
    }
  }
  ```

  



## ISP(Interface Segregation Principle)

- Interface 분리 원칙
- interface를 클라이언트에 특화되도록 분리시키라는 설계 원칙
- 클라이언트의 관점에서 클라이언트 자신이 이용하지 않는 기능에는 영향을 받지 않아야 한다.





## DIP(Dependency Inversion Principle)

- 의존 관계를 맺을 때 변화하기 쉬운 것 또는 자주 변화하는 것보다는 변화하기 어려운 것, 거의 변화가 없는 것에 의존하라는 원칙
- High-level modules should not depend on low-level modules. Both should depend on abstractions.
- Abstractions should not depend on details. Details should depend on abstractions.
- 주로 interface나 추상 클래스와 의존 관계를 맺도록 설계한다.





## SRP vs. ISP

- 어떤 클래스가 단일 책임을 수행하지 않고 여러 책임을 수행하게 되면 방대한 메소드를 가진 비대한 클래스가 될 가능성이 커지며, 당연히 비대한 interface가 제공된다.
- 이렇게 비대한 클래스를 SRP에 따라 단일 책임을 갖는 여러 클래스들로 분할하고 각자의 interface를 제공한다면 ISP도 만족할 수 있다.
- SRP를 만족하더라도 ISP를 반드시 만족한다고는 할 수 없다.
  - 단일 책임을 제공하더라도 클라이언트에 따라 특정 interface만 제공해야 할 수도 있다.



## Summary

- **SOLID란 변화에 유연하게 대처하는 설계 원칙이다.**
- SRP와 ISP는 객체가 커지지 않도록 막아준다. 객체가 많은 기능을 가지게 되면, 객체가 가진 기능의 변경 여파가 그 객체의 다른 기능에까지 번지게 되고, 이는 다시 다른 기능을 사용하는 클라이언트에게 까지 영향을 준다. 객체가 단일 책임을 갖게 하고 클라이언트마다 다른 interface를 사용하게 함으로써 한 기능의 변경이 다른 곳에까지 미치는 영향을 최소화할 수 있고 이는 결국 기능 변경을 보다 쉽게 할 수 있도록 만들어준다.
- LSP와 DIP는 OCP를 지원한다. OCP는 변화되는 부분을 캡슐화해서 추상화하고 다형성을 이용함으로써 기능 확장을 하면서도 기존 코드를 수정하지 않도록 만들어준다. 여기서, 변화되는 부분을 추상화할 수 있도록 도와주는 원칙이 바로 DIP이고, 다형성을 도와주는 원칙이 LSP이다.
- 또한, SOLID 원칙은 사용자 입장에서의 기능 사용을 중시한다. ISP는 클라이언트 입장에서 interface를 분리하고 있으며, DIP 역시 저수준 모듈을 사용하는 고수준 모듈 입장에서 추상화 타입을 도출하도록 유도한다. 또한 LSP는 사용자에게 기능 명세를 제공하고, 그 명세에 따라 기능을 구현할 것을 약속한다. 이처럼 SOLID 원칙은 사용자 관점에서의 설계를 지향하고 있다.