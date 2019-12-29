C语言

常见OJ评判结果对照表

https://www.cnblogs.com/ACShiryu/archive/2011/11/22/oj.html

输入输出

<https://blog.csdn.net/qiao1245/article/details/53020326>

### 基础

结束语句的符号是分号或大括号。

#### 程序的执行方式

语言本身无解释与编译之分，只是常用的执行方式不同。

- 解释：一个程序理解你写的程序，有特殊的计算能力，如执行过程中可以修改源代码；

- 编译：一个程序翻译你写的程序成为计算机能懂得机器语言，有确定的运算性能。

语言的能力/适用领域主要由库（类库/函数库）和传统所决定，而非语言本身。

mac中可以使用sublime进行文本编辑，但无法实现输入，输入需要使用终端gcc，也可以使用vi进行文本编辑。

#### 变量与常量

变量用于存储数据，参与运算，变量名叫做标识符 ；

等号是一种运算符，有运算符的式子叫做表达式，运算符都会有计算结果。

变量声明：

<变量类型><变量名称>=<初始值>;

~~~c
#include <stdio.h>

int main()
{
	// 变量声明时进行赋值即为初始化，没有初始化时没有明确的值 
	int price = 0;
	// const为修饰符，定义常量代替直接量，初始化后无法修改 
	const int AMOUNT = 100;
	
	// 实现输入与输出
	// f是format，""为格式化字符串，&为取地址符 
	printf("请输入金额（元）：");
	scanf("%d",&price);
	
	// C99中可以在代码任何位置定义变量
	int change = AMOUNT - price;
	
	printf("找您%d元",change);
	
	return 0;
}
~~~

#### 整数与浮点数

带小数点的数字是浮点数，浮点数类型包括单精度float与双精度double；

整数与整数的运算结果只能是整数，浮点数与整数的运算中，会将整数转换成浮点数，进行浮点数的运算；

整数int的输入输出分别为%d、%d，浮点数double的输入输出分别为%lf、%f。

~~~c
// 计算身高 
//	int foot;
//	int inch;

//	第二种：int --> double，scanf中 %d --> %lf，否则读入0 0 
	double foot; 
	double inch;
	
	printf("请输入身高的英尺和英寸：");
//	scanf("%d %d",&foot,&inch);
	scanf("%lf %lf",&foot,&inch);
	
//	5foot 7inch
//	printf("身高是 %d 米",(foot + inch / 12) * 0.3048); // -75591422

// 两个整数的运算结果只能是整数，除法时忽略小数，不四舍五入 
	printf("身高是 %f 米",(foot + inch / 12) * 0.3048); // 1.524
//	第一种：12 --> 12.0
//	printf("身高是 %f 米",(foot + inch / 12.0) * 0.3048);
~~~

#### 表达式

表达式是运算符（operator）与算子（操作数operand）的组合；

单个运算符根据算子的个数分为单目、双目，根据结合关系分为自左向右、自右向左；

多个运算符涉及到不同的优先级，其中=赋值的优先级最低。

