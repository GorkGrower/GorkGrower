# Hardware Setup

## Components

| Component | Part | Notes |
|---|---|---|
| SBC | Raspberry Pi 4 (4GB) | Pi 3B+ works too |
| Sensor | BME680 breakout (Pimoroni) | I2C, 0x76 address |
| Relay | 2-channel 5V relay board | Active-LOW trigger |
| Light | 600W LED (full spectrum) | ~300W actual draw |
| Pump | 12V submersible pump | 5-second activations |
| Camera | Pi Camera Module v2 | Or any USB webcam |

## GPIO Wiring

```
Pi GPIO 17  →  Relay IN1  →  Grow Light  (normally closed)
Pi GPIO 27  →  Relay IN2  →  Water Pump  (normally open)
Pi GPIO 2   →  BME680 SDA (I2C)
Pi GPIO 3   →  BME680 SCL (I2C)
Pi 3.3V     →  BME680 VCC
Pi GND      →  BME680 GND + Relay GND
Pi 5V       →  Relay VCC
```

## I2C Setup

```bash
# Enable I2C on Pi
sudo raspi-config  # Interface Options → I2C → Enable

# Verify BME680 detected
i2cdetect -y 1    # Should show 0x76 or 0x77
```

## Safety Notes

- Use a proper enclosure for mains-voltage relay wiring
- Add a fuse on the mains side (5A max for the light circuit)
- The pump circuit can run 12V DC — safer than mains
- Never run pump dry; add a float switch if unattended for long periods
