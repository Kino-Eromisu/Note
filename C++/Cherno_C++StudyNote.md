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
```
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
```
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
```
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
```
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
