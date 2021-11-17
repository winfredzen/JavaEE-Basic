# JavaEE-Basic

非常好的开源文档总结：

+ [Snailclimb/JavaGuide](https://github.com/Snailclimb/JavaGuide)

不错的java教程

+ [廖雪峰的官方网站-Java教程](https://www.liaoxuefeng.com/wiki/1252599548343744)

java内存模型 synchronized volatile 线程之间的通信和同步



## Mac Java

Mac电脑上有关Java的一些操作、配置

参考：

+ [How to Set $JAVA_HOME environment variable on macOS](https://mkyong.com/java/how-to-set-java_home-environment-variable-on-mac-os-x/)

通过`which java`或者`whereis java -b`，会给出Java的安装路径，在当前的Mac下，一般都是

```java
/usr/bin/java
```

1.确定终端使用的是`Bash`还是`zsh`

```shell
echo $SHELL
```



2.在Mac OS X 10.5及以后的版本，使用`/usr/libexec/java_home`返回默认JDK的位置

![001](https://github.com/winfredzen/JavaEE-Basic/blob/master/images/001.png)

`/usr/libexec/java_home -V`找到所有的安装的JDKs

![002](https://github.com/winfredzen/JavaEE-Basic/blob/master/images/002.png)





# 问题集与文章

## 语言特性

+ [java提高篇(八)----详解内部类](https://www.cnblogs.com/chenssy/p/3388487.html)
+ [Java this的一两点使用](https://www.cnblogs.com/qifengshi/p/5425238.html)
+ [深入理解Java类型信息(Class对象)与反射机制](https://blog.csdn.net/javazejian/article/details/70768369)
+ [Java中泛型Class<T>、T与Class<?>、 Object类和Class类、 object.getClass()和Object.class](https://www.cnblogs.com/zhoading/p/11300876.html)

### 引用

+ [转Java 的强引用、弱引用、软引用、虚引用](https://www.cnblogs.com/gudi/p/6403953.html)
+ [理解Java中的弱引用（Weak Reference）](https://www.cnblogs.com/absfree/p/5555687.html)



## 设计模式

+ [Java Builder 模式,你搞懂了么?](https://juejin.im/post/5c2088205188251920598d85)

