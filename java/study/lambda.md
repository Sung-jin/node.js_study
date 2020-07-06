# 람다식 (Lambda)

- Java 8 부터 함수적 프로그래밍을 지원하기 위해 Lambda Expressions(람다식) 을 지원한다.
- 람다식은 anonymouse function(익명 함수) 를 생성하기 위한 식으로 객체 지향 언어보다는 함수 지향 언어에 가깝다.
- 람다식의 형태는 매개 변수를 가진 코드 블록이지만, 런타임 시에는 익명 구현 객체를 생성한다.

> 람다식 -> 매개 변수를 가진 코드 블록 -> 익명 구현 객체

- Thread 파트에서 스레드의 run 구현 객체를 람다식으로 표현하면 다음과 같다.

```JAVA
Runnable runnable = new Runnable() {
    public void run() {...};
}

Runnable runnable = () -> {...};
// {...}; 이 람다식을 통해 구현한 익명 함수 객체이다.
// 람다식은 (매개변수) -> {실행코드}; 형태로 작성된다.
// 런타임시에 정의된 람다식은 인터페이스의 익명 구현 객체로 생성된다.
```

## 람다식 기본 문법

> (타입 매개변수, ...) -> {실행코드};

- 타입 매개변수들은 실행코드의 실행하기 위한 인자값을 제공하는 역할을 한다.
  - 매개 변수 타입은 런타임 시에 대입되는 값에 따라 자동으로 인식될 수 있기 때문에 람다식에서는 일반적으로 매개 변수의 타입을 언급하지 않는다.
  - 변수 이름은 자유이며, 실행코드에서 매개 변수를 이용하여 로직을 실행한다.
- 매개변수가 1개이면 () 를 제외하여 작성할 수 있다.
- 매개변수가 없다면 () 만 사용한다.
- 람다식의 결과를 사용하여 값을 받고 싶다면 return 을 사용하면 된다.
  - {실행코드} 의 로직이 return 만 존재한다면 {} 와 return 을 제외하여 표현할 수 있다.

```JAVA
(arg1, arg2) -> { print(`arg1 : ${arg1}, arg2 : ${arg2}`) }
arg -> { print(`arg : ${arg}`); }
() -> { print("hello world"); }
(a, b) -> { return a + b; }
(a, b) -> a + b
```

## 타겟 타입과 함수적 인터페이스

- 자바는 메소드를 단독으로 선언할 수 없고 항상 클래스의 구성 멤버로 선언하기 때문에, 람다식은 단순히 메소드를 선언하는 것이 아니라 이 메소드를 가지고 있는 객체를 생성한다.
  - 인터페이스의 변수에 람다식을 대입하면, 그 인터페이스의 익명 구현 객체를 생성하여 대입하게 된다.
    - 인터페이스는 직접 객체화화 할 수 없기 때문에 구현 클래스가 필요하다.
    - 구현 클래스가 필요한데, 여기에 람다식을 사용하면 그 인터페이스의 구현 클래스를 익명구현 클래스로 생성하고 객체화를 한다.
    - 인터페이스의 종류에 따라 대입하는 람다식의 작성 방법은 달라진다. 이를 람다식의 타겟 타입이라고 한다.

### FunctionalInterface (함수적 인터페이스)

- 모든 인터페이스를 람다식의 타겟 대입으로 사용할 수 없다.
  - 람다식은 하나의 메소드를 정의하기 때문에 두 개 이상의 추상 메소드가 선언된 인터페이스는 람다식을 이용해서 구현 객체를 생성할 수 없다.
  - 즉, 하나의 추상 메소드가 선언된 인터페이스만 람다식의 타겟 타입이 될 수 있는데, 이러한 인터페이스를 함수적 인터페이스라고 한다.
  - @FunctionalInterface 어노테이션을 사용하면, 컴파일러가 체킹할 수 있게 할 수 있다.
    - 즉, 2개 이상의 추상 메소드가 선언되면 컴파일 오류가 발생된다.
    - 위 어노테이션이 없더라도 하나의 추상 메소드만 존재한다면 함수적 인터페이스이다.

