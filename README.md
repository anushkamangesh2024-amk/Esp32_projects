# Medical Device Battery Monitoring and Safety Controller

## Executive Summary
This project implements a high-reliability digital Battery Management System (BMS) specifically engineered for **safety-critical medical devices**, including pacemakers, insulin pumps, and portable monitoring systems [1, 2]. Unlike consumer-grade battery controllers, this system prioritizes **fail-safe operation** and **strict safety compliance** over performance, ensuring that power failure or instability does not compromise patient care [2].

A cornerstone of this design is the **"Sticky Fault Latch"** mechanism. This ensures that any critical issue identified by the system—such as overheating or overcurrent—requires explicit human intervention to clear, preventing the dangers associated with automatic recovery in clinical settings [1, 3].

## Technical Applications & Compliance
The controller is optimized for low-power medical equipment that demands:
*   **High Reliability:** Deterministic digital logic for predictable behavior [2, 4].
*   **Fail-Safe Architecture:** Immediate transition to safe states upon detection of hazards [2, 3].
*   **Regulatory Alignment:** Conceptually aligned with **IEC 60601-1 safety standards**, focusing on controlled fault handling and the prevention of unsafe automatic restarts [2].

---

## System Architecture
The system operates as a purely digital control unit. It processes a variety of sensor inputs to generate real-time safety control outputs [2]. 

### Hardware Interface (Input/Output Mapping)
To meet the constraints of specialized ASIC platforms like Tiny Tapeout, the design fully utilizes an 8-bit input/output configuration [2, 4].

#### System Inputs (8 Bits)
| Signal | Bits | Description |
| :--- | :---: | :--- |
| **Voltage** | 4 | Digitized battery voltage (mapped 0–15) [2]. |
| **Current** | 2 | Monitors load current levels [2]. |
| **Temp Flag** | 1 | External indicator for over-temperature conditions [2]. |
| **Safe Reset** | 1 | Manual signal required to clear latched faults [2]. |

#### System Outputs (8 Bits)
| Signal | Bits | Description |
| :--- | :---: | :--- |
| **General Fault** | 1 | Indicates the system is in an unsafe state [3]. |
| **Shutdown** | 1 | Triggers an immediate hardware-level system disable [3]. |
| **Thermal Alarm** | 1 | A dedicated "sticky" flag for thermal events [3]. |
| **FSM State** | 2 | Real-time reporting of the current system safety state [3]. |
| **SOC** | 2 | Battery State-of-Charge level (Critical to Full) [3]. |
| **Overcurrent** | 1 | Instantaneous indicator of critical current levels [3]. |

---

## Core Functional Blocks
The controller is comprised of seven specialized logic blocks designed to work in parallel [3].

1.  **Voltage Comparator:** Categorizes battery voltage into five regions (Critical Low to Critical High) to detect deep discharge or overcharging risks [3].
2.  **SOC Estimator:** Translates voltage levels into four simplified battery states: **Critical, Low, Medium, or Full** [3].
3.  **Current Monitor:** Interprets current levels as Normal, High (Warning), or Critical (Fault). Critical current triggers a fault state immediately [3].
4.  **Thermal Guard (Sticky Latch):** Monitors overheating. Once activated, it remains active even after temperatures normalize, requiring a **Manual Reset** to ensure the device is inspected [3].
5.  **Hysteresis Counter:** Prevents "state chatter" from noisy signals by requiring **8 consecutive safe cycles** before allowing a transition from Warning back to Idle [3].
6.  **Watchdog Timer:** Escalates safety if a fault persists for **16 cycles**, moving the system into a full Shutdown to prevent unattended failures [3].
7.  **Safety Finite State Machine (FSM):** The central logic core managing the four operational states: **IDLE, WARN, FAULT, and SHUTDOWN** [3].

---

## The Safety-First Logic Model
The FSM is governed by a strict hierarchy where critical safety events bypass warnings for immediate action [3].

*   **Non-Automatic Recovery:** Critical faults are "sticky" (latched). Recovery is only possible through a combination of a manual human reset and safe environmental conditions [3].
*   **Smart Fault Handling:** The system synthesizes voltage, current, and thermal data simultaneously, prioritizing patient safety over device performance [4].
*   **Fail-Safe Defaults:** If the system reaches the **SHUTDOWN** state, the medical device is disabled to prevent catastrophic failure or fire [3].

---

## Hardware Efficiency Metrics
Despite its robust feature set, the design is highly optimized for ASIC manufacturing with a very low gate count [4].

| Block | Estimated Gate Count |
| :--- | :--- |
| **Voltage & SOC Logic** | ~30 Gates |
| **Safety FSM** | ~80 Gates |
| **Watchdog & Hysteresis** | ~70 Gates |
| **Miscellaneous & Interface** | ~120-220 Gates |
| **Total Complexity** | **~300–400 Gates** |

This efficiency ensures the controller can be integrated into the smallest medical implants and portable monitors while maintaining the highest tiers of digital reliability [4, 5].
