# Filament-Climate-Monitor

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
![ESPHome](https://img.shields.io/badge/ESPHome-Compatible-blue)

**Filament-Climate-Monitor** is a system to monitor temperature and humidity in 3D printer filament storage boxes, ensuring filament stays dry for optimal printing. It uses a DHT22 sensor to send data to Home Assistant every 15 seconds while awake. Built with ESPHome, it features deep sleep for power efficiency and is powered by a 5000mAh LiPo battery with a TP4056 charger module.

## Project Description

High humidity can damage 3D printer filament, causing print failures. This project uses a DHT22 sensor to measure temperature and humidity, sending data to Home Assistant for monitoring or alerts. Running on an ESP32 Dev Module, it wakes every 6 hours for 15 seconds to conserve power, operating on battery or USB.

### Features
* Monitors temperature and humidity with DHT22.
* Sends data to Home Assistant every 15 seconds while awake.
* Easy setup with ESPHome and OTA updates.
* Deep sleep for extended battery life with a 5000mAh LiPo.

### Project Images
(Upload photos of the device and wiring to the images/ folder in this repository and update the links below.)

## Images
![Project Overview](images/box.jpg)
![](images/device.jpg)
![](images/IMG_1444.jpg)
![](images/open_box.jpg)

(Placeholder for images. Replace with actual paths, e.g., /images/project-overview.jpg, after uploading to the repository.)

## Required Materials
* ESP32 Dev Module (~$5-10).
* DHT22 sensor (~$2-5).
* Jumper wires.
* 4.7kΩ–10kΩ pull-up resistor for DHT22.
* Optional:
  * Breadboard for prototyping.
  * 5000mAh 3.7V LiPo battery (~$10-15) and TP4056 charger module (~$1-2).
  * 5V 1A+ USB adapter for charging.

## Prerequisites
* ESPHome: Install ESPHome (2025.2.2 or later) via Home Assistant add-on or standalone (see ESPHome Docs: https://esphome.io/).
* Home Assistant: Set up for integration (ESPHome add-on or dashboard required).
* Secrets File: Create secrets.yaml with WiFi and Home Assistant credentials.

## Installation
1. Set Up Secrets: Create secrets.yaml in your ESPHome config directory with:
   - wifi_ssid: YourWiFiNetwork
   - wifi_password: YourWiFiPassword
   - filament_humidity_api_encryption_key: your-home-assistant-api-key
   - filament_humidity_ota_password: your-ota-password

2. Get the Code: Clone this repository or copy filament-humidity-sensor.yaml to your ESPHome config directory.

3. Upload Firmware: Connect ESP32 via USB and run:
   - esphome compile filament-humidity-sensor.yaml
   - esphome upload filament-humidity-sensor.yaml

4. Check Logs: Monitor the device:
   - esphome logs filament-humidity-sensor.yaml
   - Expected logs (example, 20:16, Sep 1, 2025):
     * [20:16:10][D][wifi]: Connected to WiFi!
     * [20:16:12][D][api]: Connected to Home Assistant!
     * [20:16:15][D][dht:049]: Got Temperature=28.8°C Humidity=71.5% (every ~15s while awake)
     * [20:16:15][D][sensor]: Temperature: 28.8 C sent
     * [20:16:15][D][sensor]: Humidity: 71.5 percent sent

5. Verify Home Assistant: In Home Assistant, go to Settings > Devices & Services > Integrations > ESPHome. Check humidity-sensor-dev device. Verify sensor.dev_module_temperature and sensor.dev_module_humidity in Developer Tools > States (updates every ~15s while awake).

## Wiring Diagram

Connect the components as follows:

* **DHT22 Sensor**:
  * VCC to ESP32 3.3V (or 5V, check datasheet).
  * Data to ESP32 GPIO5.
  * GND to ESP32 GND.
  * Pull-up resistor (4.7kΩ–10kΩ) between Data and VCC.
  * NC pin: Leave unconnected.

* **Power (LiPo Battery with TP4056)**:
  * LiPo Positive (+) to TP4056 B+.
  * LiPo Negative (-) to TP4056 B-.
  * TP4056 OUT+ to ESP32 VIN.
  * TP4056 OUT- to ESP32 GND.
  * TP4056 IN+ to 5V USB power (e.g., 5V 1A+ adapter).
  * TP4056 IN- to USB GND.

**Schematic**:

    DHT22                ESP32 (Dev Module)    TP4056              LiPo Battery
    +----------------+   +----------------+    +----------------+   +----------------+
    | VCC            |-->| 3.3V           |    | OUT+           |-->| VIN            |
    | Data           |-->| GPIO5          |    | OUT-           |-->| GND            |
    | GND            |-->| GND            |    | IN+            |-->| USB 5V+        |
    | NC             |   | (not connected)|    | IN-            |-->| USB GND        |
    +----------------+   +----------------+    | B+             |-->| Battery +      |
    (4.7kΩ–10kΩ resistor between Data and VCC) | B-             |-->| Battery -      |
                                              +----------------+   +----------------+

## Power Consumption and Battery Life
* Power Consumption:
  * ESP32 (WiFi on, 15s awake): ~120 mA.
  * DHT22 (5s interval while awake): ~1.2 mA.
  * Total: ~121.2 mA during awake period.
* Battery Life (5000mAh LiPo):
  * With deep sleep (15s awake every 6 hours), ~1-2 years (average ~0.32 mA).
  * Charging with TP4056 (1A): ~6-7 hours for full charge.
* Tips:
  * Adjust deep sleep duration in filament-humidity-sensor.yaml for different intervals.
  * Use a 5V 1A+ USB adapter for continuous power.

## Usage
* Filament Storage: Place in an airtight filament box with silica gel to keep humidity low.
* Home Assistant Automation:
  * Alert for high humidity:
    - automation:
      - alias: Filament Humidity Alert
      - trigger:
        - platform: numeric_state
        - entity_id: sensor.dev_module_humidity
        - above: 30
      - action:
        - service: notify.mobile_app
        - data:
          - message: Filament box humidity too high: {{ states('sensor.dev_module_humidity') }}%!
  * Log data for trends.
* Monitoring: Check Home Assistant for readings (updated every ~15s while awake).

## Contributing
* Submit pull requests for features (e.g., adjust deep sleep, add relay for dehumidifier).
* Report bugs via GitHub Issues.
* Suggest power or feature improvements.

## License
This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments
* Built with ESPHome (https://esphome.io/) for Home Assistant integration.
* Inspired by DIY IoT projects for filament storage.
