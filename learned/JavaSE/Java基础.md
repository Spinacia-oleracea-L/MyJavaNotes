# Chapter 1

## 一.JDK、JRE、JVM

### 1.三者关系

#### JRE>>>Java Runtime Environment——Java运行环境

JRE = JVM + Java的核心类库(JavaSE标准类库，其他class等)；是Java程序运行的必须环境。

#### JDK>>>Java Development Kit——Java开发工具包

JDK = JRE + Java开发工具(javac：编译器、jar：打包工具、javap：反汇编器、jdb：debugger等)。

### 2.JVM内存结构

#### 堆、栈、方法区

## 二.开发者规范

### 标识符(指变量名、方法名、类名等)

#### 命名规则：

1. 由26个英文字母大小写，0-9数字，_或$组成。
2. 数字不可以做开头。
3. 不可以使用关键字或保留字，但是可以包含关键字和保留字。
   ps：保留字：**byValue**、**cast**、**future**、**generic**、**inner**、**operator**、**outer**、**rest**、**var**、**goto**、**const**。
4. Java标识符中严格区分大小写，长度无限制。
5. 标识符中不可以包含**空格**。

#### 命名规范：

1. 包名：多单词组成时所有字母都小写(如：aaa.bbb.ccc)。
2. 类名、接口名：多单词组成时，所有单词的首字母大写(如：AaaBbbCcc)。
3. 变量名、方法名：多单词组成时，第一个单词首字母除外，其他单词字母大写(如：aaaBbbCcc)。
4. 常量名：所有单词大写。多单词时采用下划线连接(如：AAA_BBB_CCC)。

## 三.编程思想

### 化繁为简

将复杂需求，拆解成为简单需求，逐步完成。

### 先死后活

先考虑固定值，再转变成为可以灵活变化的值。

### 一块代码完成一块功能

```java
while (true) {
    System.out.println("确定退出吗？y\\n");
    choice = myScanner.next();
    // 获取到字符‘y’、‘n’才能退出无限循环
    if ("y".equals(choice) || "n".equals(choice)) {
        break;
    }
}
// 单一功能专注，退出无限循环后再判断是否正确
if (choice.equals("y")) {
    loop = false;
}
```

# Chapter 2

## 一.Java数据类型

### 1.基本数据类型

#### a.数值型

* 整数型：byte【1字节】、short【2字节】、int【4字节】、long【8字节】
* 浮点数：float【4字节】、double【8字节】

> 1.float声明时必须添加'F'、'f'。
>
> 2.Java中小数默认为double。
>
> 3.科学计数：
> 	5.12e2 = (5.12\*10的2次方)
> 	5.12e-2 = (5.12/10的2次方)

#### b.字符型

* char【2字节】：存放单个字符'a'。

> 1.ASSII码
> 2.Unicode（万国）码
> 3.UTF-8（互联网应用最广、变长的特性1-6字节）

#### c.布尔型

* Boolean【1字节】：只能存放true、false

  > 1.为给boolean值赋值时默认为false。
  > 2.其中在有些条件判断中0 = false;1 = true。

### 2.引用数据类型

#### 数组 array[]

#### 类 class

#### 接口 interface

## 二.数据类型转换

### 1.自动类型转换

1. 精度低的可以**自动**向精度高的转换：

   > 精度大小排序：
   >
   > 1. char<**int**<long<float<double
   > 2. byte<short<**int**<long<float<double
   >
   > 如：int a = 'c';

2. 发生数据运算时，Java会自动将所有数据转换成容量最大的数据类型；

   自动提升原则：表达式结果自动提升至操作数的最大精度类型。

3. 高精度不能转换为低精度。

4. byte\short--char之间**不能**自动转换。

5. byte\short--char之间**可以运算**，首先会转换成为int类型。

   ```java
   byte b3 = b1 + b2;
   // 编译错误，只要byte\short--char三者任一参与运算，就会转换成为int类型。
   ```

6. boolean不参与转换。

### 2.强制类型转换

1. 需要由高精度转换为低精度时，需要用到强制转换。

2. 强制类型转换只对最接近的数据有效。

   ```java
   int x = (int)10 * 3.5 + 6 * 1.5;		//编译报错
   int y = (int)(10 * 3.5 + 6 * 1.5);		//强转成功
   ```