```JAVA
@FunctionalInterface
public interface FunctionalInterfaceTest {
    public void method1();
    public void method2();
}
// 컴파일 에러
```

#### 함수적 인터페이스의 메소드에 따른 람다식 작성

```JAVA
@FunctionalInterface
public interface FooFuntionalInterfaceTest {
    public void method();
}

FooFunctionalInterfaceTest fooFunctionalInterfaceTest = () -> { print("Hello World~") };
fooFunctionalInterfaceTest.method(); // Hello World~

FooFunctionalInterfaceTest fooFunctionalInterfaceTest = () -> print("GoodBye World~");
fooFunctionalInterfaceTest.method(); // GoodBye World~

FooFunctionalInterfaceTest fooFunctionalInterfaceTest = () -> {
        print("Foo");
        print("Bar");
    };
fooFunctionalInterfaceTest.method(); // Foo Bar
// 매개 변수와리턴값이 없는 람다식

//////////////////////////////////////////////////////////////////////////////////////////

@FunctionalInterface
public interface BarFuntionalInterfaceTest {
    public void method(String str);
}

BarFuntionalInterfaceTest barFunctionalInterfaceTest = (name) -> { print(`${name} hello~`) };
barFunctionalInterfaceTest.method("Foo"); // Foo hello~

BarFuntionalInterfaceTest barFunctionalInterfaceTest = () -> print(`${name} hello!`);
barFunctionalInterfaceTest.method("Bar"); // Foo hello!

BarFuntionalInterfaceTest barFunctionalInterfaceTest = ("FooBar") -> {
        print(`${name} hello~`)
        print(`${name} hi~`)
    };
barFunctionalInterfaceTest.method(); // FooBar hello~ FooBar hi~
// 매개 변수가 있는 람다식

@FunctionalInterface
public interface FooBarFuntionalInterfaceTest {
    public int method(int a, int b);
}

FooBarFuntionalInterfaceTest foobarFunctionalInterfaceTest = (a, b) -> { return a + b; };
foobarFunctionalInterfaceTest.method(1, 1); // 2

FooBarFuntionalInterfaceTest foobarFunctionalInterfaceTest = (a, b) -> return a + b;
foobarFunctionalInterfaceTest.method(3, 4); // 7

FooBarFuntionalInterfaceTest foobarFunctionalInterfaceTest = (a, b) -> {
        a++;
        b++;
        return a + b;
    };
foobarFunctionalInterfaceTest.method(10, 20); // 32
// 리턴값이 있는 람다식

// 실행로직 블록을 같은 형태의 매개변수로 가지는 다른 메소드로 넘겨줘도 가능하다.

public int sum(int x, int y) { return x + y; }

FooBarFuntionalInterfaceTest foobarFunctionalInterfaceTest = (a, b) -> sum(a, b);
foobarFunctionalInterfaceTest.method(100, 200); // 300
```

## 클래스 멤버와 로컬 변수 사용

- 람다식의 실행 블록에는 클래스의 멤버 및 로컬 변수를 사용할 수 있다.
  - 클래스의 멤버는 제약 사항 없이 사용 가능
  - 로컬 변수는 제약 사향 존재

### 클래스 멤버 사용

- **람다식의 실행 블록에 클래스의 멤버를 사용할 때, this 는 익명객체가 아닌 람다식을 실행한 객체의 참조이다.**

```JAVA
@FunctionalInterface
public interface FuntionalInterfaceTest {
    public void method();
}

public class Outer {
    public int field = 10;

    class Inner {
        int field = 20;

        void method() {
            int field = 30;
            FuntionalInterfaceTest functionalInterfaceTest = () -> print(this.field);
            functionalInterfaceTest.method();
        }
    }
}

public static void main(String args[]) {
    Outer outer = new Outer();
    Outer.Inner inner = outer.new Inner();

    inner.method();
    // method() 내부의 field 가 아닌, method() 를 호출한 Inner 클래스의 field 가 참조된다.
    // 즉, 람다식의 실행 블록에 this 는 그 람다식을 실행한 객체가 된다.
}
```

### 로컬 변수 사용

