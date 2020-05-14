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
Also click the box next to **Inernal Clock** so that the timer uses the internal clock. **THIS IS VERY IMPORTANT**
This will determine what source your timer will use.
Leave the other options and channels that you do not want to use disabled.

The next step is to set the presacler and counter period for the timer to get the PWM frequency that you want.
In this example, a servo will be controlled using PWM.
A servo needs a frequency of 50 Hz for input.
The frequency of the PWM signal is determined by the equation:

`PWM frequency = (Timer Clock Frequency) / ((Prescaler + 1) * (Counter Period + 1))`

To find the Timer Clock Frequency, you must first found out what source the timer is using.
This can be found by looking up the clock tree of your device.

![Clock Tree](./src/clocktree.JPG)

Looking at the clock tree for the STM32F4, timer 4 is on the APB1 bus.
Look at your Clock Configuration to see what frequency your timer is on.

![Clock Config](./src/clock.JPG)

Notice that there are two different frequencies, the APB1 peripheral clocks and the APB1 Timer clocks.
The value that will be used in the equation (and the frequency that your clock is on) is in the AP1 Timer clocks box because it is a timer.

![Frequency Config](./src/step2.JPG)

The next step is to decide what values to use for the prescaler and counter period.
The counter period is duration the timer will count till the next pulse.
The value that you set the counter to will be divided by the counter period to determine the duty cycle.
So the counter period will determine the range of values for the duty cycle.
In this tutorial a prescaler of 840 and a counter period of 2000 is used because the value in the APB1 Timer box was 84MHz.

`(84000000) / (840 * 2000) = 50Hz`

Notice that they have been input with a `-1` at the end of each value.
This is important because the registers that store these values on the STM32 add 1 to these values automatically.
If you look down the configuration menu, you can see options for the channels that you have enabled PWM for.
You can set the default duty cycle of each channel in this menu next to Pulse.
The default value is `0` which set the duty cycle to 0 percent.
For this example, channel 1 could be set to a 50 percent duty cycle if a pulse of 1000 is input which is half the counter period.

## Starting PWM in the Code and Changing the Pulse

Each individual PWM must be started in your main function.
The command `HAL_TIM_PWM_Start(htim, Channel)` is used to start the channel.
`htim` is the pointer to the timer object that the channel is on.
`Channel` is the channel that you would like to start.

Here is an example starting PWM channels 1, 2, and 3 on timer 4:

```c
  // start PWM channels 1, 2, and 3 on timer 4
  HAL_TIM_PWM_Start(&htim4, TIM_CHANNEL_1);
  HAL_TIM_PWM_Start(&htim4, TIM_CHANNEL_2);
  HAL_TIM_PWM_Start(&htim4, TIM_CHANNEL_3);
```

The pulse of the PWM channel can be changed as well:

```c
  // set channel 3 on timer 4 to 250 (which is 12.5% duty cycle for a counter period of 2000)
  htim4.Instance->CCR3 = 250;
  
  // set channel 1 on timer 4 to full duty cycle (100% would be the counter period of 2000)
  htim4.Instance->CCR1 = 2000;
  
  // turn off channel 2 on timer 4
  htim4.Instance->CCR2 = 0;
```
