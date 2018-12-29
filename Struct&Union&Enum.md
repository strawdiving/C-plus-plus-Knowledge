## 结构体struct

### 声明和定义

结构体声明，描述了组成结构体对象的元素，创建了一个新的类型，但并没有创建一个实际的数据对象，不会分配内存。book为可选的标记。

```c++
struct book {
    char title;
    char author;
    float value;
}; //注意分号
```

定义一个使用该结构设计的结构体变量，因为结构是一种新的数据类型，在定义结构体变量时，struct可以省略。编译器使用book模板为该变量分配内存空间。

```c++
book library;
或
struct book {
    char title;
    char author;
    float value;
} library; //定义之后跟变量名
```

将声明和定义合并在一起，可以不需要使用标记。

```c++
struct {  //无标记
    char title;
    char author;
    float value;
} library;
```

如果想多次使用一个结构模板，就需要使用带有标记的形式，或者使用typedef。

#### 初始化

```c++
struct book library = {
    “Hero”,
    “Renee Vivotte”,
    1.95
} ;
```

用大括号括起来，以逗号分隔的表达式表就是结构体的初始化表，其中的每一项分别初始化对应的结构成员。初始化表达式的数目可以少于结构成员的数目，但决不可以多。表达式数目不足（至少提供一个），编译器自动把其余的结构成员初始化为0。

#### 结构体的嵌套

结构的成员可以是另一个结构。引用内层结构的成员时要包含两个结构变量的名字。

```c++
struct Employee { …;…;Date dateHired;};
Date date;
Employee employee;
employee.date.month //引用成员
```

### 结构体数组和结构体指针

```c++
struct book librarys[MAX];  // 声明结构体数组
library[0].value;          // 通过结构体对象访问第1个数组元素结构体的value成员
struct book *it;          // 声明结构体指针
// 一个结构体的名字不是该结构体的地址，必须使用&运算符
it = &library;
it = & librarys[0];
it->value     // 指针访问结构体的成员
(*it).value    // 通过结构体对象访问其成员
```

### 结构体的大小

在一些系统中，结构体的大小可能大于它内部各成员大小之和，因为系统数据的对齐存储需求会导致系统可能必须把每个偶数地址的成员放在是4的倍数的地址上。这样的结构就可能在其内部存在存储缝隙。

### 向函数传递结构信息

1. 传递结构成员

```c++
add( library.title, library.author);
```

2. 使用结构地址，传递指向结构的指针

```c++
void add(const struct book* );
……
add( & library);
```

 **注：必须使用&运算符才能得到结构体的地址！** 和数组名不一样，单独的结构名不是该结构地址的同义词。

对于大的结构，通常采用结构指针或者引用型变量，让调用和被调用函数共用内存中的结构。因为实参是存放在堆栈里的，程序要传递很大的数据对象是很麻烦的。

3. 传递结构体的拷贝——把结构作为参数传递

因为被调用函数有可能会改变调用函数要求保留的原始数据。

```c++
void add( struct book library);
……
add(library);
```

如果是同一类型的结构，允许把一个结构赋值给另一个结构，但数组不能。结构不仅可以作为参数传递给函数，也可以作为函数返回值返回。

有时候函数会返回一个结构体，如果函数生成的待返回的结构体是一个自动变量，函数返回时，自动结构体变量就已经离开了它的作用域，这时程序就需要一个用于返回的拷贝。

```c++
struct book library;
library = getinfo();
….
struct book getinfo(void) {
    struct book temp;
    ….
    return temp;
}
```

## 联合Union

结构体定义了一个相关数据的集合，而联合定义了 **一块为所有数据成员共享的内存** 。union 主要用来压缩空间。如果一些数据不可能在同一时间同时被用到，则可以使用union。

它的所有成员相对于基地址的偏移量都为0，即所有成员的首地址都是一样的。其必须符合所有成员的自身对齐方式。 **联合的大小等于最大的成员的大小。** 

一个 union 对象可以有多个数据成员，但在任何时刻只能有一个值，它属于某一个数据成员。改变联合中的一个成员，别的成员也会随之改变。当给某一个成员赋值时，别的成员的值也会有一致的含义，因为它们的值的每一个二进制位都被新赋的值所覆盖。

### 联合的定义

联合提供了便利的办法来表示一组相互排斥的值，这些值可以是不同类型的。

```c
union TokenValue {
    char cval;
    int ival;
    double dval;
};
```

除非另外指定，否则 union 的成员都为 public 成员。

联合的成员必须为简单类型：内置类型，复合类型，没有定义构造函数、析构函数或赋值操作符的类类型。

联合可以有成员函数，包括构造函数和析构函数。联合不能作基类使用。

union 不能具有静态数据成员或引用成员，而且，union 不能具有定义了构造函数、析构函数或赋值操作符的类类型的成员：

```c++
union illegal_members {
    Screen s;       // error: has constructor
    static int is; // error: static member
    int &rfi;       // error: reference member
    Screen *ps;    // ok: ordinary built-in pointer type
};
```

#### 初始化

像其他内置类型一样，默认情况下 union 对象是未初始化的。

