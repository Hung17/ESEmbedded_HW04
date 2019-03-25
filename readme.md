HW04
===
This is the hw04 sample. Please follow the steps below.

# Build the Sample Program

1. Fork this repo to your own github account.

2. Clone the repo that you just forked.

3. Under the hw04 dir, use:

	* `make` to build.

	* `make flash` to flash the binary file into your STM32F4 device.

	* `make clean` to clean the ouput files.

# Build Your Own Program

1. Edit or add files if you need to.

2. Make and run like the steps above.

3. Please avoid using hardware dependent C standard library functions like `printf`, `malloc`, etc.

# HW04 Requirements

1. Please practice to reference the user manuals mentioned in [Lecture 04], and try to use the user button (the blue one on the STM32F4DISCOVERY board).

2. After reset, the device starts to wait until the user button has been pressed, and then starts to blink the blue led forever.

3. Try to define a macro function `READ_BIT(addr, bit)` in reg.h for reading the value of the user button.

4. Push your repo to your github. (Use .gitignore to exclude the output files like object files, executable files, or binary files)

5. The TAs will clone your repo, build from your source code, and flash to see the result.

[Lecture 04]: http://www.nc.es.ncku.edu.tw/course/embedded/04/

--------------------

- [ ] **If you volunteer to give the presentation (demo) next week, check this.**

--------------------

Take your note here if you want. (Optional)

## 1. 實驗題目

上電後先等待,等user按下按鈕(user botton)後，持續閃爍Blue LED

## 2. 實驗步驟
1.查手冊知道user botton使用GPIO PORTA

[UM1472 User manual Discovery kit with STM32F407VG MCU]: http://www.nc.es.ncku.edu.tw/course/embedded/pdf/STM32F4DISCOVERY.pdf

![](https://github.com/Hung17/ESEmbedded_HW04/blob/master/images/1.png)

2.啟用RCC中控制PORTA的bit

![](https://github.com/Hung17/ESEmbedded_HW04/blob/master/images/2.png)

3.設定MODER、OTYPER、OSPEEDR、PUPDR

![](https://github.com/Hung17/ESEmbedded_HW04/blob/master/images/3.png)

4.利用GPIOx_IDR去讀取botton的狀態,用來做後續的判斷參數(讀input值)

![](https://github.com/Hung17/ESEmbedded_HW04/blob/master/images/4.png)

5.觀察user botton的電路圖做判斷式，讓其確認botton按下後使藍色LED持續閃爍

![](https://github.com/Hung17/ESEmbedded_HW04/blob/master/images/5.png)

#include <stdint.h>
#include "blink.h"
#include "reg.h"

int main(void)
{
	SET_BIT(RCC_BASE + RCC_AHB1ENR_OFFSET, GPIO_EN_BIT(GPIO_PORTA));

	//MODER PORTA pin = 00 => General purpose input mode
	CLEAR_BIT(GPIO_BASE(GPIO_PORTA) + GPIOx_MODER_OFFSET, MODERy_1_BIT(GPIO_PORTA));
	CLEAR_BIT(GPIO_BASE(GPIO_PORTA) + GPIOx_MODER_OFFSET, MODERy_0_BIT(GPIO_PORTA));

	//PUPDR PORTA pin = 00 => No pull-up,No pull-down
	CLEAR_BIT(GPIO_BASE(GPIO_PORTA) + GPIOx_PUPDR_OFFSET, PUPDRy_1_BIT(GPIO_PORTA));
	CLEAR_BIT(GPIO_BASE(GPIO_PORTA) + GPIOx_PUPDR_OFFSET, PUPDRy_0_BIT(GPIO_PORTA));
	//按鈕按下讀到低電位,也就是"0"時LED閃爍
	while(!READ_BIT(GPIO_BASE(GPIO_PORTA) + GPIOx_IDR_OFFSET, IDR_0_BIT(GPIO_PORTA)));
	blink(LED_BLUE);
}
