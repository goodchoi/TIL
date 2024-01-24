# 📍main topic : x
> ✅ 주요 키워드 : ex)싱글톤, 팩토리, 어댑터 (디자인 패턴 사용 이유, 예제, 조심해야 할 점)


## 싱글톤 패턴
> 객체의 인스턴스가 오직 1개만 생성되는 패턴


### 고전적인 싱글톤 패턴 구현
```java
public class Singleton {
    private static Singleton uniqueInstance;
    
    private Singleton() {}
    
    public static Singleton getInstance() {
        if (uniqueInstance == null) { //지연 초기화
            uniqueInstance = new Singleton();
        }
        return uniqueInstance;
    }
    
    //기타 메서드
}
```
주요 특징 
+ 생성자를 `private`로 선언함으로써 외부에서 생성자 사용을 금지
+ 인스턴스를 정적 변수로 초기화하고 인스턴스에 대한 접근을 `public`으로 열어둠.

사용 이유
+ 메모리적 이점
+ 데이터 공유의 용이함


### 문제점

1. 동기화 이슈
    + 클래스 내부에서 공유된 필드에 접근하여 상태를 변경하는 행위가 있을시 
   동기화 문제가 있다. 이를 해결하기위해 `volatile`과 `Syncronized`를 이용하여
   동기화 처리를 해줄 수있지만 성능하락을 피할 수 없다.

2. DIP, OCP 위반
    + 인스턴스 생성을 위해 구체 클래스를 의존하므로 `OCP` 원칙과 `DIP`원칙을 위반할 수 있다,
   유연한 코드의 설계를 방해한다.

3. 테스트의 어려움
    + 전역상태를 유지하기 때문에 하나의 테스트가 다른 테스트를 방해 할 수 있어서 초기화가 필수이다.

> 싱글톤 패턴은 안티패턴으로 분류되기도 한다.

## 팩토리 메서드 패턴
> 객체를 만드는 부분을 Sub class에 맡기는 패턴

`Factory` : 객체 생성을 처리하는 클래스

### 예제

+ 문제상황
```java
Pizza orderPizza(Sring type) {
    Pizza pizza;
    
    if (type.equals("cheese")) {
        pizza = new CheesePizza();    
    } else if (type.equals("greek")) {
        pizza = new GreekPizza();
    } else if (type.equals("pepperoni")) {
        pizza = new PepperoniPizza();
    }

    pizza.prepare();
    pizza.bake();
    pizza.cut();
    pizza.box();
    
    return pizza;    
}
```

위와 같이 매개변수로 요청하는 피자에 맞는 인스턴스를 생성하는 코드가 필요할때 , 
팩토리 패턴을 사용하여 유연한 설계가 가능하다.
더 적합한 예제를 위해 각 지점마다 다른 스타일의 피자를 주문 할 수 있다는 기능을 추가해보자.

+ 팩토리 패턴 적용
```java
public abstract class PizzaStore {
   public Pizza orderPizza(Sring type) {
      Pizza pizza;
      
      pizza = createPizza(type);

      pizza.bake();
      pizza.cut();
      pizza.box();

      return pizza;
   }
   
   protected abstract Pizza createPizza(String type);// 팩토리 메서드
}

public class NYPizzaStore extends PizzaStore {
    
   @Override 
   Pizza createPizza(String type) {
      Pizza pizza;
      
      if (type.equals("cheese")) {
         pizza = new NYCheesePizza();
      } else if (type.equals("greek")) {
         pizza = new NYGreekPizza();
      } else if (type.equals("pepperoni")) {
         pizza = new NYPepperoniPizza();
      } else {
         pizaa = null;
      }
      return pizza;
   }
}
```
팩토리 패턴의 설명과 똑같이 객체의 생성을 서브클래스에서 대신하고 있다.
> `abstract` 클래스대신 인터페이스를 사용할 수 있다.
> 단, default메서드를 사용해야한다.

### 사용이유
+ 직접 객체를 생성해 사용하는 것을 방지하고 이를 서브클래스에 위임하면서 결합도를 낮추기위함.

### 주의 
+ 사용
### +) 추상 팩토리 패턴
> 구상클래스에 의존하지 않고도 서로 연관되거나 의존적인 객체로 이루어진 제품군을 생성하는 인터페이스를 제공한다.

팩토리 패턴과 목적은 같다. 서브클래스가 객체의 생성을 대신하는것. 하지만 팩토리 클래스의
집중 포인트가 다르다고 할 수 있다.
+ 팩토리패턴 : 하나의 팩토리 -> 하나의 제품군에서 다양한 객체 생성
+ 추상 팩토리 패턴 : 하나의 팩토리 -> 다양한 제품군 중에서 하나의 객체 생성

```java
interface ComponentAbstractFactory {
    Button createButton();
    CheckBox createCheckBox();
    TextEdit createTextEdit();
}

class WindowFactory implements ComponentAbstractFactory {

    @Override
    public Button createButton() {
        return new WindowButton();
    }

    @Override
    public CheckBox createCheckBox() {
        return new WindowCheckBox();
    }

    @Override
    public TextEdit createTextEdit() {
        return new WindowTextEdit();
    }
}

class MacFactory implements ComponentAbstractFactory {

    @Override
    public Button createButton() {
        return new MacButton();
    }

    @Override
    public CheckBox createCheckBox() {
        return new MacCheckBox();
    }

    @Override
    public TextEdit createTextEdit() {
        return new MacTextEdit();
    }
}
```

## 어댑터 패턴
> 특정 클래스 인터페이스를 클라이언트에서 요구하는 다른 인터페이스로 변환한다.


### 사용이유
+ 레거시 코드를 사용하고 싶지만 새로운 인터페이스가 레거시 코드와 호환되지 않을때
+ 기존의 클래스를 수정하지않고 새로운 인터페이스에 맞게 호환 하고 싶을때


### 장점
+ 기존 클래스 코드를 건들지 않고 클라이언트 인터페이스와 호환 되기 때문에 `OCP` 원칙을 만족한다.
+ 만약 버그 발생시 기존 코드가 아닌 어댑터 클래스를 중점적으로 조사하기 떄문에 디버그가 쉽다.



### 주의할 점
+ 코드의 복잡성 증가


### 예제

```java
public interface Duck { //Target Interface
    public void quack();

    public void fly();
}

public interface Turkey { //Adaptee
    public void gobble();

    public void fly();
}

public class TurkeyAdapter implements Duck { //Adapter
    Turkey turkey;

    public TurkeyAdapter(Turkey turkey) {
        this.turkey = turkey;
    }

    @Override
    public void quack() {
        turkey.gobble();
    }

    @Override
    public void fly() {
        for (int i = 0; i < 5; i++) {
            turkey.fly(); // target인터페이스에 호환되게 variation
        }
    }
}
```

![Adapter.png](..%2Fimg%2FAdapter.png)


---
### 참고
+ 책) 헤드퍼스트 디자인패턴
+ [추상 팩토리(Abstract Factory) 패턴 - 완벽 마스터하기[Inpa Dev 👨‍💻:티스토리]](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%EC%B6%94%EC%83%81-%ED%8C%A9%ED%86%A0%EB%A6%ACAbstract-Factory-%ED%8C%A8%ED%84%B4-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EC%9E%90)

