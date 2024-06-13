# Project : CH32V003 RISC-V Mini Game Console
## Overview
The Handheld Gaming Console project integrates a RISC-V microcontroller to create a portable gaming system featuring a OLED display and tactile buttons. This console allows users to play classic games like Snake and Pong, with the RISC-V microcontroller managing game logic, user input, and rendering graphics. Additionally, a sound module provides audio feedback to enhance the gaming experience. This project demonstrates the innovative use of RISC-V technology in creating an immersive and interactive gaming solution.
![GameConsole_pic1](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/e3e6c984-1eec-407f-b5c5-ea7b3ac366c7)

## Components Required
1. VSDSquadron Mini
2. Oled Display
3. Push buttons (5)
4. Capacitors (100nF, 10uF)
5. Coin Battery (	CR2032)
6. Resistors (1k, 2k, 3k, 20k, 8k)
7. Buzzer (MLT-5030)
8. Hreader pins
9. BJT (	MMBT3906)
10. Switch


## Circuit Connections
The VSDSquadron Mini RISC-V development boards features a RISC-V SoC with the following capabilities:

- On-board 24MHz RC oscillator
-  3 groups of GPIO ports, totaling 15 I/O ports
- USART, I2C, and SPI
- UART implemented on USART
- 2KB SRAM for volatile data storage, 16KB CodeFlash for program memory
- On-board Programmer.

The circuit connections are as follows:
- **PA2** GPIO Pin is connected to one of the push bottons which controls the turn on/off of a game.
- **PA1** is connected to the buzzer.
- **PC4** is connected to the game controlling push buttons
- **PC2** and **PC1** act as SCL(Serial Clock Line) and SDL(Seriel Data Line), these lines are used for I2C communication with OLED Display
- And there are some VCC and GND connections.
- Power switch connected to VCC and GND accordingly.

  ![GameConsole_wiring](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/1635d8a1-8faa-4052-a98d-c688ece05d02)

  ### Table for Pin connections

![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/93ad1ecf-48d7-44a6-ac2c-c30f88f1a42a)

## My Circuit:
![SmartSelect_20240613_152042_Canva](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/48902767-7bb7-49df-b2b9-6487daf49a00)

## Software
My code:



