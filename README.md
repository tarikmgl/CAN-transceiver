# CAN Bus Transceiver Simulation in LTspice

A discrete-level hardware simulation of a Controller Area Network (CAN) Transceiver physical layer, designed from scratch using LTspice. This project models the exact internal silicon architecture of industry-standard CAN transceivers (such as the TJA1050 or SN65HVD230), mapping digital logic signals to differential bus voltages.


## Project Overview

In embedded and automotive systems, a CAN Transceiver acts as the physical bridge between a microcontroller's logic-level UART/CAN peripheral ($TXD$/$RXD$) and the physical twisted-pair network cable ($CANH$/$CANL$). 

Instead of using a pre-built black-box SPICE model, this project implements the physical layer using Voltage-Controlled Switches (SW), discrete resistor networks for Split Termination, and a Schmitt Trigger / Comparator (LT1017) to achieve high-speed differential sensing.

---

## How It Works

### Transmitter Stage (Tx)
The digital pulse train ($VTXD$) drives two complementary voltage-controlled switches to modulate the bus lines:
* **Dominant State (Logic 0):** The switches close. $CANH$ is actively driven to **$3.0\text{ V}$** and $CANL$ is driven to **$2.0\text{ V}$**, creating a distinct **$1.0\text{ V}$ differential voltage gap** ($V_{diff} = \text{CANH} - \text{CANL}$).
* **Recessive State (Logic 1):** The switches open. The active drivers disconnect. The common-mode biased **$2.5\text{ V}$ reference** pulls both $CANH$ and $CANL$ back to equilibrium via $60\ \Omega$ split-termination resistors. $V_{diff}$ drops to **$0\text{ V}$**.

### Receiver Stage (Rx)
The receiver stage continuously monitors the physical bus line and decodes the differential voltage back into micro-readable UART logic:
* Utilizes a high-speed comparator (**LT1017**) or a **Schmitt Trigger** macro model to compare $CANH$ and $CANL$.
* It decodes the inverted output (active-low CAN logic): When a voltage gap ($V_{diff} \ge 0.9\text{ V}$) is detected, the $VRXD$ output immediately snaps to **$0\text{ V}$ (Logic 0)**, mirroring the transmitted data flawlessly.

---

## Circuit Schematic

The simulation project consists of three main hardware blocks structured on the LTspice grid:
1. **The Signal Generator ($VTXD$):** Simulates a $100\text{ kHz}$ microcontroller data frame.
2. **The Split Bus Network:** Incorporates a total of $120\ \Omega$ standard bus termination matched with a $2.5\text{ V}$ common-mode stabilization core to minimize high-frequency EMI.
3. **The Receiver Interface:** An open-collector comparator layout with a $10\text{ k}\Omega$ pull-up resistor to prevent output floating.

---

## Simulation Waveforms

Running the transient analysis (`.tran 50u`) yields a highly accurate mirroring effect showing absolute synchronization between input and output data loops:

* **$V(vtxd)$ & $V(rxd)$:** Perfectly aligned $0\text{ V}$ to $5\text{ V}$ square waves showcasing zero phase lag.
* **$V(canh)$ & $V(canl)$:** The classic differential "mirror effect" oscillating between the quiet $2.5\text{ V}$ baseline up to $3.0\text{ V}$ and down to $2.0\text{ V}$ during dominant transmissions.

