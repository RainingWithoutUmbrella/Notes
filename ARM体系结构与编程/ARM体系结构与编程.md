# Day01



## 1.嵌入式系统的定义



以应用为中心 软硬件可裁剪 对功能 可靠性 成本 体积 功耗有严格要求的专用计算机系统。

## 2. 嵌入式系统的构成

**硬件系统构成**

> 硬件最小系统：
>         cpu
>            arm
>            powerpc
>            mips
>            龙芯
>            51
>            pic
>            ...
>         电源电路
>
> ​        复位电路
>
> ​        时钟电路
>
> ​        存储电路
>
> ​                  掉电丢失的存储器 （内存条）
> ​                                   RAM, 随机访问存储器
> ​                                   SRAM, 静态RAM (只要不断电，数据就不会丢)
> ​                                   DRAM，动态RAM(不仅要保证不断电，还要保证不停地刷新，否则数据也会丢)
> ​                                   DDRAM (双通道的动态RAM)
>
> ​                  掉电不丢失的存储器 （硬盘）
>
> ​                                   ROM，read only memory (掩膜项目)
> ​                                   PROM， programme(可编程)
> ​                                   EPROM, erase (可擦除)
> ​                                                      先擦后写  靠紫外线照射擦除
>
> ​                                   EEPROM，electricity
>
> ​                                   FLASH, 闪存
> ​                                               norflash
> ​                                               nandflash
> ​                                                          emmc: nandflash+IC电路       
> ​                                                            固态硬盘

**软件构成**

>应用程序：
>           C语言  数据结构
>           uc
>           qt 
>           sqlite
>       \--------------------------------------------
>       OS:linux vxworks ucos-ii wince ../无操作系统
>       \---------------------
>       驱动程序
>       \----------------
>       硬件             

关于实时操作系统和非实时操作系统
           判断一个操作系统属于实时还是非实时：调度策略
           常见的调度策略：
               基于优先级的调度策略：属于实时操作系统
                   vxworks ucos-ii
               时间片轮转:非实时操作系统
                   linux
               先来先服务

## 3. 嵌入式软件开发模式

应用软件开发时：在PC上编辑 编译 运行代码

  嵌入式软件开发时：
      在PC上编辑 编译程序
      
      在开发板中运行代码

  上位机和下位机的通信：
      串口
      网线
      USB线
      JTAG (通常出现在stm32的板子)    

## 4. 开发板硬件简介

核心板
      DataSheet.rar/x4418cv3_release20150713.pdf
         cpu, s5p6818
              8核处理器  1.4GHz
         内存，DDRAM
              1GB
         EMMC,8GB
              

   底板      
      DataSheet.rar/x6818bv2.pdf
              
   串口工具软件：SecureCRT.rar
                 如果使用的linux纯系统，推荐kermit

## 5. 开发板烧写实验

5.1 擦除自带的uboot程序
 mmc erase 0 0x1000

5.2 通过TF卡启动开发板
     1）使用IROM_Fusing_ToolV2.0将ubootpak.bin
        烧写进TF卡中
     2）通过TF卡启动开发板

5.3 通过tftp协议将pc机上的ubootpak.bin
      下载到开发板的内存中去

​       tftp ,简化版本的ftp协议  

         1) 安装tftp的server 程序
                  联网方式： sudo apt-get install tftpd-hpa

​         未联网方式：cd /home/tarena/Downloads/tftp/
​                                  sudo dpkg -i *.deb

        2) 配置tftp server               
                sudo vi /etc/default/tftpd-hpa
                #tftp server的主目录
                TFTP_DIRECTORY="/tftpboot" 
    
        3) 如果修改了配置文件 
                  要使新配置生效 重启tftp server
                    sudo /etc/init.d/tftpd-hpa restart
                     或者
                     sudo service tftpd-hpa restart

​    4.准备必要的文件
​         sudo chmod 777 /tftpboot/ -R  
​         cp /mnt/hgfs/arm/env/ubootpak.bin /tftpboot/

5.4 保证开发板可以ping通服务器

