### Chapter 2   MIPS

register-register结构，load/store指令从memory获取数据

操作数分为3类：register操作数和memory操作数，**立即数操作数**

32个32位寄存器

#### **数据传输：**

​	memory到register传输：load (lw)

​	register到memory传输：store(sw)

​	MIPS不支持直接的地址访问，支持间接寻址

#### **访问存储器操作数**

​	g = h + a[8]，s3存放a的基地址

​	首先，load到寄存器，

​	lw $t0,32($s3)

​	然后加一下

​	add $s1,$s2,$t0

​	**注意lw这里偏移量要×4**

**常数的表示**

lw $t0,AddrConstant55($s1)

支持短立即数

#### **指令格式**

R-format ：3个寄存器 ->6bits(op =000 000)+5bits(rs源1)+5bits(rt源2)+5bits(rd目的)+5bits(shamt)+6bits(func ->控制ALU)

i-format:立即数操作指令 -> 6bits(op) + 5bits(rs) + 5bits(rt) +16bits(address/immediate)

J-format  ->6bits(op) +26bits(address)

所有mips指令都是定长的结构



#### 决策指令/转移指令

举例：while指令

``` c
while(save[i] == k){
i = i+j;}
```

``` asm
Loop:	add $t1,$t3,$s3		#temp reg $t1 = 2*i
		add $t1,$t1,$t1		#temp reg $t1 = 4*i
		add $t1,$t1,$s6		#$t1 = address of save[i]
		lw $t0,0($t1)		#temp reg $t0 = save[i]
		bne $t0,$s5,Exit	#go to Exit if save[i]!=k
		add $s3,$s3,$s4		#i = i+j
		j Loop				#go to Loop
		
Exit:	...
```



set on less than -**slt**指令

slt $t0,$s3,$s4	#$t0 = 1 if $t3<$t4





#### case swith指令

根据地址跳转

``` c
switch(k){
    case 0: f = i+j;break;
    case 1: f = g+h;break;
    case 2: f = g-h;break;
    case 3: f = i-j;break;
}
```

k取 0-3，t4中存储基地址，该处存放对应代码的地址，利用jr指令跳转到寄存器中显示的地址中去。



基本代码块：

没有分支的指令序列 (without branches)

#### 子程序调用

6步骤：

1. 成员变量需要放到一个可以访问的地方去
2. 可以转移到对应地址执行子程序 （jump to)
3. 获取子程序运行过程中所需要的存储资源
4. 执行期望任务
5. 将结果的值存放到主程序可以访问的地方
6. 返回主程序

$a0-$a3,4个寄存器用来存放成员变量

$v0-$v1，两个寄存器用来存放返回结果

$ra用来存放返回地址

这样返回的时候直接jr $ra就可以



堆栈指针：$sp

MIPS没有push,pop指令

一般约定：

主程序：$s0 - $s7寄存器一定需要保护，$t0-$t9临时寄存器不需要保护



#### 加载32位立即数

32-bit constant -> lui +ori

**注意：**lui+addi会出错，后16位的最高位为1的时候addi会加一个负数

解决方法：1.lui + ori 2.如果坚持采用addi的时候，还要再加1



#### MIPS中的寻址

jump :J-format   6bits (J)+ 26bits(address) ->用的是(伪)直接地址

branch:  6bits(bne) +5bits(reg1)+5bits(reg2)+16bits(address)->相对地址（目标地址与目前的差值

Branch时PC = PC+4+Branch address

所以branch所支持的地址很短，如果超过16位怎么办？

branching far away:通过j搭配来实现

``` asm
beq $s0,$s1,L1

-->Rewrite

bne $s0,$s1,L2
j L1
L2:
...
```

不难看出，上面两段代码是等价的

**5种寻址方式：**(寻找一个操作数的过程和方法)

寄存器取址、基值寻址、立即数寻址、PC相对寻址(代码寻址)、伪随机寻址

相对寻址的优点:非常适合重定位，在指令寻址中是很好的方式



#### 指令大全

1. 算数指令 add sub addi
2. 数据转移指令 lw,sw,lb(load byte),sb,**lui**
3. 分支指令 beq,bne,slt,slti
4. 无条件跳转 j,jr,jal



#### 反汇编

注意各个寄存器的编号：

$zero:0  $v0-$v1:2-3  $a0-$a3:4-7  $t0-$t7:8-15 $s0-$s7:16-23 $t8-$t9:24-25   

$gp:28  $sp:29  $fp:30  $ra:31

给定一个32位值

先看前6位 是000000 ->R-format 这时找后六位 function ->看做什么操作，然后找三个寄存器成功反汇编

**注意一个细节： add rd,rs,rt  32位数里顺序是rs,rt,rd**

**安全方面：逆向工程**



#### 程序的翻译+启动运行(---还不是很懂！---)

按层次结构，编译+汇编+link(导入library)+加载(操作系统来做)->最后送到内存

高级语言首先被**编译**成汇编语言程序，然后**汇编**为机器语言的目标模块。链接器将多个模块与Library(库程序)组合在一起解析所有的引用。load将机器代码放入合适的内存位置以便处理器执行。



汇编器能够处理一些机器语言指令的常见变种，硬件不需要实现这些指令，他们在汇编语言中的存在简化了程序变换和编程，这类指令称为**伪指令。**

如 move $t0,$t1 ->MIPS汇编器也能够识别它

汇编器将这条汇编语言指令等价转换为： add $t0,$zero,$t1,二者是等价的。

汇编器的主要任务是**汇编成机器代码**。汇编器将汇编语言转换为目标文件(obj)，目标文件包括**机器语言指令，数据，以及将指令正确放入内存需要的信息**

标记->**符号表** 包含了符号和对应地址的对偶。



**链接**：将所有独立汇编的机器语言程序拼接在一起。

三步：1.代码和数据模块按符号特征放入内存。 2.决定数据和指令标记地址。 3.修正内部和外部引用。

当链接器将一个模块放进内存时，所有绝对引用都必须重新定位，以反映出它的真实地址。





#### 指针

``` asm
move $t0,$a0  #求出首地址
sll $t1,$a1,2
add $t2,$a0,$t1 #求出末地址
loop: sw $zero,0($t0)
	  addi $t0,$t0,4
	  slt $t3,$t0,$t2 #<末地址？
	  bne $t3,$zero,loop
	  jr $ra
```

指针版只需要在循环外做一次*4操作，运行速度会快一点。


