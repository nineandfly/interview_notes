单词缩写

AVCC正模拟电源电压
AVSS模拟电路地（模拟地）

DVCC正数字电源电压
DVSS数字电路地 （数字地）



常见单词 

reference 参考

Analog 模拟，模拟信号

 configures 配置（参数）

toggles    切换

时钟 

1低频时钟源：外接32768Hz晶振 工作在低频模式 ACLK辅助时钟
2 高频时钟源：外接8MHz晶振 MCLK 主系统时钟
3 数字控制RC振荡器 产生内部时钟 SMCLK 子系统时钟

XT1CLK 外部低频率或高频率振荡源，32.768Khz（LF模式）或4-32Mhz（HF模式）；
XT2CLK 外部高频振荡源，4-32Mhz；
VLOCLK 内部低耗低频振荡源，典型为10KHZ，精度一般；
REFOCLK 内部低频参照源，典型值32.768KHZ，常被用作锁相环FLL的基准频率，精度很高，不使用时不消耗电源，其设置往往要参考LPM模式的的设置；
DCOCLK内部数字控制振荡源，通过内部FLL单元稳定而来，并不算是一个通过振荡产生时钟的源。


### 1.GPIO

在读写IO口之前要先设置方向寄存器（PxDIR）的模式。读IO，实际上就是读输入寄存器（PxIN）；写IO，实际上就是写输出寄存器（PxOUT）
根据输出电平的“强弱”分类，可将 IO 输出分为推挽/推拉输出、上拉电阻输出、下拉电阻输出 3 种。

![](C:\Users\guo\Pictures\folder_use\msp_gpio.png)

```c
P1DIR |= BIT0 ;//设置P1.0为输出模式
P1OUT |= BIT0; //输出高电平
P1DIR &= ~BIT1 ;//设置P1.1为输入模式
P1REN  |= BIT1;//用于设置引脚电阻，上拉电阻置一，下拉电阻置0。没有输入时输入为1。

```

2.中断

使用中断需要开启总中断允许和使用中断使能。

中断向量和优先级 ，数字越大优先级越高。如果需要使用中断嵌套需要在中断函数开头开启总中断允许。



```c
_EINT();//打开总中断允许
#define RTC_VECTOR              (41 * 1u)                    /* 0xFFD2 RTC */
#define PORT2_VECTOR            (42 * 1u)                    /* 0xFFD4 Port 2 */
#define TIMER2_A1_VECTOR        (43 * 1u)                    /* 0xFFD6 Timer2_A5 CC1-4, TA */
#define TIMER2_A0_VECTOR        (44 * 1u)                    /* 0xFFD8 Timer2_A5 CC0 */
#define USCI_B1_VECTOR          (45 * 1u)                    /* 0xFFDA USCI B1 Receive/Transmit*/
#define USCI_A1_VECTOR          (46 * 1u)                    /* 0xFFDC USCI A1 Receive/Transmit*/
#define PORT1_VECTOR            (47 * 1u)                    /* 0xFFDE Port 1 */
#define TIMER1_A1_VECTOR        (48 * 1u)                    /* 0xFFE0 Timer1_A3 CC1-2, TA1 */
#define TIMER1_A0_VECTOR        (49 * 1u)                    /* 0xFFE2 Timer1_A3 CC0 */
#define DMA_VECTOR              (50 * 1u)                    /* 0xFFE4 DMA */
#define USB_UBM_VECTOR          (51 * 1u)                    /* 0xFFE6 USB Timer / cable event / USB reset */
#define TIMER0_A1_VECTOR        (52 * 1u)                    /* 0xFFE8 Timer0_A5 CC1-4, TA */
#define TIMER0_A0_VECTOR        (53 * 1u)                    /* 0xFFEA Timer0_A5 CC0 */
#define ADC12_VECTOR            (54 * 1u)                    /* 0xFFEC ADC */
#define USCI_B0_VECTOR          (55 * 1u)                    /* 0xFFEE USCI B0 Receive/Transmit */
#define USCI_A0_VECTOR          (56 * 1u)                    /* 0xFFF0 USCI A0 Receive/Transmit */
#define WDT_VECTOR              (57 * 1u)                    /* 0xFFF2 Watchdog Timer */
#define TIMER0_B1_VECTOR        (58 * 1u)                    /* 0xFFF4 Timer0_B7 CC1-6, TB */
#define TIMER0_B0_VECTOR        (59 * 1u)                    /* 0xFFF6 Timer0_B7 CC0 */
#define COMP_B_VECTOR           (60 * 1u)                    /* 0xFFF8 Comparator B */
#define UNMI_VECTOR             (61 * 1u)                    /* 0xFFFA User Non-maskable */
#define SYSNMI_VECTOR           (62 * 1u)                    /* 0xFFFC System Non-maskable */
#define RESET_VECTOR            (63 * 1u)                    /* 0xFFFE Reset [Highest Priority] */

```

