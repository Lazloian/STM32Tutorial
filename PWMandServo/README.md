# STM32 PWM and Servo Tutorial STM32CubeIDE by Henry Silva

## Setup

The Adafruit Feather stm32f405RGT6 Express is used for this tutorial, but this should work for most other stm32 microcontrollers.
The board has two external resonators that will be used as the High Speed External Clock (HSE) and Low Speed External Clock (LSE) that are set under System Core int the RCC Mode and Configuration Menu.
In the Clock Configuration Tab, the HCLK has been set to 168 MHz, which is the max value. HCLK can be whatever value, but it will have an effect on the frequency of your timers in the next step.
