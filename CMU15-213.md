# CMU15-213

## lecture-01 Course Overview

### Great Reality #1 数据在系统中的表示

当计算整形50000* 50000时，计算机输出的结果是一个负数而非我们期望的结果，同样的情况也出现在300\*400\*500\*600，结果虽然不是负数，但也不是我们所预期的，这两种情况都是因为得到的结果溢出了。

![image-20220620080425192](C:\Users\gavin\AppData\Roaming\Typora\typora-user-images\image-20220620080425192.png)

通过上面的结果可以看到，不管结果是否溢出，计算是符合交换律的

![image-20220620080642778](C:\Users\gavin\AppData\Roaming\Typora\typora-user-images\image-20220620080642778.png)

### Great Reality #2 理解编译器如何将高级语言编译为汇编语言

### Great Reality #3 理解内存的管理策略并避免相关问题

而在浮点数中，加法的交换律和结合律似乎失效了。这是因为我们期望整形是溢出的，而浮点型是舍入的，因此就会产生这样的问题。

![image-20220620081329830](C:\Users\gavin\AppData\Roaming\Typora\typora-user-images\image-20220620081329830.png)

![](C:\Users\gavin\AppData\Roaming\Typora\typora-user-images\image-20220620081506202.png)



上图一个格子表示4个字节 

在执行fun(1)和fun(2)时，程序正确的访问了内存，而在fun(3)时，指针指向了double类型的d的地址，实际上修改了d的值，因此会返回非预期的值，直到fun(6)指向并修改了某些程序执行的必需内容，程序发生了崩溃。



### Great Reality #4 程序的更底层优化

![image-20220620082325324](C:\Users\gavin\AppData\Roaming\Typora\typora-user-images\image-20220620082325324.png)

第一种先复制行再复制列比第二种先复制列再复制行的方法效率提高了很多

### Great Reality #5 计算机的互联

## lecture-02 Bits,Bytes, and Integers

![image-20220620090007730](C:\Users\gavin\AppData\Roaming\Typora\typora-user-images\image-20220620090007730.png)

![image-20220620090734583](C:\Users\gavin\AppData\Roaming\Typora\typora-user-images\image-20220620090734583.png)

![image-20220620091123968](C:\Users\gavin\AppData\Roaming\Typora\typora-user-images\image-20220620091123968.png)

做位运算时，逻辑左移和算数左移都是相同的操作，给定参数之后，溢出的高位消失不见，低位则补零。

而逻辑右移也是这样的操作，但当算数右移时，会将左侧填充符号位.

那如果左移8位，则大部分机器都会得到0

![image-20220620092532786](C:\Users\gavin\AppData\Roaming\Typora\typora-user-images\image-20220620092532786.png)

如果是有符号数，则最高位表示-2^w-1^,因此最小值为最高位取一，其余位取零，最大值为最高位取0，其余位取1

UMax无符号数最大值，TMax补码最大值，TMin补码最小值

![image-20220620093121118](C:\Users\gavin\AppData\Roaming\Typora\typora-user-images\image-20220620093121118.png)

![image-20220620093252147](C:\Users\gavin\AppData\Roaming\Typora\typora-user-images\image-20220620093252147.png)

相同的位模式在无符号数和补码表示中的结果不同，

![image-20220620094606369](C:\Users\gavin\AppData\Roaming\Typora\typora-user-images\image-20220620094606369.png)

如果要做两者之间的转换，如T2U，如果最高位为1，则将作为+2^m-1^重新计算值再相加，而U2T则将最高位作为-2^m-1^重新计算值再相加

![image-20220620095157590](C:\Users\gavin\AppData\Roaming\Typora\typora-user-images\image-20220620095157590.png)

两个值作比较时，如果这两者得表示方法不同，即一个是unsigned，另一个是signed，则需要转换再作比较，==注意其中的第三个例子==，signed -1与unsigned 0做比较，则内部会将-1先转换为unsigned类型，这个转化就是前面所说的T2U，（假设是五位的），在signed的表示下，其11111的位模式为-1(-16+8+4+2+1)，而在unsigned的表示下，其11111的位模式为31(16+8+4+2+1)，则比较的结果就是-1>0U

需要再看一下第四行第二个值得表达方式，ok,第一个值为TMax，他的位模式为(0111...1)，则TMin就是(1000....0)则TMin就是 -(TMAX)-1,两者有TMin+TMax=-1的关系，这是很好理解的，因为两者的位模式假设不涉及符号，则TMax(0111...1)+1=TMin(1000...0)，当然是有符号的，那就是==TMax(0111...1)+1=-(TMin(1000...0))==，就是一个位的关系。==因此我们用第四行第二种形式来表示TMin==

==UMax=TMax*2+1==，这也不难理解，Tmax(0111...11)的最高位为0，而转换为Umax则不需要他是0了，那我们左移一位Tmax（1.....10）然后再+1(1.....11)就得到了位模式全为1的最大值，即前面的公式

![image-20220620102242174](C:\Users\gavin\AppData\Roaming\Typora\typora-user-images\image-20220620102242174.png)

符号拓展，举两个例子(signed)：

* 原4位位模式为(0111)，拓展其符号位(00111)，仍然为7，不改变值

* 原4位位模式位(1110)，拓展其符号位(11110)，仍然为-2，不改变值，拓展的位与原最高位之和就等于原最高位(-16+8=-8)，这个是很正常的，可以再拓展一下(111110)，则(-32+16+8)=-8,结果是一样的，拓展的位其值还是原来的最高位

  因此想要进行位拓展，只要很简单的拓展最高位就好了，请放心操作，不会影响原来的值。

  而对于无符号数，只要在左侧填充零就好了

![image-20220620102809661](C:\Users\gavin\AppData\Roaming\Typora\typora-user-images\image-20220620102809661.png)

而对于缩短数字，比如无符号数(11011)，将其变为4位，则直接去掉其最高位即可(1011)，数值从27变为了11，我们可以很简单的的理解为取模操作，取得模为最高位的值(2^5-1^=16)，得到了11，如果将其变为3位，则就是模(2^4-1^=8)，得到的结果为(011)=3了。

而对于有符号数,这种运算很可能改变符号，（当然作为位拓展的反向操作的话，值都不会发生改变）