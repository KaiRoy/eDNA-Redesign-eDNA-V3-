# eDNA Board V3.0 - A Redesign of the OPEnS Lab's eDNA Control Board

<!-- Insert Image of eDNA V3.0 -->

## Table of Contents

- [Description](#description)
- [Design](#design)
- [Current State](#current-state-of-the-project)
- [What would I do differently? What changes would I make?](#what-would-i-do-differently-what-changes-would-i-make)
- [Installation](#installation) <!-- Should I have Installation and Usage above or below the Design, Current State, Changes, etc? -->
- [Usage](#usage)
- [Credits](#credits)
- [License](#license)

## Description

I have worked on the eDNA project at the OPEnS Lab for the past two years and during that time I noticed a couple of things that I wanted to change about the current electronics board (Dubbed eDNA Electronics V2). Because of other responsiblities, I never had the time to make those changes so I decided to make a Project Course out of it to ensure that I would have the time. This meant I had to limit the number of changes that I wanted to make for time purposes. Here were the goals for the project/redesign:
- Allowing for 5V sensor support (mainly I2C sensor support)
- Redesigning the Power Management Circuits to allow for easier integration of wake-from-sleep interrupts
- Write Documentation for this version and the previous version, since no documentation does not exist


## Designs

Most of the circuits on this board have already been designed and I had no intention of fundamentally changing them in this redesign. The circuits that are being redesigned are the sleep/power management circuit and the logic level converter circuit. The other circuits only get changed on the PCB Layout level, changing as many two-terminal components from 1206 to 0805 and general rearrangement of the PCB Layout.

<!-- Insert Images of eDNA V2 Power Managment Circuit -->

The power management (PM) system was designed to disable the output of the voltage regulator (Vreg). This would conserve a lot of power as most of the circuits and devices would be powered off, with only the RTC and parts of the PM circuit still functioning. This circuit work by using a D Flip Flop connected to the Microcontroller and the RTC to enable or disable the output of the Vreg Circuit. Because of this design, the microcontroller was completely off with only the RTC Interrupt able to wake up the microcontroller. This makes it difficult to add additional external interrupts to wake the system from sleep. We encountered this problem when we tried to add a button that would wake the system from sleep and trigger an operation.

<!-- Insert Image of Hypnos V3.4 Power Managment Circuit -->

Another project from the OPEnS Lab created a PM circuit that is used by the majority of projects at the OPEnS Lab, the eDNA project being one of the few projects that do not. This project, called the Hypnos Board, uses a series of MOSFETs to enable and disable power lines going to peripheral devices, leaving the microcontroller powered. We can further conserve power by putting the microctronller into a deep sleep. This will allow us to easily add new wake-from-sleep interrupts while still conserving power.

According to other employees at the OPEnS Lab and some online research the Adafruit Feather M0, the OPEnS Lab's Microcontroller of choice, consumes 1-2mA of current while in a software sleep. Based on the size of the battery, power consumption during operation, and the size of the battery, the approximate current draw while in sleep should be little enough that the sampler can complete a full 24 operations over the course of 30 days, which is what the eDNA Sampler was designed to be able to do. 

The change in the logic level converter circuit was needed because 5V I2C sensor support was desired for the eDNA Sampler. This is because many I2C sensors function off of 5V and limiting the system to only 3.3V I2C sensors can make it difficult to add desired features or functionality. The current logic level converter, a module from Sparkfun, only has 4-channels of conversion with three already in use. Since the circuit needed to be changed anyway, instead of using a module like the one from SparkFun, an IC was found to serve the purpose. The IC used in the v3.0 is the TXS0108E, an eight channel logic level converter.


## Current State of the Project 	<!-- Read and Update this area -->

I was able to do some testing by the end of the term and discovered a couple of mistakes. These mistakes had to do with how I wired certain components up on at the schematic level. I misconnceted the Sleep LED and the RTC_INT Pull-Up Resistor. The sleep LED is less important but rewiring it to my always-on 3.3V line instead of my peripheral device 3.3V line allowed me to better see if the sleep switch/circuit was functioning correctly. The RTC_INT Pull-Up Resistor on the other had was a major error. I had connected the resistor to the Peripheral 3.3V line instead of the always-on 3.3V, meaning that the power would be cut during sleep. Since the RTC_INT is necessary for waking up, this caused a constant cycle of the sleep mode being enabled, the drop in voltage triggering the interrupt, the system waking up, realizing that it has not reached the desired wake-up time, and falling back asleep. 

<!-- Insert Image of the rewired Sampler Board -->

These mistakes could have been caught if I spent more time at the planning and schematic levels review the design before moving onto the PCB design. Something I plan to allocate time for in future projects. Because of this wiring issues, this version of the board remains untested. There are plans to cut the necessary traces and solder splice wires to reconenct the components and continue testing.


## What would I do differently? What changes would I make?

In the future I would spend a lot more time verifying the schematic level of my design to ensure that I have no obvious mistakes, such as the wiring mistakes that I made in the first version of this board. If I am working on a project that is time limited, I would be sure to allocate more than enough time to spend verifying my design/schematic before moving onto the development of the PCB. 

I would also use KiCad over EAGLE in the future if possible. Having used both of the EDA softwares, I have developed a preference towards KiCad over EAGLE due to KiCad's Hotkey and Shortcut based control scheme. This has made my workflows in KiCad feel much smoother to me than my workflows in EAGLE have. In addition, I have been using the free version of EAGLE which has limitations on what can be done in the software, while KiCad does not. 

Below are a list of changes that I would recommend be made in future version of this board. I have split it into two categories as there is a difference between the changes that I would personally like to make, and the changes that I would recommend to the OPEnS Lab. Since the OPEnS Lab cannot simply make changes for the sack of making changes, the changes I recommend are ones that I see having a better Benefit-Cost Ratio conisdering the current state of the Project. 

What I would recommend for the next OPEnS Lab Version:
- Find a new Voltage Regulator IC/Redesign the Vreg Circuit
	- This IC was chosen as it had an Enable pin and its was automotive grade. Since neither of these specifications are that important I would recommend looking into a new IC that is easier to come by, since this one has been very hard to purchase in the past. 
- Redesign the Shift Registers to use the existing SPI Pins on the Feather M0
	- If that is not possible with the current Shift Registers for any reason, I would recommend finiding new Shift Registers to use in the design.

For my own personal version (outside of the OPEnS Lab):
- Switch to an ESP32 based design
	- Deep Sleep Functionality
  	- Built in RTC and WiFi Modules
  	- Similar amount of I/O Pins
	- Larger Program Memory (Our debug code is limited by the size of the Feather M0 Program Memory)
- Redesign the PCB with the intention of purchasing them preassembled. 
	- This would allow for smaller componenets to be used, components too small to be easily assembled by hand. 
	- This would allow for a two-sided PCB design i.e components on either side of the PCB, something that is hard to do by hand.


## Installation

What EDA libraries and files are needed for this project?
- eDNA Library
- Various Component Libraries
<!-- Make a singular Library for all of the components Symbols and footprints -->
<!-- Insert a link to a library guide or make my own -->

<!-- Add a link to the firmware installation guide -->
<!-- Note which code version to use -->


<!--
## Usage

Provide instructions and examples for use. Include screenshots as needed.

To add a screenshot, create an `assets/images` folder in your repository and upload your screenshot to it. Then, using the relative filepath, add it to your README using the following syntax:

    ```md
    ![alt text](assets/images/screenshot.png)
    ```

## Features

If your project has a lot of features, list them here.

## Tests

-->

## Credits

List your collaborators, if any, with links to their GitHub profiles.
- Nathan Jesudason: Modifying the [Sampler Code](https://github.com/OPEnSLab-OSU/ednaServer/tree/54-revamp-sleep-system) Changes for testing

If you used any third-party assets that require attribution, list the creators with links to their primary web presence in this section.
- OPEnS Lab's [Hypnos Board V3.4](https://github.com/OPEnSLab-OSU/OPEnS-Hypnos)
- A branch of the [ednaServer](https://github.com/OPEnSLab-OSU/ednaServer/tree/54-revamp-sleep-system) code
<!-- Link the Symbols and Footprints used? -->

## License

Licensed under the [CERN-OHL-S-2.0](LICENSE.txt) License