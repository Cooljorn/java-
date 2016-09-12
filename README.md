# java-内部类
Java知识繁杂，对Java知识模块总结，便于以后复习
1、内部类是什么鬼？
  在一个类的内部定义的类就是内部类，相对于包含它的类就叫外部类。
  内部类只是Java编译器的概念，虚拟机才不理会内部类这种玩意，每个内部类都会被编译成一个独立的类，生成一个独立的字节码文件。
2、类就类，干嘛还要寄人篱下？
  内部类与外部类关系好嘛，不想和其他类有关系，有些幽闭症，这也可能会导致内部类对外部的完全/部分隐藏，代码简洁些，封装性更好些。
3、内部类具体类型及其用法：
  静态内部类
  成员内部类
  方法内部类
  匿名内部类
  
  （1）成员内部类
    和成员变量类似，只是它成了一个类，例：
    public class Outer {
      private int a = 100;
  
      public class Inner {
        public void innerMethod(){
            System.out.println("outer a " +a);
            Outer.this.action();
        }
      }
  
        private void action(){
            System.out.println("action");
        }
  
        public void test(){
            Inner inner = new Inner();
            inner.innerMethod();
        }
      }
    
    内部类的创建：Outer outer=new Outer();  Outer.Inner inner=outer.new Inner();
      内部类是依托于外部类的，所以要先有外部类才能创建内部类。
      
    我是外部类的方法，请问我可以访问内部类吗？
      你可以访问类的成员变量吗，和他的访问修饰符有关吗，你可以访问成员变量就可以访问成员内部类。
    
    我是类的方法，请问我可以访问其他类的成员内部类吗？
      那要看看那个类给不给你访问了（就是和访问修饰符相关）。
      
    实现原理：
      代码会生成两个类，一个是Outer，另一个是Outer$Inner,
      public class Outer {
        private int a = 100;
        private void action() {
            System.out.println("action");
        }
        public void test() {
            Outer$Inner inner = new Outer$Inner(this);  //注意
            inner.innerMethod();
        }
        static int access$0(Outer outer) {         //在类Outer$Inner中访问a
            return outer.a;
        }
        static void access$1(Outer outer) {
            outer.action();
        }
    }
    
    public class Outer$Inner {
        final Outer outer;
        public Outer$Inner(Outer outer){  //构造函数
            ths.outer = outer;
        }
        public void innerMethod() {
            System.out.println("outer a "
                    + Outer.access$0(outer));
            Outer.access$1(outer);
        }
    }
    
    小总结：
      成员内部类中不可以定义静态变量和方法，这谁规定的，不懂诶。个人感觉：有一定的道理，既然是成员内部类就应当乖乖听话，
      若内部定义静态变量或方法，那就意味着内部类可以独立使用，这样的内部类不符合他的性格，如果内部类确实需要静态方法和变量，那就挪到外部嘛。
  
  
  （2）静态内部类
    顾名思义，就是用static修饰的内部类例：
    public class Outer {
      private static int shared = 100;
      public static class StaticInner {
          public void innerMethod(){
              System.out.println("inner " + shared);
          }
      }
      public void test(){
          StaticInner si = new StaticInner();
          si.innerMethod();
      }
    }
    这样做有啥意义吗？
      静态内部类与外部类的联系小一些，创建：Outer.StaticInner si=new Outer.StaticInner();  si.innerMethod();
      怎么样，是不是比成员内部类独立些了呢。
      静态内部类不依赖于外部类实例。
      
  （3）方法内部类
    就是定义在方法里面的内部类，如果类只在某个方法内被使用，就定义为方法内部类（更好的封装），例
    public class Outer {
      private int a = 100;
      public void test(final int param){
          final String str = "hello";
          class Inner {
              public void innerMethod(){
                  System.out.println("outer a " +a);
                  System.out.println("param " +param);
                  System.out.println("local var " +str);
              }
          }
          Inner inner = new Inner();
          inner.innerMethod();
        }
      }
      
      这家伙藏的有点深呀，这样的话就只内在方法内使用了，是不是有点像被关在一个公寓（类）里的房间（方法）内的赶脚。
      方法内部类是可以外部类的变量和方法的，若方法是静态的，值只能访问静态变量和方法。
      方法内部类只能访问方法中的静态变量和方法。感觉对身边人（方法内）要求比对邻居（外部类）要求多呀。
      
  （4）匿名内部类
    没有名字的内部类，有点私藏的感觉呀，完全不给外界访问。语法：
      new 父类(参数列表) {
        //匿名内部类实现部分
      }
    或者：
      new 父接口() {
         //匿名内部类实现部分
      }
    好抽象，好抽象，那又怎样，反正我不想举例了。
    说明：
      匿名内部类木有名字，也木有构造方法，被定义在方法内，只能被使用一次，
      可以访问外部类的变量和方法，只能访问方法内的final参数和局部变量。
      匿名内部类能做的方法内部类都能做，有些情况下对象只被调用一次且不需要构造方法来接受参数，那么使用匿名内部类要更加简洁。
      例如Arrays.sort方法，接受一个数组和一个Comparator接口参数来对一个字符串数组排序时：
      public void sortIgnoreCase(String[] strs){
        Arrays.sort(strs, new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return o1.compareToIgnoreCase(o2);
            }
        });
      }
      在一些事件监听器中我们也经常会用内名内部类来写响应事件。
      
      
总结：
  本篇谈了各种内部类的使用及其区别，内部类的出现就是源于它实现了更好的封装，代码的实现也会更加的简洁。具体的使用技巧必须通过大量的练习和读他人优秀代码。
  现写下这篇小结，待日后总结，能够对Java有更加深刻的理解。
