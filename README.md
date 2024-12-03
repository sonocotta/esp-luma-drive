# Esparagus Echo

![Open Source Hardware](/images/open-source-hardware-logo.png)
![Open Source Software](/images/open-source-software-logo.png)

<a href="https://www.tindie.com/stores/sonocotta/?ref=offsite_badges&utm_source=sellers_andrey-malyshenko&utm_medium=badges&utm_campaign=badge_medium"><img src="https://d2ss6ovg47m0r5.cloudfront.net/badges/tindie-mediums.png" alt="I sell on Tindie" width="150" height="78"></a>
<br />

[![Dev Chat](https://img.shields.io/discord/1233306441469657140?logo=discord&label=discord&style=flat-square)](https://discord.gg/PtnaAaQMpS)

![DSC_0032](https://github.com/user-attachments/assets/507d3c19-1dce-41ba-9a02-63a8561f2bc2)

ESP32-Luma-Drive is a simple device that is designed to be used as an ESPHome-controlled Home Assistant integrated smart lights controller. It has an onboard LED matrix consisting of 5 colors (RGBW + night yellow) and a set of MOSFET drivers to individually control 5 lines of external LED strips (RGBWW), so it can be used with either.

## Table of Contents

- [Esparagus Echo](#esparagus-echo)
  - [Table of Contents](#table-of-contents)
  - [Motivation](#motivation)
  - [Features](#features)
  - [Boards Pinout](#boards-pinout)
    - [Built-in LEDs](#built-in-leds)
    - [External LED drivers](#external-led-drivers)
  - [Software](#software)
    - [Home Assistant - media player](#home-assistant---media-player)
  - [Hardware](#hardware)
  - [Where to buy](#where-to-buy)

## Motivation

I'm a Home Assistant enthusiast and working to automate my routines around the house, including a bunch of sensors and lights. I had an idea to include existing ceiling LED strips into the Home Assistant setup a long time ago, so I did. 

The main component of the board is an ESP32-C3 MCU, that is powered from the same source that is powering the LED strip. That is usually 12V, too much for a linear regulator, so I used a high-efficiency drop-down converter instead. For ease of programming, I added a USB-C port, so whenever it is connected, it will be used to power the MCU, when 12V is missing. Although 5V is not enough to power onboard LEDs, it is convenient enough to program the MCU when needed. Why 5V is not enough to power LEDs? More on that below.

![DSC_0033](https://github.com/user-attachments/assets/ad1ebe33-76c4-4a0d-a7b2-a63134d049d4)

When I did the external LED controller, I thought that the LED strip was a great use case, but the device itself can be a glowing star of the show itself since I have no reason to not place a bunch of LEDs on it directly. This can be used to replace some of the smaller size lights around the house, with a specific use case for running runway lights on your night trip to the toilet and back from it.


## Features

| # | ESP Luma Drive |
|---|---|
| Image | image |
| MCU | ESP32-C3 module |
| Power | 5V from USB-C for flashing purposes only <br> 12V DC for driving LEDs and the MCU |
| Internal LEDs | 5 channels, 12 LEDs each: RGBW + Yellow
| External LED drivers | 5 channels, 4A each: RGBWW (suggested) |
| Wireless Connectivity | Onboard WiFi + BLE |
| Size (excl. packaging) | 94 x 54mm | 
| Size (incl. optional packaging) | 60 X 25 X 100mm | 


## Boards Pinout

### Built-in LEDs

| Pin | Description | Setup        | Calculated current (12V) | Max current (continuous) | Max current (pulse) |
|-----|-------------|--------------|--------------------------|--------------------------|---------------------|
|  0  | RED         |  4S3P + 120Ω | FV=2.2V I=26mA           |   30mA                   | 100 mA              |     
|  1  | YELLOW      |  4S3P + 120Ω | FV=2.2V I=26mA           |   25mA                   |  60 mA              |
|  10 | BLUE        |  3S4P + 100Ω | FV=3.0V I=30mA           |   30mA                   | 100 mA              |
|  20 | GREEN       |  3S4P + 100Ω | FV=3.0V I=30mA           |   30mA                   | 100 mA              |
|  21 | WHITE       |  3S4P + 100Ω | FV=3.0V I=30mA           |   30mA                   |  60 mA              |

### External LED drivers

Mames are not prescriptive, that's no more than a handy suggestion.  

| Pin | Name   | Max current (continuous) | 
|-----|--------|--------------------------|
|  3  | BLUE   | 3-4 A                    |
|  4  | GREEN  | 3-4 A                    |
|  5  | WHITE  | 3-4 A                    |
|  6  | RED    | 3-4 A                    |
|  7  | YELLOW | 3-4 A                    |

All drivers are low side, meaning they will ground the output when the appropriate pin is driven high. You connect the load between the +12V (Plus side) and Driver output (Ground). I found this to be more common LED Strips on sale. 

### Using higher voltage

Based on the above table, 12V is the maximum voltage that can guarantee a continuous full-brightness run. If you aim for the short flashes you may use higher voltage. I did tests with 19V and within a minute or two board was working fine. At your own risk, you can apply even higher voltage, considering the following limits

- XL1509 - 40V Max input voltage
- AO3400 MOSFET - 40V Max source-drain voltage
- Capacitor voltage rating - 25V
- Resistor power rating - 250mW - 50mA continuous
- LED current - make your own calculator 

## Software

Since both devices are designed to be primarily used within the Home Assistant setup, the [software](/firmware) folder contains ESPHome configurations for both devices.

- `luma-drive-external-leds` is a simple external LEDs driver with PWM on every channel and a few sample effects
- `luma-drive-internal-leds` is a simple internal LEDs driver with PWM on every channel and the same sample effects

Due to the limit in PWM channels on the ESP32-C3, you can't use all 10 drivers at the same time, you can still use secondary set in binary mode though

### Home Assistant - media player

One simple way that Esparagus Echo can be used in the Home Assistant is by using a media player device. [Yaml config](/firmware/esphome) will get you started. Below are the configuration steps that you need to do in the HA itself.

| Step | Screenshot |
|------|------------|
| **Add ESPHome Addon** <br/> <br/> Navigate to HA Settings > Addons > Add Addon <br/> Search for SSH and install it.  <br/> Enable `Show in sidebar` switch while you there | ![image](https://github.com/sonocotta/esparagus-media-center/assets/5459747/9d9d0a44-ba2a-491f-bff8-e1c08b8754e0)
| **Prepare ESP Luma Drive onboarding** <br/> <br/> Use [Web Flasher](https://web.esphome.io/?dashboard_wizard) to flash stock ESPHome into device | ![image](https://github.com/sonocotta/esparagus-media-center/assets/5459747/8ad222e8-d992-4a75-9a93-596d67ac8cb0)
| **Onboard ESP Luma Drive device into HA** <br/> <br/> Go to the HA ESPHome page and you should be able to find a new device. You need to onboard it with the below config (feel free to change names) <br/> This will take a moment or two | ![image](https://github.com/sonocotta/esparagus-media-center/assets/5459747/32b0c26a-3be1-4e15-b749-1176d46ff011)
| **Validate device in the ESPHome** | ![image](https://github.com/sonocotta/esparagus-media-center/assets/5459747/92df6029-c777-47ce-8ff9-debec70f7e05)
| **Add ESPHome Integration** <br /> <br/> Navigate to HA Settings > Devices & services > Integrations. Click the big + ADD INTEGRATION button, look for ESPHome, and click to add it. <br/> It should discover and add ESPHome media devices based on the previous step | ![image](https://github.com/sonocotta/esparagus-media-center/assets/5459747/c5d3bb12-8b07-4c49-a9e9-cdf2e6cad8ba)
| **Use your media device in the HA to control lights** | ![image](https://github.com/user-attachments/assets/d4309807-f380-40eb-bdaa-4ce0c85cff3e) ![image](https://github.com/user-attachments/assets/9a3bf685-5a68-4c1f-a2f5-d7a08760cbbe)

## Hardware

Please visit [hardware](/hardware/) section for board schematics and PCB designs. Note that PCBs are shared as multi-layer PDFs as well as Gerber archives.

### Word about the efficiency

The simplest way to light up the LED in the MCU world is to connect it to the 5V line over the current limiting resistor. The problem is the efficiency. Not so much if you use the LED for a low-current indication of something, much so if you want it to light the place.

| #      | V = 5V, FV = 2.2V, I = 30mA (Yellow, Red) | V = 5V, FV = 3V, I = 30mA (Green, Blue, White) |
|--------|-------------------------|------------------------------|
| Current-Limiting Resistor | R = (5 - 2.2) / 0.03 = 93.3Ω, 91Ω | R = (5 - 3.0) / 0.03 = 66.7Ω, 68Ω 
| LED Power    | P(LED) = 2.2 * 0.03 = 0.066W | P(LED) = 3.0 * 0.03 = 0.09W |
| Wasted power | P(R) = 2.8 * 0.03 = 0.084W | P(R) = 2.0 * 0.03 = 0.06W |
| Efficiency   | 44% | 60% | 

Not so great. To solve that we prefer to use higher voltage and chain LEDs in series to minimize the waste

| #      | V = 12V, FV = 2.2V, I = 30mA (Yellow, Red), 4 in series | V = 12V, FV = 3V, I = 30mA (Green, Blue, White), 3 in series |
|--------|-------------------------|------------------------------|
| Current-Limiting Resistor | R = (12 - 2.2x4) / 0.03 = 106.67Ω, 120Ω | R = (12 - 3.0*3) / 0.03 = 100Ω | 
| LED Power    | P(LED) = 8.8 * 0.03 = 0.264W | P(LED) = 3.0 * 0.03 = 0.09W |
| Wasted power | P(R) = 3.2 * 0.03 = 0.096W | P(R) = 9.0 * 0.03 = 0.27W |
| Efficiency   | 73.3% | 75% | 

Not ideal, but much better still

## Where to buy

You may support our work by ordering this product at Tindie (coming soon)