# 스프링 DI와  IoC
## DI(Dependency Injection)
의존성 주입이란 필요한(의존하는) 객체를 직접 생성하지 않고 외부에서 주입하는 방식을 의미함
- Main.java
```java
public class Main {

	public static void main(String[] args) {
		MyCalculator calculator = new MyCalculator();
		calculator.calculate(100, 2, new CalAdd()); //더하기
		calculator.calculate(100, 2, new CalSub()); //빼기
		calculator.calculate(100, 2, new CalMul()); //곱하기
		calculator.calculate(100, 2, new CalDiv()); //나누기
	}
}
```
- MyCalculator.java
```java
public class MyCalculator {

	public void calculate(int i, int j, Icalculator ca) {
		int result = ca.doOperation(i, j);
		System.out.println("result = " + result);
	}
}
```
- CalAdd.java
```java
public class CalAdd implements Icalculator {

	@Override
	public int doOperation(int a, int b) {
		return a + b;
	}
}
```
- CalSub.java
```java
public class CalSub implements Icalculator {

	@Override
	public int doOperation(int a, int b) {
		return a - b;
	}
}
```
- CalMul.java, CalDiv.java 동일

MyCalculator는 자신이 직접 연산하지 않고, 각각의 연산 객체들에게 연산 업무를 전달한다.   
→ MyCalculator는 CalAdd에 의존한다.   
→ MyCalculator는 CalSub에 의존한다.   
→ ...

Main 클래스에서 객체를 생성하여 MyCalculator에 주입하고 있다.
***
## IoC(inversion of control)
IoC란 '제어의 역전'으로 프로그램의 제어권을 개발자가 컨트롤하는 것이 아니라 외부에서 컨트롤하는 방식을 의미한다.
말 그대로 제어의 주체가 개발자에서 스프링으로 바뀐 것이다.

앞의 예제와 같이 프로그램에 필요한 모든 객체를 main()에서 생성한다는 것은 main()에 과다한 업무를 부여하는 것과 같다.

따라서, 프로그램 실행에 필요한 객체는 별도의 클래스에서 생성하도록 수정하여, 그 클래스에서 프로그램에서 사용하는 객체들을 생성하고 주입한다.
- CalAssembler.java
```java
public class CalAssembler {
  MyCalculator calculator;
  CalAdd calAdd;
  CalSub calSub;
  CalMul calMul;
  CalDiv calDiv;

  public CalAssembler() {
    calculator = new MyCalculator();
    calAdd = new CalAdd();
    calSub = new CalSub();
    calMul = new CalMul();
    calDiv = new CalDiv();

    assemble();
  }

  public void assemble() {
    calculator.calculate(100, 2, new CalAdd()); 
    calculator.calculate(100, 2, new CalSub()); 
    calculator.calculate(100, 2, new CalMul()); 
    calculator.calculate(100, 2, new CalDiv());
  }
}
```
- Main.java
```java
public class MainClass {
  public ststic void main(String[] args) {
    new CalAssembler();
  }
}
```

IoC 컨테이너: CalAssembler와 같이 객체를 생성하고 조립하는 특별한 공간   
빈(Bean): IoC 컨테이너의 객체   
다시 말해 스프링의 IoC 컨테이너는 빈을 생성하고 필요한 곳에 주입(DI)하는 특별한 공간이다.
