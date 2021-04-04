# 2021.3.30

# Csapp

找到了相关教学视频

 ![img](file:///C:\Users\86182\AppData\Roaming\Tencent\QQTempSys\8LDO48C$8@[GWU0353$FOVS.png)https://www.bilibili.com/video/BV1iW411d7hd?p=2

## Data Lab

*实验要求：将每个函数中的“return”语句替换为一个*

*一行或多行实现该函数的C代码。你的代码必须符合以下样式：*



```
      int Funct(arg1, arg2, ...)
  {
      /* brief description of how your implementation works */
      int var1 = Expr1;
      ...
      int varM = ExprM;

      varJ = ExprJ;
      ...
      varN = ExprN;
      return ExprR;
  }
```

*每个“Expr”都是一个表达式，仅使用以下内容：*

*1整数常量0到255（0xFF），包括在内。你是*

*不允许使用大常量，如0xffffff。*

*2函数参数和局部变量（没有全局变量）。*

*三。一元整数运算！~*

*4二进制整数运算&^ |+<>>*


*有些问题进一步限制了允许的运算符集。*

*每个“Expr”可以由多个运算符组成。你不局限于*

*每条线一名操作员。*

*有些问题进一步限制了允许的运算符集。*

*每个“Expr”可以由多个运算符组成。你不局限于*

*每条线一名操作员。*


*明确禁止您：*

*1使用任何控件构造，如if、do、while、for、switch等。*

*2定义或使用任何宏。*

*三。在此文件中定义任何其他函数。*

*4调用任何函数。*

*5使用任何其他操作，如&&、| |、-、或？:*

*6使用任何形式的铸造。*

*7使用int以外的任何数据类型*

*不能使用数组、结构或联合。*

*您可以假设您的机器：*

*1使用2s补码，32位整数表示。*

*2以算术方式执行右移。*

*三。如果移位量*

*小于0或大于31。*

*浮点编码规则*


*对于需要实现浮点运算的问题，*

*编码规则没有那么严格。您可以使用循环和*

*条件控制。允许同时使用整数和无符号。*

*可以使用任意整数和无符号常量。你可以用任何算术，*

*对整型或无符号数据执行逻辑或比较操作。*


*明确禁止您：*

*1定义或使用任何宏。*

*2在此文件中定义任何其他函数。*

*三。调用任何函数。*

*4使用任何形式的铸造。*

*5使用除int或unsigned以外的任何数据类型。这意味着你*

*不能使用数组、结构或联合。*

*6使用任何浮点数据类型、操作或常量。*



*笔记：*

*1使用dlc（data lab checker）编译器（如讲义中所述）来*

*检查你的解决方案的合法性。*

*2每个函数都有一个最大操作数（整数、逻辑、，*

*或比较），您可以将其用于实现*

*函数的一部分。dlc检查最大运算符计数。*

*注意赋值（'='）不计算在内；您可以使用*

*这些是你想要的，没有惩罚。*

*三。使用btest测试线束检查您的功能是否正确。*

*4使用BDD检查器正式验证您的函数*

*5中给出了每个函数的最大操作数*

*每个函数的标题注释。如果有任何不一致之处*

*在writeup和此文件中的最大操作数之间，请考虑*

*此文件是权威来源。*

*为避免意外评分：*

* 1. *使用dlc编译器检查您的解决方案是否符合*

**编码规则。*

* 2. *使用BDD检查器正式验证您的解决方案是否*

**正确答案。*



第一题：要求构造异或

```
 * bitXor - x^y using only ~ and & 
 *   Example: bitXor(4, 5) = 1
 *   Legal ops: ~ &
 *   Max ops: 14
 *   Rating: 1
 */
int bitXor(int x, int y) {
  return 2;
```

运算的最好方法是把数字变成二进制表示，然后运算，4的二进制为0100，5的二进制位0101，4^5=1

```
把所有结果算一下：
~x=                  11111011
~y=                  11111010
x & y=               00000100
~x & ~y=             11111110
~(x & y)=            11111011
~(~x & ~y)=          00000001
~(x & y)& ~(~x & ~y)=00000001
```

 所以只要最后一位都是1，再进行按位与运算就能算出来两个数按位异或的结果

先用按位与运算算出两个数都为1的位置，然后再用两个数取反值的按位与运算算出两个数都为0的位置

这时两个数进行与运算肯定都为0，所以最后剩下的不同的位就是两个数不同的位了，并且剩下的不同位进行与运算的值肯定为0，所以再都取反，再进行与运算，就都变成1了，也就是异或的结果，所以最后答案：

```
int bitXor(int x, int y)
{return ~(x&y) & ~(~x & ~y)}
```

安装32位库

```
sudo apt-get install gcc-multilib
```

然后把语句修改进去，然后

```
make
```

```
./btest
```

每次重新验证的时候还要make一次

![1617099674974](C:\Users\86182\Desktop\MD文档\Nep.git\1.png)



第二题：要求算出最小补码值

```
/* 
 * tmin - return minimum two's complement integer 
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 4
 *   Rating: 1
 */
int tmin(void) {

  return 2;

}
```

在c语言中补码的意思：

 补码主要是为了cpu运算器在进行减法运算时避免借位而设立的，  因为计算机中的数值编码是有限位数的，所以减法实际上相当于加上减数的补码，而乘法是循环的加法，除法是循环的减法。这种思想在数学上叫转化思想 

-1的原码：1000 0001，最高位是符号为，1表示负数，0表示正数。
-1的反码：1111 1110, 按位取反是除符号位以外，其它每个位上的0变成1，1变成0。
-1的补码：1111 1111，在反码的基础上是加上1即为补码。
1-1 = 1+ 1111 1111 = 1  0000 0000 ，因为只有8位的二进制表示方法，此时溢出了，溢出位在硬件上是没法表示的，因此结果还是0. 

int类型的取值范围是：

```
-2147483648~2147483647[-2^31~2^31-1]
```

所以补码最小值的二进制表示为 1000 0000 0000 0000 0000 0000 0000 0000 对应的十六进制为 0x8000 0000，也就是 -2147483648 。

写出脚本：

```
int tmin(void)
{return 1<<31;}
```

![1617104541386](C:\Users\86182\Desktop\MD文档\Nep.git\2.png)



第三题：要求得到最大的补码值，

```
int isTmax(int x) {
  return 2;
}
/* 
 * allOddBits - return 1 if all odd-numbered bits in word set to 1
 *   where bits are numbered from 0 (least significant) to 31 (most significant)
 *   Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 2
 */
```

和上题相反，int类型的取值为

```
-2147483648~2147483647[-2^31~2^31-1]
```

所以最大的补码值为：

```
 0x7fffffff
```

也就是符号位为0，其他全是1，

 如果可以使用 << 位运算符的话，就可以这么写 return !(x ^ ~(1 << 31)); 

 但是这里限制了，  因为 0x7fffffff + 1 == 0x80000000 且 0x80000000 * 2 == 0 那么我们可以判断 x + x + 2 的结果是不是 0：如果是 0，那么就满足 x 的值为 0x7fffffff 但是这样又有一个特例 -1 ，我们可以靠 !(~x) 来检测这个数是否为 -1  

 因为 ~(-1) == 0 ，所以如果写成 !(x + x + 2 | !(~x)) 就可以检测 x 是否为 -1 了 那么这个式子就是完整的式子了，写出脚本：

```
int isTmax(int x) 
{return !((x + x + 2) | !(~x));}
```

![1617106600570](C:\Users\86182\Desktop\MD文档\Nep.git\3.png)

有一个小例子

```
10000 -16

01111 15
```



第四题： 判断所有奇数位是否都为1，这里的奇数指的是位的阶级是2的几次幂 

```
/* 
 * allOddBits - return 1 if all odd-numbered bits in word set to 1
 *   where bits are numbered from 0 (least significant) to 31 (most significant)
 *   Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 2
 */
int allOddBits(int x) {
  return 2;
}
```

```
转成二进制
bin(int('0xFFFFFFFD',16))=11111111111111111111111111111101
bin(int('0xAAAAAAAA',16))=10101010101010101010101010101010
```

也可以gdb转二进制

![1617163990253](C:\Users\86182\Desktop\MD文档\Nep.git\4.png)

构造奇数位都是0偶数位都是1的数![1617169077112](C:\Users\86182\Desktop\MD文档\Nep.git\5.png)

然后，我们可以和 x 做按位或运算来得到一个二进制序列 ，因为 0x55555555 的奇数位上都为 0，那么这个方法就可以测试 x 的奇数位上是不是都为 1  如果 x 上有一个奇数位不为 1，那么对应结果上的位上面的值就是 0 ，如果 x 上的奇数位都为 1，那么对应的结果必定是 -1 ，再让他返回1就行了。

所以先按位异或，如果最终结果为-1，那么机会变成0，再用逻辑异或将返回值改为 1 和 0，实验要求常量不能大于 0xff，所以要改一下 ，写出脚本：

```
int allOddBits(int x) 
{return !(~(x | (0x55 + (0x55 << 8) + (0x55 << 16) + (0x55 << 24))));}
```

![1617169715005](C:\Users\86182\Desktop\MD文档\Nep.git\6.png)



第四题：要求 不使用 -操作符，求 -x 值。

```
/* 
 * negate - return -x 
 *   Example: negate(1) = -1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 5
 *   Rating: 2
 */
int negate(int x) {
  return 2;
}
```

举一个例子：

```
4的二进制数为：  0100
-4的二进制数为： 1100
4的补码为：     1011
```

因为负数就是通过正数的补码加一得到的，所以可以写出脚本：

```
int negate(int x)
{return (~x+1);}
```

![1617235205243](C:\Users\86182\Desktop\MD文档\Nep.git\7.png)



第五题：要求计算输入值是否是数字 0-9 的 ASCII 值 

```
//3
/* 
 * isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')
 *   Example: isAsciiDigit(0x35) = 1.
 *            isAsciiDigit(0x3a) = 0.
 *            isAsciiDigit(0x05) = 0.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 15
 *   Rating: 3
 */
int isAsciiDigit(int x) {
  return 2;
}
```

![1617235390360](C:\Users\86182\Desktop\MD文档\Nep.git\8.png)



我们要满足的是(x-0x30>=0)&&(x-0x39<=0),可以仿照上一题通过相反数来实现，如果一个数大于0，那么它和0x1<<31做且运算的结果为0，写出脚本：

```
int isAsciiDigit(int x)
{int a=0x1<<31;
 return !((x+ ~0x30+1)&a)& !((0x39+ ~x+1)&a);}
```



![1617237808836](C:\Users\86182\Desktop\MD文档\Nep.git\9.png)



第六题：使用位级运算实现C语言中的 x?y:z三目运算符。

```
/* 
 * conditional - same as x ? y : z 
 *   Example: conditional(2,4,5) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 16
 *   Rating: 3
 */
int conditional(int x, int y, int z) {
  return 2;
}
```

这个表达式的意思是如果x不为零，那么返回y ，否则返回z，最后要输出y和z，所以要构造一个判断的条件，~!x+1可以满足;

```
x==0,~!x+1=0x000000
x!=0,~!x+1=0xffffff
```

所以写出脚本：

```
int conditional(int x, int y, int z) {
int a=~!x+1;
return(y & ~a)|(z & a);}
```



![1617240982262](C:\Users\86182\Desktop\MD文档\Nep.git\10.png)



第七题：要求函数能够判断x和y的大小值

```
/* 
 * isLessOrEqual - if x <= y  then return 1, else return 0 
 *   Example: isLessOrEqual(4,5) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3
 */
int isLessOrEqual(int x, int y) {
  return 2;
}
```



将问题转换成判断 a+b≤0,当 a+b<0时，其符号位为 1，因此执行 a+b再取出符号位就可以了，但是存在a+b溢出导致符号位为1的情况

进一步地，当a和b的符号相同时，当a+b<0时仅当a和b都符号位都是1，符号位相同时，不存在溢出的情况，要特别判 x=y

```
int isLessOrEqual(int x, int y) {
return (!(x^y)) | ((x&~y)>>31&1) | ((!((x^y)>>31))&((x+(~y+1))>>31));}
```



![1617252985457](C:\Users\86182\Desktop\MD文档\Nep.git\11.png)



第八题：要求使用位级运算求逻辑非 

```
//4
/* 
 * logicalNeg - implement the ! operator, using all of 
 *              the legal operators except !
 *   Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
 *   Legal ops: ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 4 
 */
int logicalNeg(int x) {
  return 2;
}
```

也就是说构造函数，非0为1，0就返回0。首先想到的方法是让x和0x1做&运算，想到了第一种方法

```
int logicalNeg(int x) {
return !!(x&1);}
```

验证了一下，没有通过，因为只要被验证的数的asc码的第一位不是1，结果就都是0，所以得换方法

重新想一下

逻辑非就是非0为1，非非0为0。利用其补码（取反加一）的性质，除了0和最小数（符号位为1，其余为0），外其他数都是互为相反数关系（符号位取位或为1）。0和最小数的补码是本身，不过0的符号位与其补码符号位位或为0，最小数的为1。

```
int logicalNeg(int x) {

  return ((x|(~x+1))>>31)+1;}
```



![1617266788786](C:\Users\86182\Desktop\MD文档\Nep.git\12.png)



第九：要求 一个数用补码表示最少需要几位 

```
/* howManyBits - return the minimum number of bits required to represent x in
 *             two's complement
 *  Examples: howManyBits(12) = 5
 *            howManyBits(298) = 10
 *            howManyBits(-5) = 4
 *            howManyBits(0)  = 1
 *            howManyBits(-1) = 1
 *            howManyBits(0x80000000) = 32
 *  Legal ops: ! ~ & ^ | + << >>
 *  Max ops: 90
 *  Rating: 4
 */
int howManyBits(int x) {
  return 0;
}
```

这个关系到了补码，那么正数和负数一定由区别，如果是正数，就需要找到最高位是1的位数n，然后就需要n+1位来表示补码，例如：

```
十进制   二进制      补码
  5      0101     1010
  12     01100    10011
  15     01111    10000
```

如果是负数，就需要找到最高一位是0的，因为会变成1，就需要这些位，

写出脚本：

```
int howManyBits(int x) {
  int b16,b8,b4,b2,b1,b0;
  int sign=x>>31;
  x = (sign&~x)|(~sign&x);//如果x为正则不变，否则按位取反（这样好找最高位为1的，原来是最高位为0的，这样也将符号位去掉了，就是用来判断符号的）


// 不断缩小范围
  b16 = !!(x>>16)<<4;//高十六位是否有1
  x = x>>b16;//如果有（至少需要16位），则将原数右移16位
  b8 = !!(x>>8)<<3;//剩余位高8位是否有1
  x = x>>b8;//如果有（至少需要16+8=24位），则右移8位
  b4 = !!(x>>4)<<2;//同理
  x = x>>b4;
  b2 = !!(x>>2)<<1;
  x = x>>b2;
  b1 = !!(x>>1);
  x = x>>b1;
  b0 = x;
  return b16+b8+b4+b2+b1+b0+1;//+1表示加上符号位
}
```

![1617271034134](C:\Users\86182\Desktop\MD文档\Nep.git\13.png)



第十题： 求2乘一个浮点数 

```
//float
/* 
 * floatScale2 - Return bit-level equivalent of expression 2*f for
 *   floating point argument f.
 *   Both the argument and result are passed as unsigned int's, but
 *   they are to be interpreted as the bit-level representation of
 *   single-precision floating point values.
 *   When argument is NaN, return argument
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
unsigned floatScale2(unsigned uf) {
  return 2;
}
```

 首先排除无穷小、0、无穷大和非数值NaN，此时浮点数指数部分分别存储的的为0，0，,255，255。这些情况，无穷大和NaN都只需要返回参数，无穷小和0只需要将原数乘二再加上符号位就行了（并不会越界）。剩下的情况，如果指数+1之后为指数为255则返回原符号无穷大，否则返回指数+1之后的原符号数。 写出脚本：

```
int exp = (uf&0x7f800000)>>23;
  int sign = uf&(1<<31);
  if(exp==0) return uf<<1|sign;
  if(exp==255) return uf;
  exp++;
  if(exp==255) return 0x7f800000|sign;
  return (exp<<23)|(uf&0x807fffff);}
```

![1617278067040](C:\Users\86182\Desktop\MD文档\Nep.git\14.png)



第十一题：要求将浮点数转换为整数

```
/* 
 * floatFloat2Int - Return bit-level equivalent of expression (int) f
 *   for floating point argument f.
 *   Argument is passed as unsigned int, but
 *   it is to be interpreted as the bit-level representation of a
 *   single-precision floating point value.
 *   Anything out of range (including NaN and infinity) should return
 *   0x80000000u.
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
int floatFloat2Int(unsigned uf) {
  return 2;
}
```



 首先考虑特殊情况：如果原浮点值为0则返回0；如果真实指数大于31（frac部分是大于等于1的，1<<31位会覆盖符号位），返回规定的溢出值**0x80000000u**；剩下的情况：首先把小数部分（23位）转化为整数（和23比较），然后判断是否溢出：如果和原符号相同则直接返回，否则如果结果为负（原来为正）则溢出返回越界指定值**0x80000000u**，否则原来为负，结果为正，则需要返回其补码（相反数）。 

写出脚本：

```
int floatFloat2Int(unsigned uf) {
  int s_    = uf>>31;
  int exp_  = ((uf&0x7f800000)>>23)-127;
  int frac_ = (uf&0x007fffff)|0x00800000;
  if(!(uf&0x7fffffff)) return 0;

  if(exp_ > 31) return 0x80000000;
  if(exp_ < 0) return 0;

  if(exp_ > 23) frac_ <<= (exp_-23);
  else frac_ >>= (23-exp_);

  if(!((frac_>>31)^s_)) return frac_;
  else if(frac_>>31) return 0x80000000;
  else return ~frac_+1;
}
```



![1617278322252](C:\Users\86182\Desktop\MD文档\Nep.git\15.png)



第十二题：求2.0的x次幂

 加 127 得到指数阶码，超过表示范围则返回 0 和 INF。由于小数点后面都是 0，只需左移指数部分。 

```
int exp = x + 127;
  // 0
  if (exp <= 0)
    return 0;
  // INF
  if (exp >= 0xFF)
    return 0x7f800000;
  return exp << 23;}
```

![1617278537717](C:\Users\86182\Desktop\MD文档\Nep.git\16.png)



总结：通过看csapp这本书，更深刻的理解了计算机的各种运算的原理和规则，深入的学习了位运算的方法，尤其在运算时要考虑定义的数值类型的范围，收获还是挺多的，不过有一些地方理解的还是不太透彻