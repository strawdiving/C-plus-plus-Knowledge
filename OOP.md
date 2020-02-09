# 面向对象OOP
## 概述
面向对象编程基于三个基本概念：数据抽象、继承和动态绑定。

在 C++ 中，用类进行数据抽象；
用类派生从一个类继承另一个：派生类继承基类的成员；
动态绑定使编译器能够在运行时决定是使用基类中定义的函数还是派生类中定义的函数。

面向对象编程的关键思想是多态性。

之所以称通过继承而相关联的类型为多态类型，是因为在许多情况下可以互换地使用派生类型或基类型的“许多形态”。

## 继承
通过继承我们能够定义这样的类，它们对类型之间的关系建模，共享公共的东西，仅仅特化本质上不同的东西。
1. 派生类（derived class）能够继承基类（baseclass）定义的成员；
2. 派生类可以无须改变而使用那些与派生类型具体特性不相关的操作；
3. 派生类可以重定义那些与派生类型相关的成员函数，将函数特化，考虑派生类型的特性；
4. 除了从基类继承的成员之外，派生类还可以定义更多的成员。

称因继承而相关联的类构成了一个**继承层次**，其中一个类为根，其他类直接或间接继承根类。最底层的派生类对象包含其每个直接基类和间接基类的子对象。

### 定义基类和派生类
#### 基类
像任意其他类一样，基类也有定义其接口和实现的数据和函数成员。继承层次的根类一般都要定义虚析构函数即可。

保留字 **virtual 的目的是启用动态绑定**。在返回类型前面加上virtual,指明函数为虚函数。成员默认为非虚函数，对非虚函数的调用在编译时确定。

基类必须指出希望派生类重写哪些函数。基类通常应将派生类需要重定义的任意函数定义为虚函数，基类希望派生类继承的函数不能定义为虚函数。

除了构造函数之外，任意非 static 成员函数都可以是虚函数。保留字只在类内部的成员函数声明中出现，不能用在类定义体外部出现的函数定义上。

##### 纯虚函数
在函数形参表后面写上 = 0 以指定纯虚函数：

```c++
class Disc_item : public Item_base {
		public:
		double net_price(std::size_t) const = 0;
};
```
将函数定义为纯虚能够说明，**该函数为后代类型提供了可以覆盖的接口，但是这个类中的版本决不会调用。重要的是，用户将不能创建 Disc_item 类型的对象。**
试图创建抽象基类的对象将发生编译时错误：

```c++
// Disc_item 声明了纯虚函数
Disc_item discounted; // error: 不能定义Disc_item 对象
```

**含有（或继承）一个或多个纯虚函数的类是抽象基类。除了作为抽象基类的派生类的对象的组成部分，不能创建抽象类型的对象。**

#### 访问控制和继承
访问标号（access label）:

- **public**：用户代码可以访问类的public成员，派生类可以访问基类的public 成员。
- **private**：只能由基类的成员（类内）和友元访问，不能被类的用户访问，派生类不能访问基类的private成员。
- **protected**：像private成员一样，protected成员不能被类的用户访问；像public成员一样，可以被该类的派生类访问。
	
派生类只能通过派生类对象访问基类的protected成员（只有在派生类中才可以通过派生类对象访问基类的protected成员），派生类对其基类类型对象的protected 成员没有特殊访问权限。
	
派生类对基类的 public 和 private 成员的访问权限与程序中任意其他部分一样。

```c++
eg. B继承了A，A是B的基类，price是protected成员。
void B::memfcn(const B &d, const A &b)
{
		// attempt to use protected member
		double ret = price; // ok: uses this->price
 		ret = d.price; // ok: uses price from a B object  在B中通过B类型对象访问price
 		ret = b.price; // error: no access to price from a A 对A类型对象没有特殊访问权限
}

class Base   
{  
	potected: 
	int i;           
};  
class Derived: public Base  
{ 
	public: 
	void fun(Derived d)
	{
		d.i = 3; //ok：只有在派生类中才可以通过派生类对象访问基类的protected成员。
  } 
};  

int main()  
{  
	 Derived derived;  
	 derived.i = 3;       //error：只有在派生类中才可以通过派生类对象访问基类的protected成员。  
   return 0;  
}  
```

