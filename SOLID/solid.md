# SOLID 법칙

- SOLID 원칙이란 객체지향으로 개발할 때 따라야할 5가지 원칙을 뜻하며, 각 원칙 ( SRP, OCP, LSP, ISP, DIP )의 앞글자를 따서 지은 이름입니다.

## 직면한 문제

- 개발을 막 시작한 사람이라면 간결한 코드로 적은 리소스와 시간으로 주어진 과제를 해결하는 것만이 가장 중요한 문제라고 여기곤 합니다.
- 물론 퍼포먼스는 중요한 문제지만, 베테랑 개발자들은 중요한 문제가 더 있다고 생각한 것 같습니다.
- 교과서와 다르게 실제 과제는 요구사항이 명확하거나 고정되어 있지 않고,또 한번 제출 했다고 손을 털수도 없습니다.
- 빵을 파는 앱에서 음료룰 추가하거나, 배달하는 서비스를 추가하는 일은 너무나도 흔한 일입니다.
- 무엇보다 혼자보다 여럿이서 팀으로 하나의 문제를 해결해야 합니다.
- 그렇기에 적은노력으로 빠르게 대체할수 있는 좋은 코드를 만드는 것이 우리가 직면한 중요한 문제 입니다.
- 그리고 SOLID 원칙은 이런 중요한 문제를 현명하게 해결할 수 있도록 한 길라잡이입니다.

## 단일 책임 원칙 ( SRP | Single Responsibility Principle )

- 하나의 클래스는 하나의 일만 해야한다는 원칙
- 클래스가 변경되어야 한다면 단 하나의 이유만 있어야 한다.

### 문제

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

### 해결

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
