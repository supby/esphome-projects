# SCD4x CO2 Sensor

ESPHome configuration for a Wemos/LOLIN D1 Mini (ESP8266) with a Sensirion SCD4x
CO2 sensor (SCD40/SCD41), reporting over MQTT with WiFi fallback support.

## Features

- **CO2 Monitoring**: Measures CO2 (ppm), temperature, and humidity
- **MQTT Integration**: Publishes readings with Home Assistant discovery
- **WiFi Fallback**: Automatic Soft AP mode when WiFi is unavailable
- **OTA Updates**: Over-the-air firmware updates
- **Captive Portal**: Easy configuration in AP mode

## Hardware Requirements

- LOLIN (Wemos) D1 Mini ESP8266 board
- Sensirion SCD4x sensor (SCD40 or SCD41), I2C interface
- Power supply

### Wiring (I2C)

| SCD4x pin | D1 Mini pin |
|-----------|-------------|
| VCC (3.3V)| 3V3         |
| GND       | GND         |
| SDA       | D2 (GPIO4)  |
| SCL       | D1 (GPIO5)  |

## Setup

1. **Copy secrets file**:
   ```bash
   cp secrets.yaml.example secrets.yaml
   ```

2. **Edit secrets.yaml** with your credentials:
   - WiFi SSID and password
   - MQTT broker IP, port, username, and password
   - OTA password

3. **Compile and upload**:
   ```bash
   esphome run scd4x-co2.yaml
   ```

## Configuration Details

### Sensor
- **Platform**: `scd4x` (SCD40/SCD41)
- **Interface**: I2C — SDA on GPIO4 (D2), SCL on GPIO5 (D1)
- **Readings**: CO2 (ppm), temperature (°C), humidity (%)
- **Update Interval**: 60s
- **Automatic Self-Calibration (ASC)**: enabled by default — only effective if
  the sensor regularly sees fresh outdoor air (~400 ppm). Disable it in the YAML
  for enclosed spaces to avoid drift.

### WiFi Behavior
- Attempts to connect to the configured WiFi network
- If connection fails, starts Soft AP mode
- **AP SSID**: "SCD4x-CO2 Fallback"
- **Captive Portal**: Enabled for easy reconfiguration

## Troubleshooting

- If WiFi doesn't connect, the device starts a hotspot named "SCD4x-CO2 Fallback"
- Connect to this hotspot and navigate to 192.168.4.1 for configuration
- Use `i2c: scan: true` logs to confirm the sensor is detected (default address 0x62)
- Check logs via serial monitor for debugging information
