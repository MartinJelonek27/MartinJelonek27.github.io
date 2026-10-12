---
layout: post
title: Titanium Hip Implant Fatigue Life Analysis
description: Checked whether a titanium hip stem survives everyday life, not just the standard lab test. It passes the ISO test, but rare stumbles wear it out after about 0.9 million load cycles.
skills:
- Python
- Fatigue Analysis
- FEA
- Biomechanics
main-image: "https://upload.wikimedia.org/wikipedia/commons/thumb/4/47/Hip_joint_replacement%2C_United_States%2C_1998_Wellcome_L0060175.jpg/1280px-Hip_joint_replacement%2C_United_States%2C_1998_Wellcome_L0060175.jpg"
---

## Overview

A hip implant gets loaded every time you take a step. I wanted to know if a titanium femoral stem would last under a realistic mix of daily activities, not just under the single load used in the standard lab test.

## Approach

- Took the peak stress at the weakest spot, where the neck meets the stem, from FEA: 485 MPa at a 2300 N load.
- Built a load spectrum of eight activities, from standing still to stumbling, about 2 million cycles in total.
- Estimated the endurance limit of Ti-6Al-4V ELI from published fatigue data: 311 MPa at 99% reliability.
- Used the Goodman relation to account for mean stress, then added up the damage from every activity with Miner's rule, all in Python.

## Challenge and insight

My first version only checked the ISO test load, and it said the stem would last forever. When I checked my numbers, two things didn't add up. The endurance limit I used (620 MPa) was about twice what my own S-N curve allowed, and the activity data in my spreadsheet was never used.

The insight: passing one standard test is not the same as surviving real life. Once every activity was included, a few rare, heavy loads did most of the damage.

## Result

| Check | Result |
|---|---|
| ISO 7206-4 test load | Lasts 8.3 million cycles, passing the 5 million cycle requirement |
| Full activity mix | Fails after about 0.9 million cycles |
| Stumbles | 0.5% of the cycles, but 58% of the damage |

![Goodman diagram of each activity against the endurance limit](/_projects/Hip%20Replacement%20Fatigue%20Life%20Analysis/goodman_diagram.svg)

Each dot is one activity. Dots above the line use up fatigue life, and dots below it do no damage.

## Things to know

- **The endurance limit drives everything.** Published fatigue limits for this alloy range from 400 to 700 MPa. At the low end, the stem fails even the ISO test. At the high end, it lasts almost forever. I used the middle of the range.
- **The stumble count is an assumption.** 10,000 stumbles in 2 million cycles is high, and since stumbles cause most of the damage, the predicted life depends heavily on that number.
- **I didn't check the ISO loads against the standard itself.** I used 2300 N at R = 0.1, but the full ISO 7206-4 text isn't freely available.

## Sources

1. ISO 7206-4:2010, *Implants for surgery: Partial and total hip joint prostheses, Part 4: Determination of endurance properties and performance of stemmed femoral components.*
2. ASTM F136, *Standard Specification for Wrought Titanium-6Aluminum-4Vanadium ELI Alloy for Surgical Implant Applications.*
3. Carpenter Technology, [*Titanium Alloy Ti 6Al-4V ELI* medical datasheet](https://www.carpentertechnology.com/hubfs/Data%20Sheets/20210902--CT_Ti64ELI_Medical_Datasheet_F.pdf).
4. R. G. Budynas and J. K. Nisbett, *Shigley's Mechanical Engineering Design*, McGraw-Hill, Chapter 6.
5. R. L. Norton, *Machine Design: An Integrated Approach*, Pearson.

*Cover photo: ["Hip joint replacement, United States, 1998"](https://commons.wikimedia.org/wiki/File:Hip_joint_replacement,_United_States,_1998_Wellcome_L0060175.jpg), Wellcome Collection, [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/), via Wikimedia Commons.*
