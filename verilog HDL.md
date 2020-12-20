### Verilog HDL

1. **基本介绍**

```
HDL：硬件描述语言	Verilog：硬件描述语言的名字
主要功能：
	1.包含基本逻辑门(and,or,nand)
	2.两种基本数据类型(线网和寄存器)，线网表示构件间的物理连线，寄存器表示抽象的数据存储元件。
	3.采用三种方式对设计建模。行为描述方式——使用过程化建模；数据流方式——使用连续赋值语句方式建模；结构化方式——使用基本逻辑门和自定义模块实例化建模。
----------------------------------------
和VHDL区别：
	1.相同点：都是用于逻辑设计的硬件描述语言，能够形式化地抽象表示电路的行为和结构。
	2.不同点：Verilog HDL和C语言很像，相对更灵活，而VHDL更加严谨；在底层门级开关电路描述方面，Verilog HDL更擅长；在上层系统级抽象描述方面，VHDL更擅长
```

2. **词法标记**

```verilog
1.标识符(对大小写敏感)：包括0~9、26英文字母、_以及$,以字母或_开头。
----------------------------------------
2.扩展标识符：\开头，空白符结束。
----------------------------------------
3.空白符：空格+制表符\t+换行符\n+回车符
----------------------------------------
4.注释：和C语言一样
----------------------------------------
5.值集：对应1bit的取值范围，包括：0(低电平)、1(高电平)、x(未知逻辑值)、z(高阻状态)。
----------------------------------------
6.数：
	1.整型数。格式：占用的bit数+'+基数+值。	  Eg:4'b1010——4位二进制数1010
	  Eg:3'D3——3位十进制数3
	  Eg:4'B11zx——4位二进制数11zx
	  Eg:6'b1z——6位二进制数00001z
	  注：若省去占用的bit，会给出默认位宽(32)          －只能出现在最前面
	  注：空格不能在'和基数之间
	2.浮点数。小数表示法、指数表示法。
	  Eg:1.2——小数表示法
	  Eg:1.2E3——指数表示法
----------------------------------------
7.字符串：每个字符由8bit表示,包含在""内。
----------------------------------------
8.系统函数：以$开头。Eg:$display	打印
```

3. **代码的基本结构**

```verilog
module 模块名(		  //模块接口定义
	input I_a,		//输入
    input I_b,
    output O_sum,	//输出
    output O_count
);
    
assign O_sum = I_a^I_b;	//连续赋值方式
assign O_count = I_a&I_b;
    
endmodule
```

4. **数据对象**

```verilog
1.线网型：类似于电路板上的导线。主要包括wire(普通连线)、tri(三态门的wire线)等。
定义格式：线网类型+[signed](可选)+[vectored|scalared](可选)+对象列表
其中：
	[signed](可选):有符号数据对象
	[vectored|scalared](可选):矢量或标量
使用：
Eg:wire cout	  //1位的wire类型对象
Eg:wire[7:0] data //8位的wire类型对象
Eg:wire vectored[7:0] bus//vectored类型的8位wire
注：线网型数据对象的写操作只能在assign连续赋值语句中使用。
    vectored和scalared的区别：scalared的数据对象不能进行某一位的读/写操作。
    Eg:wire vectored[7:0]data;
       assign a = data[0];
       assign b = data[5:3];
	Eg:wire scalared[7:0]data;
       assign b = data[5:3];
----------------------------------------
2.寄存器型：最常用的数据对象之一。
定义格式：reg+[signed](可选)+[vectored|scalared](可选)+对象列表
使用方法和线网型非常类似。
Eg:reg scalared[7:0] data
   data[1]=1'b1//写操作
注：assign连续赋值语句中不能使用寄存器型对象。
----------------------------------------
3.存储器型：寄存器的组合，大小由宽度和深度决定。
定义格式：reg[m1:l1]memory[m2:l2]
其中：
    [m1:l1]:memory的宽度
    [m2:l2]:memory的深度
Eg:reg[7:0]memory[63:0]//宽度8位，深度64位
    memory[0]=8'h12
----------------------------------------
4.整型：默认32位
定义格式：integer+变量名
Eg:integer data
Eg:integer idata[7:0]//整型对象数组 
   idata[1]=1'b0
----------------------------------------
5.时间型：一般不少于64位
定义格式：time+变量名
Eg:time data
Eg:time idata[7:0]//时间型对象数组
----------------------------------------
6.实型：默认值为0，x与z看成0处理
定义格式：real+变量名
Eg:real data=1.2
----------------------------------------
7.参数：用来定义常量
定义格式：parameter 变量名=值
Eg:parameter HIGH=1
类别：普通参数(可重新赋值)和局部参数(不可改变)
Eg:localparam real PI=3.14
----------------------------------------
8.字符串型：每个字符由8bit表示,包含在""内。
```

5. **特殊操作符**

