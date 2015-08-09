---
title: Scala Constructor vs. Java Constructor
layout: post
guid: urn:uuid:04aadc1c-8153-42ec-8c45-201504201522
tags:
  - scala
---
![](/images/scala-vs-java.png)

#### 1. Constructor with parameters
**Java Code**  

```
public class Foo() {  
   public Bar bar;  
   public Foo(Bar bar) {  
       this.bar = bar;  
   }  
}  
```

**Scala Code**

```
class Foo(val bar:Bar)  
```

#### 2. Constructor with private attribute
**Java Code**  

```
public class Foo() {  
   private final Bar bar;  
   public Foo(Bar bar) {  
       this.bar = bar;  
   }  
}  
```

**Scala Code**

```
class Foo(private val bar:Bar)  
```

#### 3. Call "Super" constructor
**Java Code**  

```
public class Foo() extends SuperFoo {  
   public Foo(Bar bar) {   
      super(bar);  
   }  
}  
```

**Scala Code**

```
class Foo(bar:Bar) extends SuperFoo(bar)  

``` 

#### 3. Multiple constructors
**Java Code**  

```
public class Foo {  
    public Bar bar;  
    public Foo() {   
       this(new Bar());   
    }  
    public Foo(Bar bar) {  
       this. bar = bar;   
    }  
}  
```

**Scala Code**

```
class Foo(val bar:Bar){  
    def this() = this(new Bar)  
}  
``` 

#### 5. Methods of "getter" and "setter"
**Java Code**  

```java
public class Foo() {  
   private Bar bar;  
   public Foo(Bar bar) {  
       this.bar = bar;  
   } 
   public Bar getBar() {   
      return bar;   
   }  
   public void setBar(Bar bar) {   
      this.bar = bar;  
   }  
}  
```

**1. Scala Code**

```scala
import scala.reflect._  
class Foo(@BeanProperty var bar:Bar)  
```

**2. Scala Code**

```
import scala.reflect._  
class Foo(aBar:Bar) {  
    @BeanProperty  
    private var bar = aBar  
}  
``` 