```c
纯系统：
            1）保证开发板和ubuntu纯系统处于同一网段
     vmware+ ubuntu:
            1) 保证开发板和ubuntu处于同一网段
               ubuntu:192.168.1.8
                 
               开发板：192.168.1.6
                   setenv ipaddr 192.168.1.6
                   setenv serverip  192.168.1.8
                   saveenv
            2) 设置为桥接模式
            
            3）设置用于桥接的网卡      
     验证：在串口软件中输入测试命令
         
         第二次执行以下命令
          ping 192.168.1.8
         如果出现“host 192.168.1.8 is alive” 
         则实验成功
```

5.5 将开发板内存中的ubootpak.bin烧写到emmc

```c
tftp 0x48000000 ubootpak.bin
      
      注意：如果ping通服务器
            并且/tftpboot存在ubootpak.bin文件
            下载 过程中一直 T  T  
            建议,将tftp server重启
              sudo /etc/init.d/tftpd-hpa restart
            
     update_mmc 2  2ndboot 0x48000000 0x200 53bb0    
         2, 第二个掉电不丢失的设备
         2ndboot,emmc的分区名称
         48000000,源数据地址
         0x200, 向2ndboot分区写入时偏移的字节数
         53bb0,要写入的字节数 （观察下载的字节数）
```

## 6. 下载运行led.bin

```c
cp /mnt/hgfs/arm/env/led.bin /tftpboot/
   
   在开发板上执行
      tftp 48000000 led.bin
      go 48000000
   观察LED是否闪烁   
```

![](/home/sunny/Nutstore Files/我的坚果云/TyporaFile/ARM体系结构与编程/ 下载uboot示意图.png)

代码：led.c

```c
#define GPIOCOUT    *((volatile unsigned int *)0xc001c000)
#define GPIOCOUTENB *((volatile unsigned int *)0xc001c004)
#define GPIOCALTFN0 *((volatile unsigned int *)0xc001c020)

void delay(int);

void led_test(void)
{
    /*将W15管脚设置为可选功能1*/
    GPIOCALTFN0 &= ~(3<<24);
    GPIOCALTFN0 |= (1<<24);
    /*配置为输出模式*/
    GPIOCOUTENB |= (1<<12);
    while(1)
    {
        /*亮*/
        GPIOCOUT &= ~(1<<12);
        delay(0x1000000);
        /*灭*/
        GPIOCOUT |= (1<<12);
        delay(0x1000000);
    }
}
void delay(int n)
{
    while(n--) ;
}

```

# Day02

**LED驱动编程**

## 1.电路原理图
  PCB: 厂家未提供

  底板电路原理图：x6818bv2.pdf
     结论，如果让LED1亮或者灭
           实则就是控制导线GPIOC12上出现低/高电平
  核心板电路原理图：x4418cv3_release20150713.pdf
     搜索 “GPIOC12”
     底板和核心板中名称相同的导线是同一条导线

​      结论，如果控制LED1亮或者灭实则就是控制CPU W15管脚输出低/高电平
​      问题：软件如何控制CPU W15管脚输出低/高电平？

## 2. cpu datasheet

SEC_S5P6818X_Users_Manual_preliminary_Ver_0.00.pdf  

  P68
      W15， 选择功能1（GPIO）
      
  软件控制硬件的媒介就是特殊功能寄存器    
      
  GPIOCALTFN0 0xc001c020
      [25:24]  01, 将W15管脚配置为功能1 （GPIO）
  GPIOCOUTENB 0xc001c004
      [12]     1, 将W15管脚进一步配置为输出模式

  GPIOCOUT    0xc001c000 
      [12]     0/1 W15输出低/高电平

## 3. 位操作

```c
操作特殊功能寄存器的原则：只改相关的bit
                               其他的bit保持原值不变
  
      //错误的 将其他管脚的功能都修改为了功能0
      *((volatile unsigned int *)0xc001c020) = 0x01000000;
      //有BUG 假如bit25原来就是1           
      *((volatile unsigned int *)0xc001c020) |= 0x01000000;     
           
      
      //正确的
      *((volatile unsigned int *)0xc001c020) &= 0xfcffffff;
      *((volatile unsigned int *)0xc001c020) |= 0x01000000;     
      
      //标准写法
      *((volatile unsigned int *)0xc001c020) &= ~(3<<24); //清0操作
      *((volatile unsigned int *)0xc001c020) |= (1<<24); //置1操作
```

## 4. 编译程序

