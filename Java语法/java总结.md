### 回调函数
- 回调函数的定义，将来要作为参数传入
- 回调函数的使用，先使用函数变量代指回调函数，函数变量能传入参数并返回结果
- 回调函数的传入，传入确定的回调函数
```java
public class Number {
    //回调函数1，生成偶数规则1
    public int getEvenWith2(int x) {
        return x * 2;
    }
    //回调函数2，生成偶数规则2
    public int getEvenWith4(int x) {
        return x * 4;
    }
    //回调函数代指，待传入特定回调函数，先使用变量继续
    public int getOdd(int x, Function<Integer, Integer> getEven){
        int even = getEven.apply(x);
        return even + 1;
    }
    //更上一层调用方进行指定回调函数
    public static void main(String[] args) {
        Number number = new Number();
        int oddWith2 = number.getOdd(1, number::getEvenWith2);
        int oddWith4 = number.getOdd(1, number::getEvenWith4);
    }
}
```

### 日志的声明
- protected final Logger log = LoggerFactory.getLogger(this.class);
- private static final Logger log = LoggerFactory.getLogger(Xxx.class);

### 单例模式
- 单例模式特征：两静态、一私有，懒汉只声明，静态new，静态get，构造函数private
- 单例模式懒汉的双重检查，synchronized前后进行判空处理、并且添加volatile禁止指令重排序
