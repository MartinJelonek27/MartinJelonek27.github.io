---
layout: post
title: Turbofan Compressor Blade Stress Analysis
description: Checked whether a titanium compressor blade survives 45,000 RPM. The stress is fine, but spinning stiffens the blade until it resonates just below design speed.
skills:
- Python
- Vibration Analysis
- Stress Analysis
- Finite Element Method
main-image: /engine_cutaway.webp
---

## Overview

A compressor blade in a jet engine spins tens of thousands of times a minute. I checked whether a small titanium blade could survive 45,000 RPM, looking at both the steady pull from spinning and the vibration that builds up if the blade resonates.

## Approach

- Modeled the blade as a tapered, spinning beam in Python: Ti-6Al-4V, 60 mm long, 12.4 g.
- Calculated the centrifugal stress at the hub, where the blade attaches.
- Found the bending and torsion frequencies at every speed from 0 to 50,000 RPM and plotted them on a Campbell diagram against the engine order lines (once per revolution, twice per revolution, and so on).
- Used the Goodman relation to find how much vibration the blade can take on top of the steady stress.

## Challenge and insight

My first check used the blade's natural frequencies with the rotor standing still. The first bending mode sat at 534 Hz, and its nearest crossing was 29% below design speed, so the blade looked safe.

The realization came while I was playing my roommate's guitar. Tightening a tuning peg raised the note even though the string itself never changed. A spinning blade works the same way: the faster it spins, the harder it gets pulled outward, like a string being tightened. Once I added that pull to my model, the first bending frequency nearly tripled to 1,494 Hz at design speed. That is almost exactly twice the rotation speed (1,500 Hz), so the blade would resonate right where the engine spends most of its time.

## Result

| Check | Result |
|---|---|
| Centrifugal stress at the hub | 510 MPa, a safety factor of 1.7 on yield |
| Room left for vibration | 192 MPa |
| Resonance | 1st bending meets 2x rev at 43,700 RPM, 2.8% below design speed (10% is a common margin) |
| Fix | A 14% thicker blade moves the resonance to 49,800 RPM, adding 1.7 g without raising the hub stress |

![Campbell diagram of the blade's frequencies against engine speed](/_projects/Compressor%20Blade%20Stress%20Analysis/campbell_diagram.svg)

The gray lines are engine orders. Where a blade frequency crosses one, the blade resonates. The red dot is the problem.

## Things to know

- **This is a beam model, not FEA.** The real airfoil shape, twist, and angle would shift the frequencies, so the exact crossing speed should be confirmed with a modal FEA.
- **Flutter was not checked.** Flutter needs an aeroelastic analysis that includes the airflow, which a beam model can't do.
- **The 3D model is not mine.** The engine in the render was downloaded online (credit below). My work is the analysis, which uses simplified blade dimensions rather than measurements from the model.

## Sources

1. ASM Aerospace Specification Metals, [*Ti-6Al-4V (Grade 5), Annealed*](https://asm.matweb.com/search/specificmaterial.asp?bassnum=mtp641) datasheet.
2. R. G. Budynas and J. K. Nisbett, *Shigley's Mechanical Engineering Design*, McGraw-Hill, Chapter 6.
3. J. S. Rao, *Turbomachine Blade Vibration*, New Age International.
4. W. Campbell, "The Protection of Steam-Turbine Disk Wheels from Axial Vibration," *Transactions of the ASME*, vol. 46, 1924.

*3D model: ["JET ENGINE"](https://grabcad.com/library/jet-engine-22) by Nithya Nanthan, GrabCAD.*
