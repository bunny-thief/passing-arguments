---
title: 'Raspberry Pi Pico W + TinyGo: Hello, World!'
slug: picow-hello
date: 2024-02-26T09:00:00+01:00
draft: false
categories: ['Microcontrollers']
tags: [Go, Tinygo, Raspberry Pi, Pico W]
ShowCodeCopyButtons: true
Summary: Write Hello, World! program in TinyGo for the Raspberry Pi Pico W

cover:
  image: '/img/005-picow-hello/picow-hello.jpg'
  alt: '⚠️Raspbarry Pi and TinyGo logos' # alt text
  caption: 'Raspbarry Pi and TinyGo logos'
---

## Overview

This guide goes over how to write "Hello World" for a Raspberry Pi Pico W microcontroller with TinyGo.

The Raspberry Pi [Pico W](https://www.raspberrypi.com/products/raspberry-pi-pico/ 'Pico specs') is a microcontroller board that is based on the RP2040, a "dual-core Arm Cortex-M0+ processor with 264kB internal RAM". The Pico W is a wireless variant of the regular Pico and features a "2.4GHz 802.11n wireless LAN and Bluetooth 5.2" module.

While the Pico series is similar to single board computers from the Raspberry Foundation in that you can connect peripheral devices to them, like the _Raspberry Pi 4_ for example, Picos don't run an a full-fledged OS. The only way to interact with a Pico is to create a program on a computer and upload it to the Pico W via a USB cable. Programs that run on the Pico W can be written in C, Micropython and TinyGo. This guide will use [TinyGo](https://tinygo.org/ 'TinyGo.org') as the target language.

**TinyGo** is a compiler that takes programs written in the _Go_ programming language and compiles them to small targets that have limited resources, especially RAM, such as microcontrollers from [Arduino](https://www.arduino.cc/ 'Arduino.cc') and the Raspberry Pi Pico series.

Hello, World sounds simple but there's a major road-block you will encounter if you attempt to follow the Hello World tutorial found on TinyGo's web site. More on that later.

## Requirements

1. All the code and procedures for this guide run on a [Raspberry Pi Pico W](https://www.raspberrypi.com/products/raspberry-pi-pico/ 'Raspberry Pi Pico Series'). The contents of this guide also apply to a regular Pico. You also need a USB cable with a [Micro USB-B](https://de.wikipedia.org/wiki/Micro-USB-Standard#/media/Datei:Micro_USB.jpg 'Micro USB connector') connector to connect the Pico to your computer.
2. [Go](https://go.dev/ 'Go.dev') v1.19 or above needs to be installed. You can follow the instructions on Go's [Download and Install](https://go.dev/doc/install 'Install Go') page here if you don't already have Go installed.
3. You also need to install TinyGo. Follow the instructions on TinyGo's [Quick install](https://tinygo.org/getting-started/install/ 'Install TinyGo') quide to set it up. **TinyGo's tools depend on Go so make sure you have both installed**.
4. A code editor is needed to type a Go program. This guide will use [VS Code](https://code.visualstudio.com/download 'Install VS Code') and the [Go](https://marketplace.visualstudio.com/items?itemName=golang.Go 'Go extension') and [TinyGo](https://marketplace.visualstudio.com/items?itemName=tinygo.vscode-tinygo 'TinyGo extension') extensions. Follow the instructions to install VS Code first and then the extensions.
5. Finally we'll need an LED and a resitor and a breadboard. I'll be using a red LED and a 220Ω resistor and a standard breadboard.

## Background

Normally, writing a [Hello, World!](https://en.wikipedia.org/wiki/%22Hello,_World!%22_program '"Hello, World!" program') in a new programming language involves outputting text to the console. Because microcontrollers don't have a way to display text on the screen, it is customary to make an LED blink on and off instead. As metioned previously, the official TinyGo site has a Hello, World! [tutorial](https://tinygo.org/docs/tutorials/blinky/ 'Blinking LED tutorial') that you can follow to turn a microcontroller's on-board LED on and off.

The Hello, World! program, in the tutorial section of the TinyGo's website, should work for any microcontroller that is supported by TinyGo but it doesn't work if you happen to have a Raspberry Pi Pico W. The reason is that on-board LED for this particular board is not connected to the board directly. How can that be? Section 3.4, on page 15 of the "[Connecting to the Internet with Raspberry Pi Pico W](https://datasheets.raspberrypi.com/picow/connecting-to-the-internet-with-pico-w.pdf 'Connecting to the Internet')" manual provides a clear answer:

> Unlike the original Raspberry Pi Pico, the on-board LED on Pico W is not connected to a pin on RP2040, but instead to a
> GPIO pin on the wireless chip.

**GPIO** stands for General Purpose Input/Output. You can read more about GPIO pins [here](https://projects.raspberrypi.org/en/projects/physical-computing/1 'GPIO pins').

The other half of the story of why it doesn't work is because while the regular Pico board is supported by TinyGo, the wireless module is not. I didn't know this and literally spent hours trying to figure out why the on-board LED was not lighting up. Ok, that's enough storytelling.

This guide has four main parts.

1. Write "Hello, World!" program in VS Code.
2. Connect the LED and resistor to the Pico W.
3. Connect Pico W board to the computer.
4. Transfer program to Pico.

## Write "Hello, World!" program

Let's start out by launching the terminal by pressing `Ctrl+Alt+T` and creating a new directory named `hello` and immediately moving into it.

```
mkdir hello && cd hello
```

Next, we need to initialize a Go [module](https://go.dev/doc/tutorial/create-module 'Create a Go module'). Modules are used to group related Go packages together. Here's the command that creates a module. I named my module `hello` but it can be anything you like.

```
go mod init hello
```

And you should see the following output on the command line. You should also see a file named **go.mod** in the project's directory.

{{< 005-picow-hello/go-mod-output >}}

We should also create our `main.go` file while we have the command line open. This file will hold the code that makes the LED flash on and off.

```
touch main.go
```

Now that we have taken care of the preliminaries, we can launch VS Code with the following command.

```
code .
```

With VS Code open, we need first need to pick a target microcontroller board. If we [installed](#requirements 'Requirements') the Go and TinyGo extensions in VS Code, we should see the TinyGo status bar element at the bottom of the screen. Click on it and then type in "pico" on the Command pallette and then select pico as the target.

![⚠️Select target board ](/img/005-picow-hello/select-target.gif)

Even though the code in the "Blinking LED" tutorial doesn't work on Pico W, we can still use it as a starting point. Here's what that code looks like.

```go
package main

import (
	"machine"
	"time"
)

func main() {
	led := machine.LED
	led.Configure(machine.PinConfig{
		Mode: machine.PinOutput,
	})

	for {
		led.Low()
		time.Sleep(time.Millisecond * 500)

		led.High()
		time.Sleep(time.Millisecond * 500)
	}

}
```

Let's copy and paste all of it to our _main.go_ file. If you would like to delve deeper into what each line in the above program does, you can read the [Blinking LED](https://tinygo.org/docs/tutorials/blinky/ 'Blinking LED Tutorial') tutorial at TinyGo's site.

> ### On-board LED Constant
>
> This version of the program doesn't work because the led variable has been assigned the constant **LED** which references GPIO25. This works on the regular Pico board but not on the Pico W. The on-board LED on Pico W is referenced through the wireless module as GPIO0. Since it can only be accessed by the wireless module and TinyGo's driver doesn't support the wireless module, it is not accessible. You can see a list of declared constants for the Pico driver [here](https://tinygo.org/docs/reference/microcontrollers/machine/pico/#constants 'Pico driver constants').

### Assign GPIO16 to led variable

The on-board LED is not an option because it's not supported by the TinyGo driver for the Pico. We can however edit the program to assign a different General Purpose Input/Output (GPIO) pin to the `led` variable. We can do so by changing the line `led := machine.LED`, to `led := machine.GP16`. `GP16` is a constant that refers to GPIO16.

Our program now controls a GPIO. The code should now work but now we need to supply our own LED and resistor. We also have to connect it to the correct GPIO. The pinout reference below shows that `GP16` is the pin located all the way at the bottom on the right side.

![⚠️Pico W Pinout Diagram](/img/005-picow-hello/picow-pinout.svg)

The code for this program is available at the [Github repo](https://github.com/jacques-navarro/picow-hello/tree/00-main 'picow-hello repo'). Now that our program is ready, it's time to make connections.

## Connect LED and resistor

We have to connect the resitor and LED in series to the Pico W. Let's start out by plopping the Pico board on the breadboard. Instead of centering on the channel that goes down the middle of the breadboard, I've set it a bit off to the left side so it's easier to access the rails that connect to GPIO 16 and GPIO 18.

Next, let's connect one end of the resistor to GPIO 16 and the other to one of the power rails on the end of the breadboard. Recall from the pinout diagram above that this pin is located all the way at bottom, on the right side of the board, when the board is plugged into the breadboard and the USB connector is oriented towards the top. The resistor needs to be connected to pin first so it can reduce the current that goes through the LED.

> ### Polarity
>
> A resistor can be connected with either end to the GPIO and still function. There are however other components which must be connected using one specific end. LEDs are an example of a component which needs to be connected in a particular direction to ensure current flows through it.
> Components that need to be place in a spefic direction are said to be polarized. In a best-case scenario, if a polarized component is connected with the wrong end to a circuit, it could prevent the circuit from performing as expected. In a worst-case, it could damage the component.

With the resistor in place we can now connect the LED's longer leg, the Anode, in the same rail as the resistor. The shorter leg, the Cathode, can then be connected to the same row as GPIO 18 which is a ground pin, to complete the circuit. It should like the image below.

![⚠️Connection Diagram](/img/005-picow-hello/picow-hello-fritz.jpg)

> ### LED and current flow
>
> LED stands for **L**ight **E**mitting **D**iode. Light emitting is pretty straight forward. A Diode is a [polarized](#polarity 'Polarity') electronic component that only allows current to flow in one direction. We must make sure to connect it in the correct direction. If we connect the LED in the wrong direction, then it will prevent current from flowing and the it won't light up. How do you tell which direction an LED should be connected? Through hole LEDs, LEDs that are made to connect to a printed circuit board (PCB), have wire two legs. One of the legs is longer than the other. The longer leg, called the Anode.

We should at this point have a Go program written out and the LED and resistor connected to the Pico board. Let's connect the board to the computer so we can transfer the program to the Pico board.

## Connect Pico W board to the computer

The only thing we have to do when we connect the Pico to the computer with the USB cable is to press and hold down the little white button labeled "BOOTSEL". We need to hold the button down until the USB cable is connected to the computer. Doing so puts the Pico board into mass storage device mode, which makes it possible to transfer a program to the board. Once we let go of the button, we should hear a notification from the OS which signals it recognizes a new device has been connected. If we don't hold the button down before plugging the cable to the computer, we won't be able to transer the program to the board.

## Transfer program to Pico

With the Pico connected to the computer we are ready to transfer the program to the board. First, we have to navigate to the same directory where the program is located.

Then we initiate the transfer with the `tinygo flash` [command](https://tinygo.org/docs/reference/microcontrollers/pico/#cli-flashing 'tinygo flash'). We also need to specify the target name and the name of file. I named my file **main.go**. Make sure you type in whatever name you gave your "Hello, World!" program. Technically it possible to flash the program from any location but then you would have to include the full path to the program. I find it easier to run the flash command directly where the file is located.

```
tinygo flash -target=pico main.go
```

After the program is transferred to the Pico it automatically restarts itself and starts running the transferred program. If everything works out well you should see the LED flashing on and off.

## Main Takeaways

The on-board LED on a Pico W is not attached directly to the board. It can only be accessed through the wireless module. We cannot turn the on-board LED on and off because the wireless module is not currently supported by TinyGo's Pico driver.

Instead of using the on-board LED, we can connect an LED to the Pico using a breadboard and a resistor making sure the resistor connects to a GPIO pin first and the LED completes the circuit by connecting to ground on the board.
