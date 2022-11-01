# msp常用函数

设置时间函数

```c
#define CPU_F ((double)8000000)//CPU_F 为系统主时钟频率
#define delay_us(x) __delay_cycles((long)(CPU_F*(double)x/1000000.0))//微秒
#define delay_ms(x) __delay_cycles((long)(CPU_F*(double)x/1000.0))//毫秒
```

默认时钟为1MHZ,配置为25MHZ前需要升高系统核心电压

```c
void timerup(void)//配置时钟25MHZ
{

UCSCTL3 = SELREF_2;
UCSCTL4 |= SELA_2;
__bis_SR_register(SCG0);
UCSCTL0 = 0x0000;
UCSCTL1 = DCORSEL_7;//50Mhz范围
UCSCTL2 = FLLD_0 + 762;    //(762+1)*32768==25MHZ
__bic_SR_register(SCG0);



__delay_cycles(782000);//延时
while(SFRIFG1 & OFIFG)//等待设置完成
{
    UCSCTL7 &=~ (XT2OFFG + XT1LFOFFG + DCOFFG);
    SFRIFG1 &=~ OFIFG;

}
//UCSCTL4 = UCSCTL4&(~(SELS_7|SELM_7))|SELS_3|SELM_3;
}
```

系统升压函数,首先为什么要把系统的核心电压升高呢，为了要与时钟频率相匹配，即**1.8V（0~8MHZ）,2.0V（0~12MHZ）,2.2V（0~20MHZ），2.4V（0~25MHZ）四个级别,**即要提高时钟频率，那么相对应的就要进行系统升压。

```c
//官方升压函数，每次升一级
/*******************************************************************************
函数功能：设置内核电压值（与频率设置有关）
函数参数：u8 level ：电压阶梯 小于3
********************************************************************************/
void SetVcoreUp (int level)
{
#if 1  //仿真时修改为0，否则会卡在死循环里
  // Open PMM registers for write
  PMMCTL0_H = PMMPW_H;
  // Set SVS/SVM high side new level
  SVSMHCTL = SVSHE + SVSHRVL0 * level + SVMHE + SVSMHRRL0 * level;
  // Set SVM low side to new level
  SVSMLCTL = SVSLE + SVMLE + SVSMLRRL0 * level;
  // Wait till SVM is settled
  long i=50000;
  while (((PMMIFG & SVSMLDLYIFG) == 0)&&((i--)>0));
  // Clear already set flags
  PMMIFG &= ~(SVMLVLRIFG + SVMLIFG);
  // Set VCore to new level
  PMMCTL0_L = PMMCOREV0 * level;
  // Wait till new level reached
  i =50000;
  if ((PMMIFG & SVMLIFG))
    while(((PMMIFG & SVMLVLRIFG) == 0)&&((i--)>0));
  /*
  if ((PMMIFG & SVMLIFG))
    while ((PMMIFG & SVMLVLRIFG) == 0);
  */
  // Set SVS/SVM low side to new level
  SVSMLCTL = SVSLE + SVSLRVL0 * level + SVMLE + SVSMLRRL0 * level;
  // Lock PMM registers for write access
  PMMCTL0_H = 0x00;
#endif
}

//这里直接调用了官方的函数，但是在使用SetVcoreUp函数时注意要一级一级叠加上去，不用直接一下子升到2级或者3级，比如要升到3级

void upvcc(void)
{
SetVcoreUp(0);
SetVcoreUp(1);
SetVcoreUp(2);
SetVcoreUp(3);
 
}

//直接配置寄存器来实现，一般情况下就是直接升到最高核心电压，下面实现程序
void upVcc(void)//核心电压上升3级
{
    PMMCTL0_H = 0xA5;                      //开启PMM电源管理，即开锁
    SVSMLCTL |= SVSMLRRL_1 + SVMLE;        //配置SVML电压
    PMMCTL0 = PMMPW +PMMCOREV_3;           //配置内核电压，选择3级
    while((PMMIFG & SVSMLDLYIFG)==0);      //等待配置完成
    PMMIFG &=~ (SVMLVLRIFG + SVMLIFG + SVSMLDLYIFG);
    if((PMMIFG & SVMLIFG)==1)
        while((PMMIFG & SVMLVLRIFG)==0);
    SVSMLCTL &=~ SVMLE;                    //关闭SVML
    PMMCTL0_H = 0x00;                     //锁存配置，即关锁
}
```

