# 📚 프록시 패턴

## 개요 
2주차 과제를 진행하던 중에 사용자가 로그인 시에, session id를 발급, 저장해두어야 하는 요구사항이 생겼다. 
그래서 사용자가 특정 url에 접근할 때마다 인증/인가를 진행해야 한다는 것을 알고 프록시 패턴을 사용하고자 했다. 
이번에 개념만 알고있던 다이내믹 프록시를 사용해보고 싶어서 사용법과 개념을 학습하고자 한다. 

## 기존 프록시 패턴의 단점
프록시 패턴은 인터페이스의 구현체를 실행하기 전에 특정 작업을 앞뒤로 붙이고 싶을 때 사용한다. 
```java
interface AInterface {
    String call();
}

class AImpl implements AInterface {
    @Override
    public String call() {
        System.out.println("A 호출");
        return "a";
    }
}

class AProxy implements AInterface {
    AInterface subject;

    AProxy(AInterface subject) {
        this.subject = subject;
    }

    @Override
    public String call() {
        System.out.println("TimeProxy 실행");
        long startTime = System.nanoTime();

        String result = subject.call();

        long endTime = System.nanoTime();
        long resultTime = endTime - startTime;
        System.out.println("TimeProxy 종료 resultTime = " + resultTime);

        return result;
    }

    public static void main(String[] args) {
        AInterface proxyA = new AProxy(new AImpl());
        proxyA.call();
    }
}
```

근데 이 구조의 단점은 프록시를 붙이고 싶은 구현체마다 이 프록시 클래스를 정의하고 객체를 생성해주어야 한다는 치명적인 단점이 존재한다.

## 다이나믹 프록시
그래서 java.lang.reflect.Proxy 라이브러리에서 런타임 시에 동적으로 프록시 객체를 만들어서 붙여주는 기능을 제공한다.
```java
public class Proxy implements java.io.Serializable {

	// ...
    
    public static Object newProxyInstance(
        ClassLoader loader, 
        Class<?>[] interfaces, 
        InvocationHandler h 
    ) throws IllegalArgumentException {
        // ...
    }

}

public interface InvocationHandler {
    public Object invoke(Object proxy, Method method, Object[] args)
            throws Throwable;
}
```

InvocationHandler라는 인터페이스의 구현체를 통해 invoke 함수를 override해서 내가 수행하고 싶은 
프록시의 동작을 명시하고 newProxyInstance라는 static 메소드의 인자로 적용하고자 하는 인터페이스와 구현체들을 넣어주면 된다. 

근데 난 특정 구현체만 proxy 객체로 감싸야 한다.
그래서 조건을 판단하고 특정 구현체만 proxy 객체로 감싸지는 Factory 클래스도 구현했다. 
```java
public class ProxyFactory {
    public static <T> T wrapIfNeeded(T target, Class<T> intf, Predicate<T> needProxy) {
        if (needProxy.test(target)) {
            return (T) Proxy.newProxyInstance(
                intf.getClassLoader(),
                new Class[] { intf },
                new LoggingHandler(target)
            );
        } else {
            return target;
        }
    }
}
```
