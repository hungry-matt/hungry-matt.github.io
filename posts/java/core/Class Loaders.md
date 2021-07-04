# Class Loader?
- 클래스 로더는 런타임 중에 Java 클래스를 JVM에 동적으로 로드하는 역할을 하며 JRE(Java Runtime Environment)의 일부이다.
- 예를 들어, A라는 클래스를 인스턴스화 할 때 JVM은 A 클래스를 클래스 로더를 통해 메모리에 로드한다.
- 이렇게 런타임중에 동적으로 클래스를 로딩한다는 것은 JVM이 클래스에 대한 정보를 갖고 있지 않다는 것을 의미한다.
- Java 클래스는 한 번에 메모리에 로드되지 않고 응용프로그램에서 필요할 때 로드하는 방식이다.

JVM이 시작 되면 3개의 클래스 로더들이 사용된다.

1. 부트스트랩 클래스 로더
2. 확장 클래스 로더
3. 시스템 클래스 로더

## 1. Bootstrap Class Loader
- Java 클래스는 java.lang.ClassLoader의 인스턴스에 의해 로드 된다.

### 그런데 클래스 로더 또한 클래스 인데 java.lang.ClassLoader는 어떻게 로드 되는걸까? 
- 부트스트랩 클래스 로더는 JVM이 호출되면 실행된다.
- 주로 $JAVA_HOME/jre/lib 디렉토리에 있는 JDK 내부 클래스 rt.jar(java.lang.* , java.util.*) 및 기타 핵심 라이브러리를 로드하는 역할을 한다.
- 또한 `다른 모든 ClassLoader 인스턴스의 부모 역할을 한다.`
- 네이티브 코드로 작성 되었으며 Java 클래스가 아니다.

## 2. Extension Class Loader
- 확장 클래스 로더는 부트스트랩 클래스 로더의 하위이며 모든 애플리케이션에서 사용할 수 있도록 확장 자바 클래스들을 로드한다.
- 주로 JDK 확장 디렉토리인 $JAVA_HOME/lib/ext 디렉토리 또는 java.ext.dirs 시스템 특성에 설정된 기타 디렉토리에서 클래스들을 로드한다.

## 3. System Class Loader
- 시스템 클래스 로더는 확장 클래스 로더의 하위 이며 모든 애플리케이션의 클래스들을 JVM으로 로드한다.
- classpath 환경 변수나 -classpath, -cp 옵션에 지정된 클래스들을 로드한다.

# 클래스 로더 유형 확인
다음 예제를 통해 클래스를 로더한 클래스 로더를 확인해보자.

```java
public class PrintClassLoader {

    public void printClassLoaders() {
        System.out.println("Classloader of this class:"
        + PrintClassLoader.class.getClassLoader()); // 1

        System.out.println("Classloader of Logging:"
        + Logging.class.getClassLoader()); // 2

        System.out.println("Classloader of ArrayList:"
        + ArrayList.class.getClassLoader()); // 3
    }

}
```

실행결과:
```
Classloader of this class:sun.misc.Launcher$AppClassLoader@18b4aac2
Classloader of Logging:sun.misc.Launcher$ExtClassLoader@7cd84586
Classloader of ArrayList:null
```
1) 시스템 클래스 로더(애플리케이션 클래스 로더)에 의해 로드 되었다. classpath에서 사용자가 작성한 클래스를 `sun.misc.Launcher.AppClassLoader`가 로드하였다.
2) `com.sun.javafx.util` 패키지에 속해 있는 클래스이다. 확장 기능에 속하는 클래스로 확장 클래스 로더인 `sun.misc.Launcher.ExtClassLoader`에 의해 로드 되었다.
3) `java.util` 패키지에 속해 있는 클래s 이며 부트스트랩 클래스 로더에 의해 로드 되었다. 부트스트랩 클래스 로더는 null로 표현된다.

# How Do Class Loaders Work?
- 클래스가 아직 로드되지 않았을 경우 요청을 상위 클래스 로더에게 위임하는 방식입니다.
- 클래스 로더에는 다음 세가지 기능이 있습니다.

## 1. Delegation Model
- 클래스 로더는 `위임 모델`을 따르며, 클래스 또는 리소스를 찾기 위한 요청시 ClassLoader 인스턴스가 검색을 상위 클래스 로더에 위임합니다.

- JVM에 애플리케이션 클래스를 로드 요청이 있다고 가정시 다음과 같은 순서를 가지고 있습니다.
    1. 시스템 클래스 로더는 먼저 해당 클래스 로딩을 부모 확장 클래스 로더에 위임.
    2. 확장 클래스 로더는 요청을 부트스트랩 클래스 로더에 위임.
    3. 부트스트랩 클래스 로더는 $JAVA_HOME/jre/lib 디렉토리에서 클래스를 검색하고 없다면 확장 클래스 로더로 요청을 넘김.
    4. 확장 클래스 로더는 $JAVA_HOME/lib/ext 디렉토리에서 클래스를 검색하고 없다면 시스템 클래스 로더에 요청을 넘김.
    5. 시스템 클래스 로더는 classpath에서 해당 클래스를 검색한다. 만약 클래스가 없다면 `ClassNotFoundException`을 발생시킨다.

- 이렇게 차례로 상위 클래스 로더 위임을 하고 로드가 실패한 경우에만 시스템 클래스 로더가 클래스 자체를 로드하려 합니다.

## 2. Unique Classes
- 위임 모델의 결과로 항상 요청을 상위 클래스 로더로 위임하므로 고유 한 클래스를 보장하기가 쉽습니다.

## 3. Visibility
- 하위 클래스 로더는 상위 클래스 로더가 로드한 클래스를 볼 수 있습니다. 반대로 상위 클래스 로더는 하위 클래스 로더가 로드한 클래스를 알 수 없습니다.
- 예를 들어, 클래스 A가 시스템 클래스 로더에 의해 로드되고 클래스 B가 확장 클래스 로더에 의해 로드되는 경우, 시스템 클래스 로드가 로드한 다른 클래스에서 A 및 B 클래스를 볼 수 있습니다.
- 반대로 확장 클래스 로드가 로드한 다른 클래스에서는 A 클래스를 볼 수 없습니다.

# Reference
- [Class Loaders](https://www.baeldung.com/java-classloaders)
- [자바 클래스 로더](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_%ED%81%B4%EB%9E%98%EC%8A%A4%EB%A1%9C%EB%8D%94)
- [How is the Java Bootstrap Classloader loaded?](https://stackoverflow.com/questions/18214174/how-is-the-java-bootstrap-classloader-loaded)