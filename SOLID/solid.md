# SOLID 법칙

- SOLID 원칙이란 객체지향으로 개발할 때 따라야할 5가지 원칙을 뜻하며, 각 원칙 ( SRP, OCP, LSP, ISP, DIP )의 앞글자를 따서 지은 이름입니다.

## 직면한 문제

```text
개발을 막 시작한 사람이라면 간결한 코드로 적은 리소스와 시간으로 과제를 해결하는 것만이 가장 중요한 문제라고 여기곤 합니다.
물론 퍼포먼스는 중요한 문제지만, 베테랑 개발자들은 중요한 문제가 더 있다고 생각한 것 같습니다.
교과서와 다르게 실제 과제는 요구사항이 명확하거나 고정되어 있지 않고, 한번 제출 했다고 손을 털수도 없습니다.
빵을 파는 앱에서 음료룰 추가하거나, 배달하는 서비스를 추가하는 일은 너무나도 흔한 일이죠.
무엇보다 혼자보다 여럿이서 팀으로 문제를 해결해야 합니다.
그렇기에 적은노력으로 다양한 변화를 대처 할 코드를 만드는 것이 우리가 직면한 중요한 문제 입니다.
그리고 SOLID 원칙은 이런 중요한 문제를 현명하게 해결할 수 있도록 안내합니다.
```

## 단일 책임 원칙 ( SRP | Single Responsibility Principle )

> 하나의 클래스는 하나의 일만 해야한다는 원칙

- 클래스가 변경되어야 한다면 단 하나의 이유만 있어야 함을 의미합니다.

### SRP | AS-IS

- Shape 리스트받아서 넓이의 합을 계산하고 HTML 으로 출력하는 AreaCalculator 클래스를 만들었다.

> 출력을 XML이나 JSON으로 출력해야 한다면?

```java
class AreaCalculator {
  private List<Shape> shapes;

  public AreaCalculator(List<Shape> shapes) {
    this.shapes = shapes;
  }

  public Integer sum () {
    Integer result = 0;

    for (Shape shape: shapes) { /* 생략 */}
    return result;
  }

  public String output() {
    return toHTML("<p>sum is : {}</p>", this.sum());
  }
}
```

### SRP | TO-BE

- AreaCalculator 클래스는 계산만, SumCalculatorOutputer 클래스는 출력만 하도록 하였다.
- 나중에 Markdown이나 Text로 출력하는 요구사항이 더 들어온다 하더라도 안심이다.

```java
class AreaCalculator {
  private List<Shape> shapes;

  public AreaCalculator( List<Shape> shapes ) {
    this.shapes = shapes;
  }

  public Integer sum () {
    Integer result = 0;

    for (Shape shape: shapes) { /* 생략 */}
    return result;
  }
}

class SumCalculatorOutputer {
  private AreaCalculator calculator;

  public SumCalculatorOutputer( AreaCalculator calculator ) {
    this.calculator = calculator;
  }

  public String toHTML() { /* 생략 */}

  public String toXML() { /* 생략 */}

  public String toJSON() { /* 생략 */}
}
```

## 개방-폐쇄 원칙 ( OCP | Open-Closed Principle )

> 객체는 확장에는 열려있고, 변경에는 닫혀있어야 한다는 원칙.

- 클래스를 변경하지 않고 확장할 수 있어야 함을 의미합니다.

### OCP | AS-IS

- Square 클래스와 Circle 클래스의 리스트를 인자로 받아서 넓이의 합을 구하는 클래스 구현

> Triangle, Rectangular, Hexagon 등의 클래스를 추가해야한다면?

