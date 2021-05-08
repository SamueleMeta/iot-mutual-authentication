# IoT Mutual Authentication

<p align="center">
    <img src="https://i.imgur.com/lgi4uSY.jpg" width="250" alt="Università degli Studi Firenze"/>
</p>

## Overview

The purpose of this bachelor thesis is to implement a mutual authentication paradigm between the [Snap4City cloud platform](https://www.snap4city.org), developed by Università degli Studi di Firenze, and different IoT clients, such as the **ESP32** based Snap4City IoT button or the **ESP8266** based Arduino Uno. This work aims to highlight the requirements to the realization of such a model, its implementation and the considerable benefits that can derive from it. Particular emphasis is placed on the discussion of security, a theme as central as ever in the IoT world. 

<p align="center">
    <img src="https://i.imgur.com/emXStYK.png" width="800" alt="IoT hardware"/>
</p>

Two certificates have been generated to allow mutual authentication:

* The server-side certificate has been generated using a 2048-bit key. In the scenario under consideration, 4096-bit keys would have been superfluous, since they would have entailed a significant load on the CPU and a slowdown in the handshake phase;

* The client-side certificate has been generated using a 1024-bit key. To understand the motivations of such a choice, we must keep in mind that the design has taken place in an embedded context, where memory requirements are particularly stringent. 

<p align="center">
    <img src="https://i.imgur.com/QLCQ2qZ.png" width="400" alt="TLS handshake"/>
</p>

<p align="center">
    <sub>Image from "Bulletproof SSL and TLS" by Ivan Ristić</sub>
</p>

<br>

Once the devices were configured to support mutual authentication with the server, two proofs of concept were developed:

1. The Arduino Uno + ESP8266 setup was extended with a DHT22 sensor, which could provide temperature and pressure values. From these data it was possible to build a server-side real-time dashboard, displaying the atmospheric conditions of the room in which the device was placed. 

2. The IoT button has been programmed to respond to three different types of pressure (short, medium and long) for each key, for a total of 6 combinations. These different signals, recorded by the server, could trigger different actions, mimicking the Amazon Dash Button.

## ESP8266

<p align="center">
    <img src="https://i.imgur.com/mJLpBvk.png" width="650" alt="ESP8266 schema"/>
</p>

* If the user presses the button for a long time, the device will create an **Access Point**. This mode allows the user to configure the device by providing an interface where you can specify certificates and keys, connect to possibly multiple WiFi networks and reset the device to factory settings. During this phase the led will be white.

* If the user presses the button for a short time, the **sensor** will **read temperature and pressure** and the device will send these data to the cloud. During this phase the led will be blue while sending and green/red immediately after, according to the result of the operation.

* Although it is possible to take measurements on demand, the device will autonomously generate new data every time the 20-second **timer** expires.

## ESP32

<p align="center">
    <img src="https://i.imgur.com/MTFqtzr.png" width="650" alt="ESP32 state machine"/>
</p>

* The first time the system is started, it enters the reset state, where it remains until any button press is detected. After a pressure, for four minutes, the **Access Point** will be activated and exposed. As before, during this phase it is possible to provide a complete configuration of the device. After the first start, it is possible to return to this state by pressing both keys together for a long time.

* Any other of the six possible key combinations will result in **sending** the corresponding **message** to the server. In the following three seconds the device will be in a short sleep mode, waiting for the answer of the correct reception from the server through a green blink.

* In any other moment, the device will be in a **deep sleep** state to preserve battery power.

## Resources

This repository consists in:

* ```arduino.ino``` and ```esp8266.ino``` files, where the logic of the Arduino Uno + ESP8266 WiFi shield is implemented

* ```esp32.ino``` file, where the logic of the Snap4City IoT button is implemented

* the pdf of the thesis (in italian), where all the work and relative considerations are discussed in detail

* the pdf of the presentation, which summarizes the main aspects of the project

## References

This thesis is part of a larger project carried out by the DISIT Lab of Università degli Studi di Firenze. It is possible to contextualize my work within the general framework by visiting their [Github profile](https://github.com/disit/snap4city/tree/master/Snap4CityIoTDevices).

