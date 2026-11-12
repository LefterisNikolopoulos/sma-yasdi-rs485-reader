# SMA RS485 Inverter Reader (Sunny Mini Central / SMC 7000TL)

> **100% Standalone Single-File Python Reader for SMA Inverters via RS485 & YASDI**

A standalone, robust Python tool designed for monitoring and logging data from **SMA Solar Inverters** (specifically tested on **SMA Sunny Mini Central SMC 7000TL**) using RS485 serial communication and the official SMA **YASDI** protocol.

---

## 🌟 Highlights & Features

- **Single-File Zero-Dependency Executable (`read_sma.py`)**:
  - Embedded native ARM64 YASDI shared libraries (`libyasdimaster.so`, `libyasdidrv.so`, `libyasdi.so`).
  - Self-extracts and configures paths automatically on any fresh Raspberry Pi (Raspberry Pi OS 64-bit / Debian aarch64).
  - No manual compilation, no `cmake`, no external tarballs needed.
- **Full Channel Telemetry**:
  - Instant live readout of all channels: `Pac` (Grid Power), `Pdc` (PV Power), `Vdc` (Array Voltage), `Idc` (Array Current), `Vac`, `Iac`, `Fac` (Frequency), `E-Total` (Total Yield), Operating Status / Mode, Error states, etc.
- **Accurate Daily Yield (`E-Today`)**:
  - Automatically calculates and updates daily production (`E-Today` in kWh) relative to a local midnight baseline (`sma_daily_baseline.json`).
- **Flexible Logging & Exports**:
  - Clean formatted console tables for instant inspection.
  - **CSV Export** (`sma_data.csv`) for easy spreadsheet and Grafana / InfluxDB ingestion.
  - **JSON Export** (`sma_all_channels.json`) containing complete dumps of all available device channels.
  - Interactive mode with selectable continuous polling loop.

---

## 🔌 Hardware & Wiring (SMA SMC 7000TL)

For SMA Sunny Mini Central inverters equipped with the RS485 Piggy-Back board (`485PB-SMC-NR`):

| RS485 Adapter Pin | SMA Terminal Pin | Function |
| :--- | :--- | :--- |
| **A / Data+ / D+** | **Pin 2** | RS485 Data+ |
| **B / Data- / D-** | **Pin 7** | RS485 Data- |
| **GND** | **Pin 5** | Signal Ground / Shield |

### Bus Termination (Jumper A):
- **Last Inverter on the chain**: Set Jumper A to **ON** (terminates the line with 120Ω).
- **All other Inverters**: Set Jumper A to **OFF**.

### Communication Parameters:
- **Baudrate**: `1200` baud (fixed standard for SMA RS485 Piggy-Back communication).
- **Protocol**: SMA YASDI Master.

---

## 🚀 Quick Start

### 1. Requirements
- Raspberry Pi running Raspberry Pi OS (64-bit / aarch64) or Linux system with Python 3.
- USB to RS485 adapter (e.g. FTDI, CH340, CP2102) connected as `/dev/ttyUSB0`.

### 2. Run
Simply run the script with Python 3:

```bash
python3 read_sma.py
```

### 3. Usage Example
```text
============================================================================
      SMA RS485 Inverter Reader - Sunny Mini Central (SMC 7000TL)      
============================================================================
[*] Διαθέσιμες θύρες USB: /dev/ttyUSB0

[?] Δώσε αριθμό θύρας USB (π.χ. 0 για ttyUSB0, 1 για ttyUSB1) [0]: 0
[?] Πόσους inverters θες να διαβάσεις; [2]: 2

[*] Θύρα: /dev/ttyUSB0 | Baudrate: 1200 (Μόνιμο) | Στόχος: 2 inverters (SMC 7000TL)
[*] Φόρτωση YASDI Engine...
[+] Driver ONLINE στο /dev/ttyUSB0.
[*] Έναρξη αξιόπιστης αναζήτησης 2 inverters...
[*] Αναμονή απάντησης: 2/2 βρέθηκαν (17.0s)...
[✓] Ολοκληρώθηκε η ανίχνευση σε 17.0s.
[+] Βρέθηκαν και απάντησαν συνολικά: 2 συσκευές.

===================================================
 📊 ΣΥΝΟΨΗ METΡΗΣΕΩΝ INVERTERS
===================================================
+---------+------------+----------+----------+----------+----------+------------+------------+---------------+
| Inverter| Serial Nr  | Pac (W)  | Vdc (V)  | Idc (A)  | Pdc (W)  | E-Today kWh| E-Total kWh| Κατάσταση     |
+---------+------------+----------+----------+----------+----------+------------+------------+---------------+
| #1      | 2110289698 | 2154.0   | 480.2    | 4.6      | 2208.9   | 14.2       | 185420.0   | Mpp           |
| #2      | 2110289317 | 2110.0   | 475.8    | 4.5      | 2141.1   | 13.9       | 182105.0   | Mpp           |
+---------+------------+----------+----------+----------+----------+------------+------------+---------------+
| ΣΥΝΟΛΟ  | 2 Συσκευές | 4264.0 W |          |          |          | 28.1 kWh   |            |               |
+---------+------------+----------+----------+----------+----------+------------+------------+---------------+
[+] Τα δεδομένα αποθηκεύτηκαν στο: sma_data.csv
[+] Όλα τα κανάλια αποθηκεύτηκαν στο: sma_all_channels.json
```

---

## ⚠️ Important Troubleshooting

1. **Serial Port Busy / Bus Collisions**:
   If another daemon (such as Node-RED or another monitoring process) is accessing `/dev/ttyUSB0`, inverter discovery will fail (`0/2 devices found`).
   Stop any conflicting services:
   ```bash
   sudo systemctl stop node-red   # or node-red-stop
   ```
2. **Daylight / DC Voltage**:
   SMA Sunny Mini Central inverters are powered by the solar array (DC voltage). When the sun sets or DC disconnect is open, the inverters power off and will not answer RS485 packets.

---

## 📜 License & Acknowledgments

- **YASDI Protocol & C Libraries**: Developed and released by **SMA Solar Technology AG** under the **GNU Lesser General Public License (LGPL v2.1)**.
- **Python Reader (`read_sma.py`)**: Distributed under the **GNU Lesser General Public License (LGPL v2.1)**. See [LICENSE](LICENSE) for details.