- 람다식에서의 바깥 클래스의 필드나 메소드는 제한 없이 사용할 수 있으나, 메소드의 매개 변수 또는 로컬 변수를 사용하면 이 두 변수는 final 특성을 가져야 한다.
- 즉, 매개 변수 또는 로컬 변수를 람다식에서 읽는 것은 허용되지만, 람다식 내부 또는 외부에서 변경할 수 없다.

```JAVA
@FunctionalInterface
public interface FuntionalInterfaceTest {
    public void method();
}

public class Foo {
    void method(int arg) {  // arg 는 final 특성을 가짐
        int localVar = 40;  // localVar 는 final 특성을 가짐
        // arg, localVar 는 둘다 수정이 불가능하다.

        FuntionalInterfaceTest functionalInterfaceTest = () -> {
            print(arg);
            print(localVar);
        }
        functionalInterfaceTest.method();
    }
}

public static void main(String args[]) {
    Foo foo = new Foo();

    foo.method(100);
    // 100 40
}
```

## 표준 API 함수적 인터페이스

- 자바에서 제공하는 표준 API 에서 한 개의 추상 메소드를 가지는 인터페이스들은 모두 람다식을 이용하여 익명 구현 객체로 표현이 가능하다.
  - 대표적으로 Thread 의 동작을 하는 Runnable 클래스가 있다.
- 함수적 인터페이스는 java.util.function 표준 API 패키지로 제공한다.
  - 위 패키지의 목적은 메소드 또는 생성자의 매개 타입으로 사용되어 람다식을 대입할 수 있도록 하기 위해서이다.
  - Java 8 부터 추가되거나 변경된 API 에서 이 함수적 인터페이스들을 매개 타입으로 많이 사용된다.
  - 함수적 인터페이스는 Consumer, Supplier, Function, Operator, Predicate 로 구분된다.
    - 위의 구분 기준은 인터페이스에 선언된 추상 메소드의 매개값과 리턴값의 유무이다.

| 종류        | 추상 메소드 특징                                                 |                                       |
| --------- | --------------------------------------------------------- | ------------------------------------- |
| Consumer  | - 매개값은 있고, 리턴값은 없음                                        | (arg) -> (Consumer)                   |
| Supplier  | - 매개값은 없고, 리턴값은 있음                                        | (Supplier) -> (return value)          |
| Function  | - 매개값도 있고, 리턴값도 있음 <br/> - 주로 매개값을 리턴값으로 매핑 (타입 변환)       | (arg) -> (Function) -> (return value) |
| Operator  | - 매개값도 있고, 리턴값도 있음 <br/> - 주로 매개값을 연산하고 결과를 리턴            | (arg) -> (Function) -> (return value) |
| Predicate | - 매개값은 있고, 리턴 타입은 boolean <br/> - 매가값을 조사해서 true/false 리턴 | (arg) -> (Predicate) -> boolean       |

### Consumer 함수적 인터페이스

- Consumer 함수적 인터페이스의 특징은 리턴값이 없는 accept() 메소드를 가지고 있다.
- aceept() 메소드는 단지 매개값을 소비하는 역할만 한다.

| 인터페이스명                | 추상 메소드                         | 설명                  |
| --------------------- | ------------------------------ | ------------------- |
| Consumer\<T>          | void accept(T t)               | 객체 T 를 받아 소비        |
| BiConsumer<T, U>      | void accept(T t, U u)          | 객체 T 와 U 를 받아 소비    |
| DoubleConsumer        | void accept(double value)      | double 값을 받아 소비     |
| IntConsumer           | void accept(int value)         | int 값을 받아 소비        |
| LongConsumer          | void accept(long value)        | long 값을 받아 소비       |
| ObjDoubleConsumer\<T> | void accept(T t, double value) | 객체 T 와 double 값을 소비 |
| ObjIntConsumer\<T>    | void accept(T t, int value)    | 객체 T 와 int 값을 소비    |
| ObjLongConsumer\<T>   | void accept(T t, long value)   | 객체 T 와 long 값을 소비   |

- Consumer\<T>
  - T 라는 매개 객체를 이용하여 람다식도 한개의 매개 변수를 사용한다.
- BiConsumer<T, U>
  - T 와 U 라는 객체를 이용하여 람다식도 두개의 매개 변수를 사용한다.
