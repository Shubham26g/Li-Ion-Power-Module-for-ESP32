# ⚡ ESP32 Li-Ion Battery Power Management Module

A compact, industry-grade power management PCB designed to charge, protect, and regulate a single-cell Li-Ion battery to provide a stable **3.3V output** for an ESP32 microcontroller.

> Designed as a complete battery management solution — not just a regulator, but a full charge-discharge-monitor system ready for product integration.
> <img width="1325" height="705" alt="Screenshot 2026-06-02 234543" src="https://github.com/user-attachments/assets/510e8d22-5472-4f8a-ac41-1b95cfa05c21" />


---

## 📋 Features

| Feature | Implementation | Benefit |
|---|---|---|
| **USB-C Charging** | TP4056 linear charger (580mA) | Modern connector, universal compatibility |
| **Reverse Polarity Protection** | P-channel MOSFET (AO3401A) | Only 22mV drop vs ~300mV for a Schottky diode |
| **3.3V LDO Regulation** | AP2112K-3.3 (600mA, 70dB PSRR) | Ultra-low noise output critical for WiFi/BT RF |
| **Battery Voltage Monitoring** | Resistive divider (100kΩ/100kΩ) to ADC | ESP32 can read battery % via ADC pin |
| **Physical Power Switch** | SPDT slide switch (MSK-12C02) | Complete battery disconnect when not in use |
| **Charge Status LEDs** | Red (charging) / Green (complete) | Visual feedback without firmware |
| **Power Status LED** | Green LED on 3.3V rail | Instant power-on confirmation |
| **Test Points** | 4 exposed pads (5V, VBAT, 3V3, GND) | Easy debugging and validation |

---

## 📐 Specifications

| Parameter | Value |
|---|---|
| **Input Voltage (Battery)** | 3.0V – 4.2V (1S Li-Ion / LiPo) |
| **Input Voltage (USB-C)** | 5V |
| **Output Voltage** | 3.3V ± 2% |
| **Max Output Current** | 600mA |
| **Charge Current** | 580mA (set by 2kΩ PROG resistor) |
| **Charge Termination** | 4.2V ± 1% |
| **LDO Dropout Voltage** | 250mV @ 600mA |
| **Quiescent Current** | ~76µA (55µA LDO + 21µA divider) |
| **Reverse Polarity Drop** | ~22mV (RDS(on) = 44mΩ) |
| **PSRR** | 70dB @ 1kHz |
| **Operating Temperature** | -40°C to +85°C |
| **Board Dimensions** | ~30mm × 20mm (2-layer) |

---

## 🔧 Circuit Architecture

```
                     ┌─────────────────────────────────────────────┐
                     │           TP4056 CHARGER                    │
   USB-C ──► 5V ───►│ VCC                          BAT ├────┐     │
                     │ CHRG# ──► LED2 (Red, Charging)   │    │     │
                     │ STDBY# ──► LED3 (Green, Done)    │    │     │
                     └──────────────────────────────────┘    │
                                                             │
   ┌──────────┐   ┌────────┐   ┌──────────────┐             │
   │  Li-Ion  ├───┤ Switch ├───┤ Q1 P-MOSFET  ├─────────────┘
   │  Battery │   │ ON/OFF │   │ Rev. Polarity │
   └──────────┘   └────────┘   └──────┬───────┘
                                       │
                                  VIN (Protected)
                                       │
                          ┌────────────┴────────────┐
                          │  C4 (100µF)  C1 (10µF)  │  Input Decoupling
                          └────────────┬────────────┘
                                       │
                               ┌───────┴───────┐
                               │   AP2112K     │
                               │   3.3V LDO    │
                               │   600mA       │
                               └───────┬───────┘
                                       │
                          ┌────────────┴────────────┐
                          │  C3 (100nF)  C2 (10µF)  │  Output Decoupling
                          └────────────┬────────────┘
                                       │
                              3.3V Regulated Output
                                       │
                    ┌──────────────┬────┴────┬──────────────┐
                    │              │         │              │
                LED1 (PWR)   J2 Output   Voltage       Test Points
                                Header    Divider
                              (to ESP32)  (ADC sense)
```

---

## 📦 Bill of Materials (BOM)