3. char类型可以保存int的常量，但是不能保存int的变量。

   ```java
   char c1 = 100;	//成功
   char c2 = (char) i1;	//失败
   ```

4. byte/short类型转换时，被当作int。

### 3.基本数据类型和String类型转换

1. 基本类型 ---> String

   ```java
   String s1 = n1 + "\\" + f1 + "\\" + d1 + "\\" + b1;
   // ps：基本类型需要字符串拼接：s1 = c1 + "";
   ```

2. String ---> 基本数据类型

   ```java
   int i1 = Integer.parseInt(s1);
   double d1 = Double.parseDouble(s1);
   boolean flag = Boolean.parseBoolean("true");
   byte b1 = Byte.parseByte("127");
   ```

3. String ---> char

   ```java
   System.out.println(s1.charAt(0) + "\n" + s2.charAt(1));
   // 取字符串的第n个字符
   ```

# Chapter 3

## 一.Java运算符

### 1.算术运算符

#### +、-、*、/、%、++、--

* +、-：正负符号、加减运算、也可也表示字符串连接符

* %：取余、取模：

  > 本质：a % b = a - (a / b * b)
  >
  > 特殊：当a为小数：a - [(int)a / b * b]
  >
  > 技巧是看**被取余数**的正负值

* /：**除以**

  ```java
  System.out.println(10 / 4);
  // 默认整数为int，结果为2。
  
  System.out.println(10.0 / 4);
  // 默认小数为double，结果为2.5。
  
  double d1 = 10 / 4;
  System.out.println(d1);
  // 结果为2.0，先运算10 / 4 = 2，再赋值给d1。
  ```

* ++：前自增与后自增

  例题1：

  ```java
  int i1 = 10;
  //++前自增	等价 i1=i1+1;i2=i1;
  int i2 = ++i1;
  System.out.println("i2=" + i2 + "此时i1=" + i1);	//i2=11此时i1=11
  //后自增++	等价 i3=i1;i1=i1+1;
  int i3 = i1++;
  System.out.println("i3=" + i3 + "此时i1=" + i1);	//i3=11此时i1=12
  ```

  例题2：

  ```java
  int i = 1;
  i=i++;
  System.out.println(i);//结果为1
  //1)temp=i;2)i=i++;3)i=temp;
  
  int i = 1;
  i=++i;
  System.out.println(i);//结果为2
  //1)i=i++;2)temp=i3)i=temp;
  ```

  例题3：

  ```java
  int x = 5;
  int y = 5;
  if(x++ == 6 & ++y == 6){
  	x = 11;
  }
  System.out.println("x=" + x + "\ty=" + y);
  // x=6	y=6
  // PS：后自增都是在后方再算
  
  int x1 = 5;
  int y1 = 5;
  if(x1++ == 6 | ++y1 == 6){
  	x1 = 11;
  }
  System.out.println("x1=" + x1 + "\ty1=" + y1);
  // x=11	y=6
  // PS：后自增都是在后方再算
  ```

### 2.关系运算符

#### ==、!=、<、>、<=、>=、instanceof

关系运算符的结果都是boolean型的

* 常常用于if结构的条件中、或是循环结构的条件中

  >==相等
  >!=不等于
  ><小于
  >\>大于
  ><=小于等于
  >\>=大于等于
  >**instanceof检查是否是类对象**

### 3.逻辑运算符

用于连接多个条件（关系表达式），最终结果为boolean值

#### 短路与：&&、逻辑与：&

**短路与**：当判断第一个关系表达式不成立后
立即短路，结果为false。
不再看第二个关系表达式。效率**高**

```java
int a = 1;
int b = 3;
if(a > 2 && ++b <2){
    System.out.println("ok1");			
}
System.out.println("a=" + a + "b=" + b);
```

**逻辑与**：当判断第一个关系表达式不成立后
返回结果false后，
仍然会处理后面的表达式。效率**低**

```java
int a1 = 1;
int b1 = 3;
if(a1 > 2 & ++b1 <2){
    System.out.println("ok2");			
}
System.out.println("a1=" + a1 + "b1=" + b1);
```

