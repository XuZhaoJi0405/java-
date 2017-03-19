## java设计模式之单例模式、工厂模式和抽象工厂模式 ##

最近几天看了很多篇关于java的三种模式的相关论文，下面我将从找一堆数中奇数和偶数，并把他们分开，并在其中表现MVC的架构设计思想。
单例模式：
   单例顾名思义就是单个例子的意思。就是这个特定的单体类只能创建一个实例并通过类中的方法来被其他的类引用。常应用到某些只能单个运行的程序之中。例如售票机，打印机，都只能一次运用一个实例，单个执行。从而避免某些情况下的二义性。运用单体是也是为了提高程序在运行中占用内存的大小，提高利用的效率，更加安全，节俭得运行代码。使用单例模式最核心的一点是体现了面向对象封装特性中的“单一职责”和“对象自治”原则。适用于一个应用中只需要保留一份即可的情况，既节省了资源又方便管理

单体的实现形式有懒汉式和饿喊式，其中两者的区别如下：
   对重量级对象应用饿汉模式，类加载时速度慢，但运行时速度快；懒汉模式则与之相反，类加载时速度快，但运行时第一次获得对象的速度慢。
   
   

单体代码
```
import java.util.*;

//此处采用懒汗式且为了保证线程安全使用双重锁定检查的方式

public class Singleton_Distinguish{
    private List <Integer> odd,even;//定义声明，odd来存储奇数，even来存储偶数
    private static volatile Singleton_Distinguish Singleton=null;//先声明次单例类的一个引用
    public Singleton_Distinguish(){
    }
    public void Distinguish(List<Integer> L){
      if(odd==null)
        odd=new ArrayList<Integer>();//如果是奇数那就为奇数列新建一个数组链表
      if(even==null)
        even=new ArrayList<Integer>();//如果是奇数那就为奇数列新建一个数组链表
      for(int i=0; i<L.size();i++){//逐个访问
        if((int)L.get(i)%2==0){//判断是否整除2，从而加载到数组中
          even.add(L.get(i));
        }else{
          odd.add(L.get(i));
        }
      }
    }
    public static Singleton_Distinguish getSingleton(){ //得到新建的单体类函数
      if(Singleton==null){
        synchronized(Singleton_Distinguish.class){//可以同步执行的段
          if(Singleton==null){//在第一次调用函数的时候建立
            Singleton=new Singleton_Distinguish();
          }
        }
      }
      return Singleton;
    }
    public List<Integer> getOdd(){//得到奇数列
      return odd;
    }
    public List<Integer> getEven(){//得到偶数列
      return even;
    }
    public void ShowOdd(){//显示奇数列，在命令行中打印出来
      System.out.println("the odds is:");
      for(int i=0;i<odd.size();i++){
        System.out.print(odd.get(i)+" ");
      }
        System.out.println("");
    }
    public void ShowEven(){
      System.out.println("the even is:");
      for(int i=0;i<even.size();i++){
          System.out.print(even.get(i)+" ");
      }
      System.out.println("");
    }
}

```
饿汉式的 实现方法

```
import java.util.*;


public class Singleton_Distinguish{
    private List <Integer> odd,even;//定义声明，odd来存储奇数，even来存储偶数
    private static volatile Singleton_Distinguish Singleton=new Singleton_Distinguish();
    public Singleton_Distinguish(){

    }
    public void Distinguish(List<Integer> L){
      if(odd==null)
        odd=new ArrayList<Integer>();
      if(even==null)
        even=new ArrayList<Integer>();
      for(int i=0; i<L.size();i++){
        if((int)L.get(i)%2==0){
          even.add(L.get(i));
        }else{
          odd.add(L.get(i));
        }
      }
    }
    public static Singleton_Distinguish getSingleton(){
      return Singleton;
    }
    public List<Integer> getOdd(){
      return odd;
    }
    public List<Integer> getEven(){
      return even;
    }
    public void ShowOdd(){
      System.out.println("the odds is:");
      for(int i=0;i<odd.size();i++){
        System.out.print(odd.get(i)+" ");
      }
        System.out.println("");
    }
    public void ShowEven(){
      System.out.println("the even is:");
      for(int i=0;i<even.size();i++){
          System.out.print(even.get(i)+" ");
      }
      System.out.println("");
    }
}


```
SingletonMain

