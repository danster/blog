
## Java中关键字使用

- [多态性: interface与abstract class](#interface)
- [修饰符: final与static](#final)
- [作用域: public,protected,default,private](#public)

### <a name="interface">[Java中interface与abstract class的使用](http://blog.csdn.net/chenssy/article/details/12858267)</a>

**区别**

1. interface被继承时用的关键字是implements，而且可以同时implements多个interface，而abstract class被继承时用的关键字是extends，而且只能同时extends一个abstract class
2. interface内定义的方法都是public的，而abstract class内的则不一定
3. interface内定义的成员对象都是static & final的，而abstract class不是 
4. interface的方法不能有默认实现，只有一个申明，而abstract class内申明abstract的方法不能有实现，非申明abstract的方法可以有默认实现
5. interface被implements时，所有方法都必须被实现，必须申明是public的，而abstract class被extends时只有abstract的方法才必须被实现，别的有默认实现的直接被继承过来

**abstract class**

* 抽象类不能被实例化，实例化的工作应该交由它的子类来完成，它只需要有一个引用即可。
* 抽象方法必须由子类来进行重写。
* 只要包含一个抽象方法的抽象类，该方法必须要定义成抽象类，不管是否还包含有其他方法。
* 抽象类中可以包含具体的方法，当然也可以不包含抽象方法。
* 子类中的抽象方法不能与父类的抽象方法同名。
* abstract不能与final并列修饰同一个类。
* abstract 不能与private、static、final或native并列修饰同一个方法。

**interface**

* 个Interface的方所有法访问权限自动被声明为public。确切的说只能为public，当然你可以显示的声明为protected、private，但是编译会出错！
*  接口中可以定义“成员变量”，或者说是不可变的常量，因为接口中的“成员变量”会自动变为为public static final。可以通过类命名直接访问：ImplementClass.name。
*  接口中不存在实现的方法。
*  实现接口的非抽象类必须要实现该接口的所有方法。抽象类可以不用实现。
*  不能使用new操作符实例化一个接口，但可以声明一个接口变量，该变量必须引用（refer to)一个实现该接口的类的对象。可以使用 instanceof 检查一个对象是否实现了某个特定的接口。例如：if(anObject instanceof Comparable){}。
* 在实现多接口的时候一定要避免方法名的重复。

### <a name="final">[Java关键字final、static使用总结](http://lavasoft.blog.51cto.com/62575/18771)</a>
**final**

根据程序上下文环境，Java关键字final有“这是无法改变的”或者“终态的”含义，它可以修饰非抽象类、非抽象类成员方法和变量。你可能出于两种理解而需要阻止改变：设计或效率。  
final类不能被继承，没有子类，final类中的方法默认是final的。  
final方法不能被子类的方法覆盖，但可以被继承。  
final成员变量表示常量，只能被赋值一次，赋值后值不再改变。  
final不能用于修饰构造方法。  
父类的private成员方法是不能被子类方法覆盖的，因此private类型的方法默认是final类型的。

```  
1、final类
final类不能被继承，因此final类的成员方法没有机会被覆盖，默认都是final的。在设计类时候，如果这个类不需要有子类，
类的实现细节不允许改变，并且确信这个类不会载被扩展，那么就设计为final类。
2、final方法
如果一个类不允许其子类覆盖某个方法，则可以把这个方法声明为final方法。
使用final方法的原因有二：
第一、把方法锁定，防止任何继承类修改它的意义和实现。
第二、高效。编译器在遇到调用final方法时候会转入内嵌机制，大大提高执行效率。
        
3、final变量（常量）
用final修饰的成员变量表示常量，值一旦给定就无法改变！
final修饰的变量有三种：静态变量、实例变量和局部变量，分别表示三种类型的常量。
从下面的例子中可以看出，一旦给final变量初值后，值就不能再改变了。
另外，final变量定义的时候，可以先声明，而不给初值，这中变量也称为final空白，无论什么情况，
编译器都确保空白final在使用之前必须被初始化。但是，final空白在final关键字final的使用上提供了更大的灵活性，
为此，一个类中的final数据成员就可以实现依对象而有所不同，却有保持其恒定不变的特征。

4、final参数
当函数参数为final类型时，你可以读取使用该参数，但是无法改变该参数的值。
```
**static**  
static表示“全局”或者“静态”的意思，用来修饰成员变量和成员方法，也可以形成静态static代码块，但是Java语言中没有全局变量的概念。

被static修饰的成员变量和成员方法独立于该类的任何对象。也就是说，它不依赖类特定的实例，被类的所有实例共享。只要这个类被加载，Java虚拟机就能根据类名在运行时数据区的方法区内定找到他们。因此，static对象可以在它的任何对象创建之前访问，无需引用任何对象。
用public修饰的static成员变量和成员方法本质是全局变量和全局方法，当声明它类的对象市，不生成static变量的副本，而是类的所有实例共享同一个static变量。
static变量前可以有private修饰，表示这个变量可以在类的静态代码块中，或者类的其他静态成员方法中使用（当然也可以在非静态成员方法中使用--废话），但是不能在其他类中通过类名来直接引用，这一点很重要。实际上你需要搞明白，private是访问权限限定，static表示不要实例化就可以使用，这样就容易理解多了。static前面加上其它访问权限关键字的效果也以此类推。

```
1、static变量
按照是否静态的对类成员变量进行分类可分两种：
一种是被static修饰的变量，叫静态变量或类变量；另一种是没有被static修饰的变量，叫实例变量。
两者的区别是：
  - 对于静态变量在内存中只有一个拷贝（节省内存），JVM只为静态分配一次内存，在加载类的过程中完成静态变量的内存分配，
    可用类名直接访问（方便），当然也可以通过对象来访问（但是这是不推荐的）。
  - 对于实例变量，每创建一个实例，就会为实例变量分配一次内存，实例变量可以在内存中有多个拷贝，互不影响（灵活）。

2、静态方法
  静态方法可以直接通过类名调用，任何的实例也都可以调用，因此静态方法中不能用this和super关键字，
  不能直接访问所属类的实例变量和实例方法(就是不带static的成员变量和成员成员方法)，
  只能访问所属类的静态成员变量和成员方法。因为实例成员与特定的对象关联！这个需要去理解，想明白其中的道理，不是记忆！！！
  因为static方法独立于任何实例，因此static方法必须被实现，而不能是抽象的abstract。

3、static代码块
  static代码块也叫静态代码块，是在类中独立于类成员的static语句块，可以有多个，位置可以随便放，
  它不在任何的方法体内，JVM加载类时会执行这些静态的代码块，如果static代码块有多个，
  JVM将按照它们在类中出现的先后顺序依次执行它们，每个代码块只会被执行一次。
```
**static final**  
> static final 用来修饰成员变量和成员方法，可简单理解为“全局常量”！  
> 对于变量，表示一旦给值就不可修改，并且通过类名可以访问。对于方法，表示不可覆盖，并且可以通过类名直接访问。

特别要注意一个问题：  
> 对于被static和final修饰过的实例常量，实例本身不能再改变了，但对于一些容器类型（比如，ArrayList、HashMap）的实例变量，不可以改变容器变量本身，但可以修改容器中存放的对象，这一点在编程中用到很多。

###<a name='public'>java 作用域 public private default protected 的差别</a>

> 在Java中有四种不同的作用域，分别是 public protected default 和private，这四种不同的作用域的限制由前到后逐渐的增加。 

- 现在来看看public的作用域，在所有的作用域中，public可谓是最宽松的，在编写类文件的时候，只要该类以public关键字修饰，那么这个类的类文件就必须以该类的简单类名来命名。在类的内部，被它修饰的无论是参数还是方法，都可以在任何地方访问的到(当然前提是这个类是公开的，就是上文说到的是以public关键字修饰的类)。
- 而对于protected作用域来说，它的限制就严格了一点，它只允许本类、同一个包内的其他类（这个类不是它的子类）或者它的子类使用被它修饰的方法或者参数（以protected修饰的类貌似还没遇到过，O(∩_∩)O~，有用过的给个提醒），子类使用父类中的protected方法或者参数时不受包位置的影响，这就意味着，即使位于不同包的类想要调用它父类的protected方法或者参数也是被允许的。
- 对于default作用域（即不加任何的关键字修饰的方法或者参数），它的限制就严格的多了，除了本类和本包中的类能访问以外，就是不在同一个包内的它自己的子类也不能访问(有点像老子跟儿子分家了，老子的东西就不能给儿子用了一样O(∩_∩)O~)。
- private作用域在这四种之内是最为严格的，除了它自己，哪怕是同包内的其他类都不允许使用。通常我们使用这个关键字来实现对类的方法的隔离，它意味着，除了本类的方法能够调用这个私有化成员以外，其他的包括同包中的类和子类都不能访问这些个私有的成员。（肥水不流外人田嘛，能被private修饰的东东怎么说也是相当有价值的了）。

具体的信息看下图： 

作用域 | 当前类 | 同一包内	| 子孙类	| 其他包
:----:|:------|:-------|:------|:-----
public   |√ |√  |√  |√
protected|√	|√	|√	|×
default  |√ |√	|×	|×
private	 |√	|×	|×	|×

---

###<a name='summary'>总结</a>
**1 abstract**  
abstract class, abstract function

**2 interface**  
interface 

**3 final**  
final class  
final function = private function  
final variable : 常量  
final 参数  

**4 static**  
static function/variable/codeblock  
static final function/variable  
*Error: static abstract* 

**5 public**  
**6 protected**  
**7 private**  
**8 default**
