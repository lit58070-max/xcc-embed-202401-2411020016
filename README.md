# xcc-embed-202401-2411020016

**202401-2411020016-杨柳**

## 实验列表

| 目录 | 实验内容 | 开发板 |
|---|---|---|
| `0501_LedWater` | LED 流水灯 | STM32F407ZET6 |
| `0602_MatrixKey` | 4×3 矩阵键盘点灯 | STM32F407ZET6 |

## 开发环境

| 项目 | 版本 / 说明 |
|---|---|
| IDE | STM32CubeIDE 1.19.0 |
| 配置工具 | STM32CubeMX 6.18 |
| HAL 库 | STM32Cube FW_F4 V1.28.3 |
| 主控 | STM32F407ZET6（LQFP144，512KB Flash） |
| 时钟 | HSE 8MHz → PLL → 168MHz（APB1 42MHz / APB2 84MHz） |
| 下载器 | DAP-Link（CMSIS-DAP，SWD 四线） |

## 目录说明

每个实验目录下：

```
XXXX_实验名/
├── Core/            应用代码（main.c 在 Core/Src 下）
├── Drivers/         HAL 库与 CMSIS
├── *.ioc            STM32CubeMX 工程配置
├── .cproject        CubeIDE 工程文件
├── README.md        实验说明
└── screenshots/     现象截图
```

编译产物（`Debug/`、`*.o`、`*.elf`、`*.hex` 等）已由 `.gitignore` 排除，不纳入版本管理。
