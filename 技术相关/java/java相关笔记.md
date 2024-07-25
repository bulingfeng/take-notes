## 重新认识static关键字

static关键字可以修饰`变量，方法，代码块和嵌套类`。

### static修改变量

> static修饰变量只能修改`成员变量`，而不能修改其他类型的变量，比如入参的变量，函数的局部变量等。
>
> 而static修改成员变量的时候，这个变量其实是属于`类`的信息，也就是说存在于方法区当中。

```java
public class Obj {
  public static int objCount = 0;

  public Obj() {
    objCount++;
  }
}

public class Demo10_4 {
  public static void main(String[] args) {
    // 证明了被static修饰的变量属于类，因为通过对象或者类得到的结果是一样的，其实都是类中的信息
    Obj d1 = new Obj();
    Obj d2 = new Obj();
    System.out.println(Obj.objCount); //使用类访问objCount，打印2
    System.out.println(d1.objCount); //使用对象访问objCount，打印2
  }
}
```

### static修饰方法

> static修饰的方法叫做静态方法，静态方法只能调用静态方法，静态方法只能访问静态的成员变量。之所以这么来设计是因为：
>
> 静态变量和静态方法的权限所述关系，即类可以访问静态方法，但是类不能访问某个的对象的信息。

### static代码块

static修饰的代码块是在类加载的时候被执行的，如果一个类中有多个代码块，那么加载的顺序和手写的顺序保持一致。

### static修饰内部类

> 嵌套类也叫做内部类。常用的内部类有3种：普通内部类、静态内部类、匿名内部类。
>
> > 内部类会单独形成一个class文件，命名方式为：外部类$内部类.class.
> >
> > 而匿名内部类的命名方式是：外部类名$[序号].class。其中，[序号]为1、2、3...表示此匿名内部类是外部类的第几个匿名内部类
>
> 普通内部类和静态内部类的区别：
>
> > 1. 静态内部类里面可以可以有静态方法和静态的成员变量（jdk16之后，普通内部类也可以了）。
> > 2. 创建对方方式不同。内部类需要先创建外部类再创建内部类，而静态内部类可以直接创建。

为什么非局部的变量必须是final才能被匿名内部类访问呢？

> 这是因为java的参数传递是值传递，而匿名内部类其实就是把值进行传递，这个时候会造成修改了值的内容，这样会造成一种给程序员的误解，修改了值但是值实际上并没有改变。

```java
//此示例为假设情况，假设匿名内部类可以访问非final局部变量
public interface ICallable {
   void add();
}

public class Demo {
  public void test() {
    int a = 1;
    ICallable callback = new ICallable() {
      @Override
      public void add() {
        a++; //a变成了2
      }
    };
    System.out.println(a); //匿名内部类对a的修改没生效，仍然打印1
  }
}
```

代码解释

```java
public class Singleton {
  private Singleton() {}

  private static class SingletonHolder {
    public static final Singleton instance = new Singleton();
  }

  public static Singleton getInstance() {
    return SingletonHolder.instance;
  }
}
```

> 为何上面的代码能够实现单例模式，并且还是懒加载的模式。

>我们调用Singleton.getInstance()来获取单例对象时，JVM会先将Singleton类加载。紧接着，getInstance()函数访问SingletonHolder类的静态变量，触发JVM加载SingletonHolder类。而加载SingletonHolder类会触发静态变量的初始化操作，也就是执行SingletonHolder类中的唯一一行代码。
>
>因为instance的创建是在SingletonHolder类加载过程中完成的，而类加载过程是线程安全的，所以，instance的创建过程是线程安全的。因为只有在第一次调用getInstance()函数时，instance才会被创建，所以，instance的创建过程满足延迟加载特性。