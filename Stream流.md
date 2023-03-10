# Stream流

结合了Lambda表达式，简化集合、数组的操作

## 获取Stream流

| 获取方式     | 方法名称                                      | 说明                     |
| ------------ | --------------------------------------------- | ------------------------ |
| 单列集合     | default Stream<E>stream()                     | Collection中的默认方法   |
| 双列集合     | public static double ceil(double a)           | 无法直接使用stream流     |
| 数组         | public static <T> Stream<T> stream(T[] array) | Arrays工具类中的静态方法 |
| 一堆零散数据 | public static<T> Stream<T> of(T... values)    | Stream接口中的静态方法   |

### 单列集合

```java
//创建单列集合并添加元素
ArrayList<String> list = new ArrayList<>();
Collections.addAll(list,"a","b","c","d","e");
//获取stream流并调用终结方法打印
list.stream().forEach(s -> System.out.println(s));
```

### 双列集合

```java
//双列集合无法之间使用stream流，可将其转换为单列集合使用
//创建双列集合并添加元素
HashMap<String,Integer> hm = new HashMap<>();
hm.put("aaa",111);
hm.put("bbb",222);
hm.put("ccc",333);
//获取stream流
//方法一：获取值
hm.keySet().stream().forEach(s -> System.out.println(s));
//方法二：获取键值对对象
hm.entrySet().stream().forEach(s -> System.out.println(s));
```

### 数组

```java
//创建数组
int[] arr = {1,2,3,4,5,6,7,8,9,10};
//获取stream流
Arrays.stream(arr).forEach(s -> System.out.println(s));
```

### 一堆零散数据

```java
Stream.of(1,2,3,4,5).forEach(s -> System.out.println(s));
Stream.of("a","b","c","d","e").forEach(s -> System.out.println(s));
```

Stream接口中静态方法of的细节:

- 方法的形参是一个可变参数，可以传递一堆零散数据，也可以传递数组
- 但是数组必须是引用数据类型，如果传递基本数据类型，会把整个数组当做一个元素，放到Stream中，打印出来的结果是数组的地址值



## 常用中间方法

| 方法名称                                         | 说明                                 |
| ------------------------------------------------ | ------------------------------------ |
| Stream<T> filter(Predicate<? super T> predicate) | 过滤                                 |
| Stream<T> limit(long maxSize)                    | 获取前几个元素                       |
| Stream<T> skip(long n)                           | 跳过前几个元素                       |
| Stream<T> distinct()                             | 元素去重，依赖(hashCode和equals方法) |
| static <T> Stream<T> concat(Stream a, Stream b)  | 合并a和b两个流为一个流               |
| Stream<R> map(Function<T,R> mapper)              | 转换流中的数据类型                   |

**注意细节：**

- 中间方法，返回新的Stream流，原来的Stream流只能使用一次，建议使用链式编程
- 修改Stream流中的数据，不会影响原来集合或者数组中的数据



### filter方法示例

```java
//创建单列集合并添加元素
ArrayList<String> list = new ArrayList<>();
Collections.addAll(list,"张无忌","周芷若","张强","张三丰","谢广坤");
//过滤：把张开头的留下
list.stream().filter(new Predicate<String>() {
    @Override
    public boolean test(String s) {
        //返回值为true，表示当前数据留下
        //放回置为false，表示当前数据舍弃
        return s.startsWith("张");
    }
}).forEach(s -> System.out.println(s));
//Lambda表达式简化
list.stream().filter(s -> s.startsWith("张")).forEach(s -> System.out.println(s));
```

### map方法示例

```java
ArrayList<String> list = new ArrayList<>();
Collections.addAll(list,"张无忌-15","周芷若-14","张强-20","张三丰-100");
//需求：获取年龄并进行打印  String->int
//Function中 第一个类型：流中原本的数据类型；第二个类型：要转成之后的类型
//apply的形参s: 依次表示流里面的每一个数据
//返回值: 表示转换之后的数据
//当map方法执行完毕之后，流上的数据就变成了整数，所以在forEach中s表示的就是整数
list.stream().map(new Function<String, Integer>() {
    @Override
    public Integer apply(String s) {
        String[] arr = s.split("-");
        String ageString = arr[1];
        int age = Integer.parseInt(ageString);
        return age;
    }
}).forEach(s-> System.out.println(s));
//Lambda表达式简化
list.stream().map(s->Integer.parseInt(s.split("-")[1])).forEach(s-> System.out.println(s));
```



## 常见终结方法

| 方法名称                      | 说明                       |
| ----------------------------- | -------------------------- |
| void forEach(Consumer action) | 遍历                       |
| long count()                  | 统计流中的数据个数         |
| toArray()                     | 收集流中的数据，放到数组中 |
| collect(Collector collector)  | 收集流中的数据，放到集合中 |

### toArray方法示例

```java
ArrayList<String> list = new ArrayList<>();
Collections.addAll(list,"张无忌","周芷若","张强","张三丰","谢广坤");
//IntFunction的泛型: 具体类型的数组
//apply的形参:流中数据的个数，要跟数组的长度保持一致
//apply的返回值:具体类型的数组
//方法体: 就是创建数组

//toArray方法的参数的作用: 负责创建一个指定类型的数组
//toArray方法的底层，会依次得到流里面的每一个数据，并把数据放到数组当中
//toArray方法的返回值: 是一个装着流里面所有数据的数组
String[] arr1 = list.stream().toArray(new IntFunction<String[]>() {
    @Override
    public String[] apply(int value) {
        return new String[value];
    }
});
//Lambda表达式简化
String[] arr2 = list.stream().toArray(value -> new String[value]);
System.out.println(Arrays.toString(arr2));
```

### collect方法示例

```java
ArrayList<String> list = new ArrayList<>();
Collections.addAll(list, "张无忌-男-15", "周芷若-女-14", "张强-男-20","张三丰-男-100","谢广坤-男-41");
//需求：将所有男性收集起来
//收集List集合当中
List<String> list1 = list.stream()
                .filter(s -> "男".equals(s.split("-")[1]))
                .collect(Collectors.toList());
//收集Set集合当中
Set<String> set1 = list.stream()
                .filter(s -> "男".equals(s.split("-")[1]))
                .collect(Collectors.toSet());

//收集Map集合当中
//需求：将所有男性收集起来
//键：姓名。 值：年龄
/*
*   toMap : 参数一表示键的生成规则
*           参数二表示值的生成规则
* 参数一：
*       Function泛型一：表示流中每一个数据的类型
*               泛型二：表示Map集合中键的数据类型
*
*        方法apply形参：依次表示流里面的每一个数据
*               方法体：生成键的代码
*               返回值：已经生成的键
* 参数二：
*        Function泛型一：表示流中每一个数据的类型
*                泛型二：表示Map集合中值的数据类型
*
*       方法apply形参：依次表示流里面的每一个数据
*               方法体：生成值的代码
*               返回值：已经生成的值
* */
Map<String, Integer> map1 = list.stream()
                .filter(s -> "男".equals(s.split("-")[1]))
                .collect(Collectors.toMap(new Function<String, String>() {
                                              @Override
                                              public String apply(String s) {
                                                  return s.split("-")[0];
                                              }
                                          },
                        new Function<String, Integer>() {
                            @Override
                            public Integer apply(String s) {
                                return Integer.parseInt(s.split("-")[2]);
                            }
                        }));
//Lambda表达式简化
Map<String, Integer> map2 = list.stream()
                .filter(s -> "男".equals(s.split("-")[1]))
                .collect(Collectors.toMap(
                        s -> s.split("-")[0],
                        s -> Integer.parseInt(s.split("-")[2])));
```

