# Introduction

The Arduino IBM LoRaWAN C-library ([LMIC](https://github.com/mcci-catena/arduino-lmic)) is a portable implementation of the LoRaWAN™ 1.0.3 end-device specification in the C programming language. (“LMIC” stands for “LoRaWAN MAC in C”). It supports the EU-868, US-915, AU-915, AS-923, KR-920 and IN-866 variants of the specification and it can handle class A and class B devices. The library takes care of all logical MAC states and timing constraints and drives the SEMTECH SX1272 or SX1276 radio. This way, applications are free to perform other tasks and the protocol compliance is guaranteed by the library. In order to ensure compliance with the specification and associated regulations, the state engine has been tested and verified using a logic simulation environment. The library has been carefully engineered to precisely satisfy the timing constraints of the MAC protocol and to even consider possible clock drifts in the timing computations. Applications can access and configure all functionality via a simple event-based programming model and do not have to deal with platform-specific details like interrupt handlers. By using a thin hardware abstraction layer (HAL), the library can be easily ported to new hardware platforms. An Arduino HAL is provided which allows for easy integration with most Arduino variants. Eight-bit AVR platforms are supported as well as 32-bit platforms.

In addition to the provided LMIC library, a real-world application also needs drivers for the sensors or other hardware it desires to control. These application drivers are outside the scope of this document and are not part of this library.

<figure markdown>
  ![Application device components](/technical-specification/images/fig-1-application-device-components.png){ loading=lazy }
  <figcaption>High-level view of all application device components.</figcaption>
</figure>

## LoRaWAN Versions and Features Supported

The LMIC library supports Class A operation as described by the LoRaWAN specification V1.0.3 and V1.0.2. It does not support V1.1.

Class B support code is provided, but is not tested.

LoRaWAN 1.0.3 Class A multicast downlinks are not supported.

Class C operation is not supported.

The library has been tested for compliance with the following LoRaWAN 1.0.2 test specifications as implemented by RedwoodComm in their RWC5020A tester, using firmware version 1.170. The MCCI Catena 4610 was used as the reference device.

- EU868 V1.5 (excluding optional data rates)

- US915 V1.3 (excluding optional data rates; testing was done using channels 0~7 and 64). The TxPower test fails because the LMIC complies with LoRaWAN V1.0.3. The TxPower test uses a value that is defined for V1.0.3, but not for V1.0.2, and expects the device to reject the value.

- AS923 V1.1 (excluding optional data rates)

- KR920 V1.2

- IN865 V1.0

All tests were performed using a tethered connection.

## Class A and Class B Support

The Arduino LMIC library can be configured to support LoRaWAN Class A and Class B operation. A Class A device receives only at fixed times after transmitting a message. This allows for low power operation, but means that downlink latency is controlled by how often the device transmits. A Class B device synchronizes to beacons transmitted by the network, and listens for messages at certain intervals (“ping slots”) during the interval between beacons.

Devices (and the LMIC library) start out as Class A devices, and switch to Class B based on requests from the application layer of the device.

This document uses the term “pinging” to mean that the LMIC is operating in Class B and also listening for downlink during ping slots. If a device is pinging, then the LMIC must also be tracking the beacon. It is possible to track the beacon (perhaps for time synchronization purposes) without enabling pinging.

Since many devices and networks only support Class A operation, the library can be configured at compile time to omit support for tracking and pinging. It is possible to omit support for pinging without omitting support for tracking, but this is not a tested configuration.
