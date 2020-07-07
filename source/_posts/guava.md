---
title: Guava包
date: 2019/9/24
---
## Lists
```java
List<String> list = Lists.newArrayListWithExpectedSize(int);
``` 

`优点`:设置了list的初始化长度，在list增加的时候可以节省一部分扩容（开辟新空间，copy数据）引起的性能

#### Multimap
multimap对应于Map<String, List<v>>

```java
MultiMap<String, String> multimap = ArrayListMultimap.create();

multimap.put("mike", "man");
multimap.put("mike", "yes");
multimap.put("lucy", "women");

System.out.println(multimap.get("mike")); //输出colletion
```

#### ImmutableList
```java
ImmutableList<String> list = ImmutableList.of("a", "b", "c")
```

- 在多线程操作下是线程安全的
- 中途不可改变


#### Spilt & Join
```java
String str = "1-2-3-4-5-6";
List<String> list = Splitter.on("-").splitToList(str);
// list 为[1,2,3,4,5,6]
```
还可以使用omitEmptyStrings().trimResults()去除空串与空格,guava还支持多个字符切割

#### CacheBuilder
创建一个cache
```java
Cache<String,String> cache = CacheBuilder.newBuilder()
            .expireAfterWrite(3,TimeUnit.SECONDS).build();
```
builder常用设置的参数：
- 弱引用: .softValues(.weakValues) 一旦没有其他强引用指向key和value时，key和value对象就会被垃圾回收器回收
- 设置过期时间：expireAfterWrite，expireAfterAccess
- 设置最大存储： maximumSize
- LoadingCache: 在build中传入cacheloader方法，在未命中缓存的时候回去外存中加载数据，在重写的load()中返回的值即为key对应的value值加载到cache中
```java
public class StudyGuavaCache {
    public static void main(String[] args) throws ExecutionException {
        CacheLoader<String, String> loader = new CacheLoader<String, String> () {
            public String load(String key) throws Exception {
                Thread.sleep(1000); //休眠1s，模拟加载数据
                System.out.println(key + " is loaded from a cacheLoader!");
                return key + "'s value";
            }
        };

        LoadingCache<String,String> loadingCache = CacheBuilder.newBuilder()
                .maximumSize(3)
                .build(loader);//在构建时指定自动加载器

        loadingCache.get("key1");
        loadingCache.get("key2");
        loadingCache.get("key3");
    }
}
```
