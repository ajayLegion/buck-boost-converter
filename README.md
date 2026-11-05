# Closed-Loop Regulated Inverting Buck–Boost Converter (12V to -5V...-15V)

A power electronics design and LTspice simulation project for a closed-loop current-mode inverting buck–boost DC-DC converter. Designed to deliver a tightly regulated $-5\text{ V}$ to $-15\text{ V}$ output from an unregulated/fixed $+12\text{ V}$ DC supply across a $10:1$ dynamic load range ($0.2\text{ A}$ to $2.0\text{ A}$).

---

## 📌 Project Overview

* **Input Voltage ($V_{in}$):** $12\text{ V}$ DC
* **Output Voltage Range ($\vert{}V_{out}\vert{}$):** $5\text{ V}$ to $15\text{ V}$ (covers both Buck and Boost operation)
* **Variable Load Range ($I_{out}$):** $0.2\text{ A}$ to $2.0\text{ A}$ ($30\text{ W}$ max power stage)
* **Switching Frequency ($f_{sw}$):** $200\text{ kHz}$ ($T_s = 5\ \mu\text{s}$)
* **Target Ripple:** $\Delta V_{out} \le 1\%$ ($\le 150\text{ mV}_{\text{pp}}$ at $15\text{ V}$)
* **Topology:** Inverting Buck–Boost (Inductive Energy Transfer) with High-Side NMOS Switch
* **Control Architecture:** Cascaded Average Current-Mode Control with Soft-Start Reference

---

## ⚙️ Power Stage Design & Sizing

### 1. Duty Cycle Calculation
In Continuous Conduction Mode (CCM), accounting for parasitic drops ($V_D \approx 0.45\text{ V}$, $V_{sw} \approx 0.15\text{ V}$):

$$D_{\text{real}} = \frac{\vert{}V_{out}\vert{} + V_D}{V_{in} - V_{sw} + \vert{}V_{out}\vert{} + V_D}$$

* **At $5\text{ V}$ (Buck):** $D \approx 0.315$ ($31.5\%$)
* **At $12\text{ V}$ (Transition):** $D \approx 0.512$ ($51.2\%$)
* **At $15\text{ V}$ (Boost):** $D \approx 0.566$ ($56.6\%$)

### 2. Critical Inductance ($L_{\text{crit}}$) & Inductor Selection
To ensure the converter operates in **Continuous Conduction Mode (CCM)** down to the minimum load current of $I_{out,\min} = 0.2\text{ A}$:

$$L_{\text{crit}} = \frac{V_{in} \cdot D \cdot (1 - D)}{2 \cdot f_{sw} \cdot I_{out,\min}}$$

The factor $D(1 - D)$ peaks at $D = 0.50$, yielding a maximum critical inductance threshold:

$$L_{\text{crit}} = \frac{12 \times 0.25}{2 \times 200,000 \times 0.2} = 37.5\ \mu\text{H}$$

* **Selected Value:** $L_1 = 47\ \mu\text{H}$ ($R_{\text{ser}} = 15\text{ m}\Omega$, $R_{\text{par}} = 5\text{ k}\Omega$).
* **Margin:** $+25.3\%$ above $L_{\text{crit}}$, guaranteeing CCM across all operating points.

### 3. Output Filter Capacitor ($C_{out}$)
Sized for discontinuous diode commutation current and capacitor ESR:

$$C_{out} = 100\ \mu\text{F} \quad (R_{\text{ESR}} = 10\text{ m}\Omega)$$

---

## 🛠️ LTspice Component Selection

All components utilize built-in or explicitly defined standard SPICE physics models:

| Component | Designator | Specification / Model | Notes |
| :--- | :--- | :--- | :--- |
| **Power MOSFET** | `M1` | `IRF1405` | $55\text{ V}$, $5.3\text{ m}\Omega$ VDMOS |
| **Freewheeling Diode** | `D1` | `MBR745` | $45\text{ V}$, $7.5\text{ A}$ Schottky Rectifier |
| **Main Inductor** | `L1` | $47\ \mu\text{H}$ ($R_{\text{ser}} = 15\text{ m}\Omega$, $R_{\text{par}} = 5\text{ k}\Omega$) | High-frequency damping included |
| **Output Capacitor** | `Cout` | $100\ \mu\text{F}$ ($R_{\text{ser}} = 10\text{ m}\Omega$) | Low-ESR polymer/ceramic equivalent |
| **Input Capacitor** | `Cin` | $47\ \mu\text{F}$ ($R_{\text{ser}} = 15\text{ m}\Omega$) | Input bus stabilization |
| **Gate Resistor** | `Rgate` | $10\ \Omega$ | Prevents gate ringing & simulator stalls |
| **Bleed Resistor** | `Rbleed` | $10\text{ k}\Omega$ | Minimum DC bleed load |

---
