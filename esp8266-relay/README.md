# ESP8266 Relay Controller

ESPHome configuration for ESP8266 with relay control via MQTT and WiFi fallback support.

## Features

- **Relay Control**: GPIO4 configured as relay output
- **MQTT Integration**: Full control via MQTT protocol
- **WiFi Fallback**: Automatic Soft AP mode when WiFi is unavailable
- **OTA Updates**: Over-the-air firmware updates
- **Captive Portal**: Easy configuration in AP mode

## Hardware Requirements

- ESP8266 development board (e.g., NodeMCU, Wemos D1 Mini, ESP-01)
- Relay module connected to GPIO4
- Power supply

## Setup

1. **Copy secrets file**:
   ```bash
   cp secrets.yaml.example secrets.yaml
   ```

2. **Edit secrets.yaml** with your credentials:
   - WiFi SSID and password
   - MQTT broker IP, port, username, and password
   - OTA password
   - Fallback AP password

3. **Compile and upload**:
   ```bash
   esphome run esp8266-relay.yaml
   ```

## Configuration Details

### Relay
- **Pin**: GPIO4
- **Default State**: OFF (on boot)
- **MQTT Topic**: `<device_name>/switch/relay/command`
- **MQTT State**: `<device_name>/switch/relay/state`

### WiFi Behavior
- Attempts to connect to configured WiFi network
- If connection fails, starts Soft AP mode
- **AP SSID**: "ESP8266-Relay Fallback"
- **Captive Portal**: Enabled for easy reconfiguration

### MQTT Control
Send commands to control the relay:
- `ON` - Turn relay on
- `OFF` - Turn relay off

## Troubleshooting

- If WiFi doesn't connect, the device will start a hotspot named "ESP8266-Relay Fallback"
- Connect to this hotspot and navigate to 192.168.4.1 for configuration
- Check logs via serial monitor for debugging information
