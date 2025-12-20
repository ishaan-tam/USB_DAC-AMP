# Pocket USB DAC Headphone Amp

A compact, USB-powered headphone amplifier built around a **class-compliant USB audio codec**, with an **analog signal path**, **physical volume control**, and room to add **Baxandall bass/treble tone shaping** in later revisions.

This project prioritizes:
- **Fun, hands-on analog audio**
- **Simple, driverless USB DAC operation**
- **Incremental development** (breadboard → PCB Rev A → Rev B tone control)

Not meant to be “audiophile-perfect,” but absolutely meant to be **good enough that I actually want to use it**.

---

## Project Status

- **Current phase:** Rev 0 — Breadboard prototype
- **Goal of Rev 0:**  
  - USB device enumerates as an audio output  
  - Audio plays cleanly through headphones  
  - Volume control works  
- **Future phases:**  
  - Rev A PCB (compact, USB-C, integrated headphone stage)  
  - Rev B (add Baxandall bass/treble tone control)

---

## High-Level Architecture

USB-C
│
├─ USB 2.0 D+/D−
│
PCM2906C USB Audio Codec
│
├─ DAC L/R (line-level)
│
Buffer + Reconstruction Filter
│
(Rev B) Baxandall Tone Control (Bass / Treble)
│
Volume Control
│
Headphone Output Stage
│
3.5 mm Headphone Jack

---

## Key Design Decisions

### USB Audio Codec
- **TI PCM2906C**
  - USB Audio Class 1 (driverless)
  - Plug-and-play on **Windows, macOS, Linux**
  - Likely works on **USB-C iPhones** (with power constraints)
  - Chosen over older PCM2900 due to **availability / active status**

S/PDIF pins are **not used** in this project.

---

### Power Strategy
- **Rev 0 (breadboard):**  
  - USB 5 V only  
  - No boost converter  
- **Rev A (PCB):**  
  - USB 5 V → boost → clean analog rail (12–15 V)  
  - Boost is for **headroom**, not “audio amplification”

---

### Analog Audio Philosophy
- Keep the **DAC isolated** from headphone loads
- Use **buffers** and **volume control** before the output stage
- Tone control is **optional and playful**, not surgical EQ
- If “serious EQ” is needed → do it digitally in software

---

### Tone Control Plan
- **Baxandall bass/treble** (2 knobs)
  - Musical, forgiving
  - Simple, stable, well-understood
- Will be added in **Rev B**
- Rev A PCB should reserve insertion points for it

---

## Breadboard Prototype (Rev 0)

### What Rev 0 Includes
- PCM2906C wired per TI **Typical Application Circuit**
- 12 MHz crystal + required decoupling
- USB-C input (USB 2.0 only)
- Line-level L/R output
- External headphone amp module **or** simple buffer + volume pot
- Goal: **“I hear sound and can control volume”**

### What Rev 0 Intentionally Avoids
- Boost converters
- Parallel op-amps
- Tone controls
- EMI optimization
- Fancy layouts

Rev 0 is about **function, not polish**.

---

## Headphone Output (Rev 0 Approach)

For breadboarding, the headphone stage is intentionally simplified:

### Options
1. **External headphone amp module**
   - Accepts line-level L/R
   - Powered from 5 V
   - Drives headphones safely
2. **DIY buffer + volume + output stage**
   - Unity-gain buffer
   - Passive volume pot
   - Headphone-capable driver

Modules are preferred for Rev 0 to avoid stability issues on a breadboard.

---

## Compatibility Expectations

- **Windows:** ✅ Plug-and-play
- **macOS / Linux:** ✅ Plug-and-play
- **USB-C iPhone:** ⚠️ Likely works, power-dependent
- **Lightning iPhone:** ⚠️ Requires camera adapter

---

## Repository Structure

pocket-usb-dac-amp/
├─ README.md
├─ docs/
│ ├─ requirements.md
│ ├─ architecture.md
│ ├─ bringup-notes.md
│ └─ images/
├─ hardware/
│ ├─ rev0_breadboard/
│ │ ├─ wiring/
│ │ └─ bom/
│ │ └─ bom_rev0.xlsx
│ ├─ revA_pcb/
│ │ ├─ kicad/
│ │ ├─ outputs/
│ │ └─ bom/
│ └─ revB_tonecontrol/
├─ tools/
│ └─ bom/
└─ CHANGELOG.md


---

## BOM Management

- BOMs are maintained in **Excel**
- Each revision has its own BOM:
  - `bom_rev0.xlsx`
  - `bom_revA.xlsx`
- BOM includes:
  - Manufacturer + MPN
  - Supplier + SKU
  - Unit cost
  - Alternates
  - Notes (DNP, experimental, etc.)

KiCad-exported CSVs are treated as **derived artifacts**, not the source of truth.

---

## Development Philosophy

- Build **one working version at a time**
- Breadboard first, PCB later
- Prefer **known-good reference circuits**
- Avoid “clever” designs that increase debug time
- Leave room for iteration and learning

---

## Non-Goals (Important)

- Not chasing ultra-low THD numbers
- Not competing with commercial DACs
- Not building a DSP playground
- Not optimizing for mass production

This is a **learning + enjoyment project**, not a product launch.

---

## Next Immediate Steps

1. Finish Rev 0 breadboard wiring
2. Confirm USB enumeration + audio playback
3. Document bring-up issues and fixes
4. Decide:
   - integrated output stage vs module
5. Begin Rev A schematic capture