```java

class Sqaure {
  private Integer length;

  public Square( Integer length ) {
    this.length = length;
  }

  public Integer getLength() {
    return length;
  }
}

class Circle {
  private Integer radius;

  public Circle( Integer radius ) {
    this.radius = radius;
  }

  public Integer getRadius() {
    return radius;
  }
}

class AreaCalculator {
  private List<Object> shapes;

  public AreaCalculator( List<Object> shapes ) {
    this.shapes = shapes;
  }

  public Integer sum () {
    Integer result = 0;

    for (Object shape: shapes) { 
      if (shape instanceof Square) {
        result += Math.pow(shape.getLength(), 2);
      } else if (shape instanceof Circle) {
        result += Math.PI * Math.pow(shape.getRadius(), 2);
      }
    }
    return result;
  }
}
```

### OCP | TO-BE

- Square, Circle등의 형태 클래스를 아우르는 추상 인터페이스 Shape을 구현하여 해결한다.
- Triangle, Rectangular, Hexagon 등의 클래스가 추가되더라도 AreaCalculator의 구조가 변하는 일은 없다.

```java
interface Shape {
  public Integer area();
}

class Sqaure implements Shape {
  private Integer length;

  public Square( Integer length ) {
    this.length = length;
  }

  @Override
  public Integer area() {
    return Math.pow(length, 2);
  }
}

class Circle implements Shape {
  private Integer radius;

  public Circle( Integer radius ) {
    this.radius = radius;
  }

  @Override
  public Integer area() {
    return Math.PI * Math.pow(radius, 2);
  }
}

class AreaCalculator {
  private List<Shape> shapes;

  public AreaCalculator( List<Shape> shapes ) {
    this.shapes = shapes;
  }

  public Integer sum () {
    Integer result = 0;

    for (Shape shape: shapes) { 
      result += shape.area();
    }
    return result;
  }
}
```

## 리스코프 치환 원칙 ( LSP | Liskov Substitution Principle )

> q(x)를 T타입의 x라는 프로퍼티를 증명한다고 ( property provable ) 하자.
> 그렇다면 T타입의 하위 타입인 S타입에서도 q(y)는 y라는 프로퍼티를 증명할 수 있어야 한다.

- 모든 서브클래스나 구현클래스는 상위클래스 ( 부모클래스 )의 행위 ( 메소드 )를 동일하게 수행할수 있어야 한다는 뜻이다.

### LSP | AS-IS

- Square 클래스와 Circle 클래스의 리스트를 인자로 받아서 넓이의 합을 구하는 클래스 구현
- Square area는 Integer를 반환, Circle의 area는 String을 반환한다.

> Triangle 클래스를 추가해야한다면 area의 반환 타입은 무엇으로 해야할까?

```java
interface Shape {
  public Integer area();
}

class Sqaure implements Shape {
 /* 생략 */ 

  @Override
  public Integer area() {
    return Math.pow(length, 2);
  }
}

class Circle implements Shape {
  /* 생략 */ 

  @Override
  public String area() {  // 실제로는 타입체크 때문에 에러가 발생하지만, 설명의 편의를 위해 에러가 없다고 하자.
    return "" + Math.PI * Math.pow(radius, 2);
  }
}

class AreaCalculator {
  /* 생략 */ 

  public Integer sum () {
    Integer result = 0;

    for (Shape shape: shapes) { 
      Object area = shape.area();
      if (area instanceof Interger){
        result += shape.area();
      } else {
        result += Integer.valueOf(shape.area());
      }
    }
    return result;
  }
}

```

### LSP | TO-BE

- Java와같은 정적타입언어는 반환 값에 대해서 타입체크를 하기 때문에 위반하는 경우는 잘 없다.
- 그러나 동적타입언어에서는 반환 값에 대한 타입체크가 이루어 지지 않기 때문에 주의해야한다.

```java
interface Shape {
  public Integer area();
}

class Sqaure implements Shape {
  /* 생략 */ 

  @Override
  public Integer area() {
    return Math.pow(length, 2);
  }
}

class Circle implements Shape {
  /* 생략 */ 

  @Override
  public Integer area() {
    return Math.PI * Math.pow(radius, 2);
  }
}
```

## 인터페이스 분리 원칙 ( ISP | Interface Segregation Principle )

> 사용하지 않는 인터페이스 또는 사용하지 않는 메소드를 의존( 사용 )하도록 강요해서는 안된다는 원칙.



