![MbedOS Header Image](https://raw.githubusercontent.com/ARMmbed/mbed-os/master/logo.png)

# RoostaBoosta

Who doesn't need a good morning boost?
Return to your rich idyllic pastoral roots with RoostaBoosta™, the innovative, awesome, groundbreaking, fascinating, mind-boggling, stupendous IoT weather-predicting alarm clock!

Final project for Georgia Tech ECE4180.
Based on the newer [MbedOS 6](https://os.mbed.com/docs/mbed-os/v6.16/introduction/index.html) platform, this project incorporates networking, API calls, and audio-visual effects for an web-enabled alarm clock / weather prediction station.

Designed By:
Jake Curry,
Raymond Barrett,
Matthew Shakula, and
Azad Al Kiki

# Usage Overview
RoostaBoosta™ fucntions as an alarm clock that displays the weather to the user on the alarm. During setup, the user connects to the bluetooth module of the device, and sends the connect command. The module then responds with a list of all nearby access points, and promts the user to respond with the SSID of the perfered network. The user then gives the password, and then the device connects to the network. Then, once the user inputs the location, the device displays the current time on the LCD, and will show the local weather on a push button input. The user can then input in a 4 digit alarm time in 24 hr time (hhmm) via the bluetooth connection. When the alarm triggers, the device will get the local weather, then play a rooster sound to wake the user. The user can then either wave their hand in front of the sonar device to snooze the alarm, or push a button to permanently turn off the alarm. Once the alarm is off, the LCD displays the weather infomation, and the speaker reads it to the user. The device then returns to displaying the time until the user sets antoher alarm.

## Usage Video

[![Watch the video](https://img.youtube.com/vi/PKmjmyOEXVI/maxresdefault.jpg)](https://youtu.be/PKmjmyOEXVI)

## Hardware

The hardware used in this project is based on the hardware provided by the Georgia Tech ECE department.

Primary components used are:

- [mBed LPC1768](https://os.mbed.com/platforms/mbed-LPC1768/)
- [4D Systems uLCD-144-G2](https://www.sparkfun.com/products/11377)
- [HC-SRO4 Ultrasonic Sensor](https://www.sparkfun.com/products/15569)
- [Adafruit ESP8266 Breakout](https://www.adafruit.com/product/2471)
- [SparkFun microSD Breakout](https://www.sparkfun.com/products/544)
- [SparkFun RJ45 Ethernet Breakout](https://www.sparkfun.com/products/13021)
- [SparkFun TPA2005D1 Amp](https://www.sparkfun.com/products/11044)

Schematics and a breakout PCB are designed in [KiCAD](https://www.kicad.org/), which is free and open-source.
