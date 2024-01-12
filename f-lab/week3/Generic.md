# 📍main topic : Java Generic에 대해 설명 하시오
> ✅ 주요 키워드 : 타입 파라미터, wildcard, type erasure, 장/단점, etc

<!-- TOC -->
목차
* [Generic이란?](#generic이란)
* [Generic의 사용](#generic의-사용)
  * [Generic class](#generic-class)
  * [제네릭 제한 사항](#제네릭-제한-사항)
  * [제한된 제네릭 클래스](#제한된-제네릭-클래스)
* [와일드 카드](#와일드-카드)
  * [문제상황 인식](#문제상황-인식)
  * [와일드 카드란?](#와일드-카드란)
  * [와일드 카드의 상한과 하한](#와일드-카드의-상한과-하한)
  * [PECS(Producer-Extends, Consumer-Super) 공식](#pecs--producer-extends-consumer-super--공식)
* [제네릭 메서드](#제네릭-메서드)
* [type erasure](#type-erasure)
* [제네릭의 장점](#제네릭의-장점)
<!-- TOC -->

## Generic이란?
타입의 일반화를 말한다. 즉, 클래스 내부에서 데이터 타입을 지정하는 것이 아니라 <u>외부에서</u> 사용자에의해
지정되는 것을 말한다.

## Generic의 사용
### Generic class
```java
class Box<T> {
    T item;
    void setItem(T item) {this.item = item;}
}
```
클래스 내부 구현에선 꺾쇠(`<>`)와 함께 제네릭을 선언한다.  
`T`는 타입 변수 혹은 타입 매개변수를 의미한다.(`Type Parameter`)  
이것을 매개변수라고 칭하는 것은 메서드의 매개변수(파라미터)와 유사한 면이 많기 때문이다.

### 제네릭 제한 사항  
1. static 멤버,메서드에 타입 파라미터를 사용할 수 없다.
```java  
class Box<T> {
    static T item;  //불가
    static int compare(T t1, T t2); //불가
```
이것은 잠시 생각해보면 당연한 얘기이다. `static`키워드는 인스턴스 변수를 참조할 수없다. 메서드도 마찬가지이다.
타입 파라미터는 인스턴스 변수와 같이 동작한다.

2. new 연산자 사용불가

```java 
class Box<T> {
    T[] itemArr;  //가능
    T[] toArray() {
        T[] arr = new T[itemArr.length];  //불가
        //...
        return arr;
    }
}
```
new 키워드는 컴파일 시점에 타입을 정확히 알아야한다. `Box`클래스 컴파일 시점에서는
`T`가 무엇이 될지 알 수 없으므로 new 키워드를 사용할 수 없는 것이다
이와 같은 이유로 `instanceOf`도 사용할 수 없다.

3. 타입의 일치
```java
Box<Apple> box = new Box<Apple>(); //ok
Box<Fruit> box = new Box<Apple>(); //no
```
생성자의 타입과 클래스의 타입 파라미터는 항상 일치해야한다. 상속도 허용하지 않는다.

### 제한된 제네릭 클래스
타입 파라미터는 모든 종류의 타입을 지정한다. 이때, 타입의 종류를 제한하고 싶다면 어떤 방법이 있을까?

```java 
class Box<T extends Fruit> {
    ArrayList<T> list= new ArrayList();
    void add(T fruit) {list.add(fruit);}
}
```
위의 클래스는 `Fruit`를 상속하는 타입에 대해서만 허용한다. 또한 인터페이스의 경우 `implements`를 사용하는것이 아니라
위와 같이 `extends`를 사용한다.

또한 이렇게 제한을 걸었을 경우 장점이 발생한다.
```java
FruitBox<Fruit> fruitBox = new FruitBox<Apple>();
fruitBox.add(new Apple());
fruitBox.add(new Grape());
```
`add`메서드의 매개변수로 `T`의 자손 타입을 사용하는 것이 가능해진다.

## 와일드 카드

### 문제상황 인식
```java 
class Juicer{ 
    static Juice makeJuice(FruitBox<Fruit> box) { //과일을 건네 받아 주스로 만들어주는 메서드
        String tmp = "";
        for (Fruit f : box.getFruitList()) {
            tmp += f;
        }
        return tmp;
    }
}
```
현재 `Juicer`는 제네릭 클래스가 아니다. 따라서 `makeJuice`의 매개변수의 제네릭 타입이 `Fruit`으로 고정되어있다.
이떄는 `Juicer.makeJukce(new FruitBox<Apple>())` 의 호출이 불가능 하다. <u>왜 불가능 할까????</u>
이것은 결국 다음과 같은 에러가 있기 때문이다.
    
    FruitBox<Fruit> box = new FruitBox<Apple>();
이미 위에서 생성자의 제네릭은 클래스의 제네릭과 완전히 일치해야한다는 사실을 알기 때문에
이것이 불가능 한다는 것 또한 당연해지는 것이다. <u>즉, 제네릭의 엄격한 타입 일치가 다형성의 사용에 제약을 걸어버린다.</u> 이와 같은 상황을 해결해 주는것이
와일드 카드이다.

### 와일드 카드란?
와일드 카드는 정해지지 않은 `unknown` type으로 모든 타입을 대신한다.
위와 같은 상황에서 와일드 카드를 사용함으로써 유연한 코드로 대체한다.

```java
class Juicer{ 
    static Juice makeJuice(FruitBox<? extends Fruit> box) { //과일을 건네 받아 주스로 만들어주는 메서드
        String tmp = "";
        for (Fruit f : box.getFruitList()) {
            tmp += f;
        }
        return tmp;
    }
}
```
`FruitBox`의 제네릭이 와일드카드 `?`을 사용하는것으로 바뀌었다. 즉 `Fruit`의 자손 타입을 모두 허용한다는 뜻이다.
만약 `<?>`를 사용할시 모든 타입을 허용한다.


### 와일드 카드의 상한과 하한
타입 매개변수를 사용할 때는 `extends`키워드 밖에 사용하지 못했지만 와일드 카드에선 `super`를 사용한 하한을 제한할 수 있다.
하지만 상한과 하한을 사용할 때는 몇가지 주의점이 있다. 

+ `extends` 사용시(소모)
```java
void addElement(Collection<? extends MyParent> c) {
    c.add(new MyChild());        // 불가능(컴파일 에러)
    c.add(new MyParent());       // 불가능(컴파일 에러)
    c.add(new MyGrandParent());  // 불가능(컴파일 에러)
    c.add(new Object());         // 불가능(컴파일 에러)
}
```
`MyParent`클래스의 자손타입은 모두 허용하므로 `MyParent`, `MyChild`클래스를 모두 허용하겠지만,
실제 컬렉션의 제네릭이 `AnotherChild`인 경우 둘 모두 해당 타입으로 형변환이 불가능하기 때문에 애초에 컴파일 오류가 난다.
위와 같은 상황은 파라미터를 소모하는 `Consume` 상황이다.

+ `super` 사용시 (공급)
```java
void printCollection(Collection<? super MyParent> c) {
    // 불가능(컴파일 에러)
    for (MyChild e : c) {
        System.out.println(e);
    }

    // 불가능(컴파일 에러)
    for (MyParent e : c) {
        System.out.println(e);
    }

    // 불가능(컴파일 에러)
    for (MyGrandParent e : c) {
        System.out.println(e);
    }

    for (Object e : c) {
        System.out.println(e);
    }
}
```
`MyParent` 부모 타입들은 모두 허용할 것이다. 하지만 위와 마찬가지로 정해지지 않았기에 형변환의 가능성이 보장 될 수 없다.
따라서 컴파일 오류가 나는 것이다. 하지만 `Object`는 모든 클래스의 조상이기 때문에 언제든지 형변환이 가능하므로
허용된다.

### PECS(Producer-Extends, Consumer-Super) 공식
컬렉션으로부터 와일드카드 타입의 객체를 생성 및 만들면 `extends`를 , 갖고 있는 객체를 컬렉션에 사용 또는 소비 하면
super를 사용하라는 것.

```java
void printCollection(Collection<? extends MyParent> c) {
    for (MyParent e : c) {
        System.out.println(e);
    }
}

void addElement(Collection<? super MyParent> c) {
    c.add(new MyParent());
}
```

## 제네릭 메서드
클래스 단위가 아닌 메서드에서도 타입 파라미터를 적용할 수 있는데 이것을 제네릭 메서드라한다.
클래스의 제네릭을 사용할 수 없던 `static`메서드에 많이 사용된다.(일반 인스턴스 메서드에서도 사용가능)
클래스의 제네릭과 제네릭 메서드의 타입파라미터 `T`로 같아도 이 둘은 완전히 구분되며 전혀 상관이없다.

> static 메서드에 어떻게 제네릭이 사용 가능한가? 
> <br>
> 클래스의 제네릭은 당연히 static키워드가 붙은 변수와 메서드에 적용이 될 수 없는 것은 당연한다.
> 그렇지만 static 메서드에는 제네릭 메서드라는 것이 있어 이것을 이용해 제네릭을 활용할 수 있다.
> 그 이유는 메서드에서 넘어오는 파라미터를 통해 유추할 수 있기 때문이다. 반면 static 변수는 값 자체가 공유되어 버려서
> 타입을 모르는데 공유를 할 수도 없을 뿐더러, 공유할 이유가 없다.

```java
public static <T extends CharSequence> void printFirstChar(T param) {
    System.out.println(param.charAt(0));
}
```
## type erasure
제네릭은 JDK 1.5 버전에 처음 도입 되었다. 그렇다면 이전 버전에서는 제네릭이 도입되기전의 자바 버전의 코드들과의
호환성을 위해 컴파일 후 제네릭 타입을 소거한다. 이것을 `type erasure`라고 한다.
`type erasure`는 다음과 같은 규칙을 가진다.
+ 모든 타입 파라미터를 그들의 바운드나 Object 타입으로 교체한다.
+ 제네릭 타입을 제거한 후 타입이 일치하지 않으면 타입 캐스팅을 추가한다
+ 확장된(extended) 제네릭 타입의 다형성을 보존하기 위해 브릿지 메서드를 생성한다.


## 제네릭의 장점
1. 타입의 불일치를 컴파일 단계에서 방지 할 수 있따.
2. 불필요한 타입변환을 줄여준다.