![âCè¯­è¨è¿ç®ç¬¦ä¼åçº§âçå¾çæç´¢ç»æ](https://www.oudahe.com/uploads/201709/20/15059226081.png?20164717404)

特殊运算符：

- +-*/%可以与=生成复合赋值运算符；
- ++、--自增自减运算符，可以放在变量的前面或后面，分别是前缀、后缀形式，返回的结果不同。

~~~c
//	a++与++a 
	int a = 2;
	printf("a = %d\n",a); // 2
	printf("a++= % d\n",a++); // 2
	printf("a = %d\n",a); // 3
	printf("++a = %d\n",++a); // 4
~~~

#### exit(0) exit(1)与return()

exit（0）：正常运行程序并退出程序；

exit（1）：非正常运行导致退出程序；

return（）：返回函数，若在主函数中，则会退出函数并返回一值。

详细说：

1. return返回函数值，是关键字；  exit 是一个函数。

2. return是语言级别的，它表示了调用堆栈的返回；而exit是系统调用级别的，它表示了一个进程的结束。

3. return是函数的退出(返回)；exit是进程的退出。

4. return是C语言提供的，exit是操作系统提供的（或者函数库中给出的）。

5. return用于结束一个函数的执行，将函数的执行信息传出个其他调用函数使用；exit函数是退出应用程序，删除进程使用的内存空间，并将应用程序的一个状态返回给OS(操作系统)，这个状态标识了应用程序的一些运行信息，这个信息和机器和操作系统有关，一般是 0 为正常退出，非0 为非正常退出。

6. 非主函数中调用return和exit效果很明显，但是在main函数中调用return和exit的现象就很模糊，多数情况下现象都是一致的。

### 语句

#### 条件语句

计算两个值之间的关系的运算符，叫做关系运算，用于作比较，如==相等。运算符会产生一个结果，如+-*/。关系运算/比较运算的结果为整数1/0。

所有的关系运算符的优先级比算术运算低，比赋值运算高。结合关系为自左向右。

条件语句的种类：

- if-else语句整体是一句，根据条件进行判断选择性执行；

- 可以进行嵌套，使用大括号显示匹配关系，如三个数比较大小；

- 可以使用级联if-else if-else，生成层次关系，如用于分段函数。

注意：if-else记得加大括号！

~~~c
	int age=0;
	double salary=5000;
	
	age = 25;
	
	// 常见错误1：不加大括号 
	// 不加大括号时只识别第一条语句为if语句，printf的执行与if无关
	// 常见错误2：if后加分号
	// C中不是每条语句都需要加分号，分号代表一条语句的结束
	// 常见错误3：等号==写成赋值=
	// if只要求()里的值是零或非零 
	if (age > 60){
		salary = salary * 1.2;
		printf("salary=%f",salary);
	}
~~~

#### 循环语句

如使用if判断位数时有局限性，因此引入循环语句，如将if换成while。

循环最重要的是循环的条件。还需要对循环进行选择。

if语句与循环语句的区别在于一次性执行还是反复多次执行。

- while：先判断条件后进入循环体；
- do-while（分号结尾）：先进入循环体后判断条件，至少执行一次循环体；
- for：类似于计数器，初始条件，循环条件，循环每一轮的变化。
- for(i=0;i<n;i++)循环结束时，i=n。for循环都可以改写成while循环。for是while的简写。

~~~c
// 计算正整数位数，从右向左计数 
	int x;
	int n = 0;
	
	scanf("%d",&x);
	
//	 while循环 
//	 循环体需要先执行一次，否则x=0时n=0
//	 因此引入do-while循环 
//	x /= 10;
//	n ++;
//	
//	while (x > 0) {
//		x /= 10;
//		n ++ ;
//		printf调试 
//		printf("x=%d,n=%d",x,n);
//	}
	
//	do-while循环
//	循环体只出现一次，同时满足x=0时n=1
	do {
		x /= 10;
		n ++;
	} while (x > 0);
	
	printf("%d",n);
~~~

未知位数的整数逆序 ：

~~~c
	// 未知位数的整数逆序 
	int a = 0;
	int b = 0;
	scanf("%d",&a);
	
	do {
		int x = a % 10;
		// 输入100，输出001
		// printf("%d",x);
		b = b * 10 + x;
		a /= 10;
	} while (a > 0);
	
	// 输入100，输出1 
	printf("%d",b);
~~~

计算阶乘，引入for循环简化while循环：

~~~c
// 计算阶乘，0!=1 
	int n = 0;
	scanf("%d",&n);

	int i = 1;
	int factor = 1;
//	while (i <= n) {
//		factor *= i;
//		i ++;
//	}
//  初始化 i=1 等价于 i=2 
	for (i=2;i<=n;i++) {
		factor *= i;
	}
	
	printf("n!=%d",factor);
~~~

#### 循环控制

- 跳出单层循环：continue与break；

- 跳出多层循环：接力break（exit变量）和goto 标号。

判断素数：

~~~c
//	判断素数
//	1不是素数，最小的质数是2
//	素书即是质数。质数的定义：一个大于1的自然数，除了1和它本身外没有其他的约数 
	int a;
	
	while (scanf("%d",&a) != EOF)
	{
		int flag = 0; // 0表示是素数，1表示不是素数 
		int i;
		for (i=2;i<a;i++)
		{
			if (a%i==0)
			{
				flag = 1;
				break;
			}
		}
		
		if (flag==0)
		{
			printf("是素数\n");
		}
		else
		{
			printf("不是素数\n");
		}
	}	
~~~

#### 正序分解整数

~~~python
//	正序分解整数
//	先逆序数值再逆序输出不满足末尾有零的情况 
//	123 / 100 -> 1
//	123 % 100 -> 23
//	100 / 10 -> 10
//	23 / 10 -> 2
//	23 % 10 -> 3
//	10 / 10 -> 1
//	3 / 1 -> 3
//	3 % 1 -> 3
//	1 / 10 -> 0
	int x;
	int mask = 1;
//	mask = 10000;
//	int cnt = 1;
	scanf("%d",&x);
//	x = 12354;
	int y = x;
    
//	得到位数计算mask 
//	do
//	{	
//		y /= 10;
//		printf("y=%d,cnt=%d\n",y,cnt); 
//		cnt ++;
//	} while (y >0);
//	printf("cnt=%d\n",cnt);

//	int i;
//	for (i=cnt-1;i>1;i--)
//	{
//		mask *= 10;
//		printf("mask=%d,i=%d\n",mask,i);
//	}
	
//	不需要 cnt 
//	do-while --> while，在x=1时不满足 
	while (y >9){
		y /= 10;
		mask *= 10;
		printf("mask=%d\n",mask);
	}
	
	do
	{
		int d = x / mask;
		printf("%d",d);
		if (mask > 0)
		{
			printf(" ");
		}
		x %= mask;
		mask /= 10;
	} while (mask > 0);
~~~

#### 最大公约数

辗转相除法求最大公约数：

给出两个自然数 **a** 和 **b**：检查 **b** 是否为0；如果是，则 **a** 为最大公约数。如果不是，则分别用 **b** 和 **a** 除 **b** 的余数作为上一步中的 **a** 和 **b** 重复这一检查步骤。

~~~python
// gcd 是 greatest common divisor （最大公约数）的缩写
	int a,b;
	int gcd = 1;
	
	scanf("%d %d",&a,&b);
	
	while (b!=0)
	{
		int x;
		x = a % b;
		a = b;
		b = x;
	}
	gcd = a;
	printf("%d\n",gcd);
~~~

最小公倍数 = 两数之积 / 最大公约数

#### 素数求和

~~~c
	int m;
	int n;
	int cnt = 0;
	int sum = 0;
	scanf("%d %d",&m,&n);
//	1不是素数，需要单独处理
	if (m==1)
	{
		m = 2;
	} 
	
	int i; 
	for (i=m;i<=n;i++)
	{	
//		1不是素数，2是素数.设置默认是素数 	 
		int isPrime = 1;
		int k;
        
//		1. 判断是否是素数 
		for (k=2;k<i;k++)
		{
			if (i%k==0)
			{
				isPrime = 0;
				break;
			}
		}
        
//		2. 素数求和 
		if (isPrime==1)
		{
			cnt ++;
			sum += i;
			printf("num=%d\n",i);
		}
	}
	printf("%d %d",cnt,sum); 
~~~

### 数据类型

#### 数据类型

变量需要在使用前定义，并声明类型。提供不同类型的自动转换。

数据类型：

- 整数：内存中的表达方式为二进制数（补码），加法器中可以进行加法运算，如char、short、int、long，整数输入输出时只有两种：int、long long，也就是说不能直接读入char类型变量；
- 浮点数：内存中的表达方式为编码，因此不可以直接相加，如float、double；
- 逻辑：bool；
- 指针；
- 自定义类型。

内存中一个字节byte为8个比特位bit。可以使用sizeof查看类型字节数，但它是静态运算符，编译后结果确定，内部不做运算。

~~~c
	char a; // 1
	short b; // 2
	int c; // 4
	long d; // 4 32位编译器
	long long e; // 8
	float f; // 4
	double g; // 8
	long double h; // 16
~~~

#### 整数类型

CPU中寄存器的字长是每个寄存器的大小，即每次处理数据的宽度，也是CPU经总线向内存每次传输数据的单位，如一个字长32bit。而**int表达的就是一个寄存器的大小**。也就是说，字长是int个字节。因此int与long的字节数取决于CPU与编译器。其他整数固定大小。

计算机内部都是二进制，负数以补码方式表达，补码的意义就是拿补码和原码可以加出一个溢出的“零”，优点是便于运算。一个正数a的补码是它的负数0-a，实际是2^n-a，n是这种类型的位数。如：

1-->0000 0001, 0-->(1)0000 0000，-1=0-1=(1)0000 0000-0000 0001=1111 1111=255=2^8-1

一个字节（8位）的整数，可以表达的是 0000 0000 ~ 1111 1111，共256个数字，从-2^(n-1)到2^(n-1)-1，其中：

- 0000 0000 --> 0，可表示1个数字；
- 1111 1111 ~ 1000 0000 --> -1 ~ -128，可表示128个数字；
- 0000 0001 ~ 0111 1111 --> 1 ~ 127，可表示127个数字。

unsigned整数不包括负数，初衷并非扩展数能表达的范围，而是用于移位实现纯二进制位运算。

~~~c
//	255 --> 1111 1111
	char x = 255; // -1，整数代表补码
	int y = 255; // 255，整数代表纯二进制
	unsigned char z = 255; // 255，整数不代表补码，整数不表示负数 
//	printf("x=%d,y=%d,z=%d\n",x,y,z);
~~~

**进制只是把数字表达为字符串**，与内部如何表达数字无关。

- 10进制，格式%d；

- 0开头代表8进制，格式%o；
- 0x开头代表16进制，格式%x，4位二进制正好是一位16进制，如0001 0010-->1 2。
- bcd

~~~c
	char n = 012;
	int p = 0x12;
	
//	printf("%d %d\n",n,p); // 10 18
//	printf("%o %x\n",n,p); // 12 12
//	
//	scanf("%o",&n); // 12
//	printf("%d\n",n); // 10
~~~

#### 浮点类型

- 浮点数用离散数值表达连续数值，与真值之间的距离是误差；

- 浮点数中还包括无穷大inf、不存在nan；
- 整数二进制表达，浮点数编码表达，

浮点数存在误差的原因是编码导致的。

~~~c
	printf("%f\n",-0.0049); // -0.004900 默认7位有效数字，四舍五入 
	printf("%.3f\n",-0.0049); // -0.005 四舍五入
	printf("%.30f\n",-0.0049); // -0.04899999…不精确
	printf("%.3f\n",-0.00049); // -0.000
	
	printf("%d\n",2/0); // [Warning] division by zero [-Wdiv-by-zero]
	printf("%d\n",2/0.0); // 编译通过，返回0
	printf("%f\n",2.0/0.0); // inf
	printf("%f\n",-2.0/0.0); // -inf
	printf("%f\n",0.0/0.0); // nan
~~~

定点数与浮点数：

- 定点数：小数点固定在某个位置上的数据。 就好像 0.0000001 ，0.0001111。编码如下图所示（32位），可见小数点固定在了第23位和第24位之间，这种表示法范围和精度相互矛盾；

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20180913092423258?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdXBlaWZlbmczNTE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

- 浮点数：小数点位置可以浮动的数据。就像数学中的 1222.2*10^3也可以表示为1.2222*10^6。如单精度32位浮点数表示规则（inter 8086处理器 IEEE 754 标准）如图所示。

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20180913092826449?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdXBlaWZlbmczNTE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

用科学记数法表示，应该是这样的：(+ or - ) 1.(mantissa) * 2 ^ exponent

![img](https://img-blog.csdn.net/20170819220832242?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2hvaXNsZWZ0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

浮点数表达式：**N=M\*R^E**

注意：

- 小数点前面是有个1的；

- Ms为符号位，被设置到最高位上。非0即1，只占1位，0代表正数，-1代表负数；

- M为尾数（mantissa），就是除了符号位的尾数占的部分，有m位，Ms与M组成一个定点小数；

- E也阶码（exponent），有n+1位，一般为整数，其中E的最高位是E的符号位，用来表示阶数E的正负。因此8位二进制数字这256个数字要区分开来使用， 从0到127 表示负数， 从128到255表示正数；

- R为阶的基数，也就是底数，计算机里的底数不是10，一般是2、8、16。在一台计算机里，所有数据中的R是确定，且相同的；

- IEEE754 国际标准规定了，基数为2，阶码采用移码，尾数采用原码。

如把5.8 这个10进制小数转换为IEEE 754表示的浮点数。

5.8 = 1.45 * 2 ^ 2（5.8/2 => 2.9，2.9/2 => 1.45）。

而尾数mantissa 是0.45 ， 需要转化成二进制，怎么做呢？ 也很简单，不断地乘2，取结果的整数部分就行。因此出现无限循环，误差由此而来。详细过程如下：	

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20180913093128648?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdXBlaWZlbmczNTE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

最终，我们把5.8变成了符合IEEE 754 规范的浮点数表示：

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20180913093206471?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdXBlaWZlbmczNTE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### 字符类型

char是一种整数， 也是一种特殊的类型：字符。

字符用数字表达，二者之间的对应关系正是ASCII字符编码。如‘1’的ASCII编码为49。

终端shell用于运行程序，并对输入的控制字符进行转义，如：

- \b转义为回退一位但不删除；

- \t制表位是固定位置，而非固定个数的字符；

- \n转义为回车换行（本身只是换行）。

~~~c
//	[Warning] passing argument 1 of 'printf' makes pointer from integer without a cast [enabled by default]
//	printf('OK'); // printf要求双引号，字符要求单引号 
	
//	scanf("%c",&s); // A
//	printf("%d\n",s); // A 65, a 97
//	printf("%c\n",s+'a'-'A'); // a，字母大写转小写
	
//	printf("123\b\n456\n");
//	printf("1234\b5\n"); // 1235，\b回退一位但不删除 
//	1	2	3 // \t
//	12	34	56
~~~

单引号与双引号的区别

<https://blog.csdn.net/u013541620/article/details/43172891>

<https://blog.csdn.net/supreme42/article/details/7300451>

<https://blog.csdn.net/qiao1245/article/details/53020326>



#### 类型转换

自动类型转换（从小到大）：

- 对于printf，任何小于int的类型会被转换成int，float会被转换成double；

- 对于scanf，不会进行自动转换，需要明确变量的大小，

强制类型转换（从大到小）：

- 优先级高于四则运算。

#### 逻辑类型

bool类型变量包括true与false， 底层仍然用数值1与0表示；

逻辑运算是对逻辑量进行的运算，结果只有1或0，包括!、&&、||，优先级依次降低，存在短路风险；

逻辑量是关系运算或逻辑运算的结果，如将4<x<6表示为x>4 && x<6；

### 函数

#### 函数定义

- return停止函数的执行，并返回值给调用函数的地方，一个函数设置一个return，单一出口；
- 编译器自上而下顺序分析源代码，因此函数的定义要在函数调用之前。除非将函数的原型声明放在main函数之前；
- 函数没有参数的话，原型声明处参数写void；
- C语言在调用函数时，只能**传值给函数，不能传入变量**；
- 函数不可以嵌套定义。

#### 本地变量

- 本地变量是定义在函数/语句大括号（块）内的；
- 本地变量没有默认初始值，参数进入函数时被初始化；
- 每个函数的每次执行会产生独立的变量空间，这些变量称作本地变量（局部变量），定义在函数内部的变量以及参数都属于本地变量； 
- 本地变量的生存期与作用域都仅限于块内，变量不存在于当前变量空间时报错Not found in current context；



### 数组

#### 定义

- 定义：<数组单元类型> 数组名称 [元素个数]; C99后可以用变量定义数组的大小（即元素个数）；
- 数组是一种容器，其中所有元素具有相同的数据类型，数组创建后大小不可变，数组中的元素在内存中连续依次排列；
- 赋值号右边是用于读取数组，左边是用于写入数组，分别简称为右值与左值；
- 下标/索引从0开始计数，编译器和运行环境都不会检查数组读写时下标是否越界，程序崩溃 segmentation fault。

如数组用于统计数字出现的次数：

~~~c
//	统计数字出现的次数
	const int number = 10;
	int n;
	int cnt[number];
	int i;
	
//	初始化，本地变量 
	for (i=0;i<number;i++)
	{
//		printf("%d\n",cnt[i]);
		cnt[i] = 0;
	}
	
	scanf("%d",&n);
	while (n != -1)
	{
		cnt[n] ++;
		scanf("%d",&n);
	}

//	循环输出 	
	for (i=0;i<number;i++)
	{
		if (cnt[i] != 0)
		{
			printf("%d出现了%d次\n",i,cnt[i]);
		}
	}
~~~

#### 数组初始化

- 要把一个数组的所有元素交给另一个数组，不可以使用赋值，因为数组是const，唯一方法是使用遍历。

- 二维数组初始化时行号可以省略，列号不可以省略。

~~~c
//	数组的集成初始化，默认为 0
//  int a[]; 长度不确定数组
//	int a[] = {1,3,2,4,6,5,2,};
//	初始化默认为 0
//	int a[10] = {2}
	int a[10] = {[1]=10,88,[3]=25};
	int i;
	
	{
		for (i=0;i<7;i++)
		{
			printf("%d\n",a[i]);
		}
//		数组的大小，sizeof 字节数 
		printf("%d\n",sizeof(a)/sizeof(a[0]));
	}
	
//	invalid initialize
//	int b[] = a;
~~~

#### 构造素数表

将非素数的状态进行保存，因此需要初始化数组保存素数的状态。

~~~c
//	构造素数表，2-->25的范围内
	const int maxNumber = 25;
	int isPrime[maxNumber];
	
	int i;
	for (i=0;i<maxNumber;i++){
		isPrime[i] = 1;
	}
	
//	for test
	for (i=2;i<maxNumber;i++){
		printf("%d\t",i);
	}
	printf("\n");
//	for test
	
//	从2开始标记它的倍数为非素数 
	int x;
	for (x=2;x<maxNumber;x++){
		if (isPrime[x]){
			for (i=2;i*x<maxNumber;i++){
				isPrime[i*x] = 0; 
			}
		}
//		for test
		printf("%d\t",x);
		for (i=2;i<maxNumber;i++){
			printf("%d\t",isPrime[x]);
		}
		printf("\n");
//		for test
	}
	
	for (i=2;i<maxNumber;i++){
		if (isPrime[i]){
			printf("%d\t",i);
		}
	}
~~~

#### 二维数组

- 实际上，不管是一维还是多维数组，都是内存中一块线性连续空间，因此在内存级别上，其实都只是一维；
- 二维数组是由多个一维数组构成的，二维数组的每个元素是一维数组的首地址。

二维数组的假想布局与现实布局如下如所示：

![img](http://c.biancheng.net/cpp/uploads/allimg/120205/1-120205202345527.jpg)

![img](https://img-blog.csdn.net/20160623112221221?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### 指针

”&“操作符取出了变量 a 在内存空间中的首地址，而后通过 “ * ”  操作符取出首地址所在内存空间的数据。

一个指针包含两方面：a) 地址值；b) 所指向的数据类型。

解引用操作符（dereference operator）会根据指针当前的地址值，以及所指向的数据类型，访问一块连续的内存空间（大小由指针所指向的数据类型决定），将这块空间的内容转换成相应的数据类型，并返回左值。

指针如int*可能是指向单个int，也可能是指向一串连续的元素为int数组。

同理，char*指针指向一个字符（串）。

#### & 取地址符

scanf()中的&是个运算符，用于获得变量的地址，它的操作数必须是变量；

~~~c
	int i = 0;
//	内存地址使用16进制表示
//	d 以十进制形式输出带符号整数(正数不输出符号)
//	o 以八进制形式输出无符号整数(不输出前缀0)
//	x,X 以十六进制形式输出无符号整数(不输出前缀Ox)
//	u 以十进制形式输出无符号整数
//	p 输出指针地址
//	lu 32位无符号整数
	printf("0x%x\n",&i);
	printf("%p\n",&i);
	int j;
	j = i ++;
//	相邻变量的内存地址由高到低。因为堆栈中自顶向下分配内存 
	printf("%p\n",&j);
	printf("*******\n");
//	数组变量内存地址的关系，由低到高 
	int number[5] = {0};
//	number == &number == &number[0]
	printf("%p\n",number);
	printf("%p\n",&number);
	printf("%p\n",&number[0]);
	printf("%p\n",&number[1]);
	printf("-------\n");
//	强制类型转换
//	[Warning] cast from pointer to integer of different size [-Wpointer-to-int-cast]
//	int p = (int) &i;
//	printf("0x%x\n",p);

//	64位：4,8 32位：4,4，因此不可以用int保存指针
	printf("%lu\n",sizeof(int)); // 4
	printf("%lu\n",sizeof(&i)); // 8
//	printf("%lu\n",sizeof(p)); // 4
	
//	[Error] lvalue required as unary '&' operand
//	p = (int) &(i++);
~~~

#### pointer 指针

将地址交给整数，大小与编译器有关。因此出现指针，指针类型的变量就是保存地址的变量。

如：int* p,q表示*p是一个int，因此p是指针，而q是int型变量，没有int *型变量。

函数参数表里的数组是指针，可以使用[]运算符进行运算。因此外部函数可以直接修改数组参数。数组变量是特殊的指针，数组变量本身表达地址。

指针数组和数组指针：

指针数组：array of pointers，即用于存储指针的数组，也就是数组元素都是指针；

数组指针：a pointer to an array，即指向数组的指针。示例如下。

int* a[4]     指针数组

​                 表示：数组a中的元素都为int型指针    

​                 元素表示：*a[i] 与 * (a[i])是一样的，因为[]优先级高于*

int (*a)[4]   数组指针 

​                 表示：指向数组a的指针

​                 元素表示：(*a)[i]  

二维数组和二级指针相关的参数匹配：

![img](https://img-blog.csdn.net/20160623113621428?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

~~~c
int main(void)
{
//	&x == p
	int x = 10;
	printf("x = 0x%x\n",&x); // 0x62fe48
	printf("x = %p\n",&x); // 000000000062FE48
	f(&x);
	int z = *(&x);
	printf("z = %d\n",z); // 10 --> 25
	
	return 0;
}

int f(int *p)
{
//	*p是int，p是指针 
	printf("p = %p\n",p); // 000000000062FE48
//	访问指针地址上的变量
	printf("*p = %d\n",*p); // 10
//	指针可以实现对函数外部参数的修改。以前函数是传值，现在函数是传参
//	赋值号左边不是变量，而是值（运算结果），因此叫做左值。
//  *是个运算符，*p 表示取得p指针保存的地址指向的变量。
	*p = 25;
	printf("size of p = %d\n",sizeof(p)); // 8
//	int y = *p;
//	printf("y = %d\n",y);
}
~~~

#### 指针的初始化

<https://blog.csdn.net/mhjcumt/article/details/7351032>

指针初始化时，“=”的右操作数必须为内存中数据的地址，不可以是变量，也不可以直接用整型地址值(但是int* p=0；除外，该语句表示指针为空)。此时，*p只是表示定义的是个指针变量，并没有间接取值的意思。

指针的赋值，“=”的左操作数可以是*p，也可以是p。

当“=”的左操作数是*p时，改变的是p所指向的地址存放的数据；

当“=”的左操作数是p时，改变的是p所指向的地址。



#### 指针的作用

- 函数需要对变量进行修改时，必须传入指针，然后利用指针间接访问变量，再对变量进行修改。参数的作用是返回结果；

- 当函数返回多个值时，某些值就只能通过指针返回；
- 用于函数返回运算的状态（可能报错），结果通过指针返回。C++或JAVA中采用一异常机制。

- 变量与指针：

我一直这样粗浅的理解变量与指针：变量是个房间，而指针是门牌号。房间，无论它里面有没有人或物，它始终是存在的，而门牌号如果不挂在房间的入口处，是没意义的。

~~~c
/**
	return 除法成功，返回1；否则返回0 
*/
int divide(int a, int b, int *result);
int main()
{
	int x, y, z;
	while (scanf("%d %d",&x,&y) != EOF){
        // divide返回为0时，除法不执行，避免了程序报错
		if (divide(x, y, &z)){
			printf("%d / %d = %d\n", x, y, z); 
		}
	}
}
int divide(int a, int b, int *result)
{
	int ret = 1;
	if (b == 0){
		ret = 0;
	}else{
		*result = a / b;
	}
	return ret;
}
~~~

#### 交换两个数

如利用在函数中交换两个数必须要使用指针。

~~~c
void swap(int *x,int *y);
int main()
{
	int a;
	int b;
	a = 2;
	b = 3;
	
	printf("a = %d, b = %d\n",a,b);
	printf("pa = %p,pb = %p\n",a,b);
	swap(&a,&b);
	printf("a = %d, b = %d\n",a,b);
	printf("pa = %p,pb = %p\n",a,b);
	
	return 0;
}
void swap(int *x,int *y)
{
	printf("x = %d, y = %d\n",*x,*y);
	printf("px = %p,py = %p\n",x,y);
//	传参时复制指针，因此在swap函数中互换指针没用
//  因此函数定义时的参数是形参，函数调用时的参数是实参
//	int t = *x;
//	*x = *y;
//	*y = t;
	/*
	a = 2, b = 3
	pa = 0000000000000002,pb = 0000000000000003
	x = 2, y = 3
	px = 000000000062FE4C,py = 000000000062FE48
	x = 3, y = 2
	px = 000000000062FE4C,py = 000000000062FE48
	a = 3, b = 2
	pa = 0000000000000003,pb = 0000000000000002
	*/
//	要实现值的互换，需要修改指针的指向，而非互换指针
	int *t = x;
	x = y;
	y = t;
	/*
	a = 2, b = 3
	pa = 0000000000000002,pb = 0000000000000003
	x = 2, y = 3
	px = 000000000062FE4C,py = 000000000062FE48
	x = 3, y = 2
	px = 000000000062FE48,py = 000000000062FE4C
	a = 2, b = 3
	pa = 0000000000000002,pb = 0000000000000003
	*/
	printf("x = %d, y = %d\n",*x,*y);
	printf("px = %p,py = %p\n",x,y);
}
~~~

#### 常量指针

常量指针有两种，判断依据是const在*之前或之后：

- const int* a中const修饰*a，但是a可变，只要a指向的目标是const类型就可以。即不可通过指针修改变量；
- int* const a中const修饰a，一旦指向则不能改写，但是可以修改*a的值。即指针不可修改。如数组是指针常量。

常把⼀个非const的值转换成const的。当要传递的参数的类型比地址大的时候，这是常用的手段：

既能用比较少的字节数传递值给参数，又能避免函数对外面的变量的修改。如数组、结构体。

~~~c
//	数组是常量指针 
	// int a[] --> int * const b;
	int number[] = {1,2,5,3,4};
	int *px = number; // 可以不用&符号 
	printf("%p\n",number);
//	int *px = number[0]; // 报错，数组单元表达的是变量
//	数组变量是const的指针，所以不能被赋值
//	int m[5];
//	m = number; // error 
//	对于数组变量，*number == number[0]
	printf("*number = %d\n",*number);
	printf("*************\n");
	
//	int* const p != const int* p
	{
	int k;
	int* const p = &k;
	*p = 8;
//	p ++; // error 指针不可修改 
	printf("*p = %d, k = %d\n",*p, k);
	}
	
	{
	int k = 9;
	const int* p = &k;
//	k ++; // OK
//	*p = 12; // error 不可通过指针修改变量 
	printf("*p = %d, k = %d\n",*p, k);
	}
	
	int j = 2;
	int *pj = &j;
//	对于指针变量 *p == p[0]
	printf("*pj = %d\n",*pj); // 2
	printf("pj[0] = %d\n",pj[0]); // 2
//	printf("pj[0] = %d\n",pj[1]); // 1
//	printf("pj[0] = %d\n",pj[10]); // 0
	
//	printf("main sizeof(number) = %d\n",sizeof(number)); // 20
	printf("main sizeof(number) = %lu\n",sizeof(number));
	printf("main number = %p\n",number);
	max(number);
//	外部函数可以直接修改数组参数
//	也就是说，函数参数表里的数组是指针，可以使用[]运算符进行运算 
	printf("number[0] = %d\n",number[0]);
	
	int *p;
	printf("sizeof(p) = %d\n",sizeof(p)); // 8
	int i = 1;
	printf("sizeof(i) = %d\n",sizeof(&i)); // 8
	printf("sizeof(int) = %d\n",sizeof(int)); // 4
	printf("d = %d\n",-111); // -111
	
	return 0;
}
void max(int *number)
{
//	printf("max sizeof(number) = %d\n",sizeof(number)); // 8
	printf("max sizeof(number) = %lu\n",sizeof(number));
	printf("max number = %p\n",number);
	number[0] = 9;
~~~

#### 动态分配内存

添加#include <stdlib.h>头文件。主要是两个函数：

- void* malloc(size_t size)：申请的空间大小以字节为单位；

- free()：只能还申请来的空间的首地址。

~~~c
	int number;
	int* a;
	scanf("%d",&number);
//	int a[number];
//	C99之前需要动态分配内存，运行时确定数组大小 
	
//	强制类型转换，void* --> int* 
//	空间，以字节为单位 
	a = (int*)malloc(number*sizeof(int));
	int i;
	for ( i=0;i<number;i++ ){
//		取a[i]的地址 
		scanf("%d",&a[i]);
	}
	
	for ( i=number-1;i>=0;i-- ){
		printf("%d ",a[i]);
	}
	free(a);
~~~

#### 数组名与指针



#### 二维数组与指针

二维数组 a[i] [j] 与指针 *( *(a+i)+j) 的关系：

    1、首先要理解二维数组是由多个一维数组构成的。例如：a[3][4] = {a[0],a[1],a[2],a[3]};这里的元素：a[0],a[1],a[2],a[3]，其实表示的是一维数组的首地址，也就是说是二维数组a[3][4]的每行数组的首地址。
    2、*(*(a+i)+j)其中的i、j表示偏移量，*号表示取值。
    3、在二维数组a[i][j]中，a[i]表示第i行数组的首地址，a在这里可以理解是二维数组的首行数组的首地址。所以会有： a[i] = *(a+i); 关键点理解：*(a+i) 确实取得的是一个值，它是一个二维数组中第i个元素的地址。
    4、接下来便是： a[i]+j = *(a+i)+j; 等式两边均表示二维数组中第i行第j列元素的地址。
    5、最后： a[i][j] = *(*(a+i)+j)；便是直接取到二维数组中的元素的值。
    
    6、总结：
    a是个二维数组，表示二维数组a的地址；
    a+1是个地址，是二维数组a的第二个元素的地址，也就是a[1]的地址；
    *(a+1)是个值，但也是个地址，其值就是二维数组a的第二个元素的值，也就是a[1]的值，a[1]是个地址，也就是一维数组a[1]的地址；
    因此a+1和*(a+1)其实都是a[1]的地址值，前者是二维数组的地址，后者是二维数组的值，也即一维数组的地址；
    *(a+1)+1也就是a[1]+1，即一维数组a[1]的第二个元素，即a[1][1]的地址；
    *(*(a+1)+1)就是a[1][1]的值；
    a、a[0]、*a都是a[0]的地址，a+1、a[1]、*(a+1)都是a[1]的地址。
    
    可用以下语句验证：
    printf("%d,%d,%d",a,a[0],*a);
    printf("%d,%d,%d",a+1,a[1],*(a+1));
#### 数组传参

- 一维数组作为函数的参数：对于一维数组，指针传参与数组传参没有区别，因为函数值传递。缺点是无法获取到数组的长度，长度需要额外传递；

- 二维数组作为函数的参数：形参中的多维数组必须有一个除第一个之外的所有维度的边界。

~~~c
void test(int a[])
{
	printf("%s：%p\n", __func__, a);
	int i;
	for ( i=0; i<3; i++ )
	{
		printf("%d\t", a[i]);
	}
	printf("\n");
}

// 要向获取数组长度，可以在初始化数组的地方获取到数组的长度，作为参数传递过来
void test2(int *a) // 对于一维数组，指针传参与数组传参没有区别，因为函数值传递，a都是指针 
{
	printf("%s：%p\n", __func__, a);
	printf("sizeof(a) = %d\n", sizeof(a) / sizeof(a[0])); // const int size = 2
	int i;
	for ( i=0; i<3; i++ )
	{
		printf("%d\t", a[i]);
	}
	printf("\n");
}

void test3(int a[], int n)
{
	printf("%s：%p\n", __func__, a);
	int i;
	for ( i=0; i<n; i++ )
	{
		printf("%d\t", a[i]);
	}
	printf("\n");
}

// 编译不通过，多维数组的定义必须有一个除第一个之外的所有维度的边界
// 从实参传递来的是数组的起始地址，在内存中按数组排列规则存放(按行存放)，而并不区分行和列，
// 如果在形参中不说明列数，则系统无法决定应为多少行多少列 
//void test4(int b[][]) // [Error] type of formal parameter 1 is incomplete
// 如果将二维数组作为参数传递给函数，那么在函数的参数声明中必须指明数组的列数，数组的行数没有太大关系，
// 可以指定也可以不指定。因为函数调用时传递的是一个指针，它指向由行向量构成的一维数组
//void test4(int b[][3]) // OK

// void test7(int *b[3]) // // expected 'int *' but argument is of type 'int (*)[3]'
// int *b[3]表示一个一维数组，数组的数据类型为整型指针（int*)，数组的大小为3，这是因为[]的优先级高于*的优先级
// int (*b)[3]声明参数是一个指针，它指向具有3个元素的一维数组
void test4(int (*b)[3]) // 最方便，可以直接使用下标进行索引，不需要进行行列的计算
{
	int i, j;
	for ( i=0; i<2; i++ )
	{
		for ( j=0; j<3; j++ )
		{
			printf("%d\t", b[i][j]);
		}
		printf("\n");
	}
//	b[0][0] = 8; // 可以实现修改 
}

// 动态分配的数组不知道这个维度时，可以用指针将二位数组当做一维数组来操作
// expected 'int *' but argument is of type 'int (*)[3]'
void test5(int *b, int n)
{
	int i, j;
	for ( i=0; i<n; i++ )
	{
		printf("%d\t", b[i]);
	}
	printf("\n");
}

// 手工改变寻址方式实现灵活的去定位到某一行某一列
// expected 'int *' but argument is of type 'int (*)[3]' 
void test6(int m, int n, int *b)
{
	int i, j;
	for ( i=0; i<m; i++ )
	{
		for ( j=0; j<n; j++ )
		{
			printf("%d\t", *(b+n*i+j)); // 二维按照一维去计算 
		}
		printf("\n");
	}
}

//void test7()

int main()
{
//	一维数组 
	int a[3] = {1, 2, 3};
//	test(a);

//	test2(a);

//	int n = sizeof(a) / sizeof(a[0]); // 3
//	printf("n = %d\n", n);

//	test3(a, n);
	
//	二维数组 
	int b[2][3] = {1, 2, 3, 4, 5, 6};
//	test4(b);

//	b是二维数组名，它首先是一个指针，指向一个含有3个元素的int数组
//	而在test5中b指针指向的是int，只包含一个元素 
//	test5(b, 2*3); // 错误写法 expected 'int *' but argument is of type 'int (*)[3]'
//	test5((int *)b, 2*3);  // 正确写法 
//	int *p = &b[0][0]; // 首地址 
//	test5(p, 2*3); // 正确写法
	
//	test6(2, 3, b); // 错误写法
	test6(2, 3, (int *)b); // 正确写法
	
//	printf("%d, %d\n", sizeof(b) / sizeof(b[0][0]), sizeof(b[0]) / sizeof(b[0][0])); // 6, 3
//	printf("b[0] = %p, %p, %p\n", b, b[0], *b); // b[0]的地址
//	printf("b[1] = %p, %p, %p\n", b+1, b[1], *(b+1)); // b[1]的地址
	
	return 0;
}
~~~

#### 指针类型和指针类型转换

**值相同的两个指针所指向的变量的值可以不同。**

存储变量的内存，是由多个字节组成。而指针变量在只知道首地址（第一个字节的地址）的情况下，就能找到指定的内存区域。它是怎么做到的？原因是在声明一个指针变量的时候，会根据它将要指向的变量类型，声明对应的类型，进而规定指针在内存中每次移动的字节数。而不管是什么类型的指针变量，所存的值都是地址（int类型的值）。

“值相同的两个指针变量”，意思是两个指针变量指向同一个首地址。但是如果指针变量的类型不同，因为指针移动的字节数量不同，就可能读取出不同的数据。要实现不同类型指针变量指向同一个地址，需要使用指针类型转换。

~~~c
int main()
{
    short c[2];               // 等价于申请2个连续的内存空间，每个空间2字节 
    c[0] = 1;              　　// 为第一个short空间赋值为1 
    c[1] = 1;              　　// 为第二个short空间赋值为1 
    short *p1 = c;            // p1指向c[]首地址 
    int *p2 = (int *)p1;      // p2指向c[]首地址，并强制转换类型为 int 

    printf("p1指向：%p\np2指向：%p\n",p1,p2);    // 000000000062FE30, 000000000062FE30  
    printf("p1取出：%d\np2取出：%d\n",*p1,*p2);  // 1, 65537
    return 0;
}
~~~

### 字符串

字符数组的末尾加上 0或'\0' 编译器将其转换为字符串。0标志字符串的结束，但它不是字符串的一部分。

字符串以数组的形式存在，以数组或指针的形式访问。

**单引号表示一个字符。双引号表示一个字符串。**

#### 常量与变量

以指针与数组方式生成字符串是不同的，因此适用场景也不同。

- C语言中指针定义的字符串是常量，存储在只读区，不可修改，编译时刻确定，内存地址小，操作系统保护 。用于处理字符串；
- 数组定义的字符串可修改，存储在可读可写区，编译器将字符串常量拷贝一份至本地变量，地址大。用于构造字符串；
- **数组作为参数传入时，都是以指针的形式**。不希望数组被修改时，可以加上const；
- Python语言中字符串string也是常量，是一种不可变的数据类型。字符串运算如"+"会生成新的字符串。

~~~c
//	字符串常量
//	两者地址相同 
	char* os = "hello";
	char* os2 = "hello";
	printf("%p\n",os); 
	printf("%p\n",os2); 
//	指针不存在越界的报错
	printf("%c\n",os[6]); 
	{
		char os[] = "hello";
		printf("%c\n",os[6]);
	} 
	char H[] = "hello";
	char W[] = "world";
//	[Error] invalid operands to binary + (have 'char *' and 'char *')
//	printf("%s\n",H+W);

//	如果需要修改字符串，应该用数组方式定义。编译器将字符串常量拷贝一份至本地变量，地址大。空间自动回收 
//	两者地址不同，地址相差 10 个char
	char os3[10] = "hello";
	char os4[10] = "hello";
	printf("%p\n",os3); 
	printf("%p\n",os4);
	os3[0] = "x";
	printf("*********\n");
//	输出字符失败 变空心
//	0x1F及之前的ascii字符都是不可显示的控制字符
	printf("%s\n",os3);
	printf("%c\n",os3[0]);
	printf("%p\n",os3); 
	
//	字符串常量位于代码段，编译时刻确定，内存地址小，只读，操作系统保护。动态分配内存 
//	[Warning] assignment makes integer from pointer without a cast [enabled by default]
//  赋值从指针生成整数，没有强制转换
//	os[1] = "H";
	printf("%d\n","H"); // integer
	printf("%s\n","H"); // string

	int cnt = sizeof(os)/sizeof(os[0]);
	printf("%d\n",cnt); // 10
	
// 创建字符串的方式不对
//	[Error] subscripted value is neither array nor pointer nor vector
//	char os = "hello";

//	int i;
//	for (i=0;i<sizeof(os)/sizeof(os[0]);i++){
//		printf("%c",os[i]);
//	}

//	c以单字符的形式输出
//	s以字符串的形式输出
	printf("%s",os);
//	printf("%c",os); // 无输出
~~~

~~~python
>>> s = 'ss'
>>> t = 'tt'
>>> s+t
'sstt'
>>> id(s)
2640061697528
>>> id(t)
2640061694896
>>> id(s+t)
2640061696408
>>> s[0] = 'p'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment
~~~

#### 输入与输出

scanf函数中字符串分隔符包括：空格+tab+enter，用于分隔单词。

~~~c
//	[Warning] deprecated conversion from string constant to 'char*'
//	char* o = "huawei";
//	char* t;
	const char* o = "huawei";
	const char* t;
//	字符串赋值没有生成新的字符串 
	t = o;
	printf("%p\n",o);
	printf("%p\n",t);
	
	printf("*********\n");
//	定义空字符串 
//	char n[10] = "";
	char n[] = "";
//	printf("%c\n",n[0]);s
	
	printf("%d\n",sizeof(n)/sizeof(n[0])); // 1
//	[Error] invalid conversion from 'const char*' to 'char' [-fpermissive]
	n[0] = "A";
	printf("%c\n",n[0]);

//	指针创建字符串时无输出，不可修改 WRONG
//	定义char*不代表定义了字符串变量。因为指针还没有被初始化。char*指针指向一个字符（串）
//	char* s;
//	char* m;
//	char* s = 0;
//	char* m = 0;
//	创建字符数组 
	char s[8];
	char m[8];
//	传入指针，但不知道要读入的内容的长度，因此不安全 
//	scanf("%s",s);
//	scanf("%s %s",s,m);
//	字符串分隔符：空格+tab+enter 
	scanf("%s",s);
	scanf("%s",m);
//	安全的方式：7表示该字符串最多读入7个字符。不根据空格分割单词，而是字符个数
//	12345678 --> 1234567**8
//	scanf("%7s",s);
//	scanf("%7s",m);
	printf("%s**%s\n",s,m);
~~~

#### stdio.h和iostream.h

stdio 是C标准库里面的函数库 对应的基本都是标准输入输出等等C语言常用库的定义；

iostream是C++标准库的头定义， 对应的基本上是C++的输入输出相关库定义。iostream里用cin来输入，用cout来输出。

开发C程序用stdio， C++用stdio/iostream 都可以。

输入和输出并不是C++语言中的正式组成成分。C和C++本身都没有为输入和输出提供专门的语句结构。输入输出不是由C++本身定义的，而是在编译系统提供的I/O库中定义的。

C++的输出和输入是用“流”(stream)的方式实现的。图示通过流进行输入输出的过程。程序中使用cin､cout和流运算符 >>与<<。

![img](https://gss1.bdstatic.com/-vo3dSag_xI4khGkpoWK1HF6hhy/baike/c0%3Dbaike80%2C5%2C5%2C80%2C26/sign=14e1663e78094b36cf9f13bfc2a517bc/d1160924ab18972bf14b982be8cd7b899f510ad9.jpg)

有关流对象cin､cout和流运算符的定义等信息是存放在C++的输入输出流库中的，因此如果在程序中使用cin､cout和流运算符，就必须使用预处理命令把头文件stream包含到本文件中 #include <iostream>。

尽管cin和cout不是C++本身提供的语句，但是在不致混淆的情况下，为了叙述方便，常常把由cin和流提取运算符“>>”实现输入的语句称为输入语句或cin语句，把由cout和流插入运算符“<<”实现输出的语句称为输出语句或cout语句。

在C++中的任何运算都是通过函数来实现的，比如说，1+2，编译器会自动将这个表达式解释为:operator+(1,2)；接着再找一下有没有以operator+(int,int)为原型的函数，由于c++已经定义了一个这样的函数，因此会自动调用该函数，然后再将1和2当参数传递进去。同理，对于++运算符，比如++i，系统会自动调用operator++()，参数就是i；在C++中由于多了个对象的概念，为了便于大家对程序的书写，所以规定，对象名的前面和后面跟一个操作符等于“对象.操作符”，如果a是一个对象，那么a++或++a等价于a.++再等价于a.operator++()，即相当于调用了a对象中的一个方法函数opeartor++()。再同理，cout<<"Hello!"等价于cout.operator<<( Hello!)。

对于cout语句，cout是一个ostream类的对象，它有一个成员运算符函数operator<<，每次调用的时候就会向输出设备（一般就是屏幕）输出数据。运算符函数与一般函数基本无异，可以任意重载。因此函数operator<<能够接受不同类型的数据，如整型、浮点型、字符串甚至指针。

operator<<函数执行完毕之后，返回一个它本身的引用，因此如果它后面又紧跟着.operator<<(endl)，这相当于cout.operator<<(endl)——于是又会进行下一个输出，如果往下还有很多<<算符。如：

~~~c++
#include<iostream>
using namespace std;//此句也可以在main函数体中出现。
int main()
{
    int a;
    cout << "请输入一个数字，按回车结束" << endl;
    cin >> a;
    cout << a << endl;
    return 0;
}
//用户输入的数字由cin保存于变量a中，并通过cout输出。
#include<iostream>
using namespace std;
int main()
{
    cout << "Hello,World!" << endl;
	// cout.operator<<("Hello,World!").operator<<(endl); 与上一行语句等价
    return 0;
}//HelloWorld示例
~~~

在定义流对象时，系统会在内存中开辟一段缓冲区，用来暂存输入输出流的数据。在执行cout语句时，先把插入的数据顺序存放在输出缓冲区中，直到输出缓冲区满或遇到cout语句中的endl(或'\n',ends,flush)为止，此时将缓冲区中已有的数据一起输出，并清空缓冲区。也就是说，endl的作用就是向缓冲区插入换行符号并刷新，将内容写入输出设备。输出流中的数据在系统默认的设备(一般为显示器)输出。

#### 字符串数组

- 二维数组创建时，需要指定每个数组的长度；

- 指针创建时，每个指针指向一个数组，因此不需要指定每个数组的长度。

字符串二维数组比如可以使用在main函数，在可执行程序运行时传入参数 。

~~~c
//	[Error] array type has incomplete element type
//	char a[][] = {
//	a[0] == char[10]
	char a[][10] = {
		"enheng",
		// [Warning] initializer-string for array of chars is too long [enabled by default]
//		"kalfjgailjgowigaosgf", 
	};
//	对于长度不定的字符串，使用指针定义更安全
//	char *a[]; 

// 可执行程序运行时可以传入参数 
// 字符串数组，数组长度 
int main(int argc, char const *argv[])
{
	int i;
	for (i=0;i<argc;i++){
		printf("%d : %s\n", i, argv[i]);
	}
// cmd
F:\C-code\imooc>gcc string-4.c
F:\C-code\imooc>a.exe 123 abc
0 : a.exe
1 : 123
2 : abc
~~~

#### getchar() 与 scanf()

EOF，End Of File的缩写，在C语言中，或更精确地说成C标准函数库中表示文件结束符（end of file），返回值为int型。在while循环中以EOF作为文件结束标志，这种以EOF作为文件结束标志的文件，必须是文本文件。在文本文件中，数据都是以字符的ASCII代码值的形式存放。我们知道，ASCII代码值的范围是0~127，不可能出现-1，因此可以用EOF作为文件结束标志。也就是说，正常输入时以\0作为缓冲区的结尾，但继续等待，Ctrl+D 之后shell结尾添加 -1。Ctrl+C 会直接将程序关闭。 

**键盘标准输入与程序之间存在shell。**程序的输入都建有一个缓冲区，即输入缓冲区。一次输入过程是这样的，当一次键盘输入结束时会将输入的数据存入输入缓冲区，而cin函数直接从输入缓冲区中取数据。正因为cin函数是直接从缓冲区取数据的，所以有时候当缓冲区中有残留数据时，cin函数会直接取得这些残留数据而不会请求键盘输入。scanf()和getchar()函数是从输入流缓冲区中读取值的，而并非从键盘(也就是终端)缓冲区读取。

输入（cin）缓冲是行缓冲。当从键盘上输入一串字符并按回车后，这些字符会首先被送到输入缓冲区中存储（将回车也存到缓冲区中）。每当按下回车键后，cin 就会检测输入缓冲区中是否有了可读的数据，这种情况下cin对键盘上是否有作为流结束标志CTRL+Z或者CTRL+D，其检查的方式有两种：阻塞式以及非阻塞式。

阻塞式检查方式指的是只有在回车键按下之后才对此前是否有 Ctrl+Z 组合键按下进行检查，非阻塞式样指的是按下 Ctrl+D 之后立即响应的方式。如果在按 Ctrl+D 之前已经从键盘输入了字符，则 Ctrl+D的作用就相当于回车，即把这些字符送到输入缓冲区供读取使用，此时Ctrl+D不再起流结束符的作用。如果按 Ctrl+D 之前没有任何键盘输入，则 Ctrl+D 就是流结束的信号。阻塞式的方式有一个特点：只有按下回车之后才有可能检测在此之前是否有Ctrl+Z按下。

**输入由字符组成**，scanf可以将输入装换成整数或浮点值。使用%d或%f这样的说明符能限制可接受的输入的字符类型。

~~~c
int a;
float b;
char c;
char d[10];

//	终端统一输入42，输出不同 
//	scanf("%c", &c); // scanf()中使用%c说明符，该函数将只读取字符4 并将其存储在一个char类型的变量中
//	printf("%c", c); // 4
//	scanf("%d", &a); // 如果使用%d说明符，则scanf 读取同样的两个字符，
//但是随后它会继续计算与它们的相应的整数值为4*10+2  得到 42，然后将该整数的二进制表示保存在一个int变量中
//	printf("%d", a); // 42
//	scanf("%f", &b); // 如果使用%f说明符 则scanf（）读取这两个字符 计算它们对应的数值 42，
//然后以内部的浮点表述该值，并将结果保存在一个float变量中
//	printf("%f", b); // 42.0000
	scanf("%s", &d); // 如果使用%s说明符，该函数会读取两个字符，即字符4和字符2，并将它们存储在一个字符串中。 
	printf("%s", d); // 42
	printf("%d", sizeof(d)); // 10
~~~

scanf可以一次按照设定的输入格式输入多个变量数据，getchar()只能输入字符型,输入时遇到回车键才从缓冲区依次提取字符。getchar() 和使用%c的 scanf() 接受同样的任何字符。

在scanf("%c", &a)这样的语句前后，如果要输入空白字符，则一定要在后面加上getchar()来吃掉空白字符，相当于清除缓冲区，如(包括空格、回车和Tab)。

#### strcmp()

手写字符串比较函数。返回的是两个字符串对应元素ASCII码的差，int类型，因此相等时会返回0。

~~~c
int mycmp(char const* str1, char const* str2)
{
//	方法1：需要求取字符串的长度 
//	int index;
//	int num;
//	for (index=0;index<strlen(str1);index++){
//		if (str1[index] != str2[index]){
//			num = str1[index] - str2[index];
//			break;
//		}
//		else{
//			num = 0;
//		}
//	}
//	return num;

//	方法2：不需要求取字符串的长度 
//	int idx;
//	while (1){
//		if (str1[idx] != str2[idx]){
//			break;
////		不需要求取字符串的长度 
//		} else if (str1[idx] == '\0'){
//			break;
//		}
//		idx ++;
//	}
	
//	方法3：对2的简化，使用index遍历数组 
//	int idx;
//	while (str1[idx] == str2[idx] && str1[idx] != '\0'){
//		idx ++;
//	}
//	return str1[idx] - str2[idx];

//	方法4：使用指针遍历数组，不需要index变量 
//	while (*str1 == *str2 && *str1 != '\0' ) {
////		char* str1表示*str1是char，所以str1是指针 
//		str1 ++;
//		str2 ++;
//	}
//	return *str1 - *str2;
	
}
int main(int argc, char const *argv[])
{
	char str1[] = "hello";
	char str2[] = "hello";
	char str3[] = "Hello";
	char str4[] = "kello";
	int num = mycmp(str1, str3);
	printf("num = %d\n", num);
 	printf("%d\n", str1 == str2); // 地址不同 
	printf("%d\n", strcmp(str1, str2)); // 0
	printf("%d\n", strcmp(str1, str3)); // 1 str1 > str3, 'h' > 'H'
	printf("%d\n", strcmp(str1, str4)); // -1 str1 < str3
	
	return 0;
}

//  strlen()函数返回字符串存内容的长度
	char a[] = "hello";
	printf("strlen = %d\n", strlen(a)); // 5
	printf("sizeof = %d\n", sizeof(a)); // 6
~~~

#### strchr()

字符串中寻找特定字符。取特定字符后的字符串，返回指针。

~~~c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

int main(int argc, char const *argv[])
{
	char s[] = "hello";
//	字符串中寻找特定字符。取特定字符后的字符串，返回指针 
	char *p = strchr(s, 'l');
	
//	取特定字符前的字符串 
	char c = *p; // 备份 
	*p = '\0';
	printf("%s\n", s); // he
	char *t = (char*)malloc(strlen(s)+1);
	strcpy(t, s);
	*p = c;
	printf("%s\n", s); // hello
	printf("%s\n", t); // he
		
//	printf("%s\n", s);
////	printf("%c\n", *p); // 1 -- wrong
//	printf("%p\n", p);
////	字符串的访问方式与数组一样，不用再取地址！！
//	printf("%s\n", p); // ll0
//	
//	char *t = (char*)malloc(strlen(p)+1);
//	strcpy(t, p);
//	printf("%s\n", t); // llo
//	free(t);
//	第二次出现 
//	p = strchr(p+1, 'l');
//	printf("%s\n", p); // lo
//	printf("%c\n", p); // C
	
	return 0;
} 
~~~

### 结构类型

#### 枚举

**枚举是int。**枚举是一种用户定义的数据类型，使用关键字 enum{}，可用于存储一些可以排列的常量值（枚举量 / 符号量），之间用逗号分隔。每个常量对应一个int，取值从0开始，通常使用整数来做内部计算和外部输入输出。  

#### 结构体

- 结构是一种符复合数据类型，使用关键字 struct{}，内部可以包括不同类型的成员变量，之间用分号分隔；

- 创建变量时要将 struct + 结构名 作为类型的名字，使用方法类似于int，后面跟上变量的名字，也就是说声明结构类型与定义结构变量分开；

- 结构不同于数组，如结构的名字不是地址，类型不是指针；

- 指针 p->成员名用于获取指针所指向的结构变量中的成员，读作p所指的成员。等价于 (*p).成员名；
- 自定义数据类型 typedef 用于声明一个已有的数据类型的新名字，如 typedef struct date{} Date 原类型 新名字。定义时不需要再写struct。

~~~c
struct date{
	int month;
	int day;
	int year;
};

struct date* getStruct(struct date *d);
void output(struct date d);
void print(const struct date *d); // 常量，不可修改 

int main(int argc, char const *argv[])
{
	struct date y = {
		0, 0, 0
	};
	output(y);
	getStruct(&y);
	output(*getStruct(&y)); // *取值 &取地址 
	print(getStruct(&y));
	return 0;
}

struct date* getStruct(struct date *d)
{
	printf("Eenter the date:\n");	
//	获取指针所指向的结构变量中的成员，读作p所指的成员 
	scanf("%d%d%d", &(d->month), &(d->day), &(d->year)); // d->year == (*d).month
	return d;
}

void output(struct date d)
{
	printf("date = %d-%d-%d\n", d.month, d.day, d.year);
}

void print(const struct date *d)
{
	printf("date = %d-%d-%d\n", d->month, d->day, d->year);
}
~~~

#### 联合

union，与结构体很类似，区别在于所有的成员共享一个空间，同一时间只有一个成员是有效的，union的大小是其最大的成员。如用于得到整数内部的各个字节，文件操作中使用。

~~~c
typedef union {
	int i;
	char ch[sizeof(int)];
} CHI;

int main(int argc, char const *argv[])
{
	CHI chi;
	chi.i = 1234;
	printf("0x%x\n", chi.i); // 0x4d2 小端CPU，在内存中低位在前 
	printf("0x%X\n", chi.i); // 0x4D2
	for ( i=0; i<sizeof(int); i++ ){
		printf("%02hhX", chi.ch[i]); // FFD2040000
	}
	
	return 0;
}
~~~

#### 本地变量与全局变量

- 全局变量定义在函数外部，作用域与生存期一致，没有初始化也会有默认0值（指针NULL）；
- 本地变量定义在函数内部，作用域与生存期一致，仅限于函数内部；
- 静态本地变量多次调用时初始化只做一次，保存局部变量。静态本地变量是特殊的全局变量，位于相同的内存区域。作用域与生存期不一致，具有全局生存期，局部作用域；
- 全局变量定义在函数体外部，在全局数据区分配存储空间，且编译器会自动对其初始化。普通全局变量对整个工程可见，其他文件可以使用extern外部声明后直接使用。也就是说其他文件不能再定义一个与其相同名字的变量了（否则编译器会认为它们是同一个变量）。静态全局变量仅对当前文件可见，其他文件不可访问，其他文件可以定义与其同名的变量，降低程序模块之间的耦合，避免不同文件同名变量的冲突；
- 函数的使用方式与全局变量类似，在函数的返回类型前加上static，就是静态函数。静态函数只能在声明它的文件中可见，其他文件不能引用该函数。这样不同的文件可以使用相同名字的静态函数，互不影响。


~~~c
int* f(void);
void g(void);

int main(int argc, char const *argv[])
{
	int *p = f();
	printf("p = %d\n", *p); // 12
	g();
	printf("p = %d\n", *p); // 24
	
	return 0;
}

int* f(void)
{
	int m = 12;
	printf("&m = %p\n", &m);
//	函数返回本地变量的指针是危险的，m与n的地址相同 
	return &m; // [Warning] function returns address of local variable [-Wreturn-local-addr]
//	使用全局变量和静态本地变量的函数是线程不安全的，因此不要使用全局变量来在函数间传递参数与结果 
}

void g(void)
{
	int n = 24;
	printf("&n = %p\n", &n);
}
~~~

#### 编译预处理与宏

- #开头的都是编译预处理指令，不是C的语句，不需要分号结尾。缺点是没有参数的类型检查；
- 用于在编译之前生成临时文件，如 #include 头文件 / #define 宏macro；
- 如 gcc variable.c --save-temps 编译的顺序为：.c（源文件） --> .i（编译预处理临时文件如宏的文本替换、不含注释） --> .s（汇编代码文件） --> .o（目标代码文件） --> .out（Linux） / .exe（Windows）（可执行文件）；
- 带参数的宏与函数的区别在于宏在程序运行前执行，需要注意的是整个值需要有括号，而且参数出现的每个地方也都要加括号。宏的运行效率高于函数，但是由于展开导致占用内存大，这也是一种以空间换时间；
- 使用指令【gcc abc.c】生成abc.c对应的.exe文件，直接执行指令【a.exe】。

~~~c
#include <stdio.h>
#define DAT1(x) (x * 12)
#define DAT2(x) (x) * 12
#define DAT3(x) ( (x) * 12)

int main(int argc, char const *argv[])
{
	printf("DAT1 = %d\n", DAT1(2 + 1)); // 14
	printf("DAT2 = %d\n", 10 - DAT2(2)); // -14
	printf("DAT3 = %d\n", DAT3(2 + 1)); // 36
	
	return 0;
}
~~~

#### 头文件

项目中的文件叫做单元，链接器链接多个.c文件。

把函数原型放到一个项目中的头文件（以.h结尾）中，在需要调用这个函数的源代码文件（.c文件）中#include这个头文件将函数原型文本插入，就能让编译器在编译的时候知道函数的原型。当函数原型声明与函数定义、函数调用参数类型不一致时会报错。

#include有两种形式来指出要插入的文件：

- “”要求编译器首先在当前目录（.c文件所在的目录）寻找这个文件，如果没有，到编译器指定的目录去找，如自定义的头文件；

- <>让编译器只在指定的目录去找。编译器自己知道自己的标准库的头文件在哪里。环境变量和编译器命令行参数也可以指定寻找头文件的目录。现在的C语⾔言编译器默认会引入所有的标准库。

一般的做法就是任何.c（除了main.c）都有对应的同名的.h，把所有对外公开的函数的原型和全局变量的声明都放进去。函数和全局变量前面加上static会使得它成为只能在所在的编译单元（.c文件）中被使用的函数和全局变量。

~~~c
// main.c
#include <stdio.h>
#include "max.h"

//int max(int m, int n);

int main(int argc, char const *argv[])
{
	printf("%s\n", __FILE__); // main.c
	int m = 5;
	int n = 6;
	printf("max = %d\n", max(m, n));
	
	return 0;
}

// max.c
#include "max.h"

//[Warning] incompatible implicit declaration of built-in function 'printf' [enabled by default]
//[Error] conflicting types for 'max' 头文件内容为函数原型
float max(int m, int n)
// int max(int m, int n)
{
	printf("%s\n", __FILE__); // max.c
	return m>n?m:n;
}

// max.h
int max(int m, int n);
~~~

#### 定义与声明

定义与声明不同，定义产生代码，声明不产生代码。因此定义只包括函数与全局变量的定义。编译器遇到声明不运行。 

变量的声明使用extern关键字，不可以初始化，而定义可以初始化。类似于函数的原型是声明。

为避免在同一个.c文件里头文件中出结构的重复声明，可以使用标准头文件结构，运行条件编译和宏。

 ### 文件

#### 格式化输入输出

- 格式字符串；
- scanf与printf函数都有返回值，其中scanf 输入项目数，printf 输出字节数，包括回车符！

~~~c
	int m;
//	scanf("%d%*d", &m); // * 跳过
//	scanf("%i", &m); // 根据输入的进制进行存储 012 --> 10 
	int i1 = scanf("%*[^,], %d", &m); // abc, 123 --> 123 [^,]逗号前面的部分作为一个字符串读入 
	int i2 = printf("%d\n", m); // 123
	// scanf 输入项目数 printf 输出字节数，包括回车符！
	printf("%d: %d\n", i1, i2); // 1: 4 
	   
//	scanf("%hh", &m);
//	printf("%d\n", m);

//	printf("%d\n", 123); // 默认左对齐 
//	printf("%9d\n", 123); // 右对齐 
//	printf("%-9d\n", 123); // - 左对齐 
//	printf("%*d\n", 6, 123); // * 指定总字节数 
//	printf("%d%n\n", 123, &m); // %n 输出总字节数 
//	printf("%d\n", m);
//	printf("%9.2f\n", 123.0); // . 前面是总字节数，后面是小数点后的位数 
//	printf("%hhd\n", 12345789); // hhd 输出一个字节的整数 
~~~

#### 文件输入输出

 FILE文件结构。

~~~c
	FILE* fp = fopen("12.in", "r"); // 文件结构体 
	if (fp) {
		int num;
		fscanf(fp, "%d", &num);
		printf("%d\n", num);
		fclose(fp);
	} else {
		printf("无法打开该文件");
	}
~~~

#### 二进制文件

所有的文件最终都是二进制文件。文本文件是易于读写的一种文件（字符），如输入输出都是文本。而二进制文件需要专门的程序来读写（字节），如可执行程序、图片、音频。二进制文件的读写一般都是通过对一个结构变量的操作来进行的。

以前写C的程序，定义一个结构体，使用二进制文件，fwrite写入 + fseek定位 + fread读取，不具有可移植性，如int。文本可以解决跨平台的可移植性。

#### 位运算

一个十六进制数，如0xf，代表16个数字，2的4次方，即4位，所以两个十六进制数如0xff就是一个字节。

二进制和十六进制的对应关系如下图所示。

![img](https://gss0.baidu.com/-4o3dSag_xI4khGko9WTAnF6hhy/zhidao/wh%3D600%2C800/sign=ae7a1a0f22738bd4c474ba3791bbabee/9345d688d43f8794d00c2dfcde1b0ef41ad53a74.jpg)

所有的位运算结果都是int。 常见位运算符含义描述：

& 按位与      如果两个相应的二进制位都为1，则该位的结果值为1，否则为0；

| 按位或      两个相应的二进制位中只要有一个为1，该位的结果值为1；

^ 按位异或    若参加运算的两个二进制位值相同则为0，否则为1；

~ 取反        ~是一元运算符，用来对一个二进制数按位取反，即将0变1，将1变0；

<< 左移       用来将一个数的各二进制位全部左移N位，右补；

右移       将一个数的各二进制位右移N位，移到右端的低位被舍弃，对于无符号数，高位补0。

- 逻辑运算的结果为0或1。逻辑运算相当于把所有非0值都变成1，然后做按位运算。因此，逻辑运算不等价于按位运算；

- 异或运算相等为0，不等为1。对一个数值做两次相同的异或运算可以得到原始的数值，可用于加密。即 x ^ y ^ y --> x；

- 左移的时候有无符合没区别，右边填0；

- 右移的时候有区别，对于unsigned的类型，左边填0，对于signed的类型，左边保持符号位不变。

~~~c
	unsigned char c = 0xAA; // 1个字节 
	printf("%hhx\n", c); // aa
	printf("%hhX\n", c); // AA
	printf("%d\n", c); // -171 / 170
	printf("%hhx\n", ~c); // 按位取反，返回int ff55 
	printf("%hhx\n", (char)~c); // 55
	printf("%hhx\n", -c); // 补码 ff56 
	printf("%hhx\n", (char)-c); // 56
	printf("c = %d\n", c); // 170
	printf("c<<2 = %d\n", c<<2); // 680
	printf("c>>2 = %d\n", c>>2); // 42
	
	signed char d = 0xAA;
	printf("d = %d\n", d); // -86
	printf("d>>2 = %d\n", d>>2); // -22
	
	// 如可用于输出整数的二进制位
	int number;
	scanf("%d", &number);
	unsigned int mask = 1u<<31; // 0x1000...000
	for (; mask; mask>>=1) { // 右移 
		printf("%d", number & mask?1:0);
	}
~~~

### 数组与链表

#### 数组名与指针

[数组名不是指针](https://blog.csdn.net/ljob2006/article/details/4872167)。数组名可作为指针常量。

关于数组名的三个结论：

- 数组名的内涵在于其指代实体是一种数据结构，这种数据结构就是数组。如char a[10]表示a是char[]类型；

- 数组名的外延在于其可以隐式退化（类型转换）为指向其指代实体的指针，而且是一个指针常量。如 a++报错，因为a表示的是这个数组的首地址和第一个元素的地址，不能直接去操作a++去移动地址，如果想移动可以char *p = a；然后再操作p，p++这样是完全正确的。而当数组名作为函数形参时，在函数体内，其失去了本身的内涵，仅仅只是一个指针，同时失去了其常量特性，可以作自增、自减等操作，可以被修改；

- 指向数组的指针（数组指针）则是另外一种变量类型，仅仅意味着数组的存放地址！指针，不管是指向结构体、数组还是基本数据类型的指针，都不包含原始数据结构的内涵，在WIN32平台下，sizeof操作的结果都是4。因此指针作为参数传递时，经常要一个长度。sizeof并不是一个函数，实际上它是一个操作符，不过其使用方式看起来的确太像一个函数了。语句sizeof(int)就可以说明sizeof的确不是一个函数，因为函数接纳形参（一个变量），世界上没有一个C/C++函数接纳一个数据类型（如int）为"形参"。

一般来说，由C/C++编译的程序会在运行的时候在内存中占用一些空间，它们分为以下几个部分：

- 二进制代码区 不必过多解释了，就是放二进制代码的地方。
- 常量区 存放文字字符串和常量
- 静态存储区 存放静态和全局变量
- 堆空间 动态内存区，程序员可控制分配和释放的区域。
- 栈空间 由编译器分配内存用于存储函数参数和普通变量。

malloc能操作的是程序中的堆空间，而普通的数组则是存放在栈空间里面的，操作系统分配的内存，一般只有栈空间是连续的。通过malloc(字节数)就可以从内存中找到了一片可以安全存放数据的可用空间，操作系统是用类似于链表的方式来管理这些分片的内存空间的。为了找到这片空间，malloc函数会告诉你这片空间开头的地址，你可以把它赋值给一个变量存放起来。这样我们就知道申请到的这片内存的首地址（malloc返回）和大小（程序员指定）了。

C语言的指针也有类型，但是指针总是内存地址，是一个（32位/64位）二进制整数，长度也好大小也好都是确定的，理应一种类型就够了。那么，指针类型的作用是什么呢？其实指针类型就是用于判断指针所指向的数据的类型。因此指针值是没有变化的，变化的只是指针类型而已，如int *、double *，这样让编译器知道其中每个数据占多少空间。因此malloc函数在赋值给指针之前要有一个强制类型转换，否则void *类型一般应该是读不出数据的。

给指针malloc分配空间后严格的说不等于数组，但是可以认为它是个数组一样的使用而不产生任何问题。所以，一般我们都用“动态数组”这种名字来称呼这种东西。C语言为使得我们申请到的一片内存连续区域，可以使用[ ]操作符，像数组一样的访问到。指针本质就是个整数，但是指针的运算不是简单的改变这个整数，而是指向下一存储区这样的意思。[ ]操作符在普通数组上和用malloc生成的动态数组上的操作是完全一样的，都是类似于*(a+i)的操作。因此malloc分配的空间和数组主要的区别还是存放的内存区域在操作系统对内存管理上的区别，还有就是使用完了以后记着用free释放掉。

~~~c
//	数组名m其实被声明为常量指针const int *m，它同样存储的是数组的首地址。
	int m[] = {1, 2, 3}; // 数组
	
//	int n[3];
//	n = m; // 数组名代表指针常量,所以说赋值号左边必须是一个变量，因此这种表达错误 
//	m ++; // lvalue required as increment operand

	printf("%d\n", sizeof(m[1])); // 4(int) 
	printf("%d\n", sizeof(&m[1])); // 64位：8*8 32位：4*8
	
//	移动指针，int *类型的指针a，把a往后移4个字节到下一个元素的操作是a=a+1，而不是a=a+4 
	printf("%d\n", *(&m[1] + 1)); // 3 (1代表一个单位，而不是单纯意义上的加1，以sizeof(int)为移动单位)
	printf("%d\n", *(&m[1] + 4)); // 32760
//	为了简化访问方法，C语言使用了一种简单的对指针运算——[ ]下标运算。运行a[int]，编译器会返回 *(a+int) 的值。C语言为我们提供了这样的方法，使得我们申请到的一片内存连续区域，可以使用这样的方法，像数组一样的访问到。 
	printf("%d\n", m[2]); // 2
	
//	++与--运算符是C语言提供的增1运算符和减1运算符，它们都是单目运算符，只需要一个操作数，但操作数只能是变量，不能是常量或表达式。至于你说的它们的使用形式，只能跟一个变量搭配使用，作前缀运算符或后缀运算符，但是只要是变量就行。记住它们的作用是使变量的值增加1 个单位或减少1个单位，而并是单纯意义上的加1或减1。
	
//	sizeof(数组名)和sizeof(指针)不同，数组名不是指针！ 
//	用sizeof关键字求数组所占的内存是整个数组大小，指针不是
//	因此指针作为参数传递时，经常要一个长度
	printf("%p: %d\n", &m, sizeof(m)); // 12
	int *p = (int*)malloc(sizeof(m)); // 动态数组
	printf("%p: %d\n", p, sizeof(p)); // 8
	
	int i;
	for (i=0; i<sizeof(m)/sizeof(int); i++) {
		printf("%p: %d\n", &m[i], m[i]);
	}
	
//	for (i=0; i<sizeof(m)/sizeof(int); i++) {
//		p[i] = m[i];
//	}
	p = m; // 取地址运算，可以实现数组与数组之间的直接赋值，将数组a的首地址赋给b数组，通过指针来实现
	
//	用sizeof关键字求数组所占的内存是整个数组大小，指针不是
//	for (i=0; i<sizeof(p)/sizeof(int); i++) { // 8
	for (i=0; i<sizeof(m)/sizeof(int); i++) { // 12
		printf("%p: %d\n", &p[i], p[i]);
	}

	/*
    000000000062FE20: 1
    000000000062FE24: 2
    000000000062FE28: 3
    000000000062FE20
    00000000007413C0
    00000000007413C0: 1
    00000000007413C4: 2
    00000000007413C8: 3
	*/

	free(p);
~~~

#### 可变数组

定义array_inflate()函数，设置BLOCK_SIZE常量，实现动态扩容，需要拷贝。

~~~c
const BLOCK_SIZE = 20;

// 自定义可变数组 
typedef struct {
	int *array; // 指针 
	int size; // 大小 
} Array; // 返回array，不返回指针 

// 初始化 
Array array_create(int init_size)
{
	Array a;
	a.size = init_size;
	a.array = (int*)malloc(sizeof(int) * a.size);
	return a;
}

// 释放 
void array_free(Array *a)
{
	free(a->array);
	a->array = NULL; // 防止调用两次 
	a->size = 0;
}

// 封装 
int array_size(Array *a)
{
	return a->size;
}

int *array_at(Array *a, int index)
{
	if (index >= a->size) {
		array_inflate(a, (index/BLOCK_SIZE+1)*BLOCK_SIZE - a->size); // 越界
	}
	return &(a->array[index]);
}

int array_get(const Array *a, int index)
{
	return a->array[index];
}

void array_set(Array *a, int index, int value)
{
	a->array[index] = value;
}

// 动态扩容 
void array_inflate(Array *a, int more_size)
{
	int *p = (int*)malloc(sizeof(int) * (a->size + more_size));
	int i;
	for (i=0; i<a->size; i++) {
		p[i] = a->array[i];
	}
	free(a->array);
	a->array = p;
	a->size += more_size;
}

int main(int argc, char const *argv[])
{
	Array a = array_create(10);
	
	printf("%d\n", array_size(&a)); // 优于 a->size
	
//	array_at(&a, 0) = 10; // 赋值语句的左边应该是变量，不能是表达式
//	*array_at(&a, 0) = 10; // 返回指针的原因 
//	printf("%d\n", *array_at(&a, 0));
	
	int number;
	int cnt = 0;
	while (number != -1) {
		scanf("%d", &number);
		*array_at(&a, cnt++) = number;
//		scanf("%d", array_at(&a, cnt++));
	}
	
//	指针 == get + set 
//	int m = array_get(&a, 0); // 需要多定义一个变量 
//	array_set(&a, 0, 20);
//	printf("%d\n", array_get(&a, 0));
	
	array_free(&a);
	
	return 0;
}
~~~

#### 链表

可变数组的缺点是拷贝。

实现链表添加元素。使用指针时要注意识别**边界情况**。在->左边时需要检查是否为NULL确保代码安全。

~~~c
typedef struct _node
{
	int value;
	struct _node *next; // 编译器还未出现 Node 
} Node;

int main()
{
    Node *head = NULL; // 用于定位该链表 
	int number;
	do
	{
		scanf("%d", &number);
//		add to link-list
		Node *p = (Node*)malloc(sizeof(Node));
		p->value = number;
		p->next = NULL;
//		find the last
		Node *last = head;
		
		if ( last != NULL ) // head == NULL
		{
			while ( last->next )
			{
				last = last->next;
			}
	//		attach
			last->next = p;
		} else 
		{
			head = p; // 修改 head 为链表第一个元素  
		}
		
	} while ( number != -1 );
    
    return 0;
}

// 封装为函数
typedef struct _list
{
	Node *head;
} List; 

void add(List *pList, int number)
{
//	add to link-list
	Node *p = (Node*)malloc(sizeof(Node));
	p->value = number;
	p->next = NULL;
//	find the last
	Node *last = pList->head;
	
	if ( last != NULL ) // head == NULL
	{
		while ( last->next )
		{
			last = last->next;
		}
//		attach
		last->next = p;
	} else 
	{
		pList->head = p; // 修改 head 为链表第一个元素 
	}
}

int main(int argc, char const *argv[])
{
	List list; // 代表整个链表 
	list.head = NULL;
	int number;
	do
	{
		scanf("%d", &number);
		add(&list, number); // 指针传值，局部变量，实现修改 
	} while ( number != -1 );
    
    // 链表删除元素
    scanf("%d", &number);
	Node *p;
	Node *q;
	int isFound = 0;
	for ( q=NULL, p=list.head; p; q=p, p=p->next )
	{
		if ( p->value == number )
		{
			if ( q ) // 删除的不是链表的第一个元素 
			{	
				q->next = p->next;
			} else 
			{
//				q = p->next; // wrong
				list.head = p->next;
			}
			free(p);
			printf("找到了\n");
			isFound = 1;
			break;
		}
	}
}
~~~







<https://www.cnblogs.com/huangbiquan/p/7783057.html>

<https://www.cnblogs.com/arvintang/p/5115698.html>

https://www.cnblogs.com/WangGuiHandsome/p/9876647.html
https://wenku.baidu.com/view/3ad499d2b9f3f90f76c61bde.html
https://en.wikipedia.org/wiki/Code_page_437
https://www.cnblogs.com/lubia/p/3177098.html
https://blog.csdn.net/aidem_brown/article/details/49794831