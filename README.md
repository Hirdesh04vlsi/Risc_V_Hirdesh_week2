# Week 2 – Part 1: SoC Fundamentals & Functional Modelling

## 1. What is a System-on-Chip (SoC)?

A **System-on-Chip (SoC)** is a single integrated circuit that combines all the major components of a computing system into one chip.
Instead of having a separate processor, memory, and peripherals on different ICs connected by a PCB, SoC design integrates everything into a single silicon die.

A typical SoC includes:

* **CPU (Central Processing Unit)** – the core processor that executes instructions and runs programs.
* **Memory** – on-chip SRAM, DRAM controllers, or caches to store data and instructions.
* **I/O Interfaces** – GPIOs, UART, SPI, I2C, USB, etc., that enable communication with the external world.
* **Special modules** – GPU for graphics, DSP for signal processing, wireless modules like WiFi, Bluetooth, NFC, and even hardware accelerators for AI or cryptography.
* **Interconnect** – bus fabric or network-on-chip for connecting all modules.
* **Clock and Power Management** – PLLs for stable clocking, regulators for dynamic power scaling.

In modern nodes like **Sky130**, both digital and analog IPs can be integrated in a single SoC, making it suitable for **mixed-signal systems**.

---

<img width="1200" height="630" alt="image" src="https://github.com/user-attachments/assets/6e84a5cb-8a14-4c95-bc55-b19121513aa9" />


## 2. Why use an SoC?

The motivation for integrating everything into one chip comes from several advantages:

* **Space saving** – eliminates the need for multiple ICs on PCB.
* **Reliability** – fewer off-chip connections reduce chances of failure.
* **Energy efficiency** – on-chip communication consumes much less power than off-chip signaling.
* **High performance** – tighter integration reduces latency and increases bandwidth.
* **Cost effectiveness** – once designed, SoCs lower the overall system cost by reducing packaging and assembly expenses.

**Applications:**
SoCs dominate almost all modern electronic systems. They are used in:

* **Consumer electronics** – smartphones, tablets, smart TVs, wearables, IoT devices.
* **Automotive** – for ADAS, infotainment, powertrain control.
* **Industrial/embedded systems** – automation controllers, medical devices, robotics.

**Examples of commercial SoCs:** Apple A-series, Qualcomm Snapdragon, Samsung Exynos, Google Tensor.



---

## 3. Challenges in SoC Design

While SoCs bring great benefits, they also come with serious challenges:

* **Complexity** – integrating CPU, memory, DSP, GPU, power modules, and analog blocks requires multi-domain expertise.
* **Heat issues** – high density of transistors causes thermal management problems.
* **Flexibility** – unlike modular systems (FPGA + external chips), SoCs are less reconfigurable; redesign means a new chip.
* **Verification difficulty** – validating the entire system at chip level is very challenging.

---

## 4. Types of SoCs

SoCs can be broadly classified as:

* **Microcontroller-based SoCs** – small SoCs with CPU, memory, and basic peripherals; used in IoT and embedded devices.
* **Microprocessor-based SoCs** – larger SoCs with advanced CPUs, GPU, external memory support, and multimedia capability; used in smartphones and PCs.
* **ASIC-based SoCs** – application-specific designs optimized for a particular task (e.g., AI accelerators, networking, cryptographic engines).

---

## 5. Why BabySoC?

Designing and understanding a real industrial SoC can be extremely complex. To simplify the learning process, **BabySoC** has been created as a teaching model.

* It is a **scaled-down SoC** that captures the essence of SoC architecture.
* It focuses only on **three main components**: a CPU core (RVMyth), a PLL for clock generation, and a 10-bit DAC for analog output.
* This reduced complexity allows students to:

  * Understand the architecture of SoCs (CPU, memory, interconnect, peripherals).
  * Experiment with system behavior at a manageable scale.
  * Learn how functional modelling is done before implementing RTL or physical design.

Because BabySoC has fewer components and simpler interconnects, it is easier to analyze data flow, timing, and integration of modules.

---

## 6. BabySoC Overview (VSDBabySoC)

The **VSDBabySoC** integrates **three Intellectual Property (IP) blocks**:

1. **RVMyth Processor**

   * A simple RISC-V based processor.
   * Implements the core computing capability of BabySoC.
   * Contains a register file (32 registers), ALU, and control logic.
   * Acts as the **main CPU** of BabySoC.

2. **PLL (Phase Locked Loop)**

   * A critical circuit for **clock generation**.
   * Provides a stable clock to the processor and DAC.
   * Avoids dependence on off-chip crystal oscillators (which are prone to jitter, skew, frequency variations, and aging).
   * Important because modern SoCs often require multiple frequencies derived from one master clock.
  


3. **10-bit DAC (Digital-to-Analog Converter)**

   * Converts **digital output** of RVMyth into an **analog signal**.
   * Enables BabySoC to interface with the analog world.
   * Example usage: generating waveforms, sensor interfacing, or audio output.
   * Supports basic mixed-signal functionality.


---


## 7. Clocking in BabySoC: Why Off-Chip Clocks Are Avoided & PLL Usage