#### 短路或：||、逻辑或：|

**短路或**：当判断第一个关系表达式成立(true)后
立即短路，结果为true。
不再看第二个关系表达式。效率**高**

```java
int a = 1;
int b = 3;
if(a < 2 || ++b > 3){
    System.out.println("ok1\t");
}
System.out.println("a=" + a + "b=" + b);
```

**逻辑或**：当判断第一个关系表达式成立(true)后
仍然会处理后面的表达式。效率**低**

```java
int a1 = 1;
int b1 = 3; 
if(a1 < 2 | ++b1 > 3){
    System.out.println("ok2\t");
}
System.out.println("a1=" + a1 + "b1=" + b1);
```

#### 取反：！、逻辑异或：^

a!：当a为true，结果为false；a为false，结果为true。

a^b：当a、b不同时，结果为true；相同为false。

### 4.赋值运算符

#### 赋值：=、加等于：+=、减等于：-=、乘等于：*=、除等于：/=、模等于：%=

1. 运算顺序为：从右往左。

2. 赋值运算符的左边：只能为变量；
   赋值运算符的右边：可以为变量、表达式、常量。

3. 复合赋值运算符等价于：

   ```java
   int a = 1;
   int b = 2;
   a += b;
   //等价于
   a = a + b;
   ```

4. 复合赋值运算符会进行类型转换

   ```java
   byte b1 = 2;
   b1 += 3;		//等价于：b1 = (byte)(b1 + 3);
   b1++;			//等价于：b1 = (byte)(b1 + 1);
   ```

### 5.位运算符

#### &：按位与、|：按位或、^：按位异或、~：按位取反、>>：算术右移、<<：算术左移、>>>：无符号右移

> **位运算符主要针对二进制**
>
> 1. &：按位与
>    **两位全为1、结果为1，否则为0。**
> 2. |：按位或
>    **两位有一个1，结果就为1，否则为0。**
> 3. ^：按位异或
>    **两位同为0，两位反为1。**
> 4. ~：按位取反
>    **0~>1，1~>0。**
> 5. \>\>：向右位移
>    低位溢出，符号位不变，并用符号位补溢出的高位
>    int a = 1 >> 2;
>    000000**01** => **00**000000 
>    本质 1 / 2 / 2 = 0；
> 6. <<：向左位移
>    **符号位不变，低位补0**
>    int b = 1 << 2;
>    **00**000001 => 000001**00**
>    本质 1 * 2 *2 = 4；
> 7. \>\>\>：逻辑右移（无符号右移）
>    低位溢出，高位补0

### 6.三元运算符

#### 条件表达式 ？ 表达式1：表达式2；

1. 如果条件表达式结果为：true，返回表达式1；
2. 如果条件表达式结果为：false, 返回表达式2；

```java
int a = 10;
int b = 99;
int result = a > b ? a++ : b--;
System.out.println("result=" + result);
System.out.println("a=" + a);
System.out.println("b=" + b);
//result=99
//a=10
//b=98
```

> ps：
>
> 1. 表达式1和表达式2的结果要为可以赋给接受变量的类型（或者可以自动转换）
> 2. 三元运算符可以转换为if--else语句

## 二.运算符优先级

Java 语言中大部分运算符也是从左向右结合的，只有**单目运算符**、**赋值运算符**和**三目运算符**例外，其中，单目运算符、赋值运算符和三目运算符是从右向左结合的，也就是从右向左运算。
一般而言，单目运算符优先级较高，赋值运算符优先级较低。算术运算符优先级较高，关系和逻辑运算符优先级较低。多数运算符具有左结合性，单目运算符、三目运算符、赋值运算符具有右结合性。

| 优先级 | 运算符分类             | 结合顺序 | 运算符                               |
| ------ | ---------------------- | -------- | ------------------------------------ |
| 1      | 分隔符                 | 左结合   | . [] () ; ,                          |
| 2      | 一元运算符             | 右结合   | ! ++ -- - ~                          |
| 3      | 算术运算符、位移运算符 | 右结合   | + - * / % << >> >>>                  |
| 4      | 关系运算符             | 右结合   | > < >= <= instanceof == !=           |
| 5      | 逻辑运算符             | 左结合   | ! && \|\| ~ & \| ^                   |
| 6      | 三目运算符             | 右结合   | 布尔表达式?表达式1:表达式2           |
| 7      | 赋值运算符             | 右结合   | = += -= *= /= %= <<= >>= >>>= &= \|= |

