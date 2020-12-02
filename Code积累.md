#### ArrayList源码
- 1. Object.clone()方法为浅拷贝
- 2. ArrayList中的modCount记录数组修改的次数，用于并发修改检查(ConcurrentModificationException)
- 3. ArrayList中的SubList返回的是视图，增删会在源数组上进行修改
- 4. ArrayList中的spliterator是在stream中进行并发遍历时用到的
- 5. ArrayList中删除时除了使用迭代器还可以使用removeIf(Predicate<? super E> filter)
- 6. ArrayList中的排序算法是调用的是Arrays.sort()而LinkedList中没有排序方法

#### Code归约



#### Objects中常用的方法
- Objects.equals()
- Objects.requireNonNull()


