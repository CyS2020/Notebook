#### ArrayList源码
- Object.clone()方法为浅拷贝
- ArrayList中的modCount记录数组修改的次数，用于并发修改检查(ConcurrentModificationException)
- ArrayList中的SubList返回的是视图，增删会在源数组上进行修改
- ArrayList中的spliterator是在stream中进行并发遍历时用到的
- ArrayList中删除时除了使用iterator(并发时需加锁)还可以使用removeIf(Predicate<? super E> filter)
- ArrayList中普通for循环、增强for循环中执行add和remove操作会引发什么问题？
- ArrayList中的排序算法是调用的是Arrays.sort()而LinkedList中没有排序方法

#### Code归约



#### Objects中常用的方法
- Objects.equals()
- Objects.requireNonNull()


