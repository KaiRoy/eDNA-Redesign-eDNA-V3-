# eDNA Board V3.0 - A Redesign of the OPEnS Lab's eDNA Control Board

## Table of Contents

- [Description](#description)
- [Design](#design)
- [Current State](#current-state-of-the-project)
- [What would I do differently? What changes would I make?](#what-would-i-do-differently-what-changes-would-i-make)
- [Installation](#installation) <!-- Should I have Installation and Usage above or below the Design, Current State, Changes, etc? -->
- [Usage](#usage)
- [Credits](#credits)
- [License](#license)

## Description	<!-- Read and Update this area-->

I have worked on the eDNA project at the OPEnS Lab for the past two years and during that time I noticed a couple of things that I wanted to change about the electronics board (Dubbed eDNA Electronics V2). I never had the time to make those changes so I decided to make a Project course out of it to ensure that I would. This meant I had to limit the number of changes that I wanted to make for time purposes. Here were the goals for the project/redesign:
- Allowing for 5V sensor support (mainly I2C sensors)
- Redesigning the Power Management Circuits to allow for multiple wake-from-sleep interrupts
- Write Documentation for this version and the previous version, since it does not exist


## Design	<!-- Read and Update this area-->

Most of the circuits on this board have already been designed and I had no intention of fundamentally changing them in this redesign. The main circuits being redesigned are the sleep circuits and the logic level converter circuit. The other circuits only get changed on the PCB Layout level, changing as many two-terminal components from 1206 to 0805 and general rearrangement of the PCB Layout.

The problem with the old sleep circuit was that to conserve power, it disabled the output of the voltage regulator. Because of this, the microcontroller was completely off, preventing conventional interrupt wake functionality. To wake up the system, the previous designer created a circuit based on a D Flip Flop where the interrupt from the RTC would trigger the D Flip Flop to enable the Voltage Regulator. This makes it difficult to add additional external interrupts to wake the system from sleep. We encountered this problem when we tried to add a button that would wake the system from sleep and trigger an operation.

The benefit of this circuit is that no components can passively consume power when not in use (when in sleep). But this type of circuit is not the only one to stop the passive consumption of unnecessary components. Another project from the OPEnS Lab created a circuit that is used by the majority of projects at the OPEnS Lab, the eDNA project being one of the projects that do not. This project called the Hypnos Board, uses a series of MOSFETs to enable and disable power lines going to peripheral devices. If every circuit except the voltage regulator and microcontroller connects to the MOSFET-controlled power lines, then the only circuit consuming power would be the voltage regulator. According to other employees at the OPEnS Lab and to online research, the OPEnS Lab microcontroller of choice, the Adafruit Feather M0, consumes 1-2mA of current while in a software sleep. Based on the size of the battery, power consumption during operation, and the size of the battery, the approximate current draw while in sleep should be little enough that the sampler can complete a full 24 operations over the course of 30 days, which is what the eDNA Sampler was designed to be able to do. 

The change in the logic level converter circuit was needed because 5V I2C sensor support was desired for the eDNA Sampler. This is because many I2C sensors function off of 5V and limiting the system to only 3.3V I2C sensors can make it difficult to add desired features or functionality. The current logic level converter, a module from Sparkfun, only has 4-channels of conversion with three already in use. The second reason was that the current. Since the circuit needed to be changed anyway, instead of using a module, an IC was found to serve the purpose. The IC found was the TXS0108E.


## Current State of the Project 	<!-- Read and Update this area -->

I have met two of the four requirements, and have sort of met a third requirement. The two requirements that I have met are the 5V sensors support and the documentation requirements. I wrote up a document explaining the function of each circuit and why certain decisions were made when designing those circuits. I showed this document to one of my bosses during a meeting and they were satisfied with the result. Especially considering no documentation has ever existed explaining the function of the electronics board and why certain decisions were made. 

The 5V sensor requirement was tested using a button that I could press to create a digital signal and recorded what happened on the other side of the logic level converter with a multimeter. While I did not use an oscilloscope as I had originally planned, I felt that the multimeter test was sufficient enough to test the logic level converter circuit. 

The requirement that was partially met was the PCB redesign. There are some minor issues with the board, the sleep circuit is not fully tested, and I did not get the PCB verified by my project mentor. I did get the schematic and PCB looked at by a senior employee at the lab with a lot of experience and he did give the okay on what he saw. The only issues that I encountered were mistakes in connecting certain components to the MOSFET-controlled Power rails instead of the always-on power rails. The most significant component was the pull-up resistor for the RTC interrupt. Since the RTC interrupt functions by pulling the line low, when the pull-up lost power, it triggered the interrupt on the microcontroller. 

- Rewire the Sleep LED
- Rewire the RTC_INT Pull-Up Resistor


## What would I do differently? What changes would I make?

What I would recommend for the next OPEnS Lab Version:
- Find a new Voltage Regulator IC/Redesign the Vreg Circuit
- Find a new Shift Register IC/
- Redesign the Shift Registers to use the existing SPI Pins on the Feather M0

For my own personal version (outside of the OPEnS Lab):
- Switch to an ESP32 based design
  - Deep Sleep Functionality
  - Built in RTC and WiFi Modules
  - Similar amount of I/O Pins
  - Larger Program Memory (Our debug code is limited by the size of the Feather M0 Program Memory)
  - 


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
- Nathan Jesudason: [Code]() Changes for testing

If you used any third-party assets that require attribution, list the creators with links to their primary web presence in this section.
- OPEnS Lab's [Hypnos]() Board
<!-- Link the Symbols and Footprints used? -->

## License

Licensed under the [CERN-OHL-S-2.0](LICENSE.txt) License