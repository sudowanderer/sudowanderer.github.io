---
title: Java8 Stream List转Map Null值报错解决方法
categories: Java
date: 2020-02-20 14:12:43
tags: Java8
---
## 问题场景

user LIst转成id->email的map，若email存在Null的话，则会报NPE。user定义如下

````java
@Setter
@Getter
@ToString
class User {
     private Integer id;

     private String email;
}
````
 <!-- more -->

转换代码如下

````java
Map<Integer, String> idMap = users.stream().collect(Collectors.toMap(User::getId, User::getEmail));
````

## 报错详情

```java
Exception in thread "main" java.lang.NullPointerException
	at java.util.HashMap.merge(HashMap.java:1225)
	at java.util.stream.Collectors.lambda$toMap$58(Collectors.java:1320)
	at java.util.stream.ReduceOps$3ReducingSink.accept(ReduceOps.java:169)
	at java.util.ArrayList$ArrayListSpliterator.forEachRemaining(ArrayList.java:1382)
	at java.util.stream.AbstractPipeline.copyInto(AbstractPipeline.java:481)
	at java.util.stream.AbstractPipeline.wrapAndCopyInto(AbstractPipeline.java:471)
	at java.util.stream.ReduceOps$ReduceOp.evaluateSequential(ReduceOps.java:708)
	at java.util.stream.AbstractPipeline.evaluate(AbstractPipeline.java:234)
	at java.util.stream.ReferencePipeline.collect(ReferencePipeline.java:499)
	at com.thunisoft.sfxz.les.ztyrygl.dataimport.controller.Controller.main(Controller.java:96)
```

## 原因

查看源码可知，merge方法中如果value是null就会抛NPE，下面展示部分源码

````java
@Override
public V merge(K key, V value,
               BiFunction<? super V, ? super V, ? extends V> remappingFunction) {
    if (value == null)
        throw new NullPointerException();
}
````

## 解决方案

放弃使用Collectors，改用手动传参

````java
Map<Integer, String> idMap =  users.stream().collect(HashMap::new, (hashMap, user1) -> hashMap.put(user1.getId(), user1.getEmail()), HashMap::putAll);
````

测试一下，现在Map就可以有Null值了。打印map结果如下

````java
[User(id=1, email=admin@qq.com), User(id=2, email=null)]
````

## 后续

代码中经常需要list转map的话可以封装一下，方便使用，下面贴上本人的封装方法

```````java
public static <T, K, U> Map<K, U> collectionToMap(Collection<T> data, Function<? super T, ? extends K> keyMapper,Function<? super T, ? extends U> valueMapper) {
    return data.stream().collect(HashMap::new, (kuHashMap, t) -> kuHashMap.put(keyMapper.apply(t), valueMapper.apply(t)), HashMap::putAll);
}
```````

封装后的调用

````java
StreamUtils.collectionToMap(users, User::getId, User::getEmail);
````
