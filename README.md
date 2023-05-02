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

# Technical Overview

PCB Schematic

![Schematic](https://user-images.githubusercontent.com/94014563/235785103-63d5ec5e-4542-47a8-97de-d0ed59df846a.png)

PCB Design

![Design](https://user-images.githubusercontent.com/94014563/235785362-c81eef54-80e2-4a7e-9631-68b42fa5c4d1.png)

## Code Examples
Here a a few examples of the code. The full source code can be found [here](https://github.com/mshakula/RoostaBoosta).

Main Method:
```C++ Main Method
int
main()
{
  turnOff.fall(&pb_turnoff);
  dispWeather.fall(&pb_dispweather);
  // start thread for bt
  bt_thread.start(bt_api);
  
  // wait for wifi to connect
  while(!wifi.is_connected()){
    ThisThread::sleep_for(1s);
  }

  debug("\r\n[main] Initializing SD Block Device...");
  SDBlockDevice sd(
    rb::pinout::kSD_mosi,
    rb::pinout::kSD_miso,
    rb::pinout::kSD_sck,
    rb::pinout::kSD_cs);
  {
    spi_capabilities_t caps;
    spi_get_capabilities(rb::pinout::kSD_cs, true, &caps);
    debug(" maxumum speed: %d...", caps.maximum_frequency);
    sd.frequency(caps.maximum_frequency);
  }
  debug(" done.");

  debug("\r\n[main] Mounting SD card...");
  FATFileSystem fs(AUX_MOUNT_POINT, &sd);
  debug(" done.");

  debug("\r\n[main] Opening root file directory...");
  if (printdir()) {
    MBED_ERROR(
      MBED_MAKE_ERROR(MBED_MODULE_FILESYSTEM, errno),
      "Could not open root file directory.");
  }
  debug(" done.");

  debug("\r\n[main] Running weather demo...");
  weather_data  data_;
  weather_data* data = &data_;
  updateweather(data);
  while (true) {
    if(alarm_on){
      updateweather(data);
      alarm(data);
    }
    Display_Time(std::chrono::system_clock::from_time_t(time(NULL)));
    ThisThread::sleep_for(1s);
    if(disp_weather){
      updateweather(data);
      Display_Weather(data);
      ThisThread::sleep_for(10s);
      disp_weather=false;
    }
  }
}
```


This code that plays the weather annoucment from the sd card. play_file(char* filename) handles playing each indiviudal files. read_number(int num) interprets a number and plays the realated sounds.
```C++ play audio
void
play_audio(weather_data* data)
{
  // read temperature
  play_file("temperature/itis.pcm");
  read_number(data->temperature);
  play_file("temperature/doutside.pcm");

  // read precipitation chance
  play_file("precipitation/there_is.pcm");
  read_number(data->precipitation_chance);
  play_file("precipitation/percent_chance.pcm");

  // read wind speed
  play_file("wind/wind_speed.pcm");
  read_number(data->wind_speed);
  play_file("wind/mph.pcm");

  // read humidity
  play_file("humidity/humidity.pcm");
  read_number(data->humidity);
  play_file("humidity/percent.pcm");

  // read weather
  // only one allowed, if any condition is satified, return
  // if no sutable phrase, say nothing
  if (strstr(data->weather.c_str(), "thunder")) {
    play_file("weather/weather.pcm");
    play_file("weather/thunderstorms.pcm");
    return;
  }

  if (
    strstr(data->weather.c_str(), "rain") ||
    strstr(data->weather.c_str(), "drizzle")) {
    play_file("weather/weather.pcm");
    play_file("weather/raining.pcm");
    return;
  }

  if (
    strstr(data->weather.c_str(), "snow") ||
    strstr(data->weather.c_str(), "sleet")) {
    play_file("weather/weather.pcm");
    play_file("weather/snowing.pcm");
    return;
  }

  if (strstr(data->weather.c_str(), "partly")) {
    play_file("weather/weather.pcm");
    play_file("weather/partly_cloudy.pcm");
    return;
  }

  if (
    strstr(data->weather.c_str(), "cloud") ||
    strstr(data->weather.c_str(), "over")) {
    play_file("weather/weather.pcm");
    play_file("weather/cloudy.pcm");
    return;
  }

  if (strstr(data->weather.c_str(), "sun")) {
    play_file("weather/weather.pcm");
    play_file("weather/sunny.pcm");
    return;
  }
}
```


This is the structure that contains all the data about the weather. It is populated by the networking fuctions, and used by the LCD and audio playing fuction.
```C++ weather_data
struct weather_data
{
  int         humidity;
  int         precipitation_chance;
  int         temperature;
  int         wind_speed;
  std::string weather;
};
```


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