- DoubleConsumer
  - double 타입의 매개변수를 고정으로 사용하며, 람다식도 double 을 사용하게 된다.
- IntConsumer
  - int 타입의 매개변수를 고정으로 사용하며, 람다식도 int 을 사용하게 된다.
- LongConsumer
  - long 타입의 매개변수를 고정으로 사용하며, 람다식도 long 을 사용하게 된다.
- ObjDoubleConsumer\<T>
  - T 라는 객체와 double 타입을 고정으로 사용하며, 람다식도 T 객체와 double 을 사용하게 된다.
- ObjIntConsumer\<T>
  - T 라는 객체와 int 타입을 고정으로 사용하며, 람다식도 T 객체와 int 을 사용하게 된다.
- ObjLongConsumer\<T>
  - T 라는 객체와 long 타입을 고정으로 사용하며, 람다식도 T 객체와 long 을 사용하게 된다.

```JAVA
Consumer<String> consumer = t -> { print(t); } // t 는 String 타입
BiConsumer<String, Boolean> consumer = (t, u) -> { if(u) print(t); } // t 는 String, u 는 boolean 타입
DoubleConsumer consumer = d -> { print(d); } // d 는 Double 타입
IntConsumer consumer = i -> { print(i); } // t 는 Int 타입
LongConsumer consumer = l -> { print(l); } // l 는 Long 타입
ObjDoubleConsumer<Boolean> consumer = (t, d) -> { if(t) print(t); } // t boolean 타입 d 는 Double 타입
ObjIntConsumer<Boolean> consumer = (t, i) -> { if(t) print(t); } // t boolean 타입 i 는 Int 타입
ObjLongConsumer<Boolean> consumer = (t, l) -> { if(t) print(t); } // t boolean 타입 l 는 Long 타입
// consumer 를 선언하는 방법
consumer.accept(arg...); // 위에 선언된 방식이면 타입에 따라 arg 에 들어간 값에 따라 print 가 된다.
// consumer 를 사용하는 방법
```

### Supplier 함수적 인터페이스

- Consumer 함수적 인터페이스의 특징은 매개 변수가 없고 리턴값이 있는 getXXX 메소드를 가지고 있다.
- getXXX() 는 실행 후 호출한 곳으로 데이터를 리턴하는 역할을 한다.

| 인터페이스명          | 추상 메소드                 | 설명            |
| --------------- | ---------------------- | ------------- |
| Supplier\<T>    | T get()                | T 객체를 리턴      |
| BooleanSupplier | boolean getAsBoolean() | boolean 값을 리턴 |
| DoubleSupplier  | double getAsDouble()   | double 값을 리턴  |
| IntSupplier     | int getAsInt()         | int 값을 리턴     |
| LongSupplier    | long getAsLong()       | long 값을 리턴    |

```JAVA
Supplier<String> supplier = () -> { ... return "hello"; } // t 타입을 리턴, 여기에서는 t 타입이 string 이다
BooleanSupplier supplier = () -> { ... return true; } // boolean 타입을 리턴
DoubleSupplier supplier = () -> { ... return 1.0; } // double 타입을 리턴
IntSupplier supplier = () -> { ... return 2; } // int 타입을 리턴
LongSupplier supplier = () -> { ... return 3L; } // long 타입을 리턴
// supplier 를 선언하는 방법
supplier.getXXX(); // 위에 타입에 따라 get 에 맞는 메소드를 사용하면, 정위된 리턴값이 리턴된다.
// supplier 를 사용하는 방법
```

### Function 함수적 인터페이스

- Function 함수적 인터페이스의 특징은 매개값과 리턴값이 있는 applyXXX() 메소드를 가지고 있다.
- applyXXX() 는 매개값을 리턴값으로 매핑(타입 변환) 하는 역할을 한다.