| Ref | Component | Value / Part | Package | LCSC # | Qty |
|---|---|---|---|---|---|
| **U1** | LDO Regulator | AP2112K-3.3TRG1 | SOT-23-5 | C51118 | 1 |
| **U3** | Li-Ion Charger | TP4056 | SOP-8 | C16581 | 1 |
| **Q1** | P-Channel MOSFET | AO3401A | SOT-23 | C347476 | 1 |
| **USBC1** | USB Type-C Connector | TYPE-C-31-M-12 | SMD | C165948 | 1 |
| **CN1** | Battery Connector | B2B-PH-K-S(LF)(SN) | Through-hole | C131337 | 1 |
| **J2** | Output Pin Header | 1×4, 2.54mm | Through-hole | C5116483 | 1 |
| **SW1** | Slide Switch (SPDT) | MSK-12C02 | SMD | — | 1 |
| **C4** | Electrolytic Capacitor | 100µF | Through-hole | — | 1 |
| **C1, C2** | Ceramic Capacitor | 10µF | 0805 | C15850 | 2 |
| **C3, C5** | Ceramic Capacitor | 100nF | 0603 | C14663 | 2 |
| **C6** | Ceramic Capacitor | 10µF | 0805 | C15850 | 1 |
| **R1, R7, R8** | Resistor | 1kΩ | 0603 | C21190 | 3 |
| **R2, R3** | Resistor | 100kΩ | 0603 | C25803 | 2 |
| **R4, R5** | Resistor | 5.1kΩ | 0603 | C23186 | 2 |
| **R6** | Resistor | 2kΩ | 0603 | C22975 | 1 |
| **LED1** | Power LED (Green) | 19-217/GHC-YR1S2/3T | 0603 | — | 1 |
| **LED2** | Charging LED (Red) | KT-0603R | 0603 | C2286 | 1 |
| **LED3** | Done LED (Green) | 19-217/GHC-YR1S2/3T | 0603 | C72043 | 1 |

**Estimated BOM Cost: ~$1.20** (LCSC pricing, excluding PCB fabrication)

---

## 🧠 Key Design Decisions

### Why LDO over Buck Converter?
The AP2112K LDO was chosen over a switching regulator (e.g., LM3671) to **minimize output noise**. The ESP32's WiFi and Bluetooth radios are highly sensitive to power supply noise. A buck converter's switching harmonics (typically 1–3 MHz) can couple into the RF front-end, degrading wireless performance. The LDO provides **70dB PSRR**, ensuring clean power delivery.

**Trade-off:** Lower efficiency (~79% at 4.2V → 3.3V) vs. a buck converter (~95%). Acceptable for battery-powered applications where RF performance is critical.

### Why P-MOSFET over Schottky Diode for Reverse Polarity Protection?
A Schottky diode (e.g., SS14) would drop ~300mV at 500mA, wasting power and reducing the usable battery voltage range. The AO3401A P-MOSFET has an RDS(on) of just 44mΩ, resulting in only a **22mV drop** — a **13x improvement** in power efficiency for the protection circuit.

### Why 100kΩ for the Voltage Divider?
Using 100kΩ resistors limits the divider current to just **21µA** (VBAT/200kΩ). Lower resistance values (e.g., 10kΩ) would drain the battery faster with 210µA of continuous current. For a battery-powered device, every microamp counts.

### Why USB-C with CC Resistors?
The 5.1kΩ pull-down resistors on CC1 and CC2 are **mandatory per USB-C specification**. They signal to the USB host that this device is a power sink. Without them, USB-C to USB-C cables will **not deliver power**. Many student projects miss this detail.

### Capacitor Selection: X5R/X7R Ceramic
All ceramic capacitors use **X5R** or **X7R** dielectrics, which maintain stable capacitance across voltage and temperature ranges. Cheaper Y5V dielectrics can lose up to 80% of their rated capacitance at operating voltage — a critical failure point that many designs overlook.

---

## 📁 Project Files

```
├── Schematic/
│   ├── ESP32_Power_Module.json     # EasyEDA schematic source
│   └── ESP32_Power_Module.pdf      # Schematic export (printable)
├── PCB/
│   ├── ESP32_Power_Module_PCB.json # EasyEDA PCB layout source
│   ├── Gerber/                     # Fabrication files (JLCPCB-ready)
│   └── BOM.csv                     # Bill of Materials for assembly
├── Docs/
│   ├── Design_Decisions.md         # Detailed rationale for every choice
│   └── Block_Diagram.png           # High-level architecture diagram
└── README.md
```

---

## 🏗️ PCB Layout Highlights

- **2-layer board** with solid GND copper pour on both layers
- **Thermal vias** under TP4056 exposed pad for heat dissipation (~0.75W during charge)
- **Wide power traces** (0.5mm / 20mil) for all high-current nets
- **GND stitching vias** connecting top and bottom ground planes
- **Component placement** follows linear power flow: USB → Charger → Battery → Protection → Regulation → Output
- **Professional silkscreen** with polarity markings, pin labels, and board name

---

## ⚙️ How It Works

### Charging Mode (USB-C Connected)
1. USB-C provides 5V to the TP4056 charger IC
2. TP4056 charges the Li-Ion cell with a constant-current/constant-voltage (CC/CV) profile
3. Red LED indicates charging in progress
4. Green LED lights up when charge is complete (4.2V)

### Discharge Mode (USB-C Disconnected)
1. Slide switch connects battery to the circuit
2. P-MOSFET provides reverse polarity protection
3. AP2112K LDO regulates battery voltage (3.0V–4.2V) down to stable 3.3V
4. Green power LED confirms output is active
5. Voltage divider provides scaled battery voltage to ESP32 ADC for battery level monitoring

---

## 📜 License

This project is open-source hardware. Feel free to use, modify, and manufacture.

*Designed as part of a hardware engineering assignment — demonstrating end-to-end PCB design from schematic capture through layout, with industry-grade component selection and design practices.*
