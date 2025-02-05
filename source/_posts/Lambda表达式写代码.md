---
title: Lambda表达式写代码
top: false
cover: false
toc: true
mathjax: true
summary: Lambda表达式写代码
date: 2022-12-01 10:42:47
password:
tags:
    - lambda
    - java stream
categories:
    - java
---



日常开发中，我们很多时候需要用到`Java 8`的`Lambda`表达式，它允许把函数作为一个方法的参数，让我们的代码更优雅、更简洁。所以整理了一波工作中，我常用的，有哪些`Lambda`表达式。看完一定会有帮助的。

1. list 转 map

工作中，我们经常遇到`list`转`map`的案例。`Collectors.toMap`就可以把一个`list`数组转成一个`Map`。代码如下：

```java
public class TestLambda {

    public static void main(String[] args) {

        List<UserInfo> userInfoList = new ArrayList<>();
        userInfoList.add(new UserInfo(1L, "芋道源码", 18));
        userInfoList.add(new UserInfo(2L, "程序员芋艿", 27));
        userInfoList.add(new UserInfo(2L, "打代码的芋艿", 26));

        /**
         *  list 转 map
         *  使用Collectors.toMap的时候，如果有可以重复会报错，所以需要加(k1, k2) -> k1
         *  (k1, k2) -> k1 表示，如果有重复的key,则保留第一个，舍弃第二个
         */
        Map<Long, UserInfo> userInfoMap = userInfoList.stream().collect(Collectors.toMap(UserInfo::getUserId, userInfo -> userInfo, (k1, k2) -> k1));
        userInfoMap.values().forEach(a->System.out.println(a.getUserName()));
    }
}

//运行结果
芋道源码
程序员芋艿
```

类似的，还有`Collectors.toList()`、`Collectors.toSet()`，表示把对应的流转化为`list`或者`Set`。

2. filter（）过滤

从数组集合中，过滤掉不符合条件的元素，留下符合条件的元素。

```java
List<UserInfo> userInfoList = new ArrayList<>();
userInfoList.add(new UserInfo(1L, "芋道源码", 18));
userInfoList.add(new UserInfo(2L, "程序员芋艿", 27));
userInfoList.add(new UserInfo(3L, "打代码的芋艿", 26));
        
/**
 * filter 过滤，留下超过18岁的用户
 */
List<UserInfo> userInfoResultList = userInfoList.stream().filter(user -> user.getAge() > 18).collect(Collectors.toList());
userInfoResultList.forEach(a -> System.out.println(a.getUserName()));
        
//运行结果
程序员芋艿
打代码的芋艿
```

3. foreach 遍历

foreach 遍历list，遍历map，真的很丝滑。

```java
/**
 * forEach 遍历集合List列表
 */
List<String> userNameList = Arrays.asList("芋道源码", "程序员芋艿", "艿艿");
userNameList.forEach(System.out::println);
 
HashMap<String, String> hashMap = new HashMap<>();
hashMap.put("公众号", "芋道源码");
hashMap.put("职业", "程序员芋艿");
hashMap.put("昵称", "艿艿");
/**
 *  forEach 遍历集合Map
 */
hashMap.forEach((k, v) -> System.out.println(k + ":\t" + v));

//运行结果
芋道源码
程序员芋艿
打代码的芋艿
职业: 程序员芋艿
公众号: 芋道源码
昵称: 艿艿
```

4. groupingBy 分组

提到分组，相信大家都会想起`SQL`的`group by`。我们经常需要一个List做分组操作。比如，按城市分组用户。在Java8之前，是这么实现的：

```java
List<UserInfo> originUserInfoList = new ArrayList<>();
originUserInfoList.add(new UserInfo(1L, "芋道源码", 18,"深圳"));

originUserInfoList.add(new UserInfo(3L, "打代码的芋艿", 26,"湛江"));
originUserInfoList.add(new UserInfo(2L, "程序员芋艿", 27,"深圳"));
Map<String, List<UserInfo>> result = new HashMap<>();
for (UserInfo userInfo : originUserInfoList) {
  String city = userInfo.getCity();
  List<UserInfo> userInfos = result.get(city);
  if (userInfos == null) {
      userInfos = new ArrayList<>();
      result.put(city, userInfos);
    }
  userInfos.add(userInfo);
}
```