In a typical SoC, timing is critical because all modules rely on a stable clock. Using **off-chip clocks** (like a crystal oscillator directly) introduces several issues:

**Problems with off-chip clocks:**

1. **Clock skew** – the clock may arrive at different modules at slightly different times due to PCB traces and routing differences.
2. **Jitter** – small timing variations in the clock edges can cause instability in sequential circuits.
3. **Frequency limitations** – a single crystal frequency may not be sufficient for multiple modules needing different operating frequencies.
4. **Crystal frequency deviations** – manufacturing tolerances can cause the crystal to run slightly faster or slower than specified.
5. **Stability & tolerance issues** – temperature variations and environmental factors affect off-chip clock accuracy.
6. **Aging effects** – crystal characteristics can drift over time, impacting long-term reliability.

Because of these challenges, **BabySoC uses an on-chip Phase-Locked Loop (PLL)** to generate stable clock signals internally.

---

## 8. On-Chip PLL: Working Principle

A **PLL (Phase-Locked Loop)** is an electronic feedback system that locks the output clock frequency and phase to a reference input.

**Components of a PLL:**

1. **Phase Detector (PD)** – compares the phase of input reference clock with feedback clock.
2. **Loop Filter (LF)** – smooths the phase error signal.
3. **Voltage-Controlled Oscillator (VCO)** – generates an output clock whose frequency is controlled by the filtered signal.
4. **Feedback Path** – divides and feeds back the output clock to the phase detector.

**Working:**

* The PLL continuously adjusts the VCO to match the reference clock phase and frequency.
* Multiple derived frequencies can be generated from a single PLL by using dividers or multipliers.
* This ensures **stable, low-jitter, and synchronized clocks** for all modules in BabySoC.

**Illustration:**

 <img width="327" height="146" alt="image" src="https://github.com/user-attachments/assets/51d5828b-3edd-4a05-84df-c0d72f1d3283" />
 
**Benefits of using PLL in BabySoC:**

* Eliminates reliance on off-chip crystal stability.
* Provides multiple clock frequencies for CPU, DAC, and peripherals.
* Reduces timing errors due to skew or jitter.
* Improves reliability and repeatability across process, voltage, and temperature variations.

---

## 9. Digital-to-Analog Converter (DAC) in BabySoC

The **10-bit DAC** in BabySoC converts digital signals from the CPU into analog voltage output. This enables interfacing with sensors, audio outputs, or waveform generators.

**Working Principle:**

* Takes a **digital input code** (0–1023 for 10 bits).
* Converts it into a proportional **analog voltage** using resistor ladders (R-2R), current steering, or charge scaling.
* Output voltage ( V_{out} = V_{ref} \times \frac{Digital,Code}{2^{N}-1} ) where N = 10.

**Types of DACs:**

1. **Resistor-String DAC** – uses a chain of resistors. Simple but slower for high-speed applications.
2. **R-2R Ladder DAC** – uses a binary-weighted resistor ladder. Efficient and widely used.
3. **Current-Steering DAC** – converts digital input into proportional currents. Fastest and used in high-speed applications.
4. **Sigma-Delta DAC** – oversampling and noise-shaping for high-resolution output.

**BabySoC uses a simple 10-bit DAC** (usually R-2R ladder) for teaching mixed-signal concepts.

**Illustration:**


<img width="1280" height="720" alt="image" src="https://github.com/user-attachments/assets/57019091-f94c-47c9-8451-ffd8ec514321" />


**Integration with PLL:**

* DAC output is often clocked to synchronize with CPU operations.
* Stable PLL-generated clocks ensure that digital-to-analog conversion occurs precisely in time, avoiding glitches in output waveform.

---


## 10. Functional Modelling Before RTL / Physical Design

Before implementing the design at gate level, **functional modelling** is performed.

**Functional modelling focuses on:**

* Describing **intended behavior** at an abstract level.
* Using behavioral models (C/C++, SystemC, Verilog behavioral code).
* Validating **correctness, control flow, data flow, and module interactions**.
* Checking edge cases such as bus arbitration, interrupt handling, and timing dependencies.

**Why functional modelling is essential:**

* Catches design flaws early before committing to RTL or silicon.
* Provides a **blueprint** for RTL developers.
* Saves cost and time by reducing re-spins of hardware.

**Flow after modelling:**

1. Functional model →
2. RTL design (Verilog/VHDL) →
3. Logic synthesis & timing closure →
4. Place & route →
5. Physical verification & fabrication.

Thus, functional modelling bridges **conceptual design** and **hardware implementation**.

---

## 11. Conclusion

SoCs integrate all essential components into one chip, enabling compact, efficient, and powerful electronic systems.
While they pose challenges in terms of complexity and thermal management, their advantages make them the heart of modern electronics.

The **VSDBabySoC** is a simplified SoC consisting of RVMyth (CPU), PLL (clock generator), and a 10-bit DAC (analog output).
By learning functional modelling of BabySoC, students gain a strong foundation in SoC concepts before diving into RTL and physical design.

---