```verilog
1.赋值：=或<=
其中：
	=用在连续赋值assign和阻塞进程块赋值中
    <=用在非阻塞进程块赋值中
Eg:assign data = ~I_data
Eg:always@(data_A,data_B)
   begin
       data = data_A&data_B;
   end
Eg:always@(posedge clk)//clk上升沿有效
   begin
       q<=d;
   end
----------------------------------------
2.逻辑运算符
操作数是多位时，只要有1位是1，则为真
Eg:r = 4'b1010	//r为真
操作数含有z或x，不能确定结果的情况下返回未知值x
Eg:r = 1'b0x0z
   r&&1'b1		//返回1'bx
----------------------------------------
3.关系运算符
操作数只要有一位为x或z，结果都为x
Eg:8'b10010x00>5'b11000	//返回1'bx
操作数位宽不同，无符号数补0，有符号数补符号位
Eg:8'sb00010000>5'sb11000
//相当于8'sb00010000>5'sb11111000，真
注：两个数只要有一个无符号数，就按无符号数比较
----------------------------------------
4.相等操作符
相等==，不相等!=
操作数中只要有一位为x或z，结果就为x
全等===，非全等!==
全等是将x和z当作字符值进行比较，对应位全相等才行
----------------------------------------
5.位操作符
非：~		异或：^	同或：~^
位运算包括多位、一位运算
Eg:input[7:0]i_a;
   output[7:0]o_a;
   assign o_a = ~i_a[0]
   assign o_a = ~i_a
----------------------------------------
6.缩减操作符(符号和位操作符一致)：操作数中所有位进行两两位操作，直到产生一位的结果。
注意：缩减操作符在操作数之前
Eg:^data = 8'b10110010
//按位异或，结果：0
----------------------------------------
7.移位操作符
逻辑左移/右移<</>>	算术左移/右移<<</>>>
逻辑移位补0，算术左移补0，右移正数补0，负数补1
----------------------------------------
8.拼接操作符：将不同的信号拼接在一起形成位数更多的信号。
       格式一：{s,a,b}	//位宽：s+a+b
       格式二：{2{s}}	//位宽：2*s
       格式三：{2{s},a}	//位宽：2*s+a
Eg:a=1'b1;b=2'1b10
{a,b}=3'b110
```

6. **并行语句**

```verilog
//★并行语句的执行没有先后关系，可以同时执行
1.连续赋值语句assign
格式：assign var=表达式
注：var必须是线网型变量
★连续赋值的意义：只要表达式发生变化，var就会即时驱动，发生变化。
Eg:input [7:0]a;
   input [7:0]b;
   output[7:0]sum;
   output cout;//进位位
   assign {cout,sum}=a+b
重要应用：对双向端口(同一时刻只能执行读/写中的一种)的操作
Eg:module example(
	input din,//写入
	input wr,//读写控制信号,0写/1读
	output dout,//读出
	inout data//双向端口
);
    assign data = (~wr)?din:1'bz;//写数据
    assign dout = data;//读数据
----------------------------------------
2.模块实例化语句：允许直接用现有的模块
verilog HDL自带模块：门级电路模块(原语形式)
    ①多输入门电路：与门(and)、与非门(nand)等
    ②多输出门电路：非门(not)、缓冲门(buffer)
    ③三态门：bufif0,bufif1,notif0,notif1
使用格式如下：
nand 实例名(nand_out,input1,...,inputn)
buf 实例名(output1,...outputn,input1)
bufif0 实例名(dout,din,ctrl(控制信号))
注：ctrl有效，输入输出有关系，无效则输出z或x
自定义模块实例化格式：模块名+实例化名(端口连接方式)
其中：
    端口连接关系可以根据位置连接也可以按名称连接
Eg:按端口名称连接
module 模块名(
    input a;
    input b;
    output y
);
模块名 实例名(
	.a(d_one)
    .b(d_two)//d_one、d_two为传入参数
    .y(result)//result为结果
);
----------------------------------------
3.initial初始化语句(只执行一次)
一般用于仿真测试的信号赋值，产生一些特定信号
格式:initial
     begin
         [时序控制]顺序语句N;
     end
Eg:reg clk_one=0;//时钟信号
	initial	
	begin
        clk_one=0;
    #2  clk_one=1;
    #2  clk_one=0;//每隔2ns变一下
    end
----------------------------------------
4.always进程语句 
特点(和initial语句的区别)：
    ①循环执行
    ②可综合的
    ③时序控制(可以是变化事件条件)
格式：always
     begin
         [时序控制]顺序语句N;
     end
Eg:reg clk_one=0;clk_two=0;
Eg:always	//周期为2ns的时钟信号
	#1 clk_one = ~clk_one;
Eg:always @(posedge clk_one)//上升沿有效
   begin
       clk_two=~clk_two;   
   end		//周期为4ns的时钟信号
```

