# ESP8266 Tiny Computer

An IoT project that turns a **NodeMCU (ESP8266)** into a self-contained mini data logger and environmental monitor. It combines Wi-Fi connectivity, a real-time clock, a DHT11 sensor, an OLED display, SD card storage, an MQTT cloud dashboard, and a built-in web server — all running on a single microcontroller.

Built with the [ESP8266 Arduino platform](https://github.com/esp8266/Arduino).

---

## Features

- **Wi-Fi management** — connects to a saved network on boot; falls back to a captive-portal access point with a graphical network scanner if no network is found
- **NTP time sync** — updates the onboard RTC from an NTP server after connecting to the internet
- **DHT11 sensor logging** — reads temperature and humidity on a scheduled interval
- **Cloud dashboard** — publishes sensor data to [Adafruit IO](https://io.adafruit.com/) via MQTT
- **Local SD logging** — saves timestamped readings to a `.csv` file on an SD card
- **OLED display** — shows live status and sensor values at each boot step
- **Built-in web server** — serves a local interface over the network
- **Per-module debug flags** — granular logging control for each subsystem

## Hardware

| Component | Role |
|-----------|------|
| NodeMCU (ESP8266) | Main microcontroller |
| DHT11 | Temperature and humidity sensor |
| OLED display | Status and data display (I2C) |
| SD card module | Local CSV data logging (SPI) |
| RTC module | Real-time clock for timestamping |

## Boot Sequence

```
Power on
  └─ Initialize I2C, SPI, Web server, MQTT
       └─ Connect to saved Wi-Fi network
            ├─ [Success] Sync RTC via NTP → Connect to Adafruit IO MQTT
            └─ [Failure] Start access point → Open network scanner UI

Loop (on schedule)
  └─ Read DHT11 → Publish to Adafruit IO → Save to SD card → Update OLED
```

## Getting Started

### Prerequisites

- [Arduino IDE](https://www.arduino.cc/en/software) with ESP8266 board support enabled
- An [Adafruit IO](https://io.adafruit.com/) account (free) for the cloud dashboard

### Installation

1. Clone this repository:

```bash
git clone https://github.com/l-etx/tiny-computer.git
```

2. Open Arduino IDE and install all required libraries listed in `resources/requirements/requirements.txt` via **Sketch → Include Library → Manage Libraries**.

3. Enable ESP8266 board support under **File → Preferences → Additional Boards Manager URLs**:

```
https://arduino.esp8266.com/stable/package_esp8266com_index.json
```

4. Open `main/main.ino`, set your Adafruit IO credentials in the config, select your NodeMCU board, and upload.

## Debug Flags

Enable or disable per-module serial logging by defining any of these flags at the top of the sketch:

| Flag | Module |
|------|--------|
| `DEBUG_ESP` | ESP8266 core |
| `DEBUG_OLED` | OLED display |
| `DEBUG_MQTT` | MQTT / Adafruit IO |
| `DEBUG_RTC` | Real-time clock |
| `DEBUG_SD` | SD card |
| `DEBUG_DHT` | DHT11 sensor |
| `DEBUG_NTP` | NTP time sync |
| `DEBUG_WEBSERVER` | Web server |
| `DEBUG_IP` | Network / IP |

## Project Structure

```
tiny-computer/
├── main/               # Arduino sketch (.ino) and source files
├── resources/
│   └── requirements/
│       └── requirements.txt   # Arduino library dependencies
└── README.md
```

## References

- [WiFiManager](https://github.com/tzapu/WiFiManager) — captive portal Wi-Fi configuration
- [OLED image interfacing guide](https://create.arduino.cc/projecthub/najad/interfacing-and-displaying-images-on-oled-59344a)
- [NodeMCU data logger guide](https://thekurks.net/blog/2018/3/20/nodemcu-data-logger)
- [ESP8266 Arduino core](https://github.com/esp8266/Arduino)
