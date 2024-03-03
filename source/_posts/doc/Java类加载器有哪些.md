---
title: Java类加载器有哪些
category: 文档
tag: 总结
date: 2024-01-01 11:20:53
abbrlink: 8
---
# Java类加载器有哪些

### **Bootstrap类加载器**

启动类加载器主要加载的是JVM自身需要的类，这个类加载使用C++语言实现的，没有父类，是虚拟机自身的一部分，它负责将 **<JAVA_HOME>/lib路径下的核心类库**或**-Xbootclasspath参数指定的路径下的jar包**加载到内存中，注意必由于虚拟机是按照文件名识别加载jar包的，如rt.jar，如果文件名不被虚拟机识别，即使把jar包丢到lib目录下也是没有作用的(出于安全考虑，Bootstrap启动类加载器只加载包名为java、javax、sun等开头

### **Extention 类加载器**

扩展类加载器是指Sun公司实现的sun.misc.Launcher$ExtClassLoader类，**由Java语言实现的**，父类加载器为Bootstrap（null），是Launcher的静态内部类，它负责加载**<JAVA_HOME>/lib/ext目录下**或者由系统变量**-Djava.ext.dir指定位路径中的类库**，开发者可以直接使用标准扩展类加载器

### **Application类加载器**

称应用程序加载器是指 Sun公司实现的sun.misc.Launcher$AppClassLoader。父类加载器为ExtClassLoader，它负责加载**系统类路径java -classpath**或**-D java.class.path 指定路径下的类库**，也就是我们经常用到的**classpath路径**，开发者可以直接使用系统类加载器，一般情况下该类加载是程序中默认的类加载器，通过ClassLoader#getSystemClassLoader()方法可以获取到该类加载器
**​**

### **Custom自定义类加载器**

应用程序可以自定义类加载器，父类加载器为AppClassLoader


![image.png](/img/Java类加载器有哪些/classloader.png)