# Chapter 4

## 进制

### 进制类型

二进制	0b1001 = 9

> 以0b或者0B开始

八进制	01001 = 513

> 以数字0开始

十进制	1001 = 1001

> 无特殊格式

十六进制	0x1001 = 4097

> 以0x或者0X开始

### 原码、反码、补码

#### 对于符号数而言，Java中的数都是有符号的

1. **二进制**的最高为符号位：0表示正数；1表示负数。
2. 整数的原码、反码、补码是它本身（三码合一）。
3. 负数的反码：将原码的符号位保持不变，其余位取反（1--->0、0--->1）。
   负数的补码：反码末位 + 1。
4. 0的反码、补码都是0
5. 计算机的运算都是以补码的形式来运算的。
6. 运算结果需要转换成为原码。

```java
System.out.println(2|3);
// 2的补码：00000000 00000000 00000000 00000010
// 3的补码：00000000 00000000 00000000 00000011
// 按位或|： 00000000 00000000 00000000 00000011
// 为正数：3（正数补码就是原码）
System.out.println(2^-3);
//-3的原码：10000000 00000000 00000000 00000011
//-3的反码：11111111 11111111 11111111 11111100
//-3的补码：11111111 11111111 11111111 11111101
// 2的补码：00000000 00000000 00000000 00000010
//按位异或：11111111 11111111 11111111 11111111
//		-1：11111111 11111111 11111111 11111110
//    取反：10000000 00000000 00000000 00000001
// 为负数：-1（负数补码需要-1、取反还原为原码）	
```

# Chapter 5

## 程序控制结构

### 1.顺序结构

普通程序都是按顺序执行的。

### 2.分支结构：if、else、switch

1. 分支的判断数值不多，且符合byte、short、int、char、enum[]、String这6种类型。建议使用switch语句。
2. 对区间的判断、对结果为boolean类型的判断，使用if语句。（使用场景广泛）

#### if else

```java
import java.util.Scanner;
public class IfExercise01{

	public static void main(String[] args){

		String name;
		double grade;
		char gender;

		Scanner myScanner = new Scanner(System.in);
		System.out.println("请输入姓名：");
		name = myScanner.next();
		System.out.println("请输入成绩：");
		grade = myScanner.nextDouble();
		System.out.println("请输入性别：");
		gender = myScanner.next().charAt(0);//接受字符的方法


		if(grade > 8.0){

			if(gender == '男'){
				System.out.println("恭喜晋级！分配到男子组");
			}else if(gender == '女'){
				System.out.println("恭喜晋级！分配到女子组");
			}

		}else 
			System.out.println(name + "，您以淘汰！");

	}

}
```

#### switch

1. 表达式（数据类型）应该与case后的常量值（数据类型）一致。或者是可以自动转换可以相互比较的类型（如：输入为字符型，常量值为int；即可自动转换）。
2. 表达式的返回值为（byte、short、int、char、enum[枚举]、String）。
3. case子句中的值必须是常量或常量表达式，不能是变量。
4. default子句可选。
5. **无break，程序会顺序执行到switch结尾。**

```java
基本语法
switch(表达式){ 	 //表达式对应一个常量值

    case 常量1;//当表达式等于常量1时
    语句块1;//执行语句块1
    break;//退出switch

    case 常量2;//若执行语句1后没有break语句
    语句块2;//则不会判断常量2，直接执行语句块2
    break;

    ~~~~

    case 常量n:
    语句块n;
    break;

    default:
    语句块;//若无匹配的常量则执行default
    break;//可省略
        
}
```

### 3.循环结构：for、while、do while、多重循环

#### for

1. “②循环条件”返回值必须为boolean值的表达式。
   ps:返回值为boolean型的关系运算符、逻辑运算符
2. ”①循环变量初始化“和“④循环变量迭代”可以写在括号外，但是两边的“分号；”不能省略。
3. “①循环变量初始化”可以有多条语句，但是要求类型一致，并且中间用“逗号，”分隔。“④循环变量迭代”同理。

