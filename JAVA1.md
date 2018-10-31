Java常用语：
![](http://p27x0f47q.bkt.clouddn.com/20181030223319.png)
* package：名字必须是unique的，用来在top level来manage data structure，命名要有意义，例如

```java
package tutorial.java.course.lesson1
```
* import：代码复用，例如

```java
import tutorial.java.course.lesson1.car
import tutorial.java.course.lesson1.*
```
* class：coding的units，一般大写命名用名词，且尽可能用小的概念，例如

```java
class Car{
}
```
* method：用来实现某种功能，也叫function，函数变量应该尽量少，一般不要超过5个，函数长度应该尽量短，一般不要超过20行，例如

```java
double(2) = 4
add("ab", "c") = "abc"

drive(speed){
}
```

> package > class > method > submethod

* variable：函数中的input or output values

```java
instance variable
static variable
local variable
```

* public：modifier，还有很多其他modifiers，例如

```java
public class Car{
    public drive(Speed){
    }
}
```

* void：每个函数都要定义return value，如果不返回，则写void

```java
public class Car{
    public void drive(Speed){
    }
}
```

* @Test：@符号是annotation
* CamelCase：java用驼峰法命名
* dot：execute/call
```java
Car.drive(Speed)
```
* colon：标志statement的结束

```java
@Test
public void shouldDrive(){
    car.drive(100);
}
```
* object：java程序运行时会产生很多objects，java是OOP的
* constructor：创建object的方法，例如

```java
new Car(10, 120);
```
* variable declarition：使用variables前要先声明

```java
Car myPorsche;
```
* object allocation：将创建好的object和variable进行关联分配，可以用一行语句实现，例如

```java
Car myPorche = new Car(1, 320);
```