```
import java.io.*;
import java.util.*;
import java.util.Scanner;

public class SingletonMain{
  public static void main(String []args){
    int n;
    List <Integer>numberList=new ArrayList<Integer>();
    Scanner in=new Scanner(System.in);
    System.out.println("请输入你要输入的数字的个数");
    n=in.nextInt();
    System.out.println("请分别输入这些数");
    for(int i=0;i<n; i++){
      numberList.add(in.nextInt());
    }
    Singleton_Distinguish SingleExample=Singleton_Distinguish.getSingleton();
    SingleExample.Distinguish(numberList);
    SingleExample.ShowOdd();
    SingleExample.ShowEven();
  }
}

```

单例的优点：

    提供了对唯一实例的受控访问。因为单例类封装了它的唯一实例，所以它可以严格控制客户怎样以及何时访问它，并为设计及开发团队提供了共享的概念。
    由于在系统内存中只存在一个对象，因此可以节约系统资源，对于一些需要频繁创建和销毁的对象，单例模式无疑可以提高系统的性能。
    允许可变数目的实例。我们可以基于单例模式进行扩展，使用与单例控制相似的方法来获得指定个数的对象实例。


单例的缺点：


    由于单例模式中没有抽象层，因此单例类的扩展有很大的困难。
    单例类的职责过重，在一定程度上违背了“单一职责原则”。因为单例类既充当了工厂角色，提供了工厂方法，同时又充当了产品角色，包含一些业务方法，将产品的创建和产品的本身的功能融合到一起。
    滥用单例将带来一些负面问题，如为了节省资源将数据库连接池对象设计为单例类，可能会导致共享连接池对象的程序过多而出现连接池溢出；现在很多面向对象语言(如Java、C#)的运行环境都提供了自动垃圾回收的技术，因此，如果实例化的对象长时间不被利用，系统会认为它是垃圾，会自动销毁并回收资源，下次利用时又将重新实例化，这将导致对象状态的丢失。



适用环境：

    系统只需要一个实例对象，如系统要求提供一个唯一的序列号生成器，或者需要考虑资源消耗太大而只允许创建一个对象。
    客户调用类的单个实例只允许使用一个公共访问点，除了该公共访问点，不能通过其他途径访问该实例。
    在一个系统中要求一个类只有一个实例时才应当使用单例模式。反过来，如果一个类可以有几个实例共存，就需要对单例模式进行改进，使之成为多例模式



## 简单的工厂模式：##
模式提出的动机：人们设计所需要的控件都是来自一个积累，但是由于控件属性的不同而使他们有了不同的外观和作用。我们就希望在使用这些控件的时候有一个更加方便的调用方法，并根据传入的参数得到相应的控件对象。

简单工厂，就是有一个专门定义的类(Factory)来负责创建其他产品类的实例。


模式的结构：
Factory,Product,ConcreteProduct.

产品类的实现(此处采用了MVC的设计架构，主要是为了养成分文件的架构思想


NumberX抽象类，抽象产品(Model)
```
package NumberModel;
import java.util.*;

public abstract class NumberX{
  public abstract List <Integer> GetNumberList();//获得数字类的数字链表
  public abstract boolean GetType();//获得数字类是属于那种类型的数字类，返回FALSE是奇数，返回TURE是偶数
}
```
EvenNumber类，奇数子类，具体产品类(Model)

```
package NumberModel;
import java.util.*;
import java.lang.Integer;

public class EvenNumber extends NumberX{
  private ArrayList <Integer> EvenList=null;

  public EvenNumber(){
    EvenList=new ArrayList<Integer>();
  }
  @Override
  public List<Integer> GetNumberList(){
    return EvenList;
  }
  @Override//获得数字类是属于那种类型的数字类，告诉调用者是偶数
  public  boolean GetType()
  {
    return true;
  }
}
```
OddNumber类，偶数子类，具体产品类(Model)

```
package NumberModel;
import java.util.*;
import java.lang.Integer;

public class EvenNumber extends NumberX{
  private ArrayList <Integer> EvenList=null;

  public EvenNumber(){
    EvenList=new ArrayList<Integer>();
  }
  @Override
  public List<Integer> GetNumberList(){
    return EvenList;
  }
  @Override//获得数字类是属于那种类型的数字类，告诉调用者是偶数
  public  boolean GetType()
  {
    return true;
  }
}
```
NumberView类作为视图来显示特定的产品类的信息(View)

```
package NumberView;
import java.util.*;
import NumberModel.*;

public  class NumberView{//建立视图类
  public  void ShowNumber(NumberX NumberExample){//用来显示挑选数的数字
      List <Integer> List=NumberExample.GetNumberList();//List作为声明来访问数列
      if(NumberExample.GetType()==true)
        System.out.println("the Evens is:");
      else
        System.out.println("the odds is:");
      for(int i=0;i<List.size();i++){
        System.out.print(List.get(i)+" ");
    }
  }
}
```

NumberController类作为操作类来处理相应的产品模型（Controller)

```
package NumberController;
import java.util.*;
import java.lang.Integer;
import NumberModel.*;
import NumberView.*;

public  class NumberController{
    private NumberX NumberEx=null;//NumberEx作为所具有的number类的引用
    private NumberView View=null;//View作为所具有的NumberView类的引用
    public NumberController(NumberX Nu,NumberView vi){//抽象类的构造函数
    NumberEx=Nu;//构造函数建立数字类的时候赋值
    View=vi;//构造函数建立数字视图类的时候赋值
    }
    public NumberController(NumberX Nu){//抽象类的构造函数
    NumberEx=Nu;//构造函数建立数字类的时候赋值
    }
    public  boolean IsThisNumber(Integer i)// 判断是否属于该种数
    {
      if(NumberEx.GetType()==true){
          if(i%2==0)return true;//如果是偶数，则可以整除二
          else return false;
      }else{
        if(i%2==0)return false;
        else return true;
      }
    }
    public  void  AddNumber(Integer i){// 往数字对象中增加数据
      List<Integer> NumberList=NumberEx.GetNumberList();//作为引用来改变数字类中的数字序列
      NumberList.add(i);
    }
    public void clear(){//清除数字类链表中的数据
      List<Integer> NumberList=NumberEx.GetNumberList();//作为引用来改变数字类中的数字序列
      NumberList.clear();
    }
    public void ChangeNumber(NumberX NumberDate){
      NumberEx=NumberDate;
    }
    public void ShowNumberView(){
      if(View!=null)
        View.ShowNumber(NumberEx);//在创建时显示视图
    }
    public NumberX GetNumber(){
      return NumberEx;
    }
}
```

一个专门的工厂类，来根据用户需求返回相应的产品类

```
package simpleFactory;

import java.util.*;
import NumberController.*;
import NumberModel.*;

public class simpleFactory{
  private EvenNumber EvenNum;
  private OddNumber OddNum;
  public simpleFactory(){
    OddNum=new OddNumber();
    EvenNum=new EvenNumber();
  }
  public NumberX NumberDistinguish(List <Integer> L,int type){
    NumberController controller;
    if(type==0)
       controller=new NumberController(OddNum);
    else
       controller=new NumberController(EvenNum);
    controller.clear();
     for(int i=0; i<L.size();i++){
       if(controller.IsThisNumber(L.get(i))){
         controller.AddNumber(L.get(i));
       }
     }
  return controller.GetNumber();
  }
}
```

simpleFactoryMain类，来执行程序

```
import java.io.*;
import java.util.*;
import NumberView.*;
import NumberController.*;
import NumberModel.*;
import simpleFactory.*;

public class simpleFactoryMain{
  public static void main(String []args){
    int n;
    List <Integer>numberList=new ArrayList<Integer>();
    Scanner in=new Scanner(System.in);
    System.out.println("请输入你要输入的数字的个数");
    n=in.nextInt();
    System.out.println("请分别输入这些数");
    for(int i=0;i<n; i++){
      numberList.add(in.nextInt());
    }
    NumberX L;//L为Number类的声名用来多态得引用
    simpleFactory f= new simpleFactory();
    NumberView view = new NumberView();
    L=f.NumberDistinguish(numberList,0);
    view.ShowNumber(L);
    L=f.NumberDistinguish(numberList,1);
    view.ShowNumber(L);
  }
}

```

<font color=#0099ff size=4 face="黑体">上例子中，simpleFactory的NumberDistinguish(List <Integer> L,int type)方法是将传入的链表参数和数据类型参数来返回相应的数字类（产品）。</font>

    工厂类含有必要的判断逻辑，可以决定在什么时候创建哪一个产品类的实例，通过引入配置文件，可以在不修改任何客户端代码的情况下更换和增加新的具体产品类，在一定程度上提高了系统的灵活性。由于工厂类集中了所有产品创建逻辑，一旦不能正常工作，整个系统都要受到影响。单产品类不多的时候可以使用。
    

## 工厂模式 ##


 模式的产生：


我们需要将具体控件的创建交给特定的工厂子类去完成。从而达到不用每添加一种产品或者删除一种产品都需要改变具体工厂类的情况。如果出现新的产品，只需要去创建一个
具体的工厂类去生产该产品。符合“开闭原则”。

模式结构：

    Product：抽象产品
    ConcreteProduct：具体产品
    Factory：抽象工厂
    ConcreteFactory：具体工厂
    
抽象工厂类的实现：

```
import java.util.*;
import java.lang.Integer;

abstract class Factory{ //抽象工厂，用来分开处理每个具体的工厂
   public abstract Number NumberDistinguish(List <Integer>);
}
```

具体工厂类


偶数工厂类
```
import java.util.*;
import java.lang.Integer;

public class EvenFactory extends Factory{
  @Override
  public Number NumberDistinguish(List <Integer> list){ //传递进来的实参
    EvenNumber EL=new EvenNumber();//新建一个偶数类的实例
    NumberController controller =new NumberController(EL);//新建一个控制器来实现对数的判断处理
    for(int i=0;i<list.size();i++){
      controller.IsThisNumber(controller.get(i));
      controller.add(list.get(i));
    }
  }
}

```

奇数工厂类

```
import java.util.*;
import java.lang.Integer;

public class OddFactory extends Factory{
  @Override
  public Number NumberDistinguish(List <Integer> list){ //传递进来的实参
    OddNumber OL=new OddNumber();//新建一个奇数类的实例
    NumberController controller =new NumberController(OL);//新建一个控制器来实现对数的判断处理
    for(int i=0;i<list.size();i++){
      controller.IsThisNumber(controller.get(i));
      controller.add(list.get(i));
    }
  }
}

```
FactoryMain类来执行程序..

```
import java.util.*;

public class FactoryMain{
  public static void main(String []args){
  int n;
  List <Integer>numberList=new ArrayList<Integer>();
  Scanner in=new Scanner(System.in);
  System.out.println("请输入你要输入的数字的个数");
  n=in.nextInt();
  System.out.println("请分别输入这些数");
  for(int i=0;i<n; i++){
    numberList.add(in.nextInt());
  }
  Number N=new Number();
  N=EvenFactory(numberList);
  N.ShowNumber();
  N=OddFactory(numberList);
  N.ShowNumber();
}
}
```

在工厂方法模式中，工厂方法用来创建客户所需要的产品，同时还向客户隐藏了哪种具体产品类将被实例化这一细节，用户只需要关心所需产品对应的工厂，无须关心创建细节，甚至无须知道具体产品类的类名。基于工厂角色和产品角色的多态性设计是工厂方法模式的关键。它能够使工厂可以自主确定创建何种产品对象，而如何创建这个对象的细节则完全封装在具体工厂内部。