类有2种用户：类本身的成员和该类的用户，将类划分为private和public访问级别反映了用户种类的这一分隔：用户只能访问public接口，类成员和友元public和private成员都可以访问。
  	
有了继承，就有了第三种用户：从类派生定义新类的程序员。派生类的提供者通常（但并不总是）需要访问（一般为 private 的）基类实现，为了允许这种访问而仍然禁止对实现的一般访问，提供了附加的protected 访问标号。类的 protected 部分仍然不能被一般程序访问，但可以被派生类访问。

定义基类时：**接口函数应该为public而数据一般不应为public**。**希望禁止派生类访问的成员应该设为private，提供派生类实现所需操作或数据的成员应设为protected**。换句话说，提供给派生类型的接口是protected成员和public成员的组合。被继承的类必须决定实现的哪些部分声明为protected和private。

#### 公有、私有和受保护的继承
对类所继承的成员的访问由基类中的成员访问级别和派生类派生列表中使用的访问标号共同控制。派生类可以进一步限制但不能放松对所继承的成员的访问。
- **如果成员在基类中为 private**
则只有基类和基类的友元可以访问该成员。派生类不能访问基类的 private 成员，也不能使自己的用户能够访问那些成员。
- **如果基类成员为 public 或protected**
则派生列表中使用的访问标号决定该成员在派生类中的访问级别：
	-  **public公用继承**：基类成员保持自己的访问级别，基类的 public 成员为派生类的 public 成员，基类的 protected 成员为派生类的 protected成员。
	- **protected受保护继承**：基类的 public 和 protected 成员在派生类中为protected 成员。
	- **private私有继承**：基类的的所有成员在派生类中为 private 成员。
	- **接口继承**：public 派生类继承基类的接口。
	- **实现继承**：使用 private 或 protected 派生的类不继承基类的接口——通常被称为实现继承
  
#### 默认继承保护级别
class和struct均可定义类，用它们定义类的唯一差别在于默认的成员保护级别和默认的继承保护级别。

**默认情况下，struct的保护级别为public，而class的保护级别为private。**

```c++
struct S_Base {  
    int foo(int) { return 0; }  
  	int val;  
};  
class C_Base {  
   	int foo(int) { return 0; }  
		int val;  
};  
```
以上两个类的成员具有默认的保护级别，相当于如下代码：

```c++
struct S_Base {  
		public:  
		 int foo(int) { return 0; }  
     int val;  
};  
class C_Base {  
    private:  
		 int foo(int) { return 0; }  
		 int val;  
};
```

使用class保留字定义的派生默认具有private继承，而用struct保留字定义的类默认具有public继承。

```c++
struct S_Derived : S_Base {
}；
class C_Derived : C_Base {
};
```
相当于
```c++
struct S_Derived : public S_Base {
    public:
}；
class C_Derived : private C_Base {
    private: 
};
```

#### 派生类
为了定义派生类，使用类派生列表指定基类。类派生列表指定了一个或多个基类，具有如下形式：

```c++
class classname: access-label base-class
```

访问标号决定了对继承成员的访问权限，如果想要继承基类的接口，则应该进行public派生。

尽管不是必须这样做，派生类一般会重定义所继承的虚函数。派生类没有重定义某个虚函数，则使用基类中定义的版本。派生类型必须对想要重定义的每个继承成员进行声明。一旦函数在基类中声明为虚函数，它就一直为虚函数，派生类无法改变该函数为虚函数这一事实。派生类重定义虚函数时，可以使用 virtual 保留字，但不是必须这样做。

派生类中虚函数的声明必须与基类中的定义方式完全匹配，但有一个例外：返回对基类型的引用（或指针）的虚函数。派生类中的虚函数可以返回基类函数所返回类型的派生类的引用（或指针）。例如，基类A可以定义返回 A* 的虚函数，如果这样，派生类B类中定义的实例可以定义为返回 A* 或 B*。

派生类由多个部分组成：派生类本身定义的（非 static）成员加上由基类（非 static）成员组成的子对象。因为每个派生类对象都有基类部分，类可以像访问自己的成员一样访问共基类的public 和 protected 成员。

用作基类的类必须是已定义的。已声明但未定义的类不能用作基类。

