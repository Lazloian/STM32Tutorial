# STM32 PWM and Servo Tutorial STM32CubeIDE by Henry Silva

## Setup

The Adafruit Feather STM32F405RGT6 Express is used for this tutorial, but this should work for most other STM32 microcontrollers.
The board has two external resonators that will be used as the High Speed External Clock (HSE) and Low Speed External Clock (LSE) that are set under System Core int the RCC Mode and Configuration Menu.
In the Clock Configuration Tab, the HCLK has been set to 168 MHz, which is the max value.
HCLK can be whatever value, but it will have an effect on the frequency of your timers in the next step.

## Timer Setup and Configuration

The next step is to pick your timer that you would like to use for PWM.
The STM43F405 has 14 timers, but not all of them can do PWM.
To find out which timers that can be used, look at the reference manual for your device under the timers section.

![reference](./src/ref.JPG)

For the STM32F4, timers 2 to 5 are general purpose timers capable of PWM.
Timer 4 will be used for this tutorial since it its pin is output on the Adafruit feather board.

![enableTimer](./src/step1.JPG)

To enable timer 4, look under the Timers section and click on TIM4.
Each timer may have multiple channels on different pins.
Timer 4 has 4 channels, enable the channels on the pins you would like to use for PWM output.
Also click the box next to Inernal Clock. **THIS IS VERY IMPORTANT**
