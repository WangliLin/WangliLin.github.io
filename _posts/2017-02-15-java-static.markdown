---
layout: post
title: Java基础-static关键词
date: 2017-02-15 15:32:24.000000000 +09:00
tags: java
---

- **static是什么**
    - 在Java中并不存在全局变量的概念，但是我们可以通过static来实现一个“伪全局”的概念，在Java中static表示“全局”或“静态”的意思，用来修饰成员变量和成员方法，当然也可以修饰代码块。
    - Java把内存分为栈内存和堆内存，其中栈内存用来存放一些基本类型的变量、数组和对象的应用，堆内存主要存放一些对象。在JVM加载一个类的时候，若该类存在static修饰的成员变量和成员方法，则会为这些成员变量和成员方法在固定的位置开辟一个固定大小的内存区域，有了这些“固定”的特性，那么JVM就可以非常方便地访问他们。同时如果静态的成员变量和成员方法不出作用域的话，他们的句柄都会保持不变。同时static所蕴含“静态”的概念表示着它是不可恢复的，即在那个地方，你修改了，他是不会变回原样的，你清理了，他就不会回来。
    - static修饰的成员变量和成员方法是独立于该类的，它不依赖于某个特定的实例变量，也就是说他被该类的所有实例共享。所有实例的引用都指向同一个地方，任何一个实例对其的修改都会导致其他实例的变化。
   
 ```java    
        public class User {
                private static int userNumber  = 0 ;

                public User(){
                    userNumber ++;
                }
        
                public static void main(String[] args) {
                    User user1 = new User();
                    User user2 = new User();

                System.out.println("user1 userNumber：" + User.userNumber);
                System.out.println("user2 userNumber：" + User.userNumber);
            }
        } 
    ----------- ------------
    Output:
    user1 userNumber：2
    user2 userNumber：2
```


- **怎么使用static**
static可以用于修饰成员变量和成员方法，我们将其称之为静态变量和静态方法，直接通过类名来进行访问。


    ```java
        ClassName.popertyName
        ClassName.methodName(...)
    ```

    - **static变量**
    static修饰的变量我们称之为静态变量，没有用static修饰的变量称之为实例变量，他们两者的区别是：
    静态变量是随着类加载时被完成初始化的，它在内存中仅有一个，且JVM也只会为它分配一次内存，同时类所有的实例都共享静态变量，可以直接通过类名来访问它。
    实例变量伴随着实例，每创建一个实例就会产生一个实例变量，它与实例同生共死。
    **我们一般在这两种情况下使用静态变量：对象之间共享数据、访问方便。**
    
    - **static方法**
        - static修饰的方法我们称之为静态方法，我们通过类名对其进行直接调用。由于它在类加载的时候就存在了，它不依赖于任何实例，所以static方法必须实现，也就是说他不能是抽象方法abstract。
        - 对于静态方法来说，是没有this的，因为它不依附于任何对象，既然没有对象，就谈不上this了。并且由于这个特性，在静态方法中，不能访问类的非静态成员和非静态方法，因为非静态成员方法/变量都必须依赖具体的对象才能够被调用（反过来是可以的，即在非静态方法中是可以访问非静态成员方法/变量的。
        -   因此，如果说想在不创建对象的情况下调用某个方法，就可以将这个方法设置为static。我们最常见的static方法就是main方法，至于为什么main方法必须是static的，因为程序在执行main方法的时候没有创建任何对象。
另外，即使没有显式地声明为static，类的构造器实际上也是静态方法。

    - **static代码块**
  被static修饰的代码块，我们称之为静态代码块，静态代码块会随着类的加载一块执行。static块可以置于类中的任何地方，类中可以有多个static块，在类初次被加载的时候，会按照static块的顺序来执行每个static块，并且只会执行一次。


- **为什么static可以优化性能**
for example：

    ```java
        class Person{
            private Date birthDate;  
           
            public Person(Date birthDate) {  
                this.birthDate = birthDate;  
            }  
               
            boolean isBornBoomer() {  
                Date startDate = Date.valueOf("1946");  
                Date endDate = Date.valueOf("1964");  
                return birthDate.compareTo(startDate)>=0 && birthDate.compareTo(endDate) < 0;  
    }  
        }
    ```
isBornBoomer是用来看这个人是否是1946-1964年出生的，而每次isBornBoomer被调用的时候，都会生成startDate 和birthDate两个对象，造成了空间浪费，如果改成这样效率会更高：
    
    ```java
    class Person(){
        private Date birthDate;
        private static Date startDate,endDate;
        static{
            startDate = Date.valueOf("1946");
            endDate = Date.valueOf("1964");
        }
        
        public Person(Date birthDate){
            this.birthDate = birthDate;
        } 

        boolean isBornBoomer(){
            return birthDate.compareTo(startDate)>=0 && birthDate.compareTo(endDate) < 0;
        }
    }
    ```

- **static的常见面试题**
e.t. 下面这段代码的输出是什么？

    ```java
    
        public class Test extends Base{  
       
            static{  
                System.out.println("test static");  
            }  
               
            public Test(){  
                System.out.println("test constructor");  
            }  
               
            public static void main(String[] args) {  
                new Test();  
            }  
        }  
   
        class Base{  
               
            static{  
                System.out.println("base static");  
            }  
               
            public Base(){  
                System.out.println("base constructor");  
            }  
        }  
    ```

输出结果为：
    
        base static  
        test static  
        base constructor  
        test constructor
    

在执行开始，先要寻找到main方法，因为main方法是程序的入口，但是在执行main方法之前，必须先加载Test类，而在加载Test类的时候发现Test类继承自Base类，因此会转去先加载Base类，在加载Base类的时候，发现有static块，便执行了static块。在Base类加载完成之后，便继续加载Test类，然后发现Test类中也有static块，便执行static块。在加载完所需的类之后，便开始执行main方法。在main方法中执行new Test()的时候会先调用父类的构造器，然后再调用自身的构造器。因此，便出现了上面的输出结果。


- **static的缺陷**
static有着诸多的作用，但是它也存在一些缺陷。
    - 它只能调用static变量
    - 它只能调用static方法
    - 不能以任何形式引用this、super
    - static变量在定义时必须要进行初始化，且初始化时间要早于非静态变量

- **总结**

> “static方法就是没有this的方法。在static方法内部不能调用非静态方法，反过来是可以的。而且可以在没有创建任何对象的前提下，仅仅通过类本身来调用static方法。这实际上正是static方法的主要用途。”                ------《Java编程思想》P86

无论是变量、方法，还是代码块，只要用static修饰，就是在类被加载时就已经“准备好了”，也就是可以被使用或者已经被执行，都可以脱离对象而执行。反之，如果没有static，则必须要依赖于对象实例。