因为联合只存储一个值，所以初始化的规则和结构体不同，有2种选择：

- 可以把一个联合初始化为同样类型的另一个联合

- 只能对第一个成员变量进行初始化。初始化式必须括在一对花括号中，类型必须和第一个成员相一致。

  ```c++
  TokenValue first_token = {'a'}; 
  ```

  如果第一个成员是一个结构，那么初始化值中可以包含多个用于初始化该结构的表达式。

  ```c++
  Holder hld = { {6,24,1940} };
  ```

### 联合体的大小

像任何类一样，union 类型定义了与 union 类型的对象相关联的内存是多少。每个 union 对象的大小在编译时固定的：它至少与 union 的最大数据成员一样大。

```c
union U
{
    char s[9];
    int n;
    double d;
};
```

sizeof (u1) =16。s占9字节，n占4字节，d占8字节，因此其至少需9字节的空间。然而其实际大小为16。这是因为这里存在字节对齐的问题，9既不能被4整除，也不能被8整除。因此补充字节到16，这样就符合所有成员的自身对齐了。

```c
union U2
{
    char s[5];
    int n;
    double d;
};
```

sizeof (u2) =8。因为u2中s占5字节，n占4字节，d占8字节，因此至少需要8字节。其包含的基本数据类型为char，int，double分别占1，4，8字节，为了使u2所占空间的大小能被1，4，8整除，不需填充字节，因为8本身就能满足要求。

从这里可以看出联合体所占的空间不仅取决于最宽成员，还跟所有成员有关系。即其大小必须满足两个条件：

- 大小足够容纳最宽的成员；
- 大小能被其包含的所有基本数据类型的大小所整除。

### 使用联合的成员

可以使用普通成员访问操作符（. 和 ->）访问 union 类型对象的成员：

```c
last_token.cval = 'z';
pt->ival = 42;
```

给 union 对象的某个数据成员一个值，使得其他数据成员变为未定义的。

使用 union 对象时，我们必须总是知道 union 对象中当前存储的是什么类型的值。通过错误的数据成员检索保存在 union 对象中的值，可能会导致程序崩溃或者其他不正确的程序行为。

避免通过错误成员访问 union 值的最佳办法是， **定义一个单独的对象跟踪 union 中存储了什么值** 。这个附加对象称为 union 的判别式。经常使用 switch 语句测试判别式，然后根据 union 中当前存储的值进行处理：

```c
class Token {
    public:
	enum TokenKind {INT, CHAR, DBL};
	TokenKind tok;  //用枚举对象 tok 指出 val 成员中存储了哪种值
	union { // unnamed union
	    char cval;
	    int ival;
	    double dval;
	} val; // member val is a union of the 3 listed types
};
Token token;
switch (token.tok) {
    case Token::INT:
	token.val.ival = 42; break;
    case Token::CHAR:
	token.val.cval = 'a'; break;
    case Token::DBL:
	token.val.dval = 3.14; break;
}
```

### 匿名联合

当不需要使用联合的名字时（不用于定义对象），就可以用匿名联合的这个特性省略掉很多联合的名字。对于全局匿名联合，必须声明为静态的。匿名 union 的成员的名字出现在外围作用域中。

```c
struct test_struct { 
    char *name; 
    union {   //匿名联合
   	char gender; 
    	int id; 
    }; 
    int num; 
}; 
// 结构体变量test_struct直接使用联合体中的成员
   ……
struct test_struct test_struct = {"tanglinux", 'F', 28 }; 
printf("test_struct.gender = %c, test_struct.id = %d\n",   test_struct.gender, test_struct.id); 
……
```

**因为匿名 union 不提供访问其成员的途径，所以将成员作为定义匿名union 的作用域的一部分直接访问。**

重写前面的 switch 以便使用类的匿名union 版本，如下：

```c
class Token {
    public:
	enum TokenKind {INT, CHAR, DBL};
	TokenKind tok;
	union {  //匿名联合
	    char cval;
	    int ival;
	    double dval;
	};
};

Token token;
switch (token.tok) {
    case Token::INT:
	token.ival = 42; break;
    case Token::CHAR:
	token.cval = 'a'; break;
    case Token::DBL:
	token.dval = 3.14; break;
}
```

匿名 union 不能有私有成员或受保护成员，也不能定义成员函数。

## 枚举常量enum

利用枚举声明可以定义枚举常量，也是一种数据类型。一个枚举常量包括一组相关的标识符，其中每一个标识符对应一个整型值。

在这些枚举常量中，大括号内第一个标识符对应数值0，第二个对应1，依次类推。在声明枚举常量时，可以为某个特定的标识符指定其对应的整型值，紧随其后的标识符对应的值则依次加1。

```c
 enum flag { x =1 , y , z , e};
```

每个标识符都必须是唯一，而且不能采用保留的关键字或当前作用域内的其他任何标识符。

```c
enum spectrum { red, orange, yellow } ;
enum spectrum color ;
switch (color) {
   case red:
       //do sth
       break;
   case green :
       //do sth
       break;
}
```