### ISP | AS-IS

- Cube 형태의 3차원 입체 클래스가 추가되었다. 
- Cube의 부피를 구하는 메소드 volume을 구현하기 위해서  다음과 같이 코드를 추가하였다.

>  volume 메소드를 사용하지 않는 Square와 Circle에도 volume 메소드를 구현해야할까?

```java
interface Shape {
  public Integer area();
  
  public Integer volume(); // 3차원 입체 클래스를 지원하기 위해 부피를 구하는 메소드 추가
}

class Cube implements Shape {
  private Integer length;

  public Circle( Integer radius ) {
    this.length = length;
  }

  @Override
  public Integer area() {
    return 6 * Math.pow(length, 2);
  }
    
  @Override
  public Integer volume() {
      return Math.pow(length, 3);
  }
}

class Sqaure implements Shape {
	/* 생략 */ 
  @Override
  public Integer volume() {		// 불필요한 메소드
      return null;
  }
}

class Circle implements Shape {
	/* 생략 */ 
    
  @Override
  public Integer volume() {		// 불필요한 메소드
      return null;
  }
}


```



### ISP | TO-BE

- 3차원 형태의 클래스를 지원하는 ThreeDimensionalShape 인터페이스를 추가로 만들어서 해결한다.
- 더이상 Square와 Circle은 불필요한 메소드 구현을 강요당하지 않을 수 있다.

```java
interface Shape {
  public Integer area();
}

interface ThreeDimensionalShape {
    public Integer volume();
}

class Cube implements Shape, ThreeDimensionalShape {
  private Integer length;

  public Circle( Integer radius ) {
    this.length = length;
  }

  @Override
  public Integer area() {
    return 6 * Math.pow(length, 2);
  }
    
  @Override
  public Integer volume() {
      return Math.pow(length, 3);
  }
}
```





## 의존 역전 원칙 ( DIP | Dependency Inversion Principle )

> 엔티티는 반드시 구현체가 아닌 추상체에 의존해야 한다는 원칙. 

- 자주 변경되는 구현체 클래스에 의존하지 않아야 한다는 원칙이다.
- 상위 클래스일수록, 인터페이스일수록, 추상 클래스일수록 변하지 않을 가능성이 높기에 이에 의존하는 의미이다.

### DIP | AS-IS

- Car클래스에서 GenericTire를 의존하여 사용하는 함수를 만들었다.

> 눈이와서 더 이상 GenericTire가 동작하지 않는다면, GenericTire함수를 수정해야 할까?

```java
interface Tire {
    public Integer spin();
}

class GenericTire implements TireInterface {
    @Override
    public Integer spin() { /* 생략 */ }
}

class Car {
    private GenericTire tire;
    public Car() {
   		this.GenericTire = new GenericTire();
    }
    
    public void run() {
        /* 생략 */
        this.tire.spin();
    }
}
```



### DIP | TO-BE

- Car 가 구현체인 GenericTire 에 의존하지 않고 추상체인 Tire에 의존하도록 구조를 바꾸었다.
- 눈이 올경우 간단히 SnowTire로 교체 가능하다.

```java
interface Tire {
    public Integer spin();
}

class SnowTire implements TireInterface {
    @Override
    public Integer spin() { /* 생략 */ }
}

class GenericTire implements TireInterface {
    @Override
    public Integer spin() { /* 생략 */ }
}

class Car {
    private Tire tire;
    public Car() {
   		this.tire = new GenericTire();
    }
    
    public changeTireToSnowTire() {
        this.tire = new SnowTire();
    }
    
    public void run() {
        /* 생략 */
        this.tire.spin();
    }
}
```



## References

- https://www.digitalocean.com/community/conceptual_articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design
- https://velog.io/@kyle/%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5-SOLID-%EC%9B%90%EC%B9%99-%EC%9D%B4%EB%9E%80
- https://devlog-wjdrbs96.tistory.com/380
