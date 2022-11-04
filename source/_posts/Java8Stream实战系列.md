---
title: Java8Stream实战系列
top: false
cover: false
toc: true
mathjax: true
date: 2022-11-04 10:02:54
password:
summary:
tags:
    - jdk1.8
    - stream
    - lambda操作
categories:
    - java
---

就目前而言，Java8之类的仍然是主流，即使用比较广泛，其次就是JDK11之类的，毕竟对于企业研发而言，求稳第一。  


## 一、Java8有哪些新特性？ 

 *  1.Lambda表达式；
 *  2.新的日期API；
 *  3.引入Optional；
 *  4.新增Base64加解密API；
 *  5.接口的默认方法和静态方法；
 *  6.新增方法引用格式；
 *  7.新增Stream类；
 *  8.注解相关的改变；
 *  9.支持并行数组；
 *  10.对并发类的扩展。

这里不一一讲解，只讲Stream相关系列的实战和应用。

## 二、我在实践应用Stream相关的是如何提高开发效率的？ 

以下以实用为主(实际应用比较多的)，进行分类划分。在实际中这些通常以组合的形式进行使用，不局限于单单使用某一个。这样做的目的就是为了提高研发效率以及更快更好的解决问题。不然，使用Stream这些新特性的意义也就不存在了。


### 1.分组 


```java
Map<String, List<T>> groupMapData = originData.stream().collect(Collectors.groupingBy(T::getType));
```

### 2.条件筛选 


```java
List<T> filterList = originData.stream().filter(a -> a.getName().equals("YC")).collect(Collectors.toList());
```

### 3.排序 

```java
List<T> filterOBDHourData = originData.stream()                       .sorted(Comparator.comparing(T::getId).reversed())                       .collect(Collectors.toList());
```

  


### 4.去重 

```java
List<T> dataVOS = originData.stream().collect(        Collectors.collectingAndThen(            Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(T::getName))), ArrayList::new)
```

### 5.数组转List 
  

```java
List<String> codes = new ArrayList<>();String[] arr = target.trim().split(",");if (arr.length > 0) {  codes = Stream.of(arr).collect(Collectors.toList());}
```

### 6.数据合并(含去重以及不去重) 


```java
List<String> result = Stream.of(Lists.newArrayList("A", "B", "C"), Lists.newArrayList("A", "B")).flatMap(Collection::stream).distinct().collect(Collectors.toList());
List<String> result = Stream.of(Lists.newArrayList("A", "B", "C"), Lists.newArrayList("A", "B")).flatMap(Collection::stream).collect(Collectors.toList());
```

  


### 7.分页 


```java
List<T> dataList = originData.stream().skip((dto.getPageNum() - 1) * dto.getPageSize()).limit(dto.getPageSize()).collect(Collectors.toList());
```
  
### 8.统计 


```java
//求最大值BigDecimal max = dataList.stream().map(T::getPrice).max((x1, x2) -> x1.compareTo(x2)).get();
//求最小值BigDecimal min = dataList.stream().map(T::getPrice).min((x1, x2) -> x1.compareTo(x2)).get();
//求和 空指针异常排除BigDecimal sum = dataList.stream().map(vo -> ObjectUtils.isEmpty(vo.getPrice()) ? new BigDecimal(0) : vo.getPrice()).reduce(BigDecimal.ZERO, BigDecimal::add);
//求平均值BigDecimal average = dataList.stream().map(vo -> ObjectUtils.isEmpty(vo.getPrice()) ? new BigDecimal(0) : vo.getPrice()).reduce(BigDecimal.ZERO, BigDecimal::add).divide(BigDecimal.valueOf(dataList.size()), 2, BigDecimal.ROUND_HALF_UP);
```

### 9.拼接 

```java
String nameJoin = dataList.stream().map(T::getName).collect(Collectors.joining(","));
```

### 10.归集 


```java
Map<String, T> mapNameObject = dataList.stream().collect(Collectors.toMap(T::getName, item -> item));
```

### 11.映射Map 


```java
List<String> strList = Arrays.asList("1,2,3", "4,5,6");List<String> newStrList = strList.stream().flatMap(item -> {  return Arrays.stream(item.split(","));}).collect(Collectors.toList());
```

### 12.搜索 


```java
originData.stream().filter(data -> Boolean.FALSE ? data.getName().equals(dto.getName()) :data.getName().contains(dto    .getName())).collect(Collectors.toList());
```