```c
5.1 安装交叉编译工具   
      该系列软件（工具）本身运行在PC机上
      但是能够将.c翻译为ARM处理器认识的机器指令         
      
      sudo chmod 777 /opt -R
      cp /home/tarena/workdir/arm-linux-gcc/arm-cortex_a9-eabi-4.7-eglibc-2.18.tar.gz  /opt/
      cd /opt
      tar xf arm-cortex_a9-eabi-4.7-eglibc-2.18.tar.gz        
      vi ~/.bashrc  最后增加
        export PATH=$PATH:/opt/arm-cortex_a9-eabi-4.7-eglibc-2.18/bin/
      source  ~/.bashrc
      arm-cortex_a9-linux-gnueabi-gcc -v
  5.2 编译
      cd /home/tarena/arm/day02/01/
      arm-cortex_a9-linux-gnueabi-gcc -c -nostdlib  led.c -o led.o
      arm-cortex_a9-linux-gnueabi-ld -nostdlib -nostartfiles -Ttext=0x48000000 -eled_test led.o -o led
      arm-cortex_a9-linux-gnueabi-objcopy -O binary led led.bin
    
      以上编译过程中涉及的知识点
         -nostdlib: 编译 链接过程中不使用标准库文件
         -nostartfiles: 链接使用不添加标准启动代码
         -Ttext=0x48000000：指定代码段的起始链接地址
                            链接地址和运行地址要保持一致
                            否则程序很有可能跑飞
         -e: 指定入口点
             默认链接器找的入口点是_start函数
             
        arm-cortex_a9-linux-gnueabi-objcopy：
             剥离ELF中的附加信息                     	
     
  程序在链接的时候会添加一段标准的启动代码，给程序运行准备各种环境，如main函数的两个参数从命令行中传入。           
             
      使用到的命令
         file a.out 
         readelf -d a.out
         objdump -S a.out  >1.asm
```

## 5. 在目标板中运行代码

cp led.bin /tftpboot/

  在开发板上执行（串口）：
     tftp 48000000 led.bin
     go 48000000 

# Day03(UART驱动编程)

**知识点:volatile 关键字作用**

```c
作用：防止编译器的过度优化
                      每次取值都是去实际内存中取 
                      而不是使用缓存中的值 
                使用场景
                    a)访问特殊功能寄存器
                    b)多线程间共享的全局变量
                    c)进(线)程和中断服务程序共享的全局变量
```

## 1.基本概念

UART, 串行异步收发器
   并行通信/串行通信
   异步通信/同步通信 
   单工/半双工/全双工

   串行接口标准
       RS232
          电气特性 (EIA(RS232)电平)
              逻辑0  +3~+15v
              逻辑1  -3~-15v
          机械特性
              传输距离 <10m
              只能做1对1通信

​       RS485:工业现场
​          传输时使用差分信号
​          传输距离可以超过1000m
​          可以做1对多通信



   TTL电平（用于计算机内部）
       逻辑0： 低电平 <0.7v
       逻辑1： 高电平 >2.4v
       
   数据传输协议
       空闲状态为高电平
       发送时首先是一个起始位（一个周期的低电平）
       发送数据时从LSB开始发送
       每帧中数据位的个数5~8bit (变化)
       发送可能存在的1bit奇偶校验位 （变化）    
       发送1~2bit的停止位（高电平）（变化）
       

​       波特率： bps（bit per second）（变化）

## 2. 电路原理图

PC_TXD1 <------ UARTTXD0<------GPIOD18

   PC_RXD1                        GPIOD14 ?

   运行在ARM中的程序 控制UART控制器
   再由UART控制器去控制收发数据的管脚
   形参UART通信相关的时序
   从而简化uart驱动编程

## 3. cpu datasheet

P71
      GPIOD18, 功能1
      GPIOD14, 功能1
   GPIODALTFN0 0XC001D020
      [29:28]   01, 将GPIOD14管脚设置为功能1
      
   GPIODALTFN1 0XC001D024
      [5:4]     01, 将GPIOD18管脚配置为功能1      

   S5P6818内部集成了6个独立的UART控制器

   问题：COM1对应了CPU内部的哪个UART控制器？
         线索是COM1连接到了CPU的哪个管脚上去（电路原理图）
             GPIOD14
             GPIOD18
         结合数据手册 推断COM1对应了CPU内部对的uart0控制器   
      
   CPU感知外接硬件状态变化的方式：
       1）中断
       2）轮询
       3）DMA  directly memory access

配置：8n1 115200 non-fifo polling