| 인터페이스명                   | 추상 메소드                           | 설명                     |
| ------------------------ | -------------------------------- | ---------------------- |
| Function<T, R>           | R apply(T t)                     | 객체 T 를 객체 R 로 매핑       |
| BiFunction<T, U, R>      | R apply(T t, U u)                | 객체 T 와 U 를 객체 R 로 매핑   |
| DoubleFunction\<R>       | R apply(double value)            | double 을 객체 R 로 매핑     |
| IntFunction\<R>          | R apply(int value)               | int 를 객체 R 로 매핑        |
| IntToDoubleFunction      | double applyAsDouble(int value)  | int 를 double 로 매핑      |
| IntToLongFunction        | long applyAsDouble(int value)    | int 를 long 로 매핑        |
| LongToDoubleFunction     | double applyAsDouble(long value) | long 을 double 로 매핑     |
| LongToIntFunction        | int applyAsDouble(long value)    | long 을 int 로 매핑        |
| ToDoubleBiFunction<T, U> | double applyAsDouble(T t, U u)   | 객체 T 와 U 를 double 로 매핑 |
| ToDoubleFunction\<T>     | double applyAsDouble(T t)        | 객체 T 를 double 로 매핑     |
| ToIntBiFunction<T, U>    | int applyAsInt(T t, U u)         | 객체 T 와 U 를 int 로 매핑    |
| ToIntFunction\<T>        | int applyAsInt(T t)              | 객체 T 를 int 로 매핑        |
| ToLongBiFunction<T, U>   | long applyAsLong(T t, U u)       | 객체 T 와 U 를 long 로 매핑   |
| ToLongFunction\<T>       | long applyAsLong(T t)            | 객체 T 를 long 으로 매핑      |

```JAVA
Function<Person, String> function = t -> {... return t.getName(); }
BiFunction<Person, Person, Int> function = (t, u) -> {... return t.getAge() + u.getAge(); }
DoubleFunction<Zimbabwe> function = t -> {... zim.saveMoney(t); return zim; }
IntFunction<Person> function = t -> {... person.saveAge(t); return person; }
IntToDoubleFunction function = t -> {... return Double.valueOf(t); }
IntToLongFunction function = t -> return Long.valueOf(t);
LongToDoubleFunction function = t -> {... return Double.valueOf(t); }
LongToIntFunction function = t -> return Integer.valueOf(t);
ToDoubleBiFunction<Zimbabwe, Zimbabwe> function = (t, u) -> {... return t.getZ$() + u.getZ$(); }
ToDoubleFunction<Zimbabwe> function = t -> return t.getZ$();
ToIntBiFunction<Grade, Grade> function = (t, u) -> {... return t.getGrade() + u.getGrade(); }
ToIntFunction<Grade> function = t -> return t.getGrade();
ToLongBiFunction<EntityObject, EntityObject> function = (t, u) -> {... return t.getLongValue() + u.getLongValue(); }
ToLongFunction<EntityObject> function = t -> return t.getId();
// function 을 선언하는 방법
print(function.apply(person)); // 오성진
// function 을 사용하는 방법
// 각 메소드별 정의되어 있는 추상 메소드를 알맞게 사용하면 된다.

///////////////////////////////

public class ZimbabweAcount {
    public double balance;

    public double getBalance() { return this.balance; }
    public void setBalance(double balance) { this.balance = balance; }
}

public static void main(String args[]) {
    ZimbabweAcount acount1 = new ZimbabweAcount();
    ZimbabweAcount acount2 = new ZimbabweAcount();

    acount1.setBalance(대략 겁나 큰 숫자1);
    acount2.setBalance(대략 겁나 큰 숫자2);

    ToDoubleBiFunction<ZimbabweAcount, ZimbabweAcount> function = (a1, a2) -> return a1.getBalance() + a2.getBalance();

    Double totalBalance = function.applyAsDouble(acount1, acount2); // 대략 엄청 큰게 2개가 되어버린 숫자
}
```

### operator 함수적 인터페이스

* Function 함수적 인터페이스와 똑같이 매개 변수와 리턴값이 있는 applyXXX() 메소드를 가지고 있다.
* 매개값을 리턴값으로 매핑(타입 변환) 역할을 하는 Fuction 과 다르게, 매개값을 이용해서 연산을 수행한 후 동일한 타입으로 리턴값을 제공하는 역할을 한다.

