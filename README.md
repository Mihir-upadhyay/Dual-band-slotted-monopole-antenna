# Dual-Band Hilbert Fractal Slotted Monopole Antenna (HFSS)

A replicated simulation of a miniaturized CPW-fed circular monopole antenna built in Ansys HFSS. The design incorporates a Hilbert fractal slot and a Defected Ground Structure (DGS) to operate across dual bands for WLAN and WiMAX applications. Performance metrics such as S-parameters, gain, and surface currents are evaluated and compared against the original 2023 research paper by Benkhadda et al.

## Features

- **Dual-Band Operation:** Covers WLAN (2.4-2.5 GHz, 3.6 GHz, 4.9-5.9 GHz) and WiMAX (2.5, 3.5, 5.5 GHz) applications.
- **Miniaturization:** Uses a Hilbert fractal slot acting as a long current path to achieve lower frequency resonance on a small footprint.
- **Wide Upper Band:** Defected Ground Structure (DGS) slots on the ground plane widen the upper frequency band (2.7 to 6.4 GHz).
- **Radiation Characteristics:** Bidirectional (figure-eight) in the E-plane and nearly omnidirectional in the H-plane.

## Model Materials Required

| Component | Specification |
|---|---|
| Substrate | FR-4 (or Rogers RO4003C for optimization) |
| Board Dimensions | $24 \times 34 \times 1.6$ mm |
| Feed | Coplanar Waveguide (CPW) - 50 Ω |
| Port | SMA connector model |

## Design Parameters

| Parameter | HFSS Model Dimension |
|---|---|
| Substrate (Wsub x Lsub) | $24 \times 34$ mm |
| Substrate Thickness (h) | 1.6 mm |
| Circular Patch Radius (a) | 10.5 mm |
| Feed Line (Wf x Lf) | $3 \times 12.5$ mm |
| Feed-to-Ground Gap (g) | 0.5 mm |
| Ground Plane Length (Lg) | 10 mm |
| Hilbert Segment Length (dd)| 3.1 mm (Iteration 3 & 4) / 3.5 mm (Iteration 2)|
| Hilbert Slot Line Width (S)| 0.9 mm |

Build the model on a single side of the substrate since the patch, CPW feed line, and ground planes are all co-planar.

## Software Requirements

- [Ansys HFSS](https://www.ansys.com/products/electronics/ansys-hfss) (2025 R2.4 Student version used in this replication)
- Original paper simulated using CST and CADFEKO.

## Simulation Setup

1. Create your geometry model in the HFSS 3D Modeler.
2. Define the Air Box (Radiation boundary):
   - Set to $88 \times 72 \times 65.6$ mm (approx. 32 mm padding on every side, $\lambda/4$ at 2.45 GHz).
3. Solution Setup:
   - **Type:** Driven modal
   - **Adaptive Meshing:** Multi-frequency at 2.45, 3.6, and 5.7 GHz
   - **Convergence:** Max Delta S = 0.02, up to 15 passes
4. Frequency Sweep:
   - **Type:** Interpolating
   - **Range:** 2 to 6.5 GHz, 0.01 GHz step (451 points)

## Configuration

Set up your substrate material properties. If using standard FR-4, configure your dielectric as follows:

```text
Material: FR4_epoxy
Relative Permittivity (εr): 4.4
Dielectric Loss Tangent (tan δ): 0.025
```

The basic circular patch without slots is calculated using the cavity model for the TM11 mode:

```math
f_r = \frac{c \cdot 1.84118}{2\pi a_{eff}\sqrt{\varepsilon_r}}
```

> **Design note:** Substrate properties heavily impact resonant frequencies. If you plan to optimize this for 5G Sub-6 GHz testing, create a secondary configuration using Rogers RO4003C (Permittivity: 3.55, Loss Tangent: 0.0027).

## Simulate and Run

1. Select **Analyze All** under the Simulation tab.
2. Allow the multi-frequency adaptive meshing to complete.
3. Open the **Results** tab to plot the Rectangular Plot for `S11` (Reflection Coefficient).
4. Generate 3D Polar Plots to view the Total Gain radiation patterns at 2.45, 3.6, and 5.8 GHz.

## How It Works

**Resonance Mechanisms**

- **2.45 GHz (Lower Band):** The surface current is highly concentrated on the feed line and around the edges of the Hilbert fractal slot. The slot acts as an extended current path, artificially lowering the resonant frequency for miniaturization.
- **3.6 GHz (Middle Band):** Strong current distribution on the CPW feed line and inner ground edges near the feed. Coupling here produces the middle resonance.
- **5.8 GHz (Upper Band):** Current spreads over the main circular patch, the feed, the ground, and heavily around the DGS slots, which act to merge multiple adjacent resonances to create a wide upper band (3 to 6.4 GHz).

**Radiation**

The antenna radiates similarly to a traditional monopole: omnidirectional uniformly in the H-plane (XZ-plane) and bidirectionally with a figure-eight shape in the E-plane (YZ-plane).

## Troubleshooting

- **Missing 2.45 GHz Dip:** The lower band is hyper-sensitive to the Hilbert slot dimensions. If the dip is missing (e.g., S11 is only -6dB), ensure the Hilbert segment length (`dd`) is set to 3.5 mm instead of 3.1 mm. A shorter slot shifts the resonance to a higher frequency.
- **Resonance Shifts (Upper Band):** The DGS slots control the upper band. The width `wd` (assumed 0.5 mm) or position of these slots can shift the upper dip by 0.2 GHz or more. Adjust the ground length (`Lg`) to tune this.
- **Lower Gain than Paper:** Real-world matching losses or SMA connector modeling in HFSS (which the original paper may have omitted) can reduce peak gain. An unoptimized return loss will also drop overall simulated gain. 
- **Basic Circle Resonating at wrong frequency:** Check your effective radius calculation. A 10.5 mm radius in FR-4 should resonate near 3.3 GHz, not the 4.5 GHz stated in some reference material unless scaled.

## Possible Improvements

- Set up a Parametric Sweep on the slot line width `S` (from 0.8 to 1.1 mm) to fine-tune the 2.45 GHz dip.
- Sweep ground length `Lg` (9.5 to 10.5 mm) to improve matching and deepen the resonances at 3.6 and 5.8 GHz.
- Test Rogers RO4003C substrate to evaluate improvements in dielectric loss and bandwidth.

## License

Released under the MIT License. Feel free to use and modify.  
