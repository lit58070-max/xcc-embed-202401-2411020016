# 0602 4×3 矩阵键盘点灯

## 一、实验目的

1. 掌握矩阵键盘的扫描原理（行驱动、列读取）
2. 实现按键消抖与松手检测
3. 用键盘控制 LED 的点亮、熄灭与流水效果

## 二、硬件连接

| 功能 | 引脚 | 配置 |
|---|---|---|
| 键盘 4 行 | PE0 ~ PE3 | Output Push Pull，初始高电平 |
| 键盘 3 列 | PE4 ~ PE6 | Input，**Pull-up**（必须上拉，否则浮空） |
| LED1 ~ LED8 | PF0 ~ PF7 | Output Push Pull，低电平点亮 |

键盘为 4 行 × 3 列共 12 个按键，布局同电话键盘：

```
1  2  3
4  5  6
7  8  9
*  0  #
```

## 三、扫描原理

1. 4 行先全部输出高电平
2. 当前行输出低电平，其余保持高
3. 读 3 根列线：某列为 0，说明"当前行 + 该列"的按键被按下
4. 4 行轮流扫一遍，得到行列坐标，查表得键值
5. 扫完把行线恢复高电平

## 四、按键功能

| 按键 | 功能 |
|---|---|
| 1 ~ 8 | 点亮对应的 LED |
| 0 | 全部熄灭 |
| 9 | 由黑到亮：全灭后逐个点亮 |
| * | 由亮到黑：全亮后逐个熄灭 |
| # | 全部点亮 |

## 五、关键代码

```c
char KeyScan(void)
{
  char k = 0;
  char table[4][3] = {
    {'1', '2', '3'},
    {'4', '5', '6'},
    {'7', '8', '9'},
    {'*', '0', '#'}
  };

  for (i = 0; i < 4; i++)
  {
    HAL_GPIO_WritePin(GPIOE, GPIO_PIN_0 | GPIO_PIN_1 | GPIO_PIN_2 | GPIO_PIN_3, GPIO_PIN_SET);
    HAL_GPIO_WritePin(GPIOE, row[i], GPIO_PIN_RESET);
    HAL_Delay(1);

    for (j = 0; j < 3; j++)
    {
      if (HAL_GPIO_ReadPin(GPIOE, col[j]) == GPIO_PIN_RESET)
      {
        k = table[i][j];
        break;
      }
    }
    if (k != 0) break;
  }

  HAL_GPIO_WritePin(GPIOE, GPIO_PIN_0 | GPIO_PIN_1 | GPIO_PIN_2 | GPIO_PIN_3, GPIO_PIN_SET);
  return k;
}
```

消抖方式：读到键值后延时 10ms 再读一次，两次一致才认为有效；随后等待松手，避免连续触发。

## 六、实验现象

上电后 8 个 LED 全灭。按 1~8 对应 LED 点亮，按 0 全灭，按 9 逐个点亮，按 * 逐个熄灭，按 # 全亮。

见 `screenshots/` 目录。

## 七、遇到的问题与解决

| 现象 | 原因 | 解决 |
|---|---|---|
| 按键完全没反应 | 引脚配置错误（配成 PD 口，实际接 PE 口） | 按原理图确认行列引脚，改 `.ioc` 重新生成 |
| 没按键也读到键值 | 列线未上拉，浮空输入 | 列线配置 `Pull-up` |
| 按键乱跳、连发 | 无消抖 | 延时 10ms 二次确认 + 等松手 |
| 烧录成功但现象是上一版 | DAP-Link 未接 RST，只复位内核 | 写 AIRCR 触发系统复位，或按 RESET 键 |
