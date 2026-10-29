# 0501 LED 流水灯

## 一、实验目的

1. 掌握 STM32 GPIO 输出的配置方法（CubeMX + HAL 库）
2. 理解推挽输出与上拉电阻的作用
3. 用数组遍历 + 延时实现流水灯效果

## 二、硬件连接

| 功能 | 引脚 | 说明 |
|---|---|---|
| LED1 ~ LED8 | PF0 ~ PF7 | 低电平点亮（IO 输出 0 亮，输出 1 灭） |

GPIO 配置：`Output Push Pull`，初始电平 High（上电全灭），Pull-up，Low speed。

## 三、程序流程

```
HAL_Init()            → 初始化 HAL 与 SysTick
SystemClock_Config()  → HSE 8MHz 经 PLL 倍频到 168MHz
MX_GPIO_Init()        → 使能 GPIOF 时钟，PF0~PF7 配置为推挽输出
主循环                → for 循环遍历 led[0..7]，逐个点亮 200ms 后熄灭
```

## 四、关键代码

```c
uint16_t led[8] = {GPIO_PIN_0, GPIO_PIN_1, GPIO_PIN_2, GPIO_PIN_3,
                   GPIO_PIN_4, GPIO_PIN_5, GPIO_PIN_6, GPIO_PIN_7};
int i;

while (1)
{
  for (i = 0; i < 8; i++)
  {
    HAL_GPIO_WritePin(GPIOF, led[i], GPIO_PIN_RESET);   // 点亮
    HAL_Delay(200);
    HAL_GPIO_WritePin(GPIOF, led[i], GPIO_PIN_SET);     // 熄灭
  }
}
```

## 五、实验现象

上电后 8 个 LED 依次点亮并熄灭，从 LED1 循环到 LED8，每个灯亮 200ms。

见 `screenshots/` 目录。

## 六、遇到的问题与解决

| 现象 | 原因 | 解决 |
|---|---|---|
| 灯不亮 | PF 组时钟未使能 | 确认 `__HAL_RCC_GPIOF_CLK_ENABLE()` 已调用 |
| 灯一直是亮的 | 亮灭电平搞反 | 该板为低电平点亮，`GPIO_PIN_RESET` 才是亮 |
| 改了代码但现象不变 | 烧录后未系统复位 | 按板子 RESET 键，或写入 AIRCR 触发 SYSRESETREQ |
