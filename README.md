# EMG / FSR Bio-Signal Acquisition System (ATmega128)

This project implements a real-time bio-signal acquisition system for EMG (Electromyography) and FSR (Force Sensitive Resistor) sensors using an ATmega128 microcontroller.

The firmware samples EMG signals at 1 kHz, compresses the data using DPCM (Differential Pulse Code Modulation), and transmits packets through UART Bluetooth communication.

This system was developed as part of a Capstone Design project for a wearable rehabilitation device.

---

# System Overview

The firmware performs the following functions:

1. EMG Signal Acquisition
- ADC sampling at 1 kHz
- 20 EMG samples collected per packet

2. FSR Sensor Measurement
- Pressure sensor value acquired after EMG sampling

3. Signal Compression
- EMG samples compressed using DPCM
- Reduces wireless transmission bandwidth

4. Wireless Transmission
- UART communication with BLE module
- Packet-based data streaming

---

# Hardware

Microcontroller
- ATmega128
- 16 MHz clock

Sensors
- EMG analog front-end
- FSR pressure sensor

Communication
- BLE module (UART)

Indicators
- LED status indicator
- Buzzer alert

---

# Firmware Architecture

Main modules in the firmware:

Timer1 Interrupt
- Generates 1 kHz sampling trigger

ADC Interrupt
- Collects EMG samples
- Reads FSR sensor
- Copies data to buffer

Main Loop
- Generates compressed packet
- Calculates checksum
- Pushes packet to UART buffer

UART Interrupt
- Handles non-blocking transmission using a ring buffer

---

# Data Packet Structure

Each packet consists of 26 bytes.

Header (1 byte)  
Packet Index (1 byte)  
EMG Start Value (2 bytes)  
EMG DPCM Differences (19 bytes)  
FSR Value (2 bytes)  
Checksum (1 byte)

Total = 26 bytes

---

# Packet Format

[Header][Index][Start][Diffs][FSR][Checksum]

Example

0xFA | Packet Counter | EMG Start | DPCM Data | FSR | Checksum

---

# DPCM Compression

EMG samples are compressed using Differential Pulse Code Modulation.

diff = sample[i] - sample[i-1]

The difference value is limited to

-128 ~ 127

Clamping is applied when the difference exceeds this range.

---

# Checksum

Checksum is calculated as

Checksum = Sum(all bytes except header) & 0xFF

---

# Ring Buffer UART Transmission

To prevent data loss during high-speed streaming, a 256-byte ring buffer is used.

tx_head → write pointer  
tx_tail → read pointer

UART transmission is handled using the UDRE interrupt.

---

# Sampling Configuration

MCU: ATmega128  
Clock: 16 MHz  
Sampling Rate: 1 kHz  
UART Baud Rate: 115200  
Packet Size: 26 bytes

---

# Firmware Flow

BLE Connected  
↓  
Start Command ('a')  
↓  
Timer1 generates 1 kHz interrupt  
↓  
ADC sampling  
↓  
Collect 20 EMG samples  
↓  
Read FSR value  
↓  
Create DPCM packet  
↓  
Add checksum  
↓  
Store in ring buffer  
↓  
Transmit via UART (BLE)

---

# Author

Capstone Design Project  
EMG / FSR Bio-Signal Processing System
