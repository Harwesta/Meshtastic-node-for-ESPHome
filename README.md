
# Meshtastic-node-for-ESPHome
Working samples for Lolin32 lite &amp; Lolin ESP32C3 supermini with SX1276 LoRa module
Meshtastic role: Client_Silent

ESPHome code:
> see meshtastic-esp32c3supermini.yaml & meshtastic-lolin32lite.yaml


Wiring diagram for Lolin ESP32C3-supermini:
| __SX1276__ | __ESP32C3-supermini__ |
| :--------- | :-------------------- |
| SCK | GPIO4 |
| MOSI | GPIO6 |
| MISO | GPIO5 |
| NSS | GPIO7 |
| NReset | GPIO3 |
| DIO0 | GPIO10 |
| GND | GND |
| VCC | 3.3V |

Wiring diagram for Lolin32 lite:
| __SX1276__ | __Lolin32 lite__ |
| :--------- | :--------------- |
| SCK | GPIO18 |
| MOSI | GPIO23 |
| MISO | GPIO19 |
| NSS | GPIO5 |
| NReset | GPIO17 |
| DIO0 | GPIO4 |
| GND | GND |
| VCC | 3.3V |
