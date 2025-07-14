# 📚 Java 기본적인 개념 학습

## 1-1. char와 String의 메모리 사용 차이

char는 primitive 자료형이다. String은 객체다. 
char는 jvm의 스택영역을 사용하고, 문자 값이 직접 거기에 저장된다. 만약 특정 객체의 필드 값이면 heap에 생성될 수도 있다. 
String에 문자열을 바로 넣으면 컨스턴트 풀 영역에 String 객체가 생성되고, new 키워드로 선언하면 String 객체는 heap 영역에 생성된다.

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
| String 객체 | 컨스턴트 풀 / 힙     |리터럴 사용 시 메서드 영역, new로 만들면 힙에|

```java
String str = "apple"; //컨스턴트 풀 영역에 String 객체 생성
String str2 = new String("banana"); // 힙에 String 객체와 banana 생성, str2는 힙의 String 객체 참조
String str3 = str2; //str3는 힙 영역의 String 객체를 참조
```

String Constact Pool에서 String은 Hash 기반으로 관리된다. 

```java
 @Test
 @DisplayName("같은 문자열의 해시코드 비교 ")
 public void compareStringHashCode() {
     String abc1 = "abc";
     String abc2 = new String("abc");
     // TODO 1 변수에 정의한 문자열과 new 를 통해 생성한 인스턴스가 같은 주소를 가리키는지 비교
     assertThat(abc1).isEqualTo(abc2);
     assertThat(abc1 == abc2).isFalse();
 }
```
이 테스트는 통과한다. Why?
isEqualTo는 내부적으로 메모리 주소를 비교하는 것이 아니라 string 값을 찾아서 값끼리 비교하도록 구현되어 있기 때문이다. 
근데 변수값 자체를 == 키워드로 비교하면 false가 나는 것이다. 

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

추가적으로 다음과 같은 방법도 존재한다. 
```java
// 4. join
String s4 = String.join(", ", "A", "B", "C");

// 5. String.format
String s5 = String.format("Score: %d", 100);

// 6. Stream.joining
List<String> list = List.of("A", "B", "C");
String s6 = list.stream().collect(Collectors.joining("-"));
```

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
- 각각 다 Interface이다. (Array 빼고)
1. List 
   - List는 ArrayList와 LinkedList 2가지로 나뉜다. 차이는 자료구조에서 배웠듯이 일반 배열과 linked-list와 같다. 
2. Array
   - 이건 그냥 naive한 배열이다. [] 키워드로 선언하는 것이다. 
3. Set
   - 얘도 인터페이스이다. 구현체로 HashSet과 TreeSet이 들어올 수 있다. 각각 hash function, red-black 이진트리로 구현되어 있다. 그래서 HashSet은 삽입, 삭제, 탐색이 O(1)이다. TreeSet은 CRUD가 log(n)이지만 데이터들이 정렬된다. 
4. Map
   - 얘도 인터페이스이다. 구현체로 HashMap과 TreeMap이 들어올 수 있다. 각각 hash function, red-black 이진트리로 구현되어 있다. 그래서 HashMap은 삽입, 삭제, 탐색이 O(1)이다. TreeMap은 CRUD가 log(n)이지만 데이터들이 정렬된다.
   - HashMap 사용시 충돌이 나면 linked-list로 chaining 되다가 특정 임계값 이상으로 충돌이 나면 red-black tree로 chaining이 된다. 

다음은 HashMap에서 충돌의 개수가 임계값을 넘어가면 chaining 방법이 linked-list에서 red-black tree로 바뀌는데, 해당 부분 라이브러리 메소드이다. 
```java
/**
* Replaces all linked nodes in bin at index for given hash unless
* table is too small, in which case resizes instead.
*/
final void treeifyBin(Node<K,V>[] tab, int hash) {
  int n, index; Node<K,V> e;
  if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
      resize();
  else if ((e = tab[index = (n - 1) & hash]) != null) {
      TreeNode<K,V> hd = null, tl = null;
      do {
          TreeNode<K,V> p = replacementTreeNode(e, null);
          if (tl == null)
              hd = p;
          else {
              p.prev = tl;
              tl.next = p;
          }
          tl = p;
      } while ((e = e.next) != null);
      if ((tab[index] = hd) != null)
          hd.treeify(tab);
  }
}
```

## 2-3. 대량의 데이터를 다룰 때, Map, Set, List, Array 중에서 검색 속도가 가장 빠른 것은 무엇이고 이유를 설명

HashMap이나 HashSet은 데이터가 대용량이라면 충돌이 많이 일어날 수 있다고 생각했다.
충돌의 정도에 따라 Hash 구조를 사용하든가 아니면, 정렬된 Tree 구조를 사용하는 것이 좋다고 생각했다. 
List에서 정렬된 구조를 사용하고 이진탐색을 사용할 수도 있을 것 같다. 

HashMap이나 HashSet이 가장 효율적일 것 같다. 
충돌이 많이 일어나도 경우에 따라서 chaining을 linked-list에서 red-black 트리로 관리한다고 한다. 

## 3-1. Exception 계층 구조와 분류
- checked Exception과 unchecked Exception의 차이점 
  - 컴파일 타임에 잡히는 예외인지 아닌지에 따라 잡히면 checked, 안잡히면 unchecked이다. 대표적으로 각각 illegalArgumentException 과 NullpointerException이 있다. 
- Error와 Exception의 차이점
  - 복구가 가능하냐 불가능하냐의 차이이다. overflow 처럼 복구가 불가능하면 Error이고(시스템이 종료될 수준), try-catch로 잡히면 exception이다. 

## 3-2. 언제 사용자 정의 예외(Custom Exception)를 만들어야 하는가? (적절한 예시)
exception 별로 다르게 처리해주고 싶을 때 사용한다. 
exception이 어디서 발생했는지 판단하기 위해서 사용할 수도 있다.
UserException.
혹은 300/400/500 status code

## 3-3. 예외를 catch해서 처리하는 것 vs throws를 사용해서 상위 메소드로 전파하는 것
SRP에 따라 정하면 될 것 같다. 예외를 무조건 상위 메소드로 던지는 건 책임 전가이다. 
자신이 맡은 비즈니스 로직에서 처리하는 것이 맞다고 생각하는 예외에 대해서는 try-catch로 잡는 것이 바람직하다고 생각한다. 

## Tip 
자바 문서는 다음과 같이 검색하면 된다. 
```text
java8 javadoc
java17 javadoc
```