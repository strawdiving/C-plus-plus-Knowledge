## typedef

typedef通常被用于以下三种目的：

- 为了隐藏特定类型的实现，强调使用类型的目的。

- 简化复杂的类型定义，使其更易理解。

- 允许一种类型用于多个目的，同时使得每次使用该类型的目的明确。

typedef并没有创建新类型，只是为某个已存在的类型增加了一个新的名称；通过这种方式声明的变量与通过普通声明方式声明的变量具有完全相同的属性。类似于#define，但typedef是由编译器解释的，所以它的文本替换功能要超过预处理器的能力。

#### 用途一

定义一种类型的别名，而不只是简单的宏替换。

```c
typedef int* Length;
Length len, maxlen;
Length lengths[];
```

typedef中声明的类型在变量名的位置出现。

#### 用途二 

用于结构体。

```c
typedef struct tnode *Treeptr;
typedef struct tnode {
	char *word;
	int count;
	struct tnode *left;
	struct tnode *right;
} Treenode;

Treeptr talloc(void)
{
	return (Treeptr)malloc(sizeof(Treenode));
}
```

即创建了两个新类型关键字：Treenode（一个结构体）和Treeptr（一个指向该结构的指针）

#### 用途三

用typedef来定义与平台无关的类型。

比如定义一个叫 REAL 的浮点类型，在目标平台一上，让它表示最高精度的类型为： **typedef long double REAL** ; 

在不支持 long double 的平台二上，改为： **typedef double REAL** ; 

在连 double 都不支持的平台三上，改为： **typedef float REAL** ; 

也就是说，当跨平台时，只要改下 typedef 本身就行，不用对其他源码做任何修改。标准库就广泛使用了这个技巧，比如size_t。

另外，因为typedef是定义了一种类型的新别名，不是简单的字符串替换，所以它比宏来得稳健（虽然用宏有时也可以完成以上的用途）。

#### 用途四

为复杂的声明定义一个新的简单的别名。

1. typedef  (*)(....) 函数指针 

```c
typedef int (*PFI)(char *, char *);
PFI strcmp, numcmp;
```

声明了一个指向函数的指针，该函数具有两个char*类型的参数，返回值类型为int。

1. typedef  (*)[] 数组指针

```c
int (a[5])(int, char*);
typedef int (pFun)(int, char*);
pFun a[5]
```

声明了一个函数指针数组，用pFun替换函数指针。