| 인터페이스명               | 추상 메소드                                     | 설명                 |
| -------------------- | ------------------------------------------ | ------------------ |
| BinaryOperator\<T>   | T apply(T t, T t)                          | T 와 T 를 연산한 후 T 리턴 |
| UnaryOperator\<T>    | T apply(T t)                               | T 를 연산한 후 T 리턴     |
| DoubleBinaryOperator | double applyAsDouble(Double d1, Double d2) | 두 개의 double 연산     |
| DoubleUnaryOperator  | double applyAsDouble(Double d)             | 한 개의 double 연산     |
| IntBinaryOperator    | int applyAsInt(Int i1, Int i2)             | 두 개의 int 연산        |
| IntUnaryOperator     | int applyAsInt(Int d)                      | 한 개의 int 연산        |
| LongBinaryOperator   | long applyAsLong(Long i1, Long i2)         | 두 개의 long 연산       |
| LongUnaryOperator    | long applyAsLong(Long d)                   | 한 개의 long 연산       |

```JAVA
BinaryOperator<Person> operator = (t, u) -> {... return new Person(t, u); }
UnaryOperator<Person> operator = t -> {... return t; }
DoubleBinaryOperator operator = (t, u) -> {... return t + u; }
DoubleUnaryOperator operator = t -> {... return t; }
IntBinaryOperator operator = (t, u) -> {... return t + u; }
IntUnaryOperator operator = t -> return t;
LongBinaryOperator operator = (t, u) -> { return t * u; }
LongUnaryOperator operator = t -> return t;
// operator 을 선언하는 방법
print(operator.applyAsDouble(1.0, 2.0)); // 3.0
// operator 을 사용하는 방법
// 각 메소드별 정의되어 있는 추상 메소드를 알맞게 사용하면 된다.

///////////////////////////////
public static void main(String args[]) {
    ...

    DoubleBinaryOperator doubleSumOperator = (a1, a2) -> return a1 + a2;

    Double sumValue = doubleSumOperator.applyAsDouble(1.0, 2.0); // 3.0
}
```

## Predicate 함수적 인터페이스

* 매개 변수와 boolean 리턴값이 있는 testXXX() 메소드를 가지고 있다.
* 매개값을 이용해서 boolean 을 리턴한다.

| 인터페이스명            | 추상 메소드                 | 설명           |
| ----------------- | ---------------------- | ------------ |
| Predicate\<T>     | boolean test(T t)      | 객체 T 를 조사    |
| BiPredicate<T, U> | boolean test(T t, U u) | 객체 T, U 를 조사 |
| DoublePredicate   | boolean test(double d) | double 값을 조사 |
| IntPredicate      | boolean test(int d)    | int 값을 조사    |
| LongPredicate     | boolean test(long d)   | long 값을 조사   |


```JAVA
Predicate<Person> predicate = t -> {... return t.getSex() === SEX.MAIL }
BiPredicate<Person, Int> operator = (t, u) -> {... return t.getAge > u; }
DoublePredicate operator = t -> {... return t > 100;  }
IntPredicate operator = t -> {... return t < 100; }
LongPredicate operator = t -> {... return t === 100; }
// predicate 을 선언하는 방법
print(operator.test(person, 20)); // true or false
// operator 을 사용하는 방법
// 각 메소드별 정의되어 있는 추상 메소드를 알맞게 사용하면 된다.

///////////////////////////////
public static void main(String args[]) {
    ...

    Predicate predicate = (p) -> return p.getSex() === SEX.MAIL

    if (predicate.test(person)) { print("남자입니다."); }
    else { print("여자입니다."); }
}
```

### andThen() | compose() 디폴트 메소드

* 디폴트 및 정적 메소드는 추상 메소드가 아니기 때문에 함수적 인터페이스에 선언되어도 여전히 함수적 인터페이스의 성질을 잃지 않는다.
    * 함수적 인터페이스 성질이란, 하나의 추상 메소드를 가지고 있고, 람다식으로 익명 구현 객체를 생성할 수 있는 것을 말한다.