```c
ULCON0 0xc00a1000
      [1:0]  11, 8bit
      [2]    0,  1bit停止位
      [5:3]  000, 无校验
      [6]    0,  正常模式
   UCON0  0xc00a1004
      [1:0] 01, 接收数据时采用轮询模式接收
      [3:2] 01, 发送数据时采用轮询模式发送
      [5]   0,  正常模式
   UTRSTAT0 0XC00A1010
      [0]   0/1  接收缓冲区空/非空
      [1]   0/1  发送缓存区非空/空
   UTXH0    0xc00a1020
      [7:0] 将要发送的字节数据写入其中 
   URXH0    0xc00a1024
      [7:0] 读取接收到的数据
   P314
      UART0CLKGEN0L 0XC00A9004
         [4:2] 001, 选择PLL1作为UART的时钟源
                    PLL1=800MHz
         [12:5] 时钟分频系数
                取值为15
                分频后 800M/(15+1) = 50MHz
      UART0CLKENB  0XC00A9000
         [2]  0/1 禁止/使能时钟
         
      UBRDIV0  0XC00A1028
           26
      UFRACVAL0 0XC00A102C 
           2   //0.13*16  
      
          参考P969
             50000000/(115200*16) - 1
            =27.13-1
            =26.13 
```

## 3. 交叉编译

```c
arm-cortex_a9-linux-gnueabi-gcc -c -nostdlib main.c -o main.o
   arm-cortex_a9-linux-gnueabi-gcc -c -nostdlib uart.c -o uart.o
   arm-cortex_a9-linux-gnueabi-ld -nostdlib -nostartfiles -Ttext=0x48000000 -emain main.o uart.o -o uart
   arm-cortex_a9-linux-gnueabi-objcopy -O binary uart uart.bin
```

## 4. 下载运行

cp uart.bin /tftpboot/
   在开发板上执行
     tftp 48000000 uart.bin       
     go 48000000

# Day04

## 1. 关于\r\n的问题

windows 
     \r: 13 (0x0d)
         去到行首
     \n: 10 (0x0a)
         去到下一行

linux：
     回车换行只需要 \n

hexdump -C /tmp/1.txt  (以字符的形式看文件内容)

## 2. 实现一个shell编程框架

```c
arm-cortex_a9-linux-gnueabi-gcc -c -nostdlib main.c -o main.o
  arm-cortex_a9-linux-gnueabi-gcc -c -nostdlib uart.c -o uart.o
  arm-cortex_a9-linux-gnueabi-ld -nostdlib -nostartfiles -Ttext=0x48000000 -emain main.o uart.o -o shell
  arm-cortex_a9-linux-gnueabi-objcopy -O binary shell shell.bin   
  
  cp shell.bin /tftpboot/
  tftp 48000000 shell.bin
  go 48000000
```

uart.h

```c
#ifndef __UART_H__
#define __UART_H__

extern void uart_init(void);
extern void uart_puts(char *);
extern int  uart_gets(char *, int);

#endif
```



uart.c