如果需要声明（但并不实现）一个派生类，则声明包含类名但不包含派生列表。例如，下面的前向声明会导致编译时错误：

```c++
class Bulk_item : public Item_base;
```

正确的前向声明为：

```c++
// forward declarations of both derived and nonderived class
class Bulk_item;
class Item_base;
```

##### 派生类构造函数
派生类构造函数的初始化列表只能初始化派生类的成员，不能直接初始化继承成员。相反派生类构造函数通过将基类包含在构造函数初始化列表中来间接初始化继承成员。

```c++
class Bulk_item : public Item_base {
		public:
		Bulk_item(const std::string& book, double sales_price,
		std::size_t qty = 0, double disc_rate = 0.0):
		Item_base(book, sales_price),
		min_qty(qty), discount(disc_rate) { }
		// as before
};
Bulk_item bulk("0-201-82470-1", 50, 5, .19);

```

要建立 bulk，首先运行 Item_base 构造函数，该构造函数使用从Bulk_item 构造函数初始化列表传来的实参初始化 isbn 和 price。Item_base构造函数执行完毕之后，再初始化 Bulk_item 的成员。最后，运行 Bulk_item 构造函数的（空）函数体。

构造函数初始化列表为类的基类和成员提供初始值，它并不指定初始化的执行次序。首先初始化基类，然后根据声明次序初始化派生类的成员。

```c++
SensorsComponent::SensorsComponent(PX4AutopilotPlugin *autopilot, QObject *parent)    
	: VehicleComponent(autopilot,parent),
	 _name("Sensors")
	{…… }
```

### 友元关系
像其他类一样，基类或派生类可以使其他类或函数成为友元，**友元可以访问类的 private 和 protected 数据。**

**友元关系不能继承**
- 基类的友元对派生类的成员没有特殊访问权限。
- 如果基类被授予友元关系，则只有基类具有特殊访问权限，该基类的派生类不能访问授予友元关系的类。

```c++
class Base {
		friend class Frnd;
		protected:
			int i;
};
	// Frnd对D1的成员没有访问权限
class D1 : public Base {
  	protected:
			int j;
};
class Frnd {
		public:
			int mem(Base b) { return b.i; } // ok: Frnd是Base的友元
			int mem(D1 d) { return d.i; } // error: 友元关系不能继承，基类的友元对派生类的成员没有特殊访问权限
};
	// D2对Base的成员没有访问权限
class D2 : public Frnd {
  	public:
			int mem(Base b) { return b.i; } //error，友元关系不能继承，友元类的派生类不能访问授予友元关系的类。
};
```

如果派生类想要将自己成员的访问权授予其基类的友元，派生类必须显式地这样做；

如果基类和派生类都需要访问另一个类，那个类必须特地将访问权限授予基类和每一个派生类。

### 方法重载与覆盖
#### 方法重载
这个是发生在编译时的。方法重载也被称为编译时多态，因为编译器可以根据参数的类型来选择使用哪个方法。

```c++
public class {
    public static void evaluate(String param1);  // method #1
    public static void evaluate(int param1);   // method #2
}
```

如果编译器要编译下面的语句的话：

```c++
evaluate(“My Test Argument passed to param1”);
```
它会根据传入的参数是字符串常量，生成调用#1方法的字节码
#### 方法覆盖
这个是在运行时发生的。方法覆盖被称为运行时多态，因为在编译期编译器不知道并且没法知道该去调用哪个方法。JVM会在代码运行的时候做出决定。

```c++
public class A {
    public int compute(int input) { //method #3
	    return 3 * input;
		}
}
public class B extends A {
@Override
		public int compute(int input) { //method #4
			return 4*input;
		}
}
```

子类B中的compute(..)方法重写了父类的compute(..)方法。如果编译器遇到下面的代码：

```c++
public int evaluate(A reference, int arg2)  {
		int result = reference.compute(arg2);
}
```

编译器是没法知道传入的参数reference的类型是A还是B。因此，只能够在运行时，根据赋给输入变量“reference”的对象的类型（例如，A或者B的实例）来决定调用方法#3还是方法#4		

## 多态
### 动态绑定
动态绑定使我们能够编写程序**使用继承层次中任意类型的对象**，无须关心对象的具体类型。使用这些类的程序无须区分函数是在基类还是在派生类中定义的。

