---
layout: default
title: Proxy Pattern
parent: Design Pattern
grand_parent: Java
---


Table of contents


- TOC
{:toc}


---

# Proxy Pattern

Proxy의 의미는 대리인이다. 대리인이란 누군가를 대신한다는 의미이며 본인이 아니라도 가능한 일을 대리인이 할 수 있다. 하지만 대리인이 하지 못하는 일은 본인이 직접 처리해야 한다. 이렇게 어떤 객체를 사용할 때, 직접 참조하는 것이 아닌 해당 객체를 `대행(Proxy)하는 객체를 통해 접근하는 방식`이다. 해당 객체가 메모리에 없어도 대행 객체를 통해 기본적인 정보를 참조하거나 설정할 수 있으며 리소스가 큰 작업이 필요한 시점에 해당 객체를 생성한다.

## Proxy Pattern Example

- 화면에 문자열을 출력하는 프린터 기능이 있다고 가정하자.

- 프린터는 이름을 가지고 있으며 setter/getter 사용시 실제 프린터 인스턴스를 생성하지 않고 Proxy 인스턴스에서 대신 처리한다.

- 프린터 인스턴스의 생성 시간이 오래 걸린다는 것을 전제로 문자열이 출력되는 시점에 프린터 클래스의 인스턴스를 생성한다.

```java
public interface Printable {
    public abstract void setPrinterName(String name);
    public abstract String getPrinterName();
    public abstract void print(String string)
}
```

- Subject(주체)가 되는 인터페이스이며 Proxy의 역할과 RealSubject의 역할이 동일시된다.

- 주체의 역할로 클라이언트는 Proxy와 RealSubject의 역할을 구분할 필요가 없다.


```java
public Class Printer implements Printable {

    private String name;

    public Printer() {
        heavyJob();
    }

    public void setPrinterName(String name) {
        this.name = name;
    }

    public String getPrinterName() {
        return name;
    }

    public void print(String string) {
        System.out.println(string);
    }
    
    private void heavyJob() {
        for (int i = 0; i < 5; i++) {
            try {
                Thread.sleep(1000);
            } catch(InterruptedException ignore) {
            }
            System.out.print(".");
        }
        System.out.println("완료");
    }
}

```

- RealSubject(실제 주체)가 되는 클래스이며 Proxy가 하지 못하는 일이 발생했을 때 등장한다.

```java
public PrinterProxy implements Printable {
    private String name;
    private Printer printer;

    public PrinterProxy() {};

    @Override
    public synchronized void setPrinterName(String name) {
        if (printer != null) {
            printer.setPrinterName(name);
        }
        this.name = name;
    }

    @Override
    public String getPrinterName() {
        return name;
    }

    @Override
    public void print(String string) {
        realize();
        printer.print(string);
    }

    private synchronized void realize() {
        if (printer == null) {
            printer = new Printer();
        }
    }
}
```

- Proxy(대리인)가 되는 클래스로 클라이언트의 요구를 처리하고 자신이 처리하지 못하면 RealSubject에게 처리를 위임한다.

- getter/setter 작업을 프록시 객체가 대신하고 있어 클라이언트는 RealSubject와 Proxy를 구분할 수 없다.

- Printer 클래스는 realize() 메서드를 통해 인스턴스를 생성하게 되며 자신이 Proxy에 의해 직접 호출되고 있는지 알 수 없다.