7. **顺序语句**

```verilog
//顺序语句不一定是顺序执行的。
//★顺序语句只能出现在initial、always语句中
1.赋值语句：分为阻塞语句和非阻塞语句
格式：
	信号 = 表达式(阻塞)
	信号 <= 表达式(非阻塞) 
Eg:sum = a+b;
Eg:sum <= a+b;
-----------------------------------------
2.if条件选择语句
格式：
    if(条件表达式)
    begin
        顺序语句1;
        ........
        顺序语句N;
    end
	else if(条件表达式)
    begin
        顺序语句N+1;
        ........
        顺序语句2N;
    end
	else
        顺序语句2N+1;
-----------------------------------------
3.case条件选择语句
格式：case(case表达式)
		分支表达式1:
            顺序语句1;
        分支表达式N-1:
            顺序语句N-1;
	 	default:
            顺序语句N;
     endcase 
扩展：casez条件选择语句，条件中有z，则将z看成0
   	 casex条件选择语句，条件中有x，则不用比较这一位。
Eg:casez(sel)
	4'b1z1z:data=0;//当sel=4'b1010执行
Eg:casex(sel)
	4'b1zxz:data=0;//当sel=4'b1000执行
-----------------------------------------
4.循环语句
4.1 for循环
	格式：和c语言for语句一样
	Eg:for(i=0;i<10;i=i+1)
4.2	repeat循环
    格式：repeat(执行次数)
         	顺序语句;
4.3 while循环
    格式：while(条件)
         	顺序语句;
4.4 forever循环(永远执行)
	格式：forever
         	顺序语句;
作用：用来产生有规律的信号
Eg:reg clk;
   initial
   begin
       clk = 1'b0;
       #1 forever
           #1 clk = ~clk;//周期为2ns的信号
   end
```

8. **自定义原语(UDP)：了解**

```verilog
UDP可以实现两种基本电路的行为：
	①组合逻辑电路行为
	②时序逻辑电路行为
UDP的输出只有一个标量，输入可以多个标量。
★输出的值：0、1、x		输入的值：0、1、x、z
基本格式：
	primitive udp_name(port_list)
        port_declaration
        
        udp_body
    endprimitive
其中：
	port_list：端口列表，第一个是输出端口，后面是输入端口
	port_declaration：端口声明
	udp_body：实现逻辑部分，一般用table/endtable语句,表示真值表
-----------------------------------------
1.组合电路UDP建模
组合UDP功能表——数字电路真值表
形式：a b c d : y
Eg: primitive udp_demo(y,a,b);
    	output y;
    	input a;input b;
    	table
        //a		b	  y
          0     0     0
          0     1     0
          1     0     0
          1     x     x
          ?     1     ?
        endtable
	endprimitive
-----------------------------------------
2.时序电路UDP建模
★输出：由当前寄存器的状态和输入信号的值共同决定。
组成部分：初始化寄存器和状态列表
Eg: //电平触发方式
primitive udp_demo(q,data,enable);
	output q;
	input data;input enable;
	initial
		q = 0;
	table
	//data	enable : q(current) : q(next)
      ?		0	   :	?		:	-
      0		1	   :	?		:	0
      1		1	   :	?		:	1
    endtable
endprimitive
Eg: //边沿触发方式
primitive udp_demo(q,data,enable);
	output q;
	input clk;input d;
	initial
		q = 0;
	table
	//clk	 d     : q(current) : q(next)
      r		 0	   :	?		:	0
      n		 ?	   :	?		:	-
      r		 1	   :	?		:	1
    endtable
endprimitive
注：r是上升沿，n是下降沿
----------------------------------------
3.UDP模块实例化
udp_name 实例化名(port_list)
Eg:udp_demo udp_and(y,a,b);
```

9. **任务与函数**

```verilog
1.任务：实现某个功能的代码封装在task endtask中实现代码复用
//注：任务不能单独执行，需要通过调用来执行。
//注：任务可以相互调用，也可以调用函数
任务格式：
task 任务名;
    端口声明;
    顺序语句;
endtask
Eg:task cal_sum;
   	 input a;input b;
     output sum;
     sum = a+b;
   endtask
任务调用格式：
任务名(端口连接信号1,...,端口连接信号N)
Eg:input data1;input data2;
   cal_sum(data1,data2)
----------------------------------------
2.函数：功能和任务类似，但表达形式不同
代码封装在function endfunction中。
//注：函数不能单独执行，需要通过调用来执行
//注：函数可调用函数，但不能调用任务
函数和任务的区别：
	1.函数的参数只有输入信号
	2.函数不能带时序控制
	3.函数名就是输出信号
函数格式：
function [signed][返回值类型]函数名
    输入端口声明;
    变量定义;
    顺序语句;
endfunction
其中：
	[signed]:输出端口的位数
Eg:
function [4:0] cal_sum()
```

