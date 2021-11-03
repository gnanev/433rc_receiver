# Bare metal atMega (mega328p) Decoder library for 433 MHz remote controls

## 1. Quick Info
You can use this code to easily and reliably detect codes from wide range of 433 MHz remote controls. Since it is based on less efficient pin polling method, it is not recommended to use it in any heavy, CPU-consuming applications.

## 2. Prerequisites
- atMega328 board, like Arduino Nano, or any compatible MCU

![nano](https://user-images.githubusercontent.com/20777524/140094097-fd21740f-1920-46d2-8fdd-c5898f722bde.png)

- Programmer or any tool to flash bare metal hex

- 433MHz receiver, like AK-119-S

![ak-119s](https://user-images.githubusercontent.com/20777524/140094125-4ce89b00-3805-4a0a-97c3-94aa566cecc5.png)

## 2. Basics

Once you power up the receiver and connect the scope to it's Data (output) pin, you will see white noise

![433_noise](https://user-images.githubusercontent.com/20777524/140086493-292a3095-8a95-4b6d-b200-46aecfd1ba95.png)

When you press remote button, you will see it's code coming repeatedly, separated by intervals of silence

![433_signal](https://user-images.githubusercontent.com/20777524/140086534-f8c9de1e-e3a5-493f-b748-468dc37c005d.png)

The long pulses are decoded as logic 1, and short ones as logic 0

![pulse](https://user-images.githubusercontent.com/20777524/140095350-3ffacf98-873a-47de-a5f8-d71694c2d795.png)


## 3. Usage

Connect Data pin of the receiver to the input pin of the MCU. By default this is pin 2 of the PORTC, but you can change it by editing INPUT_PORT and INPUT_PIN definitions.
Simply call get433Data(). If there is data available, it will return TRUE, and you will get the 32 bit remote control button code.


## 4. Calibration and tweaking

The pulse widths are defined by LOGIC_0_MIN, LOGIC_0_MAX, LOGIC_1_MIN and LOGIC_1_MAX definitions. Since pulse widths are never exact lengths, the algorithm uses intervals to decode logic 1s and 0s. The time units are 

1 / (F_CPU / 256) sec

or

1000 / (F_CPU / 256) ms

Which means that at 16 MHz operating frequiency 1 unit is 0.016 ms or 62.5 units are 1 ms.
LOW_LEVEL_THRESHOLD defines silence interval after which decoding starts and ends.
You can define VALIDATING_MASK, that is, if the remote button codes have big constant part and only low 2-4 bits changes (which is usualy the case), you can use it to validate the input.

## 5. Diagnostics

If you define RC_DIAGNOSTICS, diagnostic code will be enabled. By calling getDiagnosticsBuffer() you will receive array with stored pulse lengths, regardles if there is valid code stored or not. You can use it to tune the algorithm to decode your specific remote control.
Additionally, when decoding starts, pin 1 of the PORTC will be set high and when it ends, it will be set back to low. By this, when you connect 2 channel scope to the board, you can visualize algorithm behavior.

## 6. Testing

The library was tested with 3 different remote controls from Aliexpress and works fine with all of them.

 ![remotes](https://user-images.githubusercontent.com/20777524/140107626-1d44d187-70e4-4c9e-becb-4169d573db9c.png)
