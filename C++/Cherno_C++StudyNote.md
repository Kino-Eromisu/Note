# 16.指针
**指针就是地址，是一个保存内存地址的整数，除此之外没有任何额外含义，脑子里只有这一句就行了。** 类型只是某种虚构的便于理解的东西
在代码中做的所有事情都是内存的读写。指针也是变量，这些变量也存储在内存中，所以我们可以有双指针、三指针（指向指针的指针）  

## 指针例子
```cpp
void pointerDemo() {
    int var = 8; // 定义一个var
    void* ptr = &var; // 引用这个var的地址，赋给指针ptr。ptr指向这个地址。void 可以指向任何类型的数据

    std::cout << ptr << std::endl; //0xdeb31ff814，每次运行都可能不一样。这里 ptr = 0x5ffe54
    std::cin.get();
}
```
<img width="1086" height="592" alt="image" src="https://github.com/user-attachments/assets/2c4802ce-889c-438f-a2ef-c53eab92fb2f" />

**地址列**  
最左边列是内存地址：0x00000000005ffe00 等。地址以16字节递增（每行显示16字节）  
**数据列**  
每行分为4组，每组4字节（32位）：  
text  
| 01 00 00 00 | 00 00 00 00 | 80 ee 62 8a | f9 7f 00 00 |  
**字节序（小端序）**  
注意这是小端序存储，低位字节在前：  **为什么是颠倒的？因为电脑的字节存储次序 （元组排列顺序）**
01 00 00 00 实际表示 0x00000001  
f9 7f 00 00 实际表示 0x00007ff9  

以   00 00 00 00   08 00 00 00   54 fe 5f 00   00 00 00 00   │ ········T·_····· │  举例：  
第1组: 00 00 00 00  
小端序：0x00000000  
整数值 0 或空指针  

第2组: 08 00 00 00  
小端序：0x00000008  
整数值 8 -  大小  

第3组: 54 fe 5f 00  
小端序：0x005ffe54  
重要指针：指向地址 0x5ffe54  

第4组: 00 00 00 00  
小端序：0x00000000  
整数值 0 或空指针  

指针可能是16位，32位...但都不重要，它是个整数。

## 如何申请内存并填充
```cpp
void pointerDemo() {
    int var = 8; // 定义一个var
    void* ptr = &var; // 引用这个var的地址，赋给指针ptr。ptr指向这个地址。void 可以指向任何类型的数据
    // *ptr = 10; // 这种是不被允许的，因为void* 不可以赋值，计算机并不理解这个10是什么，它有几位，是一个什么数？这个10可以是任何东西。
    // double* ptr = (double*)&var; // 强制转换为double类型也不是不行

    char* buffer = new char[8];  //char是一个字节，要求这个buffer 有八个字节。
    // 分配了8个字节的内存，并返回了一个指向那块内存开始的指针
    // memset：申请的内存，然后填充内存，需要 # include <cstring>
    memset(buffer, 0, 8); // 我们指定一个memset，用我们指定的数据去填充一个内存块.它会取一个值，比如0，然后取一个大小，也就是8字节
    // 当运行完成，我们应该删去这个内存块
    delete[] buffer; // 这是个数组，所以应该使用[]
    std::cout << ptr << std::endl; //0xdeb31ff814，每次运行都可能不一样
    std::cin.get();
}

```
<img width="1432" height="572" alt="image" src="https://github.com/user-attachments/assets/312d41f3-78a6-4974-8c03-b69d9d891580" />  
我们可以看到前八位都是0。  

## 双指针、三指针  
意味着新的指针被指向另一个指针  
```cpp
void pointerDemo() {
    int var = 8; // 定义一个var
    void* ptr = &var; // 引用这个var的地址，赋给指针ptr。ptr指向这个地址。void 可以指向任何类型的数据
    // *ptr = 10; // 这种是不被允许的，因为void* 不可以赋值，计算机并不理解这个10是什么，它有几位，是一个什么数？这个10可以是任何东西。
    // double* ptr = (double*)&var; // 强制转换为double类型也不是不行

    char* buffer = new char[8];  //char是一个字节，要求这个buffer 有八个字节。
    // 分配了8个字节的内存，并返回了一个指向那块内存开始的指针
    // memset：申请的内存，然后填充内存，需要 # include <cstring>
    memset(buffer, 0, 8); // 我们指定一个memset，用我们指定的数据去填充一个内存块.它会取一个值，比如0，然后取一个大小，也就是8字节
    // 当运行完成，我们应该删去这个内存块

    char** ptr_2 = &buffer;

    delete[] buffer; // 这是个数组，所以应该使用[]

    // std::cout << ptr << std::endl; //0xdeb31ff814，每次运行都可能不一样
    std::cin.get();
}
```
<img width="639" height="249" alt="image" src="https://github.com/user-attachments/assets/0a7e3577-0a6c-434a-8040-95ceccab7c23" />