在C++中，**通过基类的引用（或指针）** 调用 **虚函数**时，**发生动态绑定**。
基类类型的引用（或指针）既可以指向基类对象也可以指向派生类对象（因为每个派生类对象包含基类部分），这一事实是动态绑定的关键。

用引用（或指针）调用的虚函数在都 **运行时** 确定，被调用的函数是引用（或指针）所指对象的 **实际类型** 所定义的。非虚函数总是在编译时根据调用该函数的对象、引用或指针的类型而确定。
- 只有指定为虚函数的成员函数才能进行动态绑定；
- 必须通过 **基类类型的引用指针** 进行函数调用。

例：

```c++
double print_total(const Item_base&, size_t); //参数为基类类型引用
Item_base item;   //基类对象
Bulk_item bulk;   //派生类对象       
ok: 使用基类的指针或引用指向一个基类/派生类对象
print_total(item, 10);     //传递引用给基类对象
print_total(bulk, 10);     //传递引用给bulk的基类部分
Item_base *p = &item;      //指针p指向一个基类对象          
p = &bulk;                //指针p指向bulk的基类部分
```

基类类型的引用（或指针）既可以指向基类对象也可以指向派生类对象，无论实际对象具有哪种类型，编译器都将它当作基类类型对象。

- 基类类型引用和指针的关键点：
	静态类型：在编译时可知的引用类型或指针类型
	动态类型：指针或引用所绑定的对象的类型仅在运行时可知
	静态类型与动态类型可以不同。

### 覆盖虚函数机制
为了派生类虚函数调用基类中的版本，我们会用到覆盖虚函数机制。
例如，可以定义一个具有虚操作的Camera类层次Camera类中的display函数可以显示所有的公共信息，派生类（如PerspectiveCamera）可能既需要显示公共信息又需要显示自己的独特信息。可以显式调用Camera版本以显示公共信息，而不是在PerspectiveCamera的display实现中复制Camera的操作。

```c++
void PerspectiveCamera:: display（）
{      //do something
    Camera:: display（）；
}  // A是基类，B是A的派生类
```

在这种情况下，已经确切知道调用哪个实例，因此，不需要通过虚函数机制。
或
```c++
Item_base *baseP = &derived; 
//调用基类版本，不用考虑baseP的动态类型。
double d = baseP->Item_base::net_price(42);

```
强制将net_price调用确定为Item_base中定义的版本，该调用在编译时确定。
**只有成员函数中的代码才应该使用作用域操作符覆盖虚函数机制。**

派生类虚函数调用基类版本时，必须显式使用作用域操作符。如果派生类函数忽略了这样做，则函数调用会在运行时确定并且将是一个自身调用，从而导致无穷递归。

### 虚函数与默认实参
在同一虚函数的基类版本和派生类版本中使用不同的默认实参几乎一定会引起麻烦。如果通过基类的引用或指针调用虚函数，但实际执行的是派生类中定义的版本，这时就可能会出现问题。
### 去除个别成员
派生类可以恢复继承成员的访问级别，但不能使访问级别比基类中原来指定的更严格或更宽松。

```c++
class Base {
		public:
			std::size_t size() const { return n; }
		protected:
			std::size_t n;
};

class Derived : private Base {
		public:
		// 保持和size有关的成员的访问级别
			using Base::size;
		protected:
			using Base::n;
	// ...};
```
### 继承与静态成员
如果基类定义 static 成员，则整个继承层次中只有一个这样的成员。无论从基类派生出多少个派生类，每个 static 成员只有一个实例。

static 成员遵循常规访问控制。假定可以访问成员，则可以通过基类或派生类访问 static 成员，一般而言，既可以使用作用域操作符也可以使用点或箭头成员访问操作符。

```c++
struct Base { static void statmem(); // 默认public};
struct Derived : Base { void f(const Derived&); };
	
void Derived::f(const Derived &derived_obj)
{
		Base::statmem();       // ok: Base类定义了statmem
		Derived::statmem();     // ok: Derived派生类继承了statmem
		// ok: 派生类对象能从基类获取static成员
		derived_obj.statmem();  // 通过派生类对象
		statmem();             // 从派生类直接调用
}
```













  