```c
#define UART0CLKENB         *((volatile unsigned int *)0xC00A9000)
#define UART0CLKGEN0L       *((volatile unsigned int *)0xC00A9004)

#define GPIODALTFN0         *((volatile unsigned int *)0xC001D020)
#define GPIODALTFN1         *((volatile unsigned int *)0xC001D024)

#define ULCON0              *((volatile unsigned int *)0xC00A1000)
#define UCON0               *((volatile unsigned int *)0xC00A1004)
#define UTRSTAT0            *((volatile unsigned int *)0xC00A1010)
#define UTXH0               *((volatile unsigned int *)0xC00A1020)
#define URXH0               *((volatile unsigned int *)0xC00A1024)
#define UBRDIV0             *((volatile unsigned int *)0xC00A1028)
#define UFRACVAL0           *((volatile unsigned int *)0xC00A102C)

void uart_init(void)
{
    /*禁止CLK*/
    UART0CLKENB &= ~(1<<2);
    /*配置管脚功能*/
    GPIODALTFN0 &= ~(3<<28);
    GPIODALTFN0 |= (1<<28);
    GPIODALTFN1 &= ~(3<<4);
    GPIODALTFN1 |= (1<<4);

    /*时钟源选择PLL1 800MHz*/
    UART0CLKGEN0L &= ~(7<<2);
    UART0CLKGEN0L |= (1<<2);
    /*第一次分频  800/(15+1)=50MHz*/
    UART0CLKGEN0L &= ~(0xff<<5);
    UART0CLKGEN0L |= (15<<5);

    /*8N1*/
    ULCON0 = 0x03;
    /*polling*/
    UCON0  = 0x05;
    /*根据50MHz时钟源 115200bps 进行第二次分频 */
    UBRDIV0 = 26;
    UFRACVAL0 = 2;

    /*使能CLK*/
    UART0CLKENB |= (1<<2);
}
void uart_putc(char c)
{
    /*轮询发送缓冲区是否为空
     *UTRSTAT0[1]  1,空
     * */
    while(!(UTRSTAT0 & (1<<1))) ;

    UTXH0 = c;

    if(c == '\n')
        uart_putc('\r');
}
/*abcdef*/
void uart_puts(char *str)
{
    while(*str)
    {
        uart_putc(*str);
        str++;
    }
}
char uart_getc(void)
{
    /*轮询接收缓冲区中是否有有效数据
     *UTRSTAT0[0]  1,有有效数据
     * */
    while(!(UTRSTAT0 & 0x01)) ;

    return (char)(URXH0);
}
int uart_gets(char *buf, int len)
{
    int i = 0;
    char tmp = 0;

    while(i<(len-1))
    {
        tmp = uart_getc();
        /*回显*/
        uart_putc(tmp);

        buf[i] = tmp;
        
        if(tmp == '\r')
            break;
        i++;
    }
    buf[i] = '\0';
    
    return i;
}
```

main.c

```c
#include "uart.h"

#define MAX_CMD_LEN (32)

char cmd_buf[MAX_CMD_LEN];

int main(void)
{
    /*8n1 115200 polling non-fifo*/
    uart_init();

    while(1)
    {
        /*输出命令提示符*/
        uart_puts("\nesd1909# ");
        /*接收用户输入的命令*/
        uart_gets(cmd_buf, MAX_CMD_LEN);
        /*匹配命令*/

        /*执行命令*/

    }

    return 0;
}
```





```c
arm-cortex_a9-linux-gnueabi-gcc -c -nostdlib main.c -o main.o
  arm-cortex_a9-linux-gnueabi-gcc -c -nostdlib cmd.c -o cmd.o
  arm-cortex_a9-linux-gnueabi-gcc -c -nostdlib uart.c -o uart.o
  arm-cortex_a9-linux-gnueabi-gcc -c -nostdlib strcmp.c -o strcmp.o
  arm-cortex_a9-linux-gnueabi-gcc -c -nostdlib led.c -o led.o
  arm-cortex_a9-linux-gnueabi-ld -nostdlib -nostartfiles -Ttext=0x48000000 -emain main.o led.o cmd.o strcmp.o uart.o -o shell
```



cmd.h

```c
#ifndef __CMD_H__
#define __CMD_H__

typedef struct cmd
{
    char *name;
    void (*pfunc)(void);
}_cmd;

extern _cmd *find_cmd(const char *);

#endif
```



cmd.c

```c
#include "cmd.h"
#include "strcmp.h"
#include "led.h"

_cmd cmd_list[]=
{
    {"led1on", led1_on},
    {"led1off", led1_off},
};

_cmd *find_cmd(const char *user_cmd)
{
    int i = 0;

    for(; i<sizeof(cmd_list)/sizeof(cmd_list[0]);i++)
    {
        if(my_strcmp(user_cmd, cmd_list[i].name)==0)
        {
            return &(cmd_list[i]);
            //return cmd_list+i;
        }
    }
    return (_cmd *)0;
```

led.h

```c
#ifndef __LED_H__
#define __LED_H__

extern void led_init(void);
extern void led1_on(void);
extern void led1_off(void);

#endif
```

led.c

```c
void led_init(void)
{
    /*设置为输出模式*/
}
void led1_on(void)
{
}
void led1_off(void)
{
}
```

strcmp.h

```c
#ifndef __STRCMP_H__
#define __STRCMP_H__

extern int my_strcmp(const char *, const char *);

#endif
```

strcmp.c

```c
int my_strcmp(const char *s1, const char *s2)
{
    while(*s1)
    {
        if(*s1 == *s2)
        {
            s1++;
            s2++;
            continue;
        }    

        return *s1 - *s2;
    }

    return *s1 - *s2;
        
}

```

