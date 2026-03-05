<!-- Table of Contents -->
- [Introduction](#introduction)
  - [Traffic Light Finite State Machine (FSM)](#traffic-light-finite-state-machine-fsm)
  - [Demo with SimulIDE](#demo-with-simulide)
- [Project Settings](#project-settings)
  - [Requirements](#requirements)
  - [Building Firmware](#building-firmware)
  - [Loading Firmware Into MCU ATtiny4313 - SimulIDE](#loading-firmware-into-mcu-attiny4313---simulide)
  - [Executing Traffic Light Controller - SimulIDE](#executing-traffic-light-controller---simulide)
- [Circuit Design with SimulIDE](#circuit-design-with-simulide)
  - [List of Components](#list-of-components)
  - [MCU ATtiny4313 Datasheet](#mcu-attiny4313-datasheet)
  - [MCU ATtiny4313 Pin Connections](#mcu-attiny4313-pin-connections)
  - [LED - Resistor](#led---resistor)
  - [Buttons - Switch Debouncing](#buttons---switch-debouncing)
  - [GPIO Configuration](#gpio-configuration)
  - [External Interrupt Configuration: INT0 - Buttons](#external-interrupt-configuration-int0---buttons)
  - [Timer Pre-scaler Configuration](#timer-pre-scaler-configuration)
  - [Internal Interrupt Configuration - Timer](#internal-interrupt-configuration---timer)

<!-- Section -->
# Introduction

This project implements a traffic light controller, vehicular and pedestrian, with an ATtiny4313 microcontroller programmed in C, using the open-source simulator SimulIDE in GNU/Linux, internal and external interrupts, and a Finite State Machine (FSM).

  * **[mcu-traffic-light-controller](https://github.com/alfonso-caoz/mcu-traffic-light-controller)** - GitHub Project Repository

> **Concepts and Tools:** ATiny4313 MCU, C, Interrupt Service Routines (ISR), FSM, Timer Pre-scalers, Switch Debouncing, CMake, Doxygen, SimulIDE, GNU/Linux, LEDs, Buttons.

## Traffic Light Finite State Machine (FSM)

<!--
![Traffic Light FSM](./docs/html/dot_inline_dotgraph_1.png)
-->

@image html ./html/dot_inline_dotgraph_1.png "Traffic Light FSM" width=400 height=400

## Demo with SimulIDE

<!--
[DemoTrafficLight_Google_Drive](https://drive.google.com/file/d/1l8RI79wV5eYwWoGk3cDYjdmUB5gFPZs5/view?usp=sharing)
-->

\htmlonly
<center>
<div class="rwd-video">
    <iframe src="https://github.com/user-attachments/assets/ffc96954-2b37-45db-9671-6c3c5c554055" title="Demo Traffic Light" frameborder="0"
    allow="accelerometer; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
    allowfullscreen width=800 height=572></iframe>
</div>
</center>
\endhtmlonly

<!-- Section -->
# Project Settings

## Requirements

- GNU/Linux Operating System (this project was developed and tested under **Ubuntu 24.04.4 LTS**)
- [AVR-GCC](https://gcc.gnu.org/wiki/avr-gcc)

In a terminal install latest **avr-gcc** (this project was developed and tested under **avr-gcc version 7.3.0**):

```console
$ sudo apt install gcc build-essential gcc-avr binutils-avr avr-libc gdb-avr
```

- [AVR-DUDE](https://avrdudes.github.io/avrdude/)

In a terminal install latest **avrdude** (this project was developed and tested under **avrdude version 7.1**):

```console
$ sudo apt install libusb-dev avrdude
```

- [CMake](https://cmake.org/cmake/help/latest/index.html)

In a terminal install latest **cmake** (this project was developed and tested under **cmake version 3.28.3**):

```console
$ sudo apt install cmake
```

- [SimulIDE](https://simulide.com/p/)

    - Download: [SimulIDE 0.4.15 - Linux 64](https://simulide.com/p/download0415/)
    - In a terminal:

```console
$ sudo apt install libfuse2 libqt5serialport5
$ cd Downloads
$ chmod +x SimulIDE_0.4.15-SR10.AppImage
$ ./SimulIDE_0.4.15-SR10.AppImage               # App Execution
```

## Building Firmware

From the project main folder **mcu-traffic-light-controller**, run the following terminal commands:

```console
$ mkdir build
$ cd build
$ cmake ..
$ cmake --build .
```

These last commands will create the file **main.hex** to be uploaded to the microcontroller ATtiny4313 in SimulIDE.

## Loading Firmware Into MCU ATtiny4313 - SimulIDE

- Open the SimulIDE app with double click on the file **SimulIDE_0.4.15-SR10.AppImage**.
- Click on **Open Circuit** (**Ctrl + O**), go to folder **simulation**, and load the circuit **traffic_light_circuit.simu**.
- Right-click on MCU **ATtiny4313**, click on **Load firmware**, go to folder **build**, and select file **main.hex**.

## Executing Traffic Light Controller - SimulIDE

- Click on **Power Circuit**.
- Click on Button **B1** or **B2** to make the Traffic Light FSM transition if it has also been in that initial state ten or more seconds.
- You can check electrical variables in different circuit points.
- You can also click on **Pause Simulation** or stop it by clicking on **Power Circuit** again.


<!-- Section -->
# Circuit Design with SimulIDE

## List of Components

<center>

| Quantity  | Component                 |
| :---:     | :---                      |
| 1         | ATtiny4313                |
| 3         | Red LED                   |
| 3         | Green LED                 |
| 4         | 100 \f$\Omega\f$ Resistor     |
| 2         | 140 \f$\Omega\f$ Resistor     |
| 1         | 1000 \f$\Omega\f$ Resistor    |
| 1         | 1 \f$\mu\f$F Capacitor        |
| 2         | Button                    |

</center>

## MCU ATtiny4313 Datasheet

See all MCU technical characteristics in [ATtiny4313 Datasheet](https://ww1.microchip.com/downloads/en/DeviceDoc/doc8246.pdf).

## MCU ATtiny4313 Pin Connections

<center>

| Pin   | Mode   | Component                      |
| :---: | :---:  | :---                           |
| **PB1** | Output | LEDs: **LEDPR** \|\| **LEDPR**     |
| **PB2** | Output | LEDs: **LEDPG** \|\| **LEDPG**     |
| **PB3** | Output | LED: **LEDVR**                   |
| **PB4** | Output | LED: **LEDVG**                   |
| **PD2** (**INT0**) | Input  | Button **B1** \|\| **B2** |

</center>

> **LEDPR:** LED Pedestrian Red - Two LEDs connected in parallel.
> 
> **LEDPG:** LED Pedestrian Green - Two LEDs connected in parallel.
> 
> **LEDVR:** LED Vehicle Red - One LED.
> 
> **LEDVG:** LED Vehicle Green - One LED.
> 
> **B1** \|\| **B2:** Two different buttons, but connected in parallel to the same MCU pin.

## LED - Resistor

Theoretically the **MCU output voltage = 5 V**. All LEDs in this circuit have a theoretical **forward voltage = 2.4 V**, and a theoretical **maximum forward current = 30 mA**.

- **Single LEDs:** with a **R = 140** \f$\boldsymbol{\Omega}\f$, we have a safe theoretical LED forward current:

$$
    I_{1-Single-LED}=\frac{5\ V - 2.4\ V}{140\ \Omega}=18.57\ mA
$$

- **Parallel LEDs:** with a **R = 100** \f$\boldsymbol{\Omega}\f$, we have a safe theoretical LED forward current:

$$
    I_{1-Parallel-LED}=\left(\frac{5\ V - 2.4\ V}{100\ \Omega}\right)\cdot \frac{1}{2}=13\ mA
$$

## Buttons - Switch Debouncing

- **Buttons:** Buttons are set with a pull-up resistor of 1000 \f$\Omega\f$ to keep them always in a HIGH state (5 V). This way the external interruption (**INT0**) is LOW activated (0 V) when the buttons are pressed. Buttons **B1** and **B2** are connected in parallel to use just one MCU pin.

- **Switch Debouncing:** Switch bouncing is the effect where erratic electrical signals are detected when the mechanical switch bounces, generally within a range of 1 ms. Despite this circuit is implemented in a simulated environment, it is part of a good design to include this effect in case of implementing it with physical elements. 

    Switch Debouncing is achieved with a RC configuration to delay the electrical signal by the mentioned 1 ms range. We already have a **R = 1000** \f$\boldsymbol{\Omega}\f$, therefore **C = 1** \f$\boldsymbol{\mu}\textbf{F}\f$:

$$
    R\cdot C = 1\ \text{ms}
$$

$$          
    C = \frac{1\ \text{ms}}{R} = \frac{1\ \text{ms}}{1000\ \Omega} = 1\ \mu \text{F}
$$

## GPIO Configuration

- **DDRB = 0x1E** - These pins are set as outputs **PB1**, **PB2**, **PB3**, **PB4** (**LEDPR**, **LEDPG**, **LEDVR**, **LEDVG**, respectively).
- **PORTB = 0x00** - Previous LED outputs are initialized as LOW (0 V).

## External Interrupt Configuration: INT0 - Buttons

- **GIMSK |= (1 « INT0 )** - External interrupt **INT0** is enabled.
- **MCUCR = 0x0000** - External interrupt **INT0** is set as LOW activated (0 V).

## Timer Pre-scaler Configuration

- **TCCR0B |= 0x05** - Original clock frequency **Clko = 1 MHz** is decreased 1024 times. Getting a new period of **T = 1.024 ms**:

$$
    T = \left( \frac{Clko}{1024} \right)^{-1}=\left( \frac{1\ \text{MHz}}{1024} \right)^{-1}=1.024\ \text{ms}
$$

## Internal Interrupt Configuration - Timer

- **OCR0A = 244** - This sets time interrupts every 0.250 s. Calculated as:

$$
    x = \frac{0.250\ s}{T}=\frac{0.250\ s}{1.024\ ms}= 244.14 \approx 244
$$


- **TCCR0A |= 0x00** - Set to 0 to avoid pin **OC0A** (**PB2**) being set to HIGH after every time interrupt. This is the default configuration.
- **TIMSK |= 0x01** - Set to 1 to make the time interrupt work by comparison (0.250 s), not by overflow.
