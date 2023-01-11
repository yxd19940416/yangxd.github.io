---
title: Optional类让你告别丑陋判空
top: false
cover: false
toc: true
mathjax: true
summary: Optional类让你告别丑陋判空
date: 2023-01-11 23:00:54
password:
tags:
    - jdk1.8
categories:
    - java 
---
## 认识Optional 

Opitonal类就是Java提供的为了解决大家平时判断对象是否为空用，通常会用 `null!=obj`这样的方式存在的判断，从而令人头疼导致空指针异常，同Optional的存在可以让代码更加简单，可读性跟高，代码写起来更高效

```java
Student student = new Student();
if (null == student){
    return "student为null";
}
return student;
```

```java
Student student = new Student();
return Optional.ofNullable(student).orElse("student为null");
```

测试展示类Student 代码(如果有朋友不明白可以看一下这个)：

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Student {
    private String name;
    private Integer age;
}
```

### Optional对象创建 

首先我们先打开Optional的内部,去一探究竟 先把几个创建Optional对象的方法提取出来

```java
public final class Optional<T> {
   private static final Optional<?> EMPTY = new Optional<>();
   private final T value;
   // 我们可以看到两个构造方格都是private 私有的
   // 说明 我们没办法在外面去new出来Optional对象
   private Optional() {
        this.value = null;
    }
   private Optional(T value) {
        this.value = Objects.requireNonNull(value);
    }
    // 这个静态方法大致 是创建出一个包装值为空的一个对象因为没有任何参数赋值
   public static<T> Optional<T> empty() {
        @SuppressWarnings("unchecked")
        Optional<T> t = (Optional<T>) EMPTY;
        return t;
    }
    // 这个静态方法大致 是创建出一个包装值非空的一个对象 因为做了赋值
   public static <T> Optional<T> of(T value) {
        return new Optional<>(value);
    }
    // 这个静态方法大致是 如果参数value为空，则创建空对象，如果不为空，则创建有参对象
   public static <T> Optional<T> ofNullable(T value) {
        return value == null ? empty() : of(value);
    }
 }
```

再做一个简单的实例展示 与上面对应

```java
// 1、创建一个包装对象值为空的Optional对象
Optional<String> optEmpty = Optional.empty();
// 2、创建包装对象值非空的Optional对象
Optional<String> optOf = Optional.of("optional");
// 3、创建包装对象值允许为空也可以不为空的Optional对象
Optional<String> optOfNullable1 = Optional.ofNullable(null);
Optional<String> optOfNullable2 = Optional.ofNullable("optional");
```

### Optional.get()方法(返回对象的值) 

get()方法是返回一个option的实例值

源码：

```java
public T get() {
    if (value == null) {
        throw new NoSuchElementException("No value present");
    }
    return value;
}
```

也就是如果value不为空则做返回，如果为空则抛出异常 “`No value present`” 简单实例展示

```java
Student student = new Student();
student.setAge(18);
System.out.println(Optional.ofNullable(student).get());
```

![image_f663defc.png](Optional类让你告别丑陋判空/image_f663defc-20230111230028418.png)

### Optional.isPresent()方法(判读是否为空) 

isPresent()方法就是会返回一个boolean类型值，如果对象不为空则为真，如果为空则false

源码:

```java
public boolean isPresent() {
    return value != null;
}
```

简单的实例展示:

```java
Student student = new Student();
student.setAge(18);
if (Optional.ofNullable(student).isPresent()){
    System.out.println("不为空");
}else {
    System.out.println("为空");
}
```

![image_528dbe6b.png](Optional类让你告别丑陋判空/image_528dbe6b-20230111230028718.png)

### Optional.ifPresent()方法(判读是否为空并返回函数) 

这个意思是如果对象非空，则运行函数体

源码:

```java
public void ifPresent(Consumer<? super T> consumer) {
    //如果value不为空，则运行accept方法体
    if (value != null)
        consumer.accept(value);
}
```

看实例：

```java
Student student = new Student();
student.setAge(18);
Optional.ofNullable(student).ifPresent(s -> System.out.println("年龄：" + s.getAge()));
```

如果对象不为空，则会打印这个年龄，因为内部已经做了NPE（非空判断），所以就不用担心空指针异常了

![image_6deaa283.png](Optional类让你告别丑陋判空/image_6deaa283-20230111230028538.png)

### Optional.filter()方法(过滤对象) 

filter()方法大致意思是，接受一个对象，然后对他进行条件过滤，如果条件符合则返回Optional对象本身，如果不符合则返回空Optional

源码：

```java
public Optional<T> filter(Predicate<? super T> predicate) {
    Objects.requireNonNull(predicate);
    //如果为空直接返回this
    if (!isPresent())
        return this;
    else
        //判断返回本身还是空Optional
        return predicate.test(value) ? this : empty();
}
```

简单实例：

```java
Student student = new Student();
student.setAge(18);
Optional.ofNullable(student).filter(s -> s.getAge()>10);
```

### Optional.flatMap()方法(Optional对象进行二次包装) 

map()方法将对应`Optional< Funcation >`函数式接口中的对象，进行二次运算，封装成新的对象然后返回在Optional中

源码:

```java
public<U> Optional<U> flatMap(Function<? super T, Optional<U>> mapper) {
    Objects.requireNonNull(mapper);
    if (!isPresent())
        return empty();
    else {
        return Objects.requireNonNull(mapper.apply(value));
    }
}
```

实例：

```java
Student student = new Student();
student.setAge(18);
Optional<Object> optName = Optional.ofNullable(student).map(s -> Optional.ofNullable(s.getName()).orElse("name为空"));
```

### Optional.orElse()方法(为空返回对象) 

常用方法之一，这个方法意思是如果包装对象为空的话，就执行orElse方法里的value，如果非空，则返回写入对象

源码:

```java
public T orElse(T other) {
    //如果非空，返回value，如果为空，返回other
    return value != null ? value : other;
}
```

实例：

```java
Student student = new Student();
student.setAge(18);
Optional.ofNullable(student).orElse(new Student("小明", 12));
```

### Optional.orElseGet()方法(为空返回Supplier对象) 

这个与orElse很相似，入参不一样，入参为Supplier对象，为空返回传入对象的.get()方法，如果非空则返回当前对象。推荐：[Java面试题][Java]

源码:

```java
public T orElseGet(Supplier<? extends T> other) {
    return value != null ? value : other.get();
}
```

实例：

```java
Optional<Supplier<Student>> sup=Optional.ofNullable(Student::new);
//调用get()方法，此时才会调用对象的构造方法，即获得到真正对象
Optional.ofNullable(student).orElseGet(sup.get());
```

Suppiler是一个接口，是类似Spring的懒加载，声明之后并不会占用内存，只有执行了get()方法之后，才会调用构造方法创建出对象 创建对象的语法的话就是`Supplier supStudent= Student::new;` 需要使用时`supStudent.get()`即可

### Optional.orElseThrow()方法(为空返回异常) 

方法作用的话就是如果为空，就抛出你定义的异常，如果不为空返回当前对象，在实战中所有异常肯定是要处理好的，为了代码的可读性

源码：

```java
public <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) throws X {
    if (value != null) {
        return value;
    } else {
        throw exceptionSupplier.get();
    }
}
```

实例：

```java
//简单的一个查询
Member member = memberService.selectByPhone(request.getPhone());
Optional.ofNullable(member).orElseThrow(() -> new ServiceException("没有查询的相关数据"));
```

## 相似方法进行对比分析 

可能看到这，没用用过的话会觉得`orElse()`和`orElseGet()`还有`orElseThrow()`很相似，map()和flatMap()好相似,不用着急，都是从这一步过来的，我再给大家总结一下不同方法的异同点 `orElse()`和`orElseGet()`和`orElseThrow()`的异同点

> 方法效果类似，如果对象不为空，则返回对象，如果为空，则返回方法体中的对应参数，所以可以看出这三个方法体中参数是不一样的 orElse（T 对象） orElseGet（`Supplier < T >`对象） orElseThrow（异常）

map()和orElseGet的异同点

> 方法效果类似，对方法参数进行二次包装，并返回,入参不同 map（function函数） `flatmap（Optional< function >`函数）

具体要怎么用，要根据业务场景以及代码规范来定义，下面可以简单看一下我在实战中怎用使用神奇的Optional。推荐：[Java面试题][Java]

## 实战场景 

场景1：在service层中 查询一个对象，返回之后判断是否为空并做处理

```java
//查询一个对象
Member member = memberService.selectByIdNo(request.getCertificateNo());
//使用ofNullable加orElseThrow做判断和操作
Optional.ofNullable(member).orElseThrow(() -> new ServiceException("没有查询的相关数据"));
```

场景2：我们可以在dao接口层中定义返回值时就加上Optional

```java
public interface LocationRepository extends JpaRepository<Location, String> {
Optional<Location> findLocationById(String id);
}
```

然在是Service中

```java
public TerminalVO findById(String id) {
    //这个方法在dao层也是用了Optional包装了
    Optional<Terminal> terminalOptional = terminalRepository.findById(id);
    //直接使用isPresent()判断是否为空
    if (terminalOptional.isPresent()) {
    //使用get()方法获取对象值
        Terminal terminal = terminalOptional.get();
        //在实战中，我们已经免去了用set去赋值的繁琐，直接用BeanCopy去赋值
        TerminalVO terminalVO = BeanCopyUtils.copyBean(terminal, TerminalVO.class);
        //调用dao层方法返回包装后的对象
        Optional<Location> location = locationRepository.findLocationById(terminal.getLocationId());
        if (location.isPresent()) {
            terminalVO.setFullName(location.get().getFullName());
        }
        return terminalVO;
    }
    //不要忘记抛出异常
    throw new ServiceException("该终端不存在");
}
```

实战场景还有很多，包括return时可以判断是否返回当前值还是跳转到另一个方法体中，其它的还有很多。

## Optional使用注意事项 

Optional真么好用，真的可以完全替代if判断吗？我想这肯定是大家使用完之后Optional之后可能会产生的想法，答案是否定的 举一个最简单的栗子：

例子：如果我只想判断对象的某一个变量是否为空并且做出判断呢？

```java
Person person=new Person();
person.setName("");
persion.setAge(2);
//普通判断
if(StringUtils.isNotBlank(person.getName())){
   //名称不为空执行代码块
}
//使用Optional做判断
Optional.ofNullable(person).map(p -> p.getName()).orElse("name为空");
```

我觉得这个例子就能很好的说明这个问题，只是一个很简单判断，如果用了Optional我们还需要考虑包装值，考虑代码书写，考虑方法调用，虽然只有一行，但是可读性并不好，如果别的程序员去读，我觉得肯定没有if看的明显。

## Jdk 9对Optional优化 

首先增加了三个方法: `or()`、`ifPresentOrElse()` 和 `stream()`。or() 与orElse等方法相似，如果对象不为空返回对象，如果为空则返回or()方法中预设的值。

`ifPresentOrElse()` 方法有两个参数：一个 Consumer 和一个 Runnable。如果对象不为空，会执行 Consumer 的动作，否则运行 Runnable。相比`ifPresent()`多了OrElse判断。`stream()`将Optional转换成stream，如果有值就返回包含值的stream，如果没值，就返回空的stream。