main.c

```c
#include "uart.h"
#include "cmd.h"
#include "led.h"

#define MAX_CMD_LEN (32)

char cmd_buf[MAX_CMD_LEN];

int main(void)
{
    _cmd *ptr = (_cmd *)0;

    /*8n1 115200 polling non-fifo*/
    uart_init();
    /*将相应的管脚设置为输出模式*/
    led_init();

    while(1)
    {
        /*输出命令提示符*/
        uart_puts("\nesd1909# ");
        /*接收用户输入的命令*/
        uart_gets(cmd_buf, MAX_CMD_LEN);
        /*匹配命令*/
        ptr = find_cmd(cmd_buf);
        /*执行命令*/
        if(ptr)//成功
        {
            ptr->pfunc();
        }
        else
        {
            uart_puts("\nunknown command: ");
            uart_puts(cmd_buf);
        }
    }

    return 0;
}
```

# Day05

## 1. 程序链接过程

默认链接时 是按照.o文件出现的顺序将各个.o代码段装配到elf的代码段中去

如果链接时不想使用默认规则可以自行编辑一个链接脚本，将想要的规则放入该链接脚本链接时指定使用该链接脚本即可 

## 2. mma8653芯片驱动

2.1 mma8653 芯片
     测量加速度
  2.2 电路原理图   
     MCU_SCL_2 ----->GPIOD6
     MCU_SDA_2 ----->GPIOD7

## 3. mma8653 datasheet:mma8653fcr1.pdf
   该芯片的接口: IIC

## 4. 研究I2C通信 

4.1 I2C的定义
      飞利浦公司开发的两线式串行（同步）总线

​         通信时 
​           SCL，时钟线 （区别同步通信和异步通信的主要特征）
​           SDA，数据线

用于板级芯片之间的互联

4.2 特点
      1）通信时只需要两条线 硬件互联成本低
      2）总线上的设备有主从之分
      3）总线上数据传输的发起和结束都由主设备控制
      4）每个从设备都由唯一的从设备地址
         该地址一般为7bit
      5）支持总线冲突仲裁

4.3 通信协议

三个辅助信号
         start： SCL高电平 SDA出现下降沿
         stop：  SCL高电平 SDA出现上升沿
         ack 应答信号
                 第九个周期 SCL处于高电平期间 SDA为低电平
                                                 （一定是接收方拉低）

空闲时：SDA SCL处于高电平 （通过接上拉电阻）

收发数据时：低送高取
          发送发在SCL处于低电平时 调整SDA上的电压值      
          接收方在SCL处于高电平时 去读SDA上的电压值    
          发送时从高bit开始

## 5. cpu datasheet

P70
     GPIOD6的功能1
     GPIOD7的功能1
  GPIODALTFN0 0XC001D020
     [13:12] 01
     [15:14] 01
     
  S5P6818内部集成了3个独立的I2C控制器
  通信速率为100Kbit/s

问题：MMA8653芯片挂在了哪个I2C控制器上？
      通过MMA8653芯片连接的CPU管脚做推断
      挂在了2th I2C控制器上

I2CCON2  0XC00A6000
[3:0]  1111,
           分频系数设置 15+1 = 16
           经验：最慢速度收发 排除硬件的部分干扰
                 调试通过后，可以修改该值

​					PCKL/256 /(15+1)

[4]   读操作
            0/1 未产生/产生了中断未决
                通过轮询该bit是否为1 
                判断数据收发是否完成
          写操作    
             0， 该bit清0
             1， 无效

[5]   0/1 禁止 /使能中断
[6]   0, I2C CLK = PCLK/16
        1, I2C CLK = PCKL/256  
[7]   0/1 禁止/使能自动回复ACK
  I2CSTAT2 0XC00A6004

[0]  0/1 收到/未收到ACK
[3]  0/1 总线仲裁成功/失败
[4]  0/1 禁止/使能串行输出

[5]  读操作
           0/1 总线空闲/忙
         写操作
           0 产生stop信号
           1 产生start信号

[7:6]  10 master recv
           11 master tranmist

I2CDS2 0XC00A600C
    [7:0] 用于收发数据       
  I2CLC2  0XC00A6010
    [2] 0/1 禁止/使能滤波
    [1:0]  01/10/11都可以

