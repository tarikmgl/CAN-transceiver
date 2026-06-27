# CAN Bus Transceiver Simulation in LTspice

A discrete-level hardware simulation of a Controller Area Network (CAN) Transceiver physical layer, designed from scratch using LTspice. This project models the physical mechanism of mapping digital logic signals to differential bus voltages and decoding them back via a comparator interface.

---

## Project Overview

In embedded and automotive systems, a CAN Transceiver acts as the physical bridge between a microcontroller's logic-level UART/CAN peripheral (TX/RX) and the physical network bus lines (CANH/CANL). 

This project implements the transceiver architecture using Voltage-Controlled Switches (SW), standard termination resistors, and a digital Schmitt Trigger / Comparator (A1) to demonstrate robust differential signaling.

---

## How It Works

### Transmitter Stage (Tx)
The digital pulse train $V(vtx)$ actively drives two complementary voltage-controlled switches to modulate the CAN bus:
* **Dominant State (Logic 0):** When $V(vtx)$ drops to 0V, the switches close. $CANH$ is actively driven toward **3.0V** through $V_1$ ($3.5\text{V}$), and $CANL$ is driven toward **2.0V** through $V_3$ ($1.5\text{V}$), creating a **1.0V differential gap** ($V_{diff} = \text{CANH} - \text{CANL}$).
* **Recessive State (Logic 1):** When $V(vtx)$ rises to 5V, the switches open. The active voltage drivers disconnect, leaving the bus lines to stabilize at their baseline levels through the resistor network.

### Receiver Stage (Rx)
The receiver stage continuously monitors the physical bus line and decodes the differential voltage back into micro-readable UART logic:
* Utilizes a digital Schmitt Trigger component (**A1**) to compare $CANH$ and $CANL$.
* It decodes the inverted output (active-low CAN logic) using the inverting output node: When a differential voltage gap is detected, the $V(vrx)$ output perfectly switches to track the input data frame.

---

## Circuit Schematic

The simulation setup consists of three primary functional hardware blocks:
1. **The Signal Generator ($V_2$):** Simulates a 100 kHz microcontroller data frame ($V(vtx)$) switching between 0V and 5V.
2. **The Bus Termination Network:** Incorporates $60\ \Omega$ isolation resistors ($R_1, R_2$) combined with a central $120\ \Omega$ load resistor ($R_3$) to model standard bus impedance characteristics.
3. **The Receiver Interface ($A_1$):** A Schmitt-trigger differential comparator receiving $CANH$ at the non-inverting pin and $CANL$ at the inverting pin to recreate the logical $V(vrx)$ feedback loop.

---

## Simulation Waveforms

Running the transient analysis (`.tran 50u`) yields accurate, highly synchronized signal transitions across the nodes:

* **Input & Output Logic:** The source pulse $V(vtx)$ and the received data loop $V(vrx)$ match flawlessly, validating the transceiver logic transmission.
* **Differential Bus Lines:** $V(canh)$ (Blue) and $V(canl)$ (Red) showcase the classic differential "mirror effect", splitting symmetrically during dominant states and converging toward equilibrium during recessive cycles.

1. Clone this repository:
   ```bash
   git clone [https://github.com/](https://github.com/)[Your-Username]/can-transceiver-ltspice.git
