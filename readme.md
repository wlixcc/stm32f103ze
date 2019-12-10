# 请先阅读[Mac下stm32开发(clion)](https://zhuanlan.zhihu.com/p/95498261)


1. 所有代码基于`正点原子-精英板` b站[教学视频链接](https://www.bilibili.com/video/av8938442?from=search&seid=6964447435862961564)
2. mcu型号:`STM32F103ZET6`, 各种配置基于开发板原理图进行配置
3. 如果不想重复配置`STM32CubeMX`(避免每次都配置IO口,时钟等)。新建工程,直接复制已配置好的`.ioc`文件到新工程,打开`STM32CubeMX`后生成代码到新项目中就可以


## 1.LED

> 实现点亮两个led灯

1. `LED0` 对应 `PB5` 口, `LED1` 对应 `PE5`口。 
2. 在STM32CubeMX中配置好两个IO口为输出,默认`高电平` led熄灭
3. 代码生成后, `main` 函数中将IO设置为低电平，点亮led

## 2.蜂鸣器

> 实现蜂鸣器每隔一秒响一次

1. 蜂鸣器对应`PB8`口,默认设置成低电平输出


## 3.键盘&外部中断
> 实现3个按键分别控制蜂鸣器、LED0、LED1的状态

1. `KEY0-->LED0`,  `KEY1-->LED1`, `KEYUP->蜂鸣器`,  三个键盘全部由外部中断触发
2. STM32CubeMX中需要在`NVIC`中开启这几个外部中断,并配置优先级,这里设置抢占优先级为1。

## 4.[串口通信](https://zhuanlan.zhihu.com/p/96184047)
> 实现两个字节分别控制LE0和LED1

1. 因为`serial monitor`只能发送字符,所以这里为了简化. 
		
		//字符'0'代表熄灭，字符'1'点亮。 第0字节控制LED0,第一字节控制LED1
		uint8_t buffer[2] = {'0', '0'};

2. 接收完成后串口返回同样的数据,同时蜂鸣器响一下。
		


## 开发板原理图
1. 位于`resource`目录下


## 参考文档

1. [HAL](https://simonmartin.ch/resources/stm32/dl/)