P302
    I2CCLKENB2 0XC00B0000
       [3] 1, 使能给到I2C控制器的PCLK时钟信号

P294
    IPRESTREG0 
       [22]  0,给I2C 2th控制器复位

iic.h

```c
#ifndef __IIC_H__
#define __IIC_H__

extern void iic2_init(void);
extern void iic2_start(unsigned char, 
                       unsigned int );
extern void iic2_stop(void);
extern void iic2_tx(unsigned char *, 
                     unsigned char);
extern void ii2c_rx(unsigned char *, 
                     unsigned char);
#endif
```

iic.c

```c
#define GPIOD_ALTFN0    *((volatile unsigned int *)0xC001D020)
#define I2CCLKENB2      *((volatile unsigned int *)0xC00B0000)
#define IPRESET0        *((volatile unsigned int *)0xC0012000)
#define I2CCON          *((volatile unsigned int *)0xC00A6000)
#define I2CSTAT         *((volatile unsigned int *)0xC00A6004)
#define I2CADD          *((volatile unsigned int *)0xC00A6008)
#define I2CDS           *((volatile unsigned int *)0xC00A600C)
#define I2CLC           *((volatile unsigned int *)0xC00A6010)


void iic2_init(void)
{
    /*配置管脚 GPIOD6 GPIOD7 功能1*/
    GPIOD_ALTFN0 &= ~(3<<14 | 3<<12);
    GPIOD_ALTFN0 |= (1<<14 | 1<<12);

    /*使能I2C控制器时钟*/
    I2CCLKENB2 |= (1<<3);

    /*复位I2C控制器*/
    IPRESET0 &= ~(1<<22); //reset
    IPRESET0 |= 1<<22;

    /*i2c控制器的配置*/
    I2CCON = (1<<8)|(1<<7)|(1<<6)|(1<<5)|0x0f;
    /*使能滤波 延时5个PCLK周期*/
    I2CLC = (1<<2) | 1;
}
/*发送start 从设备地址<<1|R/W 接受ACK*/
void iic2_start(unsigned char slave_addr, 
                unsigned int rdwr)
{
    /*发送使能*/
    I2CSTAT |= (1<<4);

    /*使能中断 使能自动ACK*/
    I2CCON |= (1<<8) | (1<<7) |(1<<5);
    
    /*设置主设备的收发模式*/
    if(rdwr) 
    {
        /*master recv*/
        I2CSTAT &= ~(3<<6);
        I2CSTAT |= 2<<6;
        slave_addr = (slave_addr<<1) | 1;
    }
    else
    {
        /*master send*/
        I2CSTAT |= 3<<6;
        slave_addr = (slave_addr<<1) | 0;
    }
    /*填充移位寄存器*/
    I2CDS = slave_addr;
    /*清除pending*/
    I2CCON &= ~(1<<4);
    /*发送start 发送I2CDS寄存器中内容*/
    I2CSTAT |= 1<<5;
    /*轮询是否发送完成*/
    while(!(I2CCON & (1<<4))) ;
    /*判断是否收到ACK*/
    if(I2CSTAT&0x01) 
        uart_puts("\nnot received ACK");
}
void iic2_stop(void)
{
    /*发送stop信号*/
    I2CSTAT &= ~(1<<5);
}
void iic2_tx(unsigned char *buf, 
             unsigned char len)
{
    int count = 0;

    for(; count<len; count++)
    {
        /*清除pending位*/
        I2CCON &= ~(1<<4);
        
        /*写入要发送的数据*/
        I2CDS = buf[count];
        //I2CDS = *(buf+count);
        while(!(I2CCON & (1<<4)));
        /*判断是否收到ACK*/
        if(I2CSTAT&0x01) 
            uart_puts("\nnot received ACK");
    }
}
void iic2_rx(unsigned char *buf, 
             unsigned char len)
{
    int count = 0;

    while(count < len)
    {
        /*接收最后一个字符之前 禁止自动回ACK*/
        if(count == (len-1))
        {
            /*禁止自动ACK*/
            I2CCON &= ~(1<<7);
        }
        /*清除中断pending位*/
        I2CCON &= ~(1<<4);

        /*轮询是否有数据到来*/
        while(!(I2CCON & (1<<4))) ;
        
        /*读取数据*/
        buf[count] = I2CDS;
        
        count++;
    }
}
```