# 17 引用
引用说白了就是指针的扩展
不占用内存，没有真正的存储空间。引用就是给现有的变量起一个别名，变量和它的引用指向相同的地址。
**不能用引用去引用一个引用**
```cpp
void inforcement(int value) {
    // 也相当于引用，将a引用到这里，作为value
    value++;
    std::cout << value << std::endl;
}

// 引用（Cherno）
void referenceDemo() {
    int a = 10;
    int &ref = a; // ref 只存在在这里，是a的小名，我们如果打印ref，只会得到a
    ref = 20; // 这里通过改变ref，将a改为了20
    inforcement(a);
    std::cout << a << std::endl;
}
```

如果引用的是a的地址，也就是&a，使用的时候需要解引用。结果也是一样的
```cpp
void inforcement(int* value) { //需要进行解引用
    // 如果传递的是一个指针，也就是a的地址，那么我们就可以直接写入这个内存
    (*value)++; //*value 解引用就是逆向引用
    std::cout << *value << std::endl;
}

// 引用（Cherno）
void referenceDemo() {
    int a = 10;
    int &ref = a; // ref 只存在在这里，是a的小名，我们如果打印ref，只会得到a
    ref = 20; // 这里通过改变ref，将a改为了20
    inforcement(&a); // 这里如果改为引用呢
    // std::cout << a << std::endl;
}
```

实在是过于复杂不好看，修改一下：
```cpp
void inforcement(int& value) { // 不需要指针了，引用即可
    // 将a引用到这里，作为value
    value++;
    std::cout << value << std::endl;
}

// 引用（Cherno）
void referenceDemo() {
    int a = 10;
    int &ref = a;
    ref = 20; 
    inforcement(a); // 只引用a
}
```

# 18 类
类就是一个class，将函数和参数规整更美观好看，便于维护这样。
class xxx{};
public部分就是公开的，所有函数可见的。
```cpp
// 引用（Cherno）
void referenceDemo() {
    int a = 10;
    int &ref = a; // ref 只存在在这里，是a的小名，我们如果打印ref，只会得到a
    ref = 20; // 这里通过改变ref，将a改为了20
    inforcement(a);
}

class Player{
public: //在类以外的任何地方都可见
    int x = 1,y = 2;
    int speed = 10;
    void move(int xa, int ya) {
        x += xa * speed;
        y += ya * speed;

        std::cout << "x = " << x << ", y = " << y << std::endl;
    }
}; // class  } need ;

int main(){
    Player player;
    player.move( 1, 10);

    std::cin.get();
}
```

如果我们不将void函数写在class内，要如何使用class中的参数？可以用，一个引用就可以了..但真的很麻烦啊。
```cpp
class Player{
public: //在类以外的任何地方都可见
    int x = 1,y = 2;
    int speed = 10;
}; // class  } need ;

void move(Player& player, int xa, int ya) {
    player.x += xa * player.speed;
    player.y += ya * player.speed;

    std::cout << "x = " << player.x << ", y = " << player.y << std::endl;
}
int main(){
    Player player;
    move(player, 1, 1);

    std::cin.get();
}

```

## class 中的静态参数 static
在类或结构体外使用static关键字，和在类或结构体内部使用static  
外面的static，意味着声明的static的符号链接只在其内部，只能对定义它的单元可见  
内部的static，意味着这个变量实际上将于类的所有实例共享内存  
**外面的只能自己用， 类内的表示这个属性跟实例无关，是公共的，所有对象共有的**
static只能在被声明的cpp文件中被“看到”
**我觉得可以理解为虚空袋、小猪储钱罐。公开的则是在箱子里的东西**

## static、struct、class
### static 
**静态局部变量(函数内部)**
作用：延长局部变量的生命周期，使其在程序整个生命周期内都存在，但作用域保持不变（仅在定义它的函数内可见）  
初始化：只在第一次执行到该语句时被初始化  
内存：存储在全局数据区，而不是栈上  
```cpp
void counter() {
    static int count = 0; // 只初始化一次
    count++;
    std::cout << count << std::endl;
}

int main() {
    counter(); // 输出 1
    counter(); // 输出 2
    counter(); // 输出 3
    // 每次调用counter()，使用的都是同一个count变量
    return 0;
}
```

