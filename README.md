# Pocket USB DAC Headphone Amp

# USB DAC + Baxandall Tone Control Headphone Amp  
**Checklist / Build Plan**

make sure that resistors are thin film, thich film resistors can introduce distortion
Chose electrolytic caps in the signal path as long as they are large enough, if chosing ceramic (sepecially in the signal path) chose type cog, otehrwise most ceramic caps will have piezoelectric effects and will introduce distortion 
bypass caps can be pretty much any type

For voltage ratings, useually go with double of what the expected DC voltage is
---

## Phase 0 — Lock the architecture
- [ ] Use **Phil’s USB headphone amplifier** as the base design
  - [ ] USB-C power + data
  - [ ] USB audio codec (PCM290x family)
  - [ ] DAC output buffer / reconstruction filter
  - [ ] Volume control stage
  - [ ] Output/current-drive stage to headphone jack
- [ ] Insert **TI Baxandall bass/treble tone control** as a line-level block
  - [ ] Signal flow:  
    **Phil DAC buffer → TI Baxandall tone → Phil volume → Phil output stage**
- [ ] Decide on **single master volume**
  - [ ] Use Phil’s volume control only
  - [ ] Bypass TI’s R1/R2 volume pots

---

## Phase 1 — KiCad schematic integration
- [ ] Create a **combined KiCad schematic**
  - [ ] Start from Phil’s schematic as the backbone
  - [ ] Add TI Baxandall tone block between Phil buffer output and Phil volume input
  - [ ] Keep TI input buffers initially (lower risk)
- [ ] **Bypass TI R1/R2 (volume pots)**
  - [ ] Remove R1/R2 from the TI tone schematic
  - [ ] Feed TI input buffer directly from the coupling cap
  - [ ] Add a DC bias path so the node does not float
  - [ ] (Optional) Add footprints / jumpers to re-enable pots if needed
- [ ] **Resolve virtual ground vs real ground**
  - [ ] Identify Phil’s analog reference / bias node (half-rail if single-supply)
  - [ ] Tie all TI “mid” nodes to Phil’s analog reference
  - [ ] Do NOT generate a separate TI virtual ground if Phil already provides one
- [ ] Perform a **sanity check**
  - [ ] Verify signal levels between stages
  - [ ] Ensure tone boost will not clip internal op-amps
  - [ ] Ensure compatibility with Phil’s volume stage input impedance

---

## Phase 2 — BOM & sourcing
- [ ] Create a **BOM from the combined schematic**
  - [ ] ICs (codec, op-amps, regulators)
  - [ ] Precision resistors where required
  - [ ] Capacitors (type + voltage rating)
  - [ ] Dual-gang pots for bass/treble
  - [ ] USB-C connector, headphone jack
  - [ ] Protection parts (ESD diodes, fuses, ferrites)
- [ ] Align BOM with **JLCPCB / LCSC availability**
  - [ ] Decide which ICs are hand-soldered
  - [ ] Mark which passives are PCBA-assembled
- [ ] Order parts
  - [ ] Order extras for iteration (pots, op-amps, passives)

---

## Phase 3 — Build & test
- [ ] Bring-up in stages
  - [ ] Test Phil’s base circuit (USB enumeration + clean audio)
  - [ ] Enable TI Baxandall tone block
  - [ ] Verify flat response at center
  - [ ] Verify bass/treble behavior
- [ ] Full system test
  - [ ] Verify volume control behavior
  - [ ] Test with headphones
  - [ ] Listen for noise, clipping, oscillation

---

## Phase 4 — Rev A refinement
- [ ] Adjust Baxandall network for **subtle** tone range if needed
- [ ] Improve decoupling/layout if noise or instability appears
- [ ] Finalize schematic and prepare for PCB layout / PCBA




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


