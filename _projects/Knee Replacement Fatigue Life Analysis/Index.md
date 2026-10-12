---
layout: post
title: Titanium Knee Implant Fatigue Life Analysis
description: My dad had a knee replacement recently and I wanted to see how it holds up. With healthy bone under it, the metal tray never wears out. If the bone under one side is lost, going down stairs starts to use up its life.
skills:
- Python
- Fatigue Analysis
- Finite Element Method
- Biomechanics
main-image: /knee_xray.webp
---

## Overview

A knee replacement puts a thin titanium plate, the tibial tray, on top of the shin bone. It holds the plastic insert the knee moves on and gets loaded with every step. I checked whether a year of everyday activity slowly wears the tray out through fatigue, both with healthy bone under it and with the bone under one side worn away, which is the worst case used in the standard lab test.

## Approach

- Used the same method as my [hip implant analysis](/projects/Hip%20Replacement%20Fatigue%20Life%20Analysis/index/): Ti-6Al-4V with an endurance limit of 311 MPa at 99% reliability, the Goodman relation for mean stress, and Miner's rule to add up the damage.
- Took the force on the inner side of the knee from patients with instrumented knee implants: about 2 times body weight when walking and 2.4 times when going down stairs.
- Built a year of activity from a study that tracked 31 hip replacement patients through a normal day at home and counted a median of 6,048 steps and 164 stairs (Morlock et al., 2001). I added 60 sit-to-stands a day from a study of 140 adults (Dall and Kerr, 2010), for 2.3 million load cycles a year.
- Calculated the bending stress in a generic tray (70 x 46 mm, 3.5 mm thick) in Python for two cases: healthy bone under the whole tray, and bone lost under the inner half.

## Challenge and insight

My first model treated the tray like a diving board, with the inner half hanging off the middle and nothing under it. That is how the standard lab test holds it. With real daily loads, the answer swung wildly: at 80 kg the tray lasted about 100 years, but at 90 kg only 3 years. That didn't match reality, since tray fractures are rare.

The insight: in a healthy knee the tray isn't a diving board, it's a board lying on a mattress. The bone pushes back along the whole underside, so the tray can't bend much. Once I modeled the bone as a bed of springs, the bending dropped by a factor of 3.7 and every activity fell well below the fatigue limit. The diving board case still matters, because it is what happens if the bone under one side wears away, and that is how real trays have broken.

## Result

| Check | Result |
|---|---|
| Healthy bone under the tray | No fatigue damage. Going down stairs, the worst activity, uses less than a quarter of the fatigue strength |
| Standard lab test (900 N, inner half unsupported) | Passes 10 million cycles with no damage |
| Bone lost under the inner half, 80 kg | Right at the limit: about 100 years of life |
| Bone lost under the inner half, 90 kg | About 3 years of life |
| Going down stairs (bone lost) | 1.3% of the cycles, but 100% of the damage |

![Goodman diagram of each activity, with and without bone support](/_projects/Knee%20Replacement%20Fatigue%20Life%20Analysis/goodman_diagram.svg)

Each dot is one activity. Hollow dots have healthy bone under the tray, and filled dots have bone lost under the inner half. Only going down stairs without bone support crosses the line.

## Things to know

- **This is a generic implant, not my dad's.** I don't know the exact model or size of his implant, so I used typical tray dimensions and an 80 kg patient.
- **The model is simple.** The tray is a flat beam with a stress concentration of 1.5 where it meets the central stem. A real tray has ribs, a locking rim, and a curved outline, so the exact stresses would need FEA. Without bone support, small changes matter a lot: a 3.0 mm tray lasts a few months, and a 4.0 mm tray never wears out.
- **The activity counts are assumptions.** They come from studies of hip replacement patients and healthy adults, and I counted every step and stair as a load on the replaced knee, which is on the safe side.

## Sources

1. A. Trepczynski, I. Kutzner, G. Bergmann, W. R. Taylor, and M. O. Heller, ["Peak knee adduction moments and medial contact forces: relations across subjects and activities"](https://media.isbweb.org/images/conferences/isb-congresses/2013/oral/ss-knee.06.pdf), International Society of Biomechanics Congress, 2013.
2. M. Morlock et al., "Duration and frequency of every day activities in total hip patients," *Journal of Biomechanics*, vol. 34, no. 7, pp. 873-881, 2001.
3. P. M. Dall and A. Kerr, "Frequency of the sit to stand task: an observational study of free-living adults," *Applied Ergonomics*, vol. 41, no. 1, pp. 58-61, 2010.
4. ASTM F1800, [*Standard Practice for Cyclic Fatigue Testing of Metal Tibial Tray Components of Total Knee Joint Replacements*](https://store.astm.org/f1800-19.html), and ZwickRoell, [*Fatigue tests on the tibial plateau to ASTM F1800 and ISO 14879*](https://www.zwickroell.com/en/medical/biomechanics-orthopedics/knee-implants) (900 N test load).
5. F. Panagopoulos et al., "Catastrophic Tibial Baseplate Fracture in the TC-PLUS Total Knee System: A Case Report and Literature Review," *Cureus*, vol. 17, no. 10, e94665, 2025.
6. Carpenter Technology, [*Titanium Alloy Ti 6Al-4V ELI* medical datasheet](https://www.carpentertechnology.com/hubfs/Data%20Sheets/20210902--CT_Ti64ELI_Medical_Datasheet_F.pdf).
7. R. G. Budynas and J. K. Nisbett, *Shigley's Mechanical Engineering Design*, McGraw-Hill, Chapter 6.
8. M. Hetényi, *Beams on Elastic Foundation*, University of Michigan Press, 1946, and A. B. Vesić, "Bending of beams resting on isotropic elastic solid," *Journal of the Engineering Mechanics Division*, ASCE, vol. 87, 1961.

*Cover image: cropped from ["Medical X-Ray imaging UOI07 nevit"](https://commons.wikimedia.org/wiki/File:Medical_X-Ray_imaging_UOI07_nevit.jpg) by Nevit Dilmen, [CC BY-SA 3.0](https://creativecommons.org/licenses/by-sa/3.0/), via Wikimedia Commons. It shows a natural knee next to a replaced knee, and it is not my dad's X-ray.*
