# SOLID 법칙

- SOLID 원칙이란 객체지향으로 개발할 때 따라야할 5가지 원칙을 뜻하며, 각 원칙 ( SRP, OCP, LSP, ISP, DIP )의 앞글자를 따서 지은 이름입니다.

## 직면한 문제

- 개발을 막 시작한 사람이라면 간결한 코드로 적은 리소스와 시간으로 과제를 해결하는 것만이 가장 중요한 문제라고 여기곤 합니다.
- 물론 퍼포먼스는 중요한 문제지만, 베테랑 개발자들은 중요한 문제가 더 있다고 생각한 것 같습니다.
- 교과서와 다르게 실제 과제는 요구사항이 명확하거나 고정되어 있지 않고,또 한번 제출 했다고 손을 털수도 없습니다.
- 빵을 파는 앱에서 음료룰 추가하거나, 배달하는 서비스를 추가하는 일은 너무나도 흔한 일이죠.
- 무엇보다 혼자보다 여럿이서 팀으로 문제를 해결해야 합니다.
- 그렇기에 적은노력으로 대처 할 코드를 만드는 것이 우리가 직면한 중요한 문제 입니다.
- 그리고 SOLID 원칙은 이런 중요한 문제를 현명하게 해결할 수 있도록 안내합니다.

## 단일 책임 원칙 ( SRP | Single Responsibility Principle )

> 하나의 클래스는 하나의 일만 해야한다는 원칙

- 클래스가 변경되어야 한다면 단 하나의 이유만 있어야 함을 의미합니다.

### SRP | 문제

- Shape 리스트받아서 넓이의 합을 계산하고 HTML 으로 출력하는 AreaCalculator 클래스를 만들었다.
- 여기서 출력을 XML이나 JSON으로 출력해야 한다면?

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

### SRP | 해결

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

### OCP | 문제

- Square 클래스와 Circle 클래스의 리스트를 인자로 받아서 넓이의 합을 구하는 클래스 구현

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

- 삼각형, 직사각형, 육각형 등의 형태 클래스를 추가해야한다면?

### OCP | 해결

- Square, Circle등의 형태 클래스를 아우르는 추상 인터페이스 Shape을 구현하여 해결한다.

```java
interface Shape {
  public Integer area();
}

class Sqaure {
  private Integer length;

  public Square( Integer length ) {
    this.length = length;
  }

  public Integer area() {
    return Math.pow(length, 2);
  }
}

class Circle {
  private Integer radius;

  public Circle( Integer radius ) {
    this.radius = radius;
  }

  public Integer area() {
    return Math.PI * Math.pow(radius, 2);
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
      result += shape.area();
    }
    return result;
  }
}
```
