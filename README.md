# FT0317A Ceiling Fan Light Integration for Home Assistant

This project provides complete Home Assistant integration for ceiling fans with built-in lights controlled by the FT0317A RF 433MHz remote control. The integration enables full control and state tracking of both the fan and light functions through Home Assistant while maintaining compatibility with the original remote control.

## Project Overview

The integration consists of two main components that work together to provide seamless control:

1. **ESPHome Configuration** for Sonoff RF Bridge R2 - Acts as a simple RF gateway that transmits received codes and reports original remote codes to Home Assistant
2. **Home Assistant Configuration** - Provides comprehensive entities and automation for complete fan and light control

> **Example Compatible Device**: This integration was developed and tested with the [GONEO Bladeless Ceiling Fan Light](https://www.goneoglobal.com/collections/ceiling-fan-light/products/bladeless-ceiling-fan-light) (Model: F19A), which uses the FT0317A remote control. Many other compatible devices likely exist, as these ceiling fans are primarily manufactured in China and widely available on platforms like AliExpress with the same FT0317A remote.

## Limitations

- **Timer Functions**: The original remote's timer features (1h, 4h, 8h) are not currently supported
- **Manual Reset Required**: The reset script requires manual preparation (turning on light and setting warm temperature)

## Project Structure

```
ft0317a_ceiling_fan_light/
├── esphome/
│   └── sonoff-rf-bridge.yaml      # ESPHome configuration for RF Bridge
└── ha/
    └── packages/
        └── ft0317a_ceiling_fan_light/
            ├── common.yaml        # RF codes, automations, and scripts
            ├── fan.yaml           # Fan entity and controls
            └── light.yaml         # Light entity and controls
```

## Hardware Requirements

- **Sonoff RF Bridge R2** - RF 433MHz transceiver with "direct hack" modification ([instructions here](https://github.com/xoseperez/espurna/wiki/Hardware-Itead-Sonoff-RF-Bridge---Direct-Hack)) - requires soldering and cutting some traces
- **FT0317A Remote Control** - (optional) Original remote ([FCC documentation and manual](https://device.report/shenzhen-funpower-general-technology/ft0317a))
- **Compatible Ceiling Fan** - Fan with built-in light supporting FT0317A remote

## Setup Instructions

### 1. ESPHome Configuration

1. Flash the Sonoff RF Bridge R2 with the provided ESPHome configuration
2. Update the secrets file with your WiFi credentials and encryption keys
3. The bridge will automatically capture and forward RF codes to Home Assistant

### 2. Home Assistant Configuration

1. Copy the `ha/packages/ft0317a_ceiling_fan_light/` directory to your Home Assistant packages folder
2. Add the package to your `configuration.yaml`:
   ```yaml
   homeassistant:
     packages: !include_dir_named packages
   ```
3. Restart Home Assistant to load the new entities

### 3. Initial Setup

The device will be in unknown state so perform "RF Device Reset" script to synchronize states. Follow the instructions appearing in notifications to correctly step through the procedure.

## Usage

### Home Assistant Interface
- **Fan Entity**: `fan.rf_ceiling_fan` - Full fan control with preset modes and percentage
- **Light Entity**: `light.rf_ceiling_light` - Complete light control with brightness and temperature
- **Reset Button**: `button.rf_device_reset` - Reset device state synchronization

> [!WARNING]
> Do not manually modify any input entities (input_boolean, input_number, input_select) as they are responsible for maintaining device state synchronization. Incorrect modifications can lead to inconsistent behavior. Use the provided entities and controls instead.


### Reset Procedure

If state synchronization is lost:

1. Press the "RF Device Reset" button in Home Assistant or manually execute `script.rf_device_reset` script.
2. Follow the notification instructions:
   - Turn on the light manually (any brightness)
   - Set the light to warm color temperature
3. The script will automatically calibrate and restore previous settings

## Technical Details

### RF Protocol
- **Frequency**: 433MHz
- **Protocol**: Custom protocol with dual-code transmission (A + B codes)
- **Pulse Length**: 300μs with specific timing patterns
- **Repetition**: 5 times per transmission with 33ms delay between A and B codes

### State Management
The integration maintains comprehensive state tracking through:
- Input booleans for power states
- Input numbers for brightness levels
- Input selects for preset modes and color temperatures
- Template entities that combine states into standard Home Assistant entities

## Credits

This project was completely "vibe coded" using AI tools. While there's room for improvement, it serves its purpose effectively. The RF code transmission functionality was inspired by the excellent work of [@alm4096/Arlec-DC-Ceiling-Fan-Remote-FT0317A-Arduino](https://github.com/alm4096/Arlec-DC-Ceiling-Fan-Remote-FT0317A-Arduino).

## License

This project is provided as-is for educational and personal use. Please ensure compliance with local RF transmission regulations when using RF bridge devices. 