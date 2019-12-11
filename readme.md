# 请先阅读[Mac下stm32开发(clion)](https://zhuanlan.zhihu.com/p/95498261)


1. 所有代码基于`正点原子-精英板` b站[教学视频链接](https://www.bilibili.com/video/av8938442?from=search&seid=6964447435862961564)
2. mcu型号:`STM32F103ZET6`, 各种配置基于开发板原理图进行配置
3. 如果不想重复配置`STM32CubeMX`(避免每次都配置IO口,时钟等)。新建工程,直接复制已配置好的`.ioc`文件到新工程,打开`STM32CubeMX`后生成代码到新项目中就可以


## 1.LED

> 实现点亮两个led灯, 参考`led`文件夹下项目

1. `LED0` 对应 `PB5` 口, `LED1` 对应 `PE5`口。 
2. 在STM32CubeMX中配置好两个IO口为输出,默认`高电平` led熄灭
3. 代码生成后, `main` 函数中将IO设置为低电平，点亮led

## 2.蜂鸣器

> 实现蜂鸣器每隔一秒响一次, 参考`beep`文件夹下项目

1. 蜂鸣器对应`PB8`口,默认设置成低电平输出


## 3.键盘&外部中断
> 实现3个按键分别控制蜂鸣器、LED0、LED1的状态

1. `KEY0-->LED0`,  `KEY1-->LED1`, `KEYUP->蜂鸣器`,  三个键盘全部由外部中断触发
2. STM32CubeMX中需要在`NVIC`中开启这几个外部中断,并配置优先级,这里设置抢占优先级为1。

## 4.[串口通信](https://zhuanlan.zhihu.com/p/96184047)
> 实现两个字节分别控制LE0和LED1。 参考`uart`文件下项目

1. 因为`serial monitor`只能发送字符,所以这里为了简化. 
		
		//字符'0'熄灭LED，字符'1'点亮LED。 第0字节控制LED0,第一字节控制LED1
		uint8_t buffer[2] = {'0', '0'};

2. 接收完成后串口返回同样的数据,同时蜂鸣器响一下。
		

## 5.DMA
> 实现DMA控制LED灯,和`串口通讯`实现效果相同。不同的是改用DMA读写

1. `SMT32CubeMX`中配置DMA,`TX`的的`mode`为`noraml`, `RX`的mode为`circular`。

## 6.基本定时器定时

	Updatefrequency = TIM clk/((PSC+1)*(ARR+1))
	
> 实现LE0每隔1秒闪烁一次, LED1每个0.5秒闪烁一次。 参考`timer`文件下项目

1. `STM32CubeMX`中配置Timer6, Timer7。 时钟频率为`72MHZ`,定时一秒可以设置`PSC=7200-1`, `ARR=10000-1` 根据计算公式,计算出`Updatefrequency`为1s
2. NVIC设置中断使能,配置中断优先级
3. 开启定时

		HAL_TIM_Base_Start_IT(&htim6);
  		HAL_TIM_Base_Start_IT(&htim7);
4. 回调函数

		void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
		{
		    if (htim->Instance == htim6.Instance) {
		        HAL_GPIO_TogglePin(LED0_GPIO_Port, LED0_Pin);
		    }
		    if (htim->Instance == htim7.Instance) {
		        HAL_GPIO_TogglePin(LED1_GPIO_Port, LED1_Pin);
		    }
		}

	

## 开发板原理图
1. 位于`resource`目录下


## 参考

1. [HAL](https://simonmartin.ch/resources/stm32/dl/)
2. [HAL_Delay() stuck in a infinite loop](https://stackoverflow.com/questions/53899882/hal-delay-stuck-in-a-infinite-loop)
3. [STM32CubeMX Tutorial Series: Basic Timer](https://www.waveshare.com/wiki/STM32CubeMX_Tutorial_Series:_Basic_Timer)