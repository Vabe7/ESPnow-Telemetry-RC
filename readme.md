# ESPnow Telemetry RC #

This repository presents a cheap solution to achieve remote control and Mavlink telemetry at the same time with a JR module for less than 20 bucks.

The code is mostly based on the MavlinkToPassthru repositroy from zs6buj which you can find here https://github.com/zs6buj/MavlinkToPassthru and the ESP-Now-Serial-Bridge from yuri-rage https://github.com/yuri-rage/ESP-Now-Serial-Bridge. 

Most of the work has been done by the two of them, I just combined the two and added a PPM input to RC override.

## Disclaimer ##
This is **NOT** a commercial and reliable software, just something I fiddeld around with! There have been no range checks, no loss of connections tests and so on. I just can say that it works on my machine with my setup in a range of about 10m. I think it will work fine with further distances but I don't know. In theory there is an RSSI implementation but from my experience it seems random most of the time. Also my implementation of the PPM input is prone to cause latency. 

I am flattered if you are interested in the program and want to try it out, but I won't take any responsibility for flyaway or crashed vehicles!

## Required Hardware
For the receiver on the drone/plane/rover you need an ESP with the telemtry bridge software from yuri-rage.

Whilst an ESP8266 could work, I strongly recommend an ESP32 for the transmitter side since it features more processing power. I personally developed the software on an ESP32 Devkit, but for the final version I changed to a Devkit with an ESP32-WROOM-32U module to connect an external antenna. A little hint at this point: I had trouble receiving the telemetry on my remote control because not every pin labeled ground was really connected to ground on my board. So in case everything except the downlink to the remote control works, try another ground pin.

## Setup ##
Most of the setup options are equal to MavlinkToPassthru so the best place to start is probably here: https://github.com/zs6buj/MavlinkToPassthru/wiki

**Important information**: It is not possible to use ESPnow and Wifi station mode at the same time! Only as access point. 
<details>
    <summary>Why?</summary>
    <p>ESPnow requires that both devices send on the same Wifi-Channel. If we connect to an existing Wifi the channel would be given by this wifi and we couldn't connect to the receiver via ESPnow (ignoring the edge case when the existing network and the ESPnow communication is on the same channel by chance).<p>
</details>
I have taken precaution to counter a wrong setup like this but I suggest you don't try it.

The ESPnow setup is done via [config.h](include/config.h). 

**RECVR_MAC** is the MAC address of the board on the drone/plane/rover

**WIFI_CHAN** is the Wifi-Channel to use

**Heartbeat_Timeout** is the time in milliseconds after which the connection is considered lost

**PPM_Input** is to enable the PPM input and RC_Override

**PPM_Pin** is the pin on which the PPM signal is transmitted to the ESP

**PPM_Channels** is the number of transmitted channels, this should be the same as in the transmitter

The setup I have tested and can confirm working is an ESP32-WROOM-32U whith telemetry output on pin 4 to my Jumper T16 running Yaapu telemetry, PPM input on pin 18 and a telemetry output via UDP. This is also the setup for the configuration that is currently set up.