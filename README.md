[![Join us on Slack chat room](https://img.shields.io/badge/Slack-Join%20the%20chat%20room-orange)](https://join.slack.com/t/panasonic-wemos/shared_invite/enQtODg2MDY0NjE1OTI3LTgzYjkwMzIwNTAwZTMyYzgwNDQ1Y2QxYjkwODg3NjMyN2MyM2ViMDM3Yjc3OGE3MGRiY2FkYzI4MzZiZDVkNGE)


# Panasonic H Series Aquarea air-water heat pump protocol

Eine deutschsprachige [README_DE.md](README_DE.md) findest du hier. \
Een nederlandse vertaling [README_NL.md](README_NL.md) vind je hier. \
Suomen kielellä [README_FI.md](README_FI.md) luettavissa täällä.

*Help on translation to other languages is welcome.*

# Current releases
Current beta release is version 0.6b. The [compiled binary](binaries/HeishaMon.ino.d1-v0.6.bin) can be installed on a Wemos D1 mini(and generally on any ESP8266 based board - but without guarantee ). Or download the code compile it yourself (see required libraries below).

# Using the software
The current arduino beta image is able to communicate with the Panasonic Aquarea H-series (and most probably with the new J-series as well, since PCB looks identical). \
If you want to compile this image yourself be sure to use the mentioned libraries and support for a filesystem on the esp8266 so select the correct flash option in arduino ide for that.

When starting for the first time an open-wifi-hotspot will be visible allowing you to configure your wifi network and your mqtt server. Configuration page will be located at http://192.168.4.1 . \
If you ever want to factory reset, just double reset the esp8266 within 0.1 second. It will then format the filesystem and remove the wifi setting and start the wifi hotspot again. \
After configuring and booting the image will be able to read and talk to your heatpump. The GPIO13/GPIO15 connection will be used for communications so you can keep your computer/uploader connected to the board if you want. \
Serial 1 (GPIO2) can be used to connect another serial line (GND and TX from the board only) to read some debugging data.

All received data will be sent to different mqtt topics (see below for topic descriptions). There is also a 'panasonic_heat_pump/log' mqtt topic which provides debug logging and a hexdump of the received packets (if enabled in the web portal).

You can connect a 1wire network on GPIO4 which will report in seperate mqtt topics (panasonic_heat_pump/1wire/sensorid).

The software is also able to measure Watt on a S0 port of two kWh meters. You only need to connect GPIO12 and GND to the S0 of one kWh meter and if you need a second kWh meter use GPIO14 and GND. It will report on mqtt topic panasonic_heat_pump/s0/1 and panasonic_heat_pump/s0/2 and also in the JSON output.

Updating the firmware is as easy as going to the firmware menu and, after authentication with username 'admin' and password you provided during setup, uploading the binary there.

A json output of all received data (heatpump and 1wire) is available at the url http://heishamon.local/json (replace heishamon.local with the ip address of your heishamon device if MDNS is not working for you).

Within the 'integrations' folder you can find examples how to connect your automation platform to the HeishaMon.

# Further information
Below you can find some technical details about the project. How to build your own cables. How to build your own PCB etc.

## Connection details:
Communication can be established thru one of the two sockets: CN-CNT or CN-NMODE, which are hardwired/shortcut, so there is no possibility to use them both at the same time for more then one device (except sniffing). \
Communication parameters: TTL 5V UART 9600,8,E,1  \
 \
CN-CNT Pin-out (from top to bottom) \
1 - +5V (250mA)  \
2 - 0-5V TX  \
3 - 0-5V RX  \
4 - +12V (250mA) \
5 - GND \
 \
CN-NMODE Pin-out (from left to right) \
"Warning! As printed on the PCB, the left pin is pin 4 and right pin is pin 1. Do not count 1 to 4 from left!  \
4 - +5V (250mA)  \
3 - 0-5V TX  \
2 - 0-5V RX  \
1 - GND



## Where to get connectors
[RS-Online orders](Connectors_RSO.md)

[Conrad orders](Connectors_Conrad.md)

Use some 24 AWG shielded 4-conductors cable.


## How to connect
The PCB's needed to connect to the heatpump are designed by project members and are listed below. \
[PCD Designs from the project members](PCB_Designs.md) \
[Picture Wemos D1 beta](WEMOSD1.JPG) \
[Picture ESP12-F](NewHeishamon.JPG)

You can order completed PCB from some project members also. \
[Tindie shop](https://www.tindie.com/stores/thehognl/) from Igor Ybema (aka TheHogNL) based in the Netherlands \
[Trab.dk shop](https://www.trab.dk/en/search?controller=search&orderby=position&orderway=desc&search_query=panasonic&submit_search=)  from Morten Trab based in Denmark




## Building the test arduino image
boards: \
esp8266 by esp8266 community version 2.6.3 [Arduino](https://github.com/esp8266/Arduino/releases/tag/2.6.3)

[libs we use](LIBSUSED.md)


## MQTT topics
[Current list of documented MQTT topics can be found here](MQTT-Topics.md)

## DS18b20 1-wire support
The software also supports ds18b20 1-wire temperature sensors reading. A proper 1-wire configuration (with 4.7kohm pull-up resistor) connected to GPIO4 will be read each 30 secs and send at the panasonic_heat_pump/1wire/"sensor-hex-address" topic.


## Protocol info packet:
To get information from heat pump, "magic" packet should be send to CN-CNT:

`71 6c 01 10 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 12`


## Protocol byte decrypt info:
[Current list of documented bytes decrypted can be found here](ProtocolByteDecrypt.md)


## Integration Examples for Opensource automation systems
[Openhab2](Integrations/Openhab2)

[Home Assistant](https://github.com/Egyras/HeishaMon/tree/master/Integrations/Home%20Assistant)

[IOBroker Manual](Integrations/ioBroker_manual)

[Domoticz](Integrations/Domoticz)