* java.util.function 패키지의 함수적 인터페이스는 하나 이상의 디폴트 및 정적 메소드를 가지고 있다.
* Consumer, Function, Operator 종류의 함수적 인터페이스는 andThen(), compose() 디폴트 메소드를 가지고 있다.
* andThen(), compose() 는 두 개의 함수적 인터페이스를 순차적으로 연결하고, 첫 번째 처리 결과를 두 번째 매개값으로 제공해서 최종 결과값을 얻을 때 사용한다.
    * andThen() : 앞의 인터페이스 부터 처리한 후, 결과를 뒤의 매개변수로 들어가 있는 인터페이스에 넘겨준다.
      - Consumer
        1. Consumer\<T>
        2. BiConsumer<T, U>
        3. DoubleConsumer
        4. IntConsumer
        5. LongConsumer
      - Function
        1. Function<T, R>
        2. BiFunction<T, U, R>
      - Operator
        1. BinaryOperator\<T>
        2. DoubleUnaryOperator
        3. IntUnaryOperator
        4. LongUnaryOperator
    * compose() : 매개 변수의 인터페이스를 처리하고, 그 결과를 호출한 인터페이스의 매개값으로 넘겨준다.
      - Consumer
        1. LongConsumer
      - Function
        1. Function<T, R>
      - Operator
        1. DoubleUnaryOperator
        2. IntUnaryOperator
        3. LongUnaryOperator

```JAVA
...
andThenInterface = A.andThen(B);
...

result1 = andThenInterface.method();
// andThenInterface 메소드 호출 -> A 람다식 결과를 B 의 매개변수로 실행 -> 최종 결과 리턴

...
composeInterface = A.compose(B);
...

result2 = composeInterface.method();
// composeInterface 메소드 호출 -> B 람다식 결과를 A 의 매개변수로 실행 -> 최종 결과 리턴

///////////////////////////////////////////////////////////////
// consumer
// 처리 결과를 리턴하지 않기 때문에, andThen() 디폴트 메소드는 함수적 인터페이스의 호출 순서만 정한다.

// function, operator
// 먼저 실행한 함수적 인터페이스의 결과를 다음 함수적 인터페이스의 매개값으로 넘겨주고, 최종 처리 결과를 리턴한다.
// 두 함수적 인터페이스를 연결할 때, 연결당시에 넘겨지는 값은 전달데이터 이다.

public enum GENDER {
    MALE, FEMALE
}

@Getter
@Setter
@AllArgsConstructor
public class Member {
    private String name;
    private String id;
    private int sex;
}

public static void main(String args[]) {
    Member member = new Member("오성진", "test", 1);

    Consumer<Member> consumerA = (m) -> { print(m.getName();) }
    Consumer<Member> consumerB = (m) -> { print(m.getId();) }

    Consumer<Member> consumerAB = consumerA.andThen(consumerB);
    consumerAB.accept(member);
    // 오성진test

    Function<Member, Int> functionA = m -> return m.getSex;
    Function<Int, GENDER> functionB = i -> return GENDER.valueOf(i);

    Function functionAB = functionA.andThen(functionB);
    GENGER gender = functionAB.apply(member); // MALE
}
```

### 디폴트 메소드 - and(), or(), negate() | 정적 메소드 - isEqual()

* 위의 메소드는 Predicate 종류의 함수적 인터페이스에 가지고 있다.
    * negate() : ! 연산자랑 같다. (결과값이 ture 면 false 로, false 면 true)
* and(), or(), negate(), isEqual() 의 경우 모든 Predicate 함수적 인터페이스에서 제공된다.
    * isEqual() 메소드는 test() 매개값인 sourceObject 와 isEqual() 의 매개값인 targetObject 를 java.util.Objects 클래스의 equals() 의 매개값으로 제공하고, Objects.equals(sourceObject, targetObject) 의 리턴값을 얻어 새로운 Predicate\<T> 를 생성한다.

> Predicate\<Object> predicate = Predicate.isEqual(targetObject); <br/>
> boolean result = predicate.test(sourceObject); <br/>
> 위처럼 isEqual() 객체를 생성할 때 들어간 매개변수와 비교되는 객체를 만들고, test() 메소드를 사용할 때 객체를 생성할때 생성한 targetObject 와 비교하여 결과를 리턴한다.

| sourceObject | targetObject | 리턴값                  |
| ------------ | ------------ | -------------------- |
| null         | null         | true                 |
| not null     | null         | false                |
| null         | not null     | false                |
| not null     | not null     | isEqual().test() 리턴값 |