**静态成员变量 (在类内部)**
作用：属于类本身，而不是类的某个特定对象。所有该类的对象共享同一份静态成员变量  
内存：在程序开始时分配内存，与是否创建对象无关  
定义：在类内部声明，必须在类外部定义（分配存储空间）  
```cpp
class MyClass {
public:
    static int staticVar; // 声明
};

int MyClass::staticVar = 10; // 定义和初始化（必须放在类外）

int main() {
    MyClass obj1, obj2;
    obj1.staticVar = 20;
    std::cout << obj2.staticVar; // 输出 20，因为obj1和obj2共享同一个变量

    // 更推荐的访问方式：通过类名直接访问
    MyClass::staticVar = 30;
    std::cout << MyClass::staticVar; // 输出 30
    return 0;
}
```

**静态成员函数 (在类内部)**
作用：属于类本身的函数，而非对象。它不能访问类的非静态成员（变量或函数），因为它没有 this 指针  
调用：可以通过类名直接调用，也可以通过对象调用（但不推荐）  
```cpp
class MyClass {
private:
    static int staticVar;
    int normalVar = 5;
public:
    static void staticFunction() {
        staticVar = 100; // 正确：可以访问静态成员
        // normalVar = 10; // 错误！不能访问非静态成员
        std::cout << "This is a static function.\n";
    }
};

int MyClass::staticVar = 0; // 定义

int main() {
    MyClass::staticFunction(); // 无需创建对象，直接通过类名调用
    return 0;
}
```

**静态全局变量/函数 (在文件作用域)**
作用：当用于全局变量或函数时，static 将其链接性改为内部链接。这意味着该变量或函数只在定义它的文件内可见，其他文件无法通过 extern 引用它。这可以避免命名冲突。  
```cpp
File1.cpp

cpp
static int fileLocalVar = 42; // 只能在 File1.cpp 中使用
static void fileLocalFunction() { ... } // 只能在 File1.cpp 中调用
```
```cpp
File2.cpp

cpp
extern int fileLocalVar; // 错误！链接器找不到，因为fileLocalVar在File1中是static的
```

### class
封装了数据（成员变量） 和操作数据的方法（成员函数）  
默认private。如果未指定访问修饰符，成员默认是私有的。  
三大支柱：  
封装：将数据和函数绑定在一起，并控制对其的访问（通过 public, private, protected）。  
继承：允许一个类（派生类）基于另一个类（基类）来创建，继承其特性和行为。  
多态：允许使用统一的接口来处理不同的派生类对象。  
```cpp
class Animal {
private: // 私有成员，只能在类内部访问
    std::string name;

protected: // 保护成员，类内部和派生类中可以访问
    int age;

public: // 公有成员，任何地方都可以访问
    Animal(const std::string& n, int a) : name(n), age(a) {} // 构造函数

    // 成员函数
    void speak() const {
        std::cout << name << " makes a sound." << std::endl;
    }

    // 虚函数，为多态做准备
    virtual void eat() {
        std::cout << name << " is eating." << std::endl;
    }

    // 获取私有成员的值（Getter）
    std::string getName() const {
        return name;
    }
};

class Dog : public Animal { // 公有继承
public:
    Dog(const std::string& n, int a) : Animal(n, a) {}

    // 重写基类的虚函数（多态）
    void eat() override {
        std::cout << getName() << " eats dog food." << std::endl; // 通过公有接口访问私有name
    }

    // 派生类特有方法
    void bark() {
        std::cout << "Woof! Woof!" << std::endl;
    }
};

int main() {
    Dog myDog("Buddy", 3);
    myDog.speak(); // 输出: Buddy makes a sound.
    myDog.eat();   // 输出: Buddy eats dog food. (多态)

    // myDog.name = "Max"; // 错误！name是private的
    // myDog.age = 4;      // 错误！在类外，age是protected的

    return 0;
}
```

### struct
默认public。
```cpp
// 使用 struct 定义一个简单的数据容器
struct Point {
    // 默认是 public
    int x;
    int y;

    // struct 也可以有成员函数和构造函数
    void print() {
        std::cout << "(" << x << ", " << y << ")" << std::endl;
    }
};

int main() {
    Point p1;
    p1.x = 10; // 直接访问，因为默认是 public
    p1.y = 20;
    p1.print();

    return 0;
}
```

使用 struct：当你需要的是一个主要是公有数据的被动数据结构（POD - Plain Old Data），几乎没有或完全没有成员函数，或者只是简单的 getter/setter。例如：坐标点、配置参数、返回多个值的函数结果。  
使用 class：当你需要的是一个具有完整封装、复杂行为、不变量的主动对象。大部分面向对象的设计都使用 class。  

# 24 枚举（ENUM）
枚举ENUM全称为enumeration，基本上就是一个数值集合，是给一个值命名的一种方法，但必须是**整数**。  
<img width="711" height="799" alt="image" src="https://github.com/user-attachments/assets/556d7860-c390-43d7-a4a9-9248de62b525" />