而使用Java8的`groupingBy`分组器，清爽无比:

```java
Map<String, List<UserInfo>> result = originUserInfoList.stream()
.collect(Collectors.groupingBy(UserInfo::getCity));
```

5. sorted+Comparator 排序

工作中，排序的需求比较多，使用`sorted+Comparator`排序，真的很香。

```java
List<UserInfo> userInfoList = new ArrayList<>();
userInfoList.add(new UserInfo(1L, "芋道源码", 18));
userInfoList.add(new UserInfo(3L, "打代码的芋艿", 26));
userInfoList.add(new UserInfo(2L, "程序员芋艿", 27));

/**
 *  sorted + Comparator.comparing 排序列表，
 */
userInfoList = userInfoList.stream().sorted(Comparator.comparing(UserInfo::getAge)).collect(Collectors.toList());
userInfoList.forEach(a -> System.out.println(a.toString()));

System.out.println("开始降序排序");

/**
 * 如果想降序排序，则可以使用加reversed()
 */
userInfoList = userInfoList.stream().sorted(Comparator.comparing(UserInfo::getAge).reversed()).collect(Collectors.toList());
userInfoList.forEach(a -> System.out.println(a.toString()));

//运行结果
UserInfo{userId=1, userName='芋道源码', age=18}
UserInfo{userId=3, userName='打代码的芋艿', age=26}
UserInfo{userId=2, userName='程序员芋艿', age=27}
开始降序排序
UserInfo{userId=2, userName='程序员芋艿', age=27}
UserInfo{userId=3, userName='打代码的芋艿', age=26}
UserInfo{userId=1, userName='芋道源码', age=18}
```

6.distinct 去重

`distinct`可以去除重复的元素:

```java
List<String> list = Arrays.asList("A", "B", "F", "A", "C");
List<String> temp = list.stream().distinct().collect(Collectors.toList());
temp.forEach(System.out::println);
```

7. findFirst 返回第一个

`findFirst` 很多业务场景，我们只需要返回集合的第一个元素即可：

```java
List<String> list = Arrays.asList("A", "B", "F", "A", "C");
list.stream().findFirst().ifPresent(System.out::println);
```

8. anyMatch 是否至少匹配一个元素

`anyMatch` 检查流是否包含至少一个满足给定谓词的元素。

```java
Stream<String> stream = Stream.of("A", "B", "C", "D");
boolean match = stream.anyMatch(s -> s.contains("C"));
System.out.println(match);
//输出
true
```

9. allMatch 匹配所有元素

`allMatch` 检查流是否所有都满足给定谓词的元素。

```java
Stream<String> stream = Stream.of("A", "B", "C", "D");
boolean match = stream.allMatch(s -> s.contains("C"));
System.out.println(match);
//输出
false
```

10. map 转换

`map`方法可以帮我们做元素转换，比如一个元素所有字母转化为大写，又或者把获取一个元素对象的某个属性，`demo`如下：

```java
List<String> list = Arrays.asList("jay", "tianluo");
//转化为大写
List<String> upperCaselist = list.stream().map(String::toUpperCase).collect(Collectors.toList());
upperCaselist.forEach(System.out::println);
```

11. Reduce

Reduce可以合并流的元素，并生成一个值

```java
int sum = Stream.of(1, 2, 3, 4).reduce(0, (a, b) -> a + b);
System.out.println(sum);
```

12. peek 打印个日志

`peek()`方法是一个中间`Stream`操作，有时候我们可以使用`peek`来打印日志。

```java
List<String> result = Stream.of("程序员芋艿", "芋道源码", "打代码的芋艿")
            .filter(a -> a.contains("芋艿"))
            .peek(a -> System.out.println("关注公众号:" + a)).collect(Collectors.toList());
System.out.println(result);
//运行结果
关注公众号:程序员芋艿
关注公众号:芋道源码
[程序员芋艿, 芋道源码]
```

