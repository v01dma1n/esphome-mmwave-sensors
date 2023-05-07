# ESPHome / Home Assistant integrations for mmWave sensors

Collection of header files and esphome yaml configs to integrate some Seeed Studio mmWave sensors into Home Assistant.

Currently the following sensors are partially implemented

* Seeed Studio
  * `MR24D` - 24GHz Human Stationary Sensor
  * `R24D` - 24GHz Human Static Presence Lite
  * `R60A` - 60GHz Respiratory Heartbeat Sensor

## Installation

### Which header files for what sensor?

If you don't bother or use all the supported sensors just copy all files from the header directory.

* `MR24D`
  * Headers
    * header/mr24d.h
    * header/mrx_definitions.h
    * header/mrx_frame.h
  * ESPHome Config
    * esphome/mr24d.yaml
* `R24D`
  * Headers
    * r24d.h
    * rx_definitions.h
    * rx_frame.h
  * ESPHome Config
    * esphome/r24d.yaml
* `R60A`
  * Headers
    * r60a.h
    * rx_definitions.h
    * rx_frame.h
  * ESPHome Config
    * esphome/r60a.yaml

The `R24D` and `R60A` sensor use the same frame definition and thus share the header files `rx_definitions.h` and `rx_frame.h`.

### Hardware

#### MCU 

The template assumes the use of a `ESP32`. Specifically the `AZDelivery ESP32 Devkit C`. But any other `ESP32` and `ESP8266` should work I guess. But I have not tested this yet.

#### Wiring

The template assumes the following wiring:

`GPIO16` of the MCU configured as `RX` connected to the `TX Pin` of the sensor
`GPIO17` of the MCU configured as `TX` connected to the `RX Pin` of the sensor

This can be changed in the `uart` section of the ESPHome config.

### Steps

#### Header Files

1. Locate the `esphome` directory in your home assistant `config` directory.
2. Inside the `esphome` directory create a new directory called `header`
3. Copy the approapriate header files from this repository to the new `header` directory.

#### ESPHome

1. Grab the ESPHome template for your sensor from the `esphome` directory.
2. Change the `substitutions` for `device_id` and `device_name` at the beginning of the template to your liking.
   * `device_id` is the internal name.
   * `device_name` is the public name presented to home assistant. 
3. Create a new device in ESPHome, with the `device_id` from the `substitution` as its name.
4. This will create a new default configuration for that device. You now have two configs, one from this repository and one generated from ESPHome.
5. The sections for the specific board (e.g. `esp32`), `api`, `ota` and `wifi` should be taken from the one generated by ESPHome.
6. The other sections `substitutions`, `esphome`, `logger` and everything after `wifi` are taken from this repository.
7. Save the configuration and apply it to your device.

#### Options

##### Webserver

The device will expose a web interface showing the sensor states, logs and ota upload. You can remove this by deleting the `web_server` and `http_request` sections from the ESPHome config.

##### Header Directory Name

You can change the name of the `header` directory in home assistant if it doesn't apply to your naming scheme. If you do so, you need to adjust the paths inside the `esphome` section of the ESPHome config accordingly.

## Supported Functions

|                     | `MR24D` | `R24D` | `R60A` | Info                                                                      |
|---------------------|-------|------|------|---------------------------------------------------------------------------|
| presence            | x     | x    | x    | binary (detected/clear)                                                   |
| motion              | x     | x    | x    | binary (detected/clear)                                                   |
| body movement       | x     | x    | x    | measurement  (0: unoccupied,  1: static presence,  2-100: activity level) |
| proximity           | x     | x    |      | measurement (<0: approaching,  0 none or stationary,  >0 moving away)     |
| distance            |       |      | x    | measurement (distance to object in cm)                                    |
| x, y, z angle       |       |      | x    | measurements  (relative position of object to sensor)                     |
| heartrate           |       |      | x    | measurement                                                               |
| breathing rate      |       |      | x    | measurement                                                               |
| breathing info      |       |      | x    | measurement  (1: normal,  2: fast,  3: slow, 4: none)                     |
| radar out of bounds |       |      | x    | measurement (0: out of range, 1: within range)                            |
