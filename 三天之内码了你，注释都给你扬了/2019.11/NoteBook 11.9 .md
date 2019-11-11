## Collection(集合)

>  集合就像一个容器 , 将多个项目集合在一个单元中

### 组成

#### 接口层

- 接口提供抽象数据类型(Object)来表示接口
- `java.util.Collection` 是集合的根接口 , 包含一些每个集合类的都要实现重要方法,`size()`, `iterator()`, `add()`, `remove()`, `clear()`
-  其他重要的接口有 List、Set、Queue、Map，Map 是唯一个不继承Collection的接口，但它也属于集合框架的一部分。

#### 实现类

- 重要的实现类有 ArrayList、LinkedList、HashMap、TreeMap、TreeSet
- 线程安全的实现类有CopyOnWriteArrayList、ConcurrentHashMap、CopyOnWriteArrayList，主要在`util.current`b包中

#### 算法

常见的有搜索，排序，洗牌


### Collection Interfaces （集合接口）

- 集合接口是Java集合框架的基础，核心的集合接口层都是通用的
- 当声明集合，语法同样使用于泛型
- 为了使核心集合接口的数量保持可管理性 ， Java平台没有为 每种集合类型的每个变体提供单独的接口

### Collection Interface

> 集合层次结构的根，集合表示一组称其为元素的对象 ， Java平台 不提供此接口的任何直接实现

通用方法：
- size( ) - 大小
- isEmpty( ) - 判空
- contains( ) - 包含
- add( ) - 添加
- remove( ) - 移出
- iterator - 迭代器

### Iterator Interface

> 在Java集合框架中，Iterator用于取代 Enumeration（枚举）

- 从接口中获取实例
- 迭代器允许调用者在迭代过程中修改集合

### Set Interface

> 无序不重复的集合

- HashSet
- TreeSet
-  LinkedHashSet

### List Interface

> 有序但重复的集合，动态长度的数组

-  ArrayList
-  LinkedList

有用的算法：
- sort - 排序
- shuffle - 随机
- reverse - 反转
- binarySearch - 二房查找

