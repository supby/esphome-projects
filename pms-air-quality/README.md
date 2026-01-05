# PMS Air Quality Sensor

ESPHome configuration for air quality monitoring using PMS series particulate matter sensors with MQTT integration, WiFi fallback support, and OTA updates.

## Features

- **PMS Sensor Support**: Compatible with PMS5003, PMS7003, PMS1003, PMSA003 sensors
- **Comprehensive Measurements**: 
  - PM1.0, PM2.5, PM10 concentration measurements
  - Particle counts for different size ranges (0.3µm to 10µm)
- **MQTT Integration**: Automatic discovery and reporting to MQTT broker
- **WiFi Fallback**: Automatic Soft AP mode when WiFi is unavailable
- **OTA Updates**: Over-the-air firmware updates
- **Captive Portal**: Easy configuration in AP mode
- **Additional Sensors**: WiFi signal strength, uptime, IP address, connection status

## Hardware Requirements

- **ESP8266 development board** (e.g., NodeMCU, Wemos D1 Mini)
- **PMS sensor** (PMS5003, PMS7003, PMS1003, or PMSA003)
- **Power supply** (5V, at least 500mA recommended)
- **Jumper wires** for connections

### PMS Sensor Pinout

PMS sensors typically have the following pins:
- VCC (5V power)
- GND (ground)
- TX (transmit data)
- RX (receive data)
- SET (optional - sleep mode control)
- RESET (optional - reset sensor)

## Wiring Diagram

Connect the PMS sensor to ESP8266 as follows:

```
PMS Sensor    ->    ESP8266 (NodeMCU)
--------------------------------
VCC (5V)      ->    VIN or 5V
GND           ->    GND
TX            ->    GPIO13 (D7)
RX            ->    GPIO15 (D8)
```

**Note**: Some PMS sensors may have different voltage requirements. Check your specific sensor datasheet. Most PMS sensors work with 5V power but output 3.3V logic levels, making them compatible with ESP8266.

## Setup

1. **Copy secrets file**:
   ```bash
   cd pms-air-quality
   cp secrets.yaml.example secrets.yaml
   ```

2. **Edit secrets.yaml** with your credentials:
   - WiFi SSID and password
   - MQTT broker IP, port, username, and password
   - OTA password

3. **Compile and upload**:
   ```bash
   esphome run pms-air-quality.yaml
   ```

## Configuration Details

### Sensor Measurements

The configuration reports the following measurements via MQTT:

**Particulate Matter Concentrations** (µg/m³):
- **PM1.0**: Ultra-fine particles
- **PM2.5**: Fine particulate matter (most important for health monitoring)
- **PM10.0**: Coarse particulate matter

**Particle Counts** (particles per 0.1L):
- Particles >0.3µm
- Particles >0.5µm
- Particles >1.0µm
- Particles >2.5µm
- Particles >5.0µm
- Particles >10.0µm

### Update Intervals
- Sensor readings: Every 60 seconds
- WiFi signal: Every 60 seconds
- Uptime: Every 60 seconds

### WiFi Behavior
- Attempts to connect to configured WiFi network
- If connection fails, starts Soft AP mode
- **AP SSID**: "PMS-AirQuality Fallback"
- **AP Password**: "12345678" (changeable in yaml file)
- **Captive Portal**: Enabled for easy reconfiguration

### MQTT Topics

The device publishes to MQTT topics with automatic Home Assistant discovery:
- `pms-air-quality/sensor/pm25/state`
- `pms-air-quality/sensor/pm10/state`
- `pms-air-quality/sensor/pm100/state`
- And more for all configured sensors...

### Home Assistant Integration

The configuration includes MQTT discovery, so sensors will automatically appear in Home Assistant when connected to the same MQTT broker.

## Air Quality Index Reference

Use these PM2.5 values as a reference (EPA AQI):

| PM2.5 (µg/m³) | AQI Level | Health Impact |
|---------------|-----------|---------------|
| 0-12 | Good | Little to no risk |
| 12.1-35.4 | Moderate | Unusually sensitive people should consider limiting prolonged outdoor exertion |
| 35.5-55.4 | Unhealthy for Sensitive Groups | People with respiratory or heart disease should limit outdoor exertion |
| 55.5-150.4 | Unhealthy | Everyone should limit prolonged outdoor exertion |
| 150.5-250.4 | Very Unhealthy | Everyone should avoid prolonged outdoor exertion |
| 250.5+ | Hazardous | Everyone should avoid all outdoor exertion |

## Troubleshooting

### WiFi Issues
- If WiFi doesn't connect, the device will start a hotspot named "PMS-AirQuality Fallback"
- Connect to this hotspot (password: 12345678) and navigate to 192.168.4.1 for configuration
- Check logs via serial monitor for debugging information

### Sensor Not Reading
- Verify wiring connections (TX/RX must be correct)
- Check if the sensor is receiving 5V power
- Confirm the sensor type in the YAML matches your hardware
- Monitor serial output for UART errors

### MQTT Not Working
- Verify MQTT broker is accessible from the ESP8266
- Check MQTT credentials in secrets.yaml
- Ensure MQTT broker allows connections from the device IP
- Check MQTT topics in broker logs

## Power Consumption

- Typical power consumption: ~200-400mA (ESP8266 + PMS sensor)
- PMS sensor uses ~100mA when actively measuring
- Consider using a quality 5V power supply (minimum 500mA capacity)

## Optional Modifications

### Change Sensor Type
Edit the `type:` parameter under sensor configuration:
```yaml
sensor:
  - platform: pmsx003
    type: PMS5003T  # For PMS5003T with temp/humidity
    # or
    type: PMS7003   # For PMS7003
```

### Change GPIO Pins
Modify the UART configuration for different pin assignments:
```yaml
uart:
  rx_pin: GPIO5   # Your chosen RX pin
  tx_pin: GPIO4   # Your chosen TX pin
```

### Adjust Update Interval
Change `update_interval` to read more or less frequently:
```yaml
update_interval: 30s  # Read every 30 seconds
```

## License

This configuration is provided as-is for educational and personal use.