```java
基本语法：
for(①循环变量初始化;②循环条件;④循环变量迭代){
	③循环操作(循环体);
	~~~~~
}
// ps:执行顺序①②③④
```

#### while

1. “②循环条件”返回值必须为boolean值的表达式。
   ps:返回值为boolean型的关系运算符、逻辑运算符
2. while循环为先判断再执行语句
   ps：for\if都是如此

```java
基本语法：
①循环变量初始化;
while(②循环条件;){
	③循环操作(循环体);
	~~~~~
	④循环变量迭代
}
// ps:执行顺序①②③④
```

过路问题

```java
public class ContorlDemo01{

	public static void main(String[] args){

		int money = 100000;
		int i = 1;
		while(i <= Integer.MAX_VALUE){
		//while不可以省略循环条件
			if(money > 50000){
				money -= (int)(money * 0.05);
				System.out.println(money);
			}else if(money < 1000){
				break;
			}else if(money <= 50000){
			//当"<=50000"在"<1000"之前，无法碰触到break语句，负数也满足“<=5000”。
				money -= 1000;
				System.out.println(money);
			}
			i++;
		}

		System.out.println("共经过了路口：" + i +"个");
	}

}
```



#### do while

注意：
1.“④循环条件”返回值必须为boolean值的表达式。
ps:返回值为boolean型的关系运算符、逻辑运算符
2.先执行，再判断。一定会执行一次循环

```java
基本语法：
①循环变量初始化;
do{
	②循环操作(循环体);
	~~~~~
	③循环变量迭代
}
while(④循环条件);

// ps:执行顺序①②③④，至少会执行一次
```

#### 多重循环

1. 将一个循环放在另一个循环体内，就形成了嵌套循环。其中，for、while、do while均可以作为外循环和内循环。
   [建议使用两层循环，最多不要超过3层，否则代码可读性很差]
2. 事实上，嵌套循环就是把内层循环当成外层循环的循环体。只有内层循环条件为false时，才会完全跳出内层循环，才可以结束外层的当次循环，开始下一次的循环。
3. 设外层循环次数为m次，内层次数为n次。则内层循环体需执行m*n次。
   **ps:常看看MultipleCycles代码**

### 4.跳转控制语句：break、continue、return

#### break

介绍：
用于终止某个语句的执行，一般用于switch\循环结构中。

```java
// 基本语法：
{
	~~~~
	break;
	~~~~
}

//配合标签使用：

/**
* break语句如果出现在多层嵌套语句中，可以通过标签指明要终止的为那一层语句块。
*/
label_1: {	~~~~
	label_2: {	~~~~
		label_3: {	~~~~
					break(label_3);
					~~~~
				}
			}
		}

```

1. break语句可以指定退出具体的某一层。
2. label_1是标签名，可自定义。
3. break后指定到某个标签，就退出标签所在循环。
4. 实际开发中，**尽量避免标签使用**。
5. 若没有指定标签，则break退出最近的循环体。
6. break一般放在循环体语句内部最后，因为循环体break之后的语句不执行

循环结构

```java
import java.util.Scanner;
public class RandomDemo01{
	

	public static void main(String[] args){

		Scanner myScanner = new Scanner(System.in);
		System.out.println("请输入终止数：");
		int num_Text = myScanner.nextInt();
		int count = 0;
		int temp = 0;


		for(int i = 0; i <= Integer.MAX_VALUE;i++){

			temp = (int)(Math.random() * 100);
			count++;
			if(temp == num_Text){

				break;
			}

		}
		System.out.println("共随机次数：" + count);

	}
}
```

#### continue

介绍：
用于结束本次循环，继续执行下一次循环。

```java
// 基本语法

{
	~~~~
	continue;
	~~~~
}
```

**注意：continue语句出现在多层嵌套循环语句体中时，可以通过标签指明要跳过的是哪一层循环。**

#### return

介绍：
return使用在某一个方法内时，表示跳出所在方法。

**注意：如果写在main方法中，表示退出程序。**

# Chapter 6

## 数组array[ ]的介绍

## 数组的操作

## 二维数组