13. Max，Min 最大最小

使用lambda流求最大，最小值，非常方便。

```java
List<UserInfo> userInfoList = new ArrayList<>();
userInfoList.add(new UserInfo(1L, "芋道源码", 18));
userInfoList.add(new UserInfo(3L, "打代码的芋艿", 26));
userInfoList.add(new UserInfo(2L, "程序员芋艿", 27));

Optional<UserInfo> maxAgeUserInfoOpt = userInfoList.stream().max(Comparator.comparing(UserInfo::getAge));
maxAgeUserInfoOpt.ifPresent(userInfo -> System.out.println("max age user:" + userInfo));

Optional<UserInfo> minAgeUserInfoOpt = userInfoList.stream().min(Comparator.comparing(UserInfo::getAge));
minAgeUserInfoOpt.ifPresent(userInfo -> System.out.println("min age user:" + userInfo));

//运行结果
max age user:UserInfo{userId=2, userName='程序员芋艿', age=27}
min age user:UserInfo{userId=1, userName='芋道源码', age=18}
```

14. count 统计

一般`count()`表示获取流数据元素总数。

```java
List<UserInfo> userInfoList = new ArrayList<>();
userInfoList.add(new UserInfo(1L, "芋道源码", 18));
userInfoList.add(new UserInfo(3L, "打代码的芋艿", 26));
userInfoList.add(new UserInfo(2L, "程序员芋艿", 27));

long count = userInfoList.stream().filter(user -> user.getAge() > 18).count();
System.out.println("大于18岁的用户:" + count);
//输出
大于18岁的用户:2
```

15. 常用函数式接口

其实lambda离不开函数式接口，我们来看下JDK8常用的几个函数式接口：

 *  `Function<T, R>`（转换型）: 接受一个输入参数，返回一个结果
 *  `Consumer<T>` （消费型）: 接收一个输入参数，并且无返回操作
 *  `Predicate<T>` （判断型）: 接收一个输入参数，并且返回布尔值结果
 *  `Supplier<T>` （供给型）: 无参数，返回结果

`Function<T, R>` 是一个功能转换型的接口，可以把将一种类型的数据转化为另外一种类型的数据

```java
    private void testFunction() {
        //获取每个字符串的长度，并且返回
        Function<String, Integer> function = String::length;
        Stream<String> stream = Stream.of("程序员芋艿", "芋道源码", "打代码的芋艿");
        Stream<Integer> resultStream = stream.map(function);
        resultStream.forEach(System.out::println);
    }
```

`Consumer<T>`是一个消费性接口，通过传入参数，并且无返回的操作

```java
   private void testComsumer() {
        //获取每个字符串的长度，并且返回
        Consumer<String> comsumer = System.out::println;
        Stream<String> stream = Stream.of("程序员芋艿", "芋道源码", "打代码的芋艿");
        stream.forEach(comsumer);
    }
```

`Predicate<T>`是一个判断型接口,并且返回布尔值结果.

```java
    private void testPredicate() {
        //获取每个字符串的长度，并且返回
        Predicate<Integer> predicate = a -> a > 18;
        UserInfo userInfo = new UserInfo(2L, "程序员芋艿", 27);
        System.out.println(predicate.test(userInfo.getAge()));
    }
```

`Supplier<T>`是一个供给型接口,无参数，有返回结果。

```java
    private void testSupplier() {
        Supplier<Integer> supplier = () -> Integer.valueOf("666");
        System.out.println(supplier.get());
    }
```

这几个函数在日常开发中，也是可以灵活应用的，比如我们DAO操作完数据库，是会有个result的整型结果返回。我们就可以用`Supplier<T>`来统一判断是否操作成功。如下：

```java
    private void saveDb(Supplier<Integer> supplier) {
        if (supplier.get() > 0) {
        System.out.println("插入数据库成功");
        }else{
        System.out.println("插入数据库失败");
        }
        }

@Test
public void add() throws Exception {
        Course course=new Course();
        course.setCname("java");
        course.setUserId(100L);
        course.setCstatus("Normal");
        saveDb(() -> courseMapper.insert(course));
        }
```