# 📚 Java 기본적인 개념 학습

## 1-1. char와 String의 메모리 사용 차이

char는 primitive 자료형이다. String은 객체다. 
char는 jvm의 스택영역을 사용하고, 문자 값이 직접 거기에 저장된다. 
String은 문자값이 컨스턴스 풀 영역에 저장되고, String 객체는 heap 영역에 생성되며, 할당받은 상수값의 컨스턴트 풀 영역 주소를 할당받게 된다. 

```java
public class Main {
    public static void main(String[] args) {
        char ch = 'A';
        String str = "Hello";
    }
}
```
| 변수/데이터    | 저장 위치(영역)      | 설명 |
|-----------|----------------|-------|
| ch        | 스택             |지역변수, 2byte 값(‘A’) 자체가 스택에 저장 |
| "Hello"   | 메서드 영역(컨스턴트 풀) |상수풀(constant pool)에 저장|
| str 참조 변수 | 스택             |str 변수(주소값)가 스택에 저장|
| String 객체 | 힙              |리터럴 사용 시 메서드 영역, new로 만들면 힙에|

```java
String str = "apple"; //컨스턴트 풀 영역에 String 객체 생성
String str2 = new String("banana"); //컨스턴트 풀 영역에 banana 생성, 힙에 String 객체 생기고 banana 참조, str2는 힙의 String 객체 참조
String str3 = str2; //str3는 힙 영역의 String 객체를 참조
```

String Constact Pool에서 String은 Hash 기반으로 관리된다. 


## 1-2. String 객체가 immutable인 이유와 장단점
String이 불변이다? 무슨 말일까? 
```java
String str = "apple";
str = "banana";
```
이처럼 변할 수 있는거 아닌가?

String에 문자열을 삽입하는 형태의 코드다. 이렇게하면 jvm에서 어떻게 동작하냐면, apple이라는 상수가 컨스턴트 풀 영역에 
생성되고, str이라는 변수가 apple 상수가 저장된 곳의 주소를 가르키는 구조이다. 
banana로 재할당 하면 컨스턴트 영역에 apple은 그대로 존재하고, banana가 새로 생성되고, str의 참조 주소값이 banana의 주소로 바뀌는 것이다. 
그래서 apple 자체가 변화하지 않으니 불변이라고 하는 것이다. 

그럼 왜 이렇게 문자열을 불변으로 다룰까?

1. heap 영역의 공간을 아낄 수 있다.
   - 같은 값을 가지는 객체에 대해서 같은 주소값을 참조하게 하면 되므로 메모리를 아낄 수 있다. 
2. 보안상의 문제를 방지한다. 
   - 해커의 공격으로 인해 id, pwd 같은 중요한 정보가 변화하는 것을 막을 수 있다. 
3. 멀티 스레드 환경에서 race condition을 사전에 방지할 수 있다. 
   - 변화시킬 수 없으니 값을 참조만 하게 된다. 
4. Hash code를 미리 계산해두고 확인하는 방식을 사용할 수 있다. 
   - Java에서는 String의 Hash 값을 사용할 때마다 계산하는 것이 아니라 미리 계산해두고 참조만 하는 형식으로 사용한다. 

## 1-3. String concatencation(문자열 연결)의 다양한 방법들 
대표적으로 3가지 방법이 존재한다. 

1. concat
```java
String strSample1 = "Hello";
String result = strSample1.concat("World");
System.out.println(result); // HelloWorld
```
concat 명령어는 합치려는 문자열 중 하나라도 null이면 안된다. 
더하려는 문자을 new String()으로 새로운 객체를 생성하는 방식으로 하나 만들고, 붙이는 것이다.

즉, 위의 예시에서는 컨스턴스 풀 영역에 Hello, World, HelloWorld 3개가 메모리 영역에 생성된다. 

2. StringBuilder
```java
StringBuilder result = new StringBuilder();
result.append("Hello");
result.append("World");

System.out.println(result);
```
주소값이 변하지 않는다. Hello가 1000번에 생성되었으면, HelloWorld 라는 문자열도 똑같이 1000번에 쓰인다. 

null을 append 하면 어떻게 될까?
"null" 문자열 자체를 더해주게 된다. 
```java
public AbstractStringBuilder append(String str) {
    if (str == null)
        return appendNull();
    ...
}

private AbstractStringBuilder appendNull() {
    int c = count;
    ensureCapacityInternal(c + 4);
    final char[] value = this.value;
    value[c++] = 'n';
    value[c++] = 'u';
    value[c++] = 'l';
    value[c++] = 'l';
    count = c;
    return this;
}
```

to_string 메소드를 호출하기 전까지 새로운 String 객체를 생성하지 않는다. 

3. `+` 연산자
```java
String a = "hello";
String b = "world";
String c = a + b; // "helloworld"
```

자바 1.5 이전에는 concat의 방식과 같고, 1.5 이후에는 StringBuilder를 사용하는 방식과 같다고 한다. 

concat이랑 `+` 연산을 사용하는 것은 새로운 객체를 생성하기 때문에 효율적이지 못하다고 할 수 있다. 
StringBuilder를 사용하도록 하자.

## 1-4. StringBuilder와 StringBuffer의 차이
StringBuilder와 StringBuffer는 둘 다 내부적으로 String 연산을 위한 Buffer를 가진다. 
차이는 Builder의 경우에 문자열 파싱 능력이 뛰어나고, Buffer는 멀티 스레드에서 안전하다는 점이 다르다. 

1. StringBuffer
default로 16개의 문자를 저장할 수 있는 버퍼가 주어지고, 생성자에서 조절가능하다.
내부적으로 synchronized 키워드가 선언되어 있어서 멀티 스레드 환경에서 동기화를 지원한다. 

2. StringBuilder
StringBuffer와 동일하지만 synchronized 선언을 하지 않아서 동기화를 지원하지  않는다. 

## 2-1. Generic이란?
클래스 선언을 할 때 특정 클래스 타입을 지정하지 않고 컴파일 타임에 들어오는 클래스로 지정하고 싶을 때 사용한다. 

Object로 선언하면 되지 않는가? 라는 의문이 들 수 있다. 
- Object로 선언하면 다운 캐스팅 해줘야 하니까 어쨌든 type을 알아야 한다. 

## 2-2. Map, Set, List, Array는 각각 어떤 특징을 가지고 있나요? (예: 중복 데이터 허용 여부, 순서보장, 키-값 쌍 저장 등의 관점)
- 각각 다 Interface이다. 

## 2-3. 대량의 데이터를 다룰 때, Map, Set, List, Array 중에서 검색 속도가 가장 빠른 것은 무엇이고 이유를 설명

## 3-1. Exception 계층 구조와 분류
- checked Exception과 unchecked Exception의 차이점
- Error와 Exception의 차이점

## 3-2. 언제 사요아 정의 예외(Custom Exception)를 만들어야 하는가? (적절한 예시)

## 3-3. 예외를 catch해서 처리하는 것 vs throws를 사용해서 상위 메소드로 전파하는 것
