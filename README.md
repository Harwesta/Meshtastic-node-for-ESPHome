
# Meshtastic-node-for-ESPHome
Working samples for Lolin32 lite &amp; Lolin ESP32C3 supermini with SX1276 LoRa module

Wiring diagram:

ESPHome code:

esphome:
  name: meshtastic1
  friendly_name: meshtastic1

esp32:
  variant: ESP32C3
  board: esp32-c3-devkitm-1
  framework:
    type: esp-idf

external_components:
  - source: github://Andrik45719/esphome-meshtastic@main
    components: [meshtastic]
    refresh: 24h

logger:

api:
  encryption:
    key: "pWAk7PYOa35tCMeTbUvK0Wod43/Xl46VTrL87ugXMms="

ota:
  - platform: esphome
    password: !secret ota_password

wifi:
  output_power: 9dB
  min_auth_mode: WPA2
  ssid: !secret wifi_ssid
  password: !secret wifi_password
  ap:
    ssid: meshtastic
    password: !secret wifi_password

captive_portal:

web_server:

mqtt:
  broker: 192.168.2.2
  username: hassio
  password: !secret mqtt_password

# Configure SPI for LoRa radio
spi:
  clk_pin: GPIO4
  mosi_pin: GPIO6
  miso_pin: GPIO5

# Configure LoRa radio (SX1276 example)
sx127x:
  id: lora_radio
  cs_pin: GPIO7
  rst_pin: GPIO3
  dio0_pin: GPIO10
  #dio1_pin: GPIO0
  frequency: 868.825MHz
  spreading_factor: 9
  bandwidth: 250_0kHz
  coding_rate: CR_4_5
  modulation: LORA
  preamble_size: 16
  sync_value: 0x2b
  rx_start: true
  crc_enable: true
  pa_power: 17
  pa_pin: BOOST

# Configure Meshtastic component
meshtastic:
  - id: mesh_node
    lora: lora_radio
    hw_model: 39  # DIY_V1
    hop_limit: 7
    ok_to_mqtt: true
    # Configure channels
    channels:
      - name: MediumFast
        psk: "AQ=="
      - name: k11
        psk: !secret k11_public_key

    # Configure this node
    nodes:
      - node_number: 1002297332
        name: "k11 Node"
        short_name: "k11"
        private_key: !secret k11_private_key
        public_key: !secret k11_public_key
      - node_number: 181026172
        name: "+And"
        public_key: !secret and_public_key
    position:
      latitude:  56.8663
      longitude:  60.6091
      altitude: 325
      broadcast_interval: 3h
# Handle received packets
#    on_packet:
#      then:
#        - lambda: |-
#            ESP_LOGD("mesh", "Packet from %08X to %08X, port %d", from, to, portnum);

    on_packet:
      then:
        - lambda: |-
            ESP_LOGD("id_mesh", "Packet from %08X to %08X port %d %s", from, to, portnum, format_hex(data).c_str());
            if (portnum == meshtastic_PortNum_TEXT_MESSAGE_APP) {
            }

# Send text messages via button press
button:
  - platform: template
    id: door
    name: "Дверь открыта"
    on_press:
      then:
        - meshtastic.send_text_message:
            id: mesh_node
            channel: k11
#            to: 181026172  # Node ID
            text: "Дверь открыта"
  - platform: template
    id: kitchen
    name: "Протечка в кухне"
    on_press:
      then:
        - meshtastic.send_text_message:
            id: mesh_node
            channel: k11
#            to: 181026172  # Node ID
            text: "Протечка в кухне"
  - platform: template
    id: su
    name: "Протечка в с/у"
    on_press:
      then:
        - meshtastic.send_text_message:
            id: mesh_node
            channel: k11
#            to: 181026172  # Node ID
            text: "Протечка в с/у"
  - platform: template
    id: bedroom
    name: "Протечка в спальне"
    on_press:
      then:
        - meshtastic.send_text_message:
            id: mesh_node
            channel: k11
#            to: 181026172  # Node ID
            text: "Протечка в спальне"
  - platform: template
    id: guestroom
    name: "Протечка в гостиной"
    on_press:
      then:
        - meshtastic.send_text_message:
            id: mesh_node
            channel: k11
#            to: 181026172  # Node ID
            text: "Протечка в гостиной"
  - platform: template
    id: poweroff
    name: "Нет электричества"
    on_press:
      then:
        - meshtastic.send_text_message:
            id: mesh_node
            channel: k11
#            to: 181026172  # Node ID
            text: "Отключение ЭЭ на k11"
  - platform: template
    id: poweron
    name: "Включение ЭЭ"
    on_press:
      then:
        - meshtastic.send_text_message:
            id: mesh_node
            channel: k11
#            to: 181026172  # Node ID
            text: "Включение ЭЭ на k11"            
  - platform: restart
    name: "Restart"

binary_sensor:
  - platform: status
    name: "Status"

sensor:
  - platform: wifi_signal
    name: "RSSI level"
    update_interval: 180s