### minBy(), maxBy()

* operator 의 BinaryOperator\<T> 에 minBy(), maxBy() 정적 메소드가 제공한다.


| 리턴 타입              | 정적 메소드                                  |
| ------------------ | --------------------------------------- |
| BinaryOperator\<T> | minBy(Comparator<? super T> comparator) |
| BinaryOperator\<T> | maxBy(Comparator<? super T> comparator) |

```JAVA
@Getter
@Setter
@AllArgsConstructor
public class person {
    private String name;
    private int age;
}

public static void main(String args[]) {
    Person p1 = new Person("foo", 20);
    Person p2 = new Person("bar", 25);

    BinaryOperator<Person> binaryOperator = BinaryOperator.minBy( (p1, p2) -> Integer.compare(p1.age, p2.age) );
    Person young = binaryOperator.apply(p1, p2);
}    
```

## Method References (메소드 참조)

* 메소드를 참조해서 매개 변수의 정보 및 리턴 타입을 알아내어, 람다식에서 불필요한 매개 변수를 제거하는 것이 목적.
* 람다식은 종종 기존 메소드를 단순히 호출만 하는 경우가 많다.
* 정적 또는 인터스턴스 메소드, 생성자 참조 모두 가능하다.

```JAVA
(left, right) -> Math.MAX(left, right);
Math::MAX; // 메소드 참조
IntBinaryOperator intBinaryOperator = Math::MAX;
// IntBinaryOperator 의 경우 2개의 int 를 받아서 int 를 리턴하므로, 위처럼 메소드 참조로 표현할 수 있다.
```

### 정적 메소드와 인턴스 메소드 참조

* 정적 메소드 참조일 경우, **클래스::메소드** 형태로 기술한다.
* 인스턴스 메소드 참조일 경우, **참조변수::메소드** 형태로 기술한다.

### 매개 변수의 메소드 참조

* 메소드는 람다식 외부의 클래스 멤버일 수도 있고, 람다식에서 제공되는 매개 변수의 멤버일 수도 있다.

> 람다식에서 제공되는 매개 변수의 멤버인 경우 <br/>
> (a, b) -> { a.instanceMethod(b); }

* 위의 경우 **a::instanceMethod** 향태로 표현하면 된다.
    * 정적 메소드 참조와 동일하지만, 실질적으로는 a 의 인스턴스 메소드가 참조되어 실행된다.

### 생성자 참조

* 단순히 객체를 생성하고 리턴하도록 구성된 람다식은 생성자 참조로 대치할 수 있다.
* 생성자가 오버로딩 되어 있다면, 컴파일러는 함수적 인터페이스의 추상 메소드와 동일한 매개 변수 타입과 개수를 가지고 있는 생성자를 찾아서 실행한다.
    * 해당 생성자가 없을 경우, 컴파일 오류가 발생한다.

> (a, b) -> return new Foo(a, b); <br/>
> Foo::new

```JAVA
public class Calculator {
    public static int staticMethod(int x, int y) {
        return x + y;
    }
    public in t instanceMethod(int x, int y) {
        return x + y;
    }
}

@Getter
@Setter
public class Member {
    private String name;
    private int age;

    public Member(String name) {
        this.name = name;
    }

    public Member(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

public static void main(String args[]) {
    Calculator calc = new Calculator();
    IntBinaryOperator staticOperator = (x, y) -> Calculator::staticMethod;
    IntBinaryOperator instanceOperator = (x, y) -> calc::instanceMethod;

    /////////////////////////////////////////////////

    // ToIntBiFunction<String, String> function = (a, b) -> a.compareToIgnoreCase(b);
    ToIntBiFunction<String, String> function = String::compareToIgnoreCase;
    int result = function.applyAsInt("Foo", "FOO"); // 0

    /////////////////////////////////////////////////
    
    Function<String, Member> function1 = Memeber::new;
    // Member(String name)
    Function<String, int, Member> function1 = Memeber::new;
    // Member(String name, int age)

    Member member1 = function1.apply("foo");
    Member member2 = function2.apply("foo", 20);
}
```
