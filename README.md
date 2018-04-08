Single Channel LoRaWAN Gateway (Up and Downlink)
==============================
This repository contains a proof-of-concept implementation of a dual
channel LoRaWAN gateway.

It has been tested on the Raspberry Pi platform, using a Semtech SX1276/78 (2x HopeRF RFM95W uputronics hat).
(other configurations and the SX1272 not tested, but should be able to make working)

The code is for testing and development purposes only, and is not meant
for production usage.

Part of the source has been copied from the Semtech Packet Forwarder
(with permission).

Was forked from @bokse001 https://github.com/bokse001/dual_chan_pkt_fwd to create a POC of implementing a single channel up and down to Dragino Raspberry LoRa Hat v1.4.

It contains code from both the ESP-1ch-Gateway-v3.0 by Maarten Westenberg https://github.com/things4u/ESP-1ch-Gateway-v3.0
and TheThingsNetwork/packet_forwarder https://github.com/TheThingsNetwork/packet_forwarder

[WARNING] This is bleeding edge development so do not expect clean coding, but it has been working for a while now. Lots of things
still to do.

Raspberry PI pin mapping is as follow and pin number in file `global_conf.json` are WiringPi pin number (wPi colunm)

```
root@xxxx # gpio readall
 +-----+-----+---------+------+---+---Pi 3---+---+------+---------+-----+-----+
 | BCM | wPi |   Name  | Mode | V | Physical | V | Mode | Name    | wPi | BCM |
 +-----+-----+---------+------+---+----++----+---+------+---------+-----+-----+
 |     |     |    3.3v |      |   |  1 || 2  |   |      | 5v      |     |     |
 |   2 |   8 |   SDA.1 |   IN | 1 |  3 || 4  |   |      | 5V      |     |     |
 |   3 |   9 |   SCL.1 |   IN | 1 |  5 || 6  |   |      | 0v      |     |     |
 |   4 |   7 | GPIO. 7 |   IN | 1 |  7 || 8  | 1 | ALT5 | TxD     | 15  | 14  |
 |     |     |      0v |      |   |  9 || 10 | 1 | ALT5 | RxD     | 16  | 15  |
 |  17 |   0 | GPIO. 0 |  OUT | 0 | 11 || 12 | 0 | IN   | GPIO. 1 | 1   | 18  |
 |  27 |   2 | GPIO. 2 |   IN | 0 | 13 || 14 |   |      | 0v      |     |     |
 |  22 |   3 | GPIO. 3 |   IN | 0 | 15 || 16 | 0 | IN   | GPIO. 4 | 4   | 23  |
 |     |     |    3.3v |      |   | 17 || 18 | 1 | IN   | GPIO. 5 | 5   | 24  |
 |  10 |  12 |    MOSI | ALT0 | 0 | 19 || 20 |   |      | 0v      |     |     |
 |   9 |  13 |    MISO | ALT0 | 0 | 21 || 22 | 0 | IN   | GPIO. 6 | 6   | 25  |
 |  11 |  14 |    SCLK | ALT0 | 0 | 23 || 24 | 1 | OUT  | CE0     | 10  | 8   |
 |     |     |      0v |      |   | 25 || 26 | 1 | OUT  | CE1     | 11  | 7   |
 |   0 |  30 |   SDA.0 |   IN | 1 | 27 || 28 | 1 | IN   | SCL.0   | 31  | 1   |
 |   5 |  21 | GPIO.21 |  OUT | 0 | 29 || 30 |   |      | 0v      |     |     |
 |   6 |  22 | GPIO.22 |  OUT | 0 | 31 || 32 | 1 | IN   | GPIO.26 | 26  | 12  |
 |  13 |  23 | GPIO.23 |  OUT | 0 | 33 || 34 |   |      | 0v      |     |     |
 |  19 |  24 | GPIO.24 |   IN | 0 | 35 || 36 | 0 | IN   | GPIO.27 | 27  | 16  |
 |  26 |  25 | GPIO.25 |   IN | 0 | 37 || 38 | 0 | IN   | GPIO.28 | 28  | 20  |
 |     |     |      0v |      |   | 39 || 40 | 0 | OUT  | GPIO.29 | 29  | 21  |
 +-----+-----+---------+------+---+----++----+---+------+---------+-----+-----+
 | BCM | wPi |   Name  | Mode | V | Physical | V | Mode | Name    | wPi | BCM |
 +-----+-----+---------+------+---+---Pi 3---+---+------+---------+-----+-----+

```

* For [Dragino RPI Lora][2] HAT    
pins configuration in `global_conf.json`
```
  "pin_nss": 6,
  "pin_dio0": 7,
  "pin_rst": 0
```
Installation
------------

Install dependencies as indicated in original README.md below then

```shell
cd /home/pi
git clone -b single_chan_pkt_fwd https://github.com/ethermine/dual_chan_pkt_fwd single_chan_pkt_fwd
cd single_chan_pkt_fwd
make
sudo make install
````

To start service, as root or sudo (should already be started at boot if you done make install and rebooted of course), stop service or look service status
```shell
systemctl start single_chan_pkt_fwd
systemctl stop single_chan_pkt_fwd
systemctl status single_chan_pkt_fwd
````

To see gateway log in real time
```shell
sudo journalctl -f -u single_chan_pkt_fwd
````

Configuration
-------------

Defaults:

- LoRa:   SF7 at 868.1 Mhz and 868.3 Mhz
- Server: loraserver.local, port 1700  (loraserver.io Local Server)

Please configure the global_conf.json file with your settigns like network interface, location, email and description.

**Original README.md below**

Features
--------

listen on configurable frequency and spreading factor
SF7 to SF12
status updates
can forward to two servers
Not (yet) supported:

PACKET_PUSH_ACK processing
SF7BW250 modulation
FSK modulation
downstream messages (tx)

Dependencies
------------

-	SPI needs to be enabled in the Raspberry Pi configuration menu (to start up use CLI command: raspi-config) 
-	Install WiringPi  (GPIO access library – refer to: http://wiringpi.com/ ): sudo apt-get install wiringpi 
-	Run packet forwarder as root (sudo)


Connections
-----------

SX127x	Raspberry PI
3.3V	 3.3V (header pin #1)
GND	GND (pin #6)
MISO	MISO (pin #21)
MOSI	MOSI (pin #19) 
SCK	CLK (pin #23)
NSS	GPIO6 (pin #22)
DIO0	GPIO7 (pin #7)
RST	GPIO0 (pin #11)
Configuration

Defaults:
---------

LoRa: SF7 at 868.1 Mhz
Server: 54.229.214.112, port 1700 (The Things Network: croft.thethings.girovito.nl)
Edit source node (main.cpp) to change configuration (look for: "Configure these values!").

Please set location, email and description.

License
-------

The source files in this repository are made available under the Eclipse Public License v1.0, except:

base64 implementation, that has been copied from the Semtech Packet Forwarder;
RapidJSON, licensed under the MIT License.
parson, copied from http://kgabis.github.com/parson/ 
