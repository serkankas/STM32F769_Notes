## General Purpose Input Output Examples

I am going to use in following action.

1. Open CubeIDE
1. Select The Board as STM32F769 DISC0
1. Give Project a Name
1. Clear the Pinouts

From there, I would like to create three example at the same time.

1. Flip Flop LED
1. Control Over LED
1. Control with Button

My attemp is, toggle LED in every 500 ms. Also, light the second LED whenever we press the button. First, lets find the LEDs and BUTTON pins. In [schematic](/DOCs/Schematic.PDF) pages 13, we can see that there are two LEDs and one user button. In page 4, I can see that Button is binded to __Pin A0__. One of LED is connected to __Pin J5__, the other LED is connected to __Pin J13__. That's the necessary information that we need. Check your CubeMX that those pins setted correctly.

| Function | Mode
| --- | ---
| Button | GPIO Input
| LED | GPIO Output

Set your pins accordingly. Then open MX section, Set your RCC (HSE) to Crystal and SYS (Debug) to Serial-Wire. From there, you can generate the codes.

In [HAL Documents](/DOCs/HAL%20and%20Low%20Layer.pdf) section 28.2.4 (page 378) shows us the avaliable functions for GPIOs. The HAL initialization has been made with code generations. If not, you may need to manually enable both clock and necessary pins. Since CubeMX and CubeIDE handles that by its own, I am going to skip it.

In file structure, you'll be able to find two important file for us.
1. __main.c__ , which located in \<project-folder>\Core\Src\main.c
1. __stm32f7xx_hal_gpio.h__ , whichs located under \<project-folder>\Drivers\STM32F7xx_HAL_Driver\Inc\stm32f7xx_hal_gpio.h

We can easily see the functions inside that header. The functions we look for are those:
1. ReadPin (will be used to get status of Button)
1. WritePin (wşkk be ysed to change status of LED according to our wishes)
1. TogglePin (Continous Toggle LED)

With that being sad, let's dive into the code section in main.

the code structure should be look a like this in main.c

```c
// some includes, etc.
 int main(void){
    //some inits, etc.
    //...
  /* USER CODE BEGIN WHILE */
  while (1)
  {
    /* USER CODE END WHILE */

    /* USER CODE BEGIN 3 */
  }
  /* USER CODE END 3 */
}
//more functions.
```

If you plan to use infinite while loop, Then I suggest you to write your code between ```/* USER CODE BEGIN 3 */``` and ```/* USER CODE END 3 */```. If you plan to use anything before join the loop, then use from ```/* USER CODE BEGIN WHILE */``` to ```/* USER CODE END WHILE */```. The final code should look similar to like this.

```c
  /* USER CODE BEGIN WHILE */
  uint8_t counter;
  counter = 0;
  while (1)
  {
    /* USER CODE END WHILE */

    /* USER CODE BEGIN 3 */
    if(counter == 10){
      counter = 0;
      HAL_GPIO_TogglePin(GPIOJ, GPIO_PIN_5);
    }

    if( HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_0) == GPIO_PIN_SET ){
      HAL_GPIO_WritePin(GPIOJ, GPIO_PIN_13, GPIO_PIN_SET);
    }else{
      HAL_GPIO_WritePin(GPIOJ, GPIO_PIN_13, GPIO_PIN_RESET);
    }

    counter++;
    HAL_Delay(50);
  }
  /* USER CODE END 3 */
```

The ```uint8_t counter;``` decleration should be at above that line, however it's still okay to use there. So 50ms delay is appliad thanks to HAL_Delay function. Rest is also self explanatory. If you wish to stop that toggle while you press the button, then you should ad another while loop in if cases to wait until switch is leaved.

After this, we can jump into section for Interrupts [NVIC](/Examples/NVIC_Example.md).