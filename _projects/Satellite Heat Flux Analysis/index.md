---
layout: post
title: CubeSat Thermal Control Model
description: Sized the insulation blanket and radiator that keep a 6U CubeSat's battery between 0 and 45°C in orbit. Flipping the blanket's outer layer cut the hottest temperature by 24°C.
skills:
- Python
- Heat Transfer
- Thermal Analysis
- Orbital Mechanics
main-image: "https://upload.wikimedia.org/wikipedia/commons/thumb/0/07/ISS-51_CubeSat_deployment_-_A_pair_of_CubeSats.jpg/1280px-ISS-51_CubeSat_deployment_-_A_pair_of_CubeSats.jpg"
---

## Overview

A small satellite in low Earth orbit goes from full sunlight to Earth's shadow every 95 minutes, but its battery has to stay between 0 and 45°C. I sized the insulation blanket (multi-layer insulation, or MLI) and the radiator for a 6U CubeSat in a 550 km orbit.

## Approach

- Modeled a 6U CubeSat (226 x 100 x 366 mm, 10 kg) pointing at Earth in Python and stepped it around a 550 km orbit, where it spends about 36 minutes of every 95-minute orbit in Earth's shadow.
- Calculated the sunlight, the sunlight reflected off Earth, and Earth's own infrared heat landing on each face, using NASA's hot and cold design values (Anderson et al., 2001; Sharp, 2002).
- Treated each blanket as an outer layer that radiates heat to space and leaks a small amount inward, set by the blanket's effective emittance (0.02). The radiator is silver-backed Teflon tape on the side that faces away from the Sun.
- Checked a hot case (8 W of electronics, strongest sunlight) and a cold case (3 W, weakest sunlight) against the battery limits, then sized the radiator.

## Challenge and insight

My first model added all the sunlight hitting the blanket straight into the satellite, as if the blanket weren't there. It predicted 116°C. The fix was to treat the blanket's outer layer as its own surface: it absorbs sunlight, radiates most of it back to space, and only a little leaks through.

That led to a surprise. My blanket had the shiny aluminum side facing out. It absorbs only 14% of the sunlight, but it is also bad at radiating heat away, so in full sun it heats up to 234°C and pushes about a third of what it absorbs into the satellite. Flipping the blanket so the Kapton side faces out absorbs three times more sunlight but lets only 1% of it in, which cut the hot case from 58°C to 34°C.

## Result

| Check | Result |
|---|---|
| First model (blanket ignored) | 116°C in the hot case |
| Shiny side out, 200 cm² radiator | 58°C hot, 12°C cold |
| Kapton side out, 200 cm² radiator | 34°C hot, -18°C cold |
| Radiator sizing | The hot case needs at least 158 cm² and the cold case at most 120 cm², so no size works on its own |
| Final design | 170 cm² radiator plus a 1 W heater in the cold case: 0°C to 42.5°C |
| Change over one orbit | Under 1°C for the satellite body, while the blanket's outer layer swings from -162°C to 79°C |

![Satellite temperature against radiator area for the hot and cold cases](/_projects/Satellite%20Heat%20Flux%20Analysis/radiator_sizing.svg)

A bigger radiator cools both cases. The hot case needs a big one and the cold case a small one, so the final design uses the hot-case size and adds a small heater for the cold case.

![Satellite body and blanket temperatures over two orbits](/_projects/Satellite%20Heat%20Flux%20Analysis/orbit_temperatures.svg)

The blanket's outer layer swings by over 200°C every orbit, but the satellite body barely moves. Its 10 kg of mass soaks up the changes, so the orbit average sets its temperature.

## Things to know

- **The whole satellite is one temperature.** I lumped the structure, electronics, and battery into one 10 kg block. Real parts sit a few degrees apart, which a model with more nodes would capture.
- **The blanket's effective emittance is an estimate.** Measured values vary a lot, and small blankets perform worse than large ones (Bae and Rodriguez-Ruiz, 2019). Between 0.005 and 0.05, the hot case moves from 52°C to 29°C. A better blanket actually makes the hot case worse here, because the blanket is a path for heat to escape.
- **No safety margin or solar panels.** Real designs add a temperature margin for uncertainty, and the deployable solar panels were not modeled.

## Sources

1. B. J. Anderson, C. G. Justus, and G. W. Batts, [*Guidelines for the Selection of Near-Earth Thermal Environment Parameters for Spacecraft Design*](https://ntrs.nasa.gov/citations/20020004360), NASA/TM-2001-211221, 2001.
2. J. R. Sharp, ["Thermal Orbital Environmental Parameter Study on the Propulsive Small Expendable Deployer System (ProSEDS) Using Earth Radiation Budget Experiment (ERBE) Data"](https://tfaws.nasa.gov/TFAWS02/Data/Thermal/Sharp.pdf), NASA Thermal and Fluids Analysis Workshop, 2002.
3. Sheldahl, [*The Red Book*](https://www.jbc-tech.com/media/y2zpfrx0/sheldahl-red-book.pdf), Rev E, 2020 (blanket and radiator tape properties).
4. D. Bae and J. Rodriguez-Ruiz, ["Review of MLI Behavior at Low Temperatures and Application to L'Ralph Thermal Modeling"](https://ntrs.nasa.gov/api/citations/20190030285/downloads/20190030285.pdf), NASA Thermal and Fluids Analysis Workshop, 2019.
5. NASA Small Spacecraft Systems Virtual Institute, [*State-of-the-Art of Small Spacecraft Technology*, Chapter 7: Thermal Control](https://www.nasa.gov/smallsat-institute/sst-soa/thermal-control).
6. Cal Poly CubeSat Program, [*6U CubeSat Design Specification*](https://s3vi.ndc.nasa.gov/ssri-kb/static/resources/6U_CDS_2018-06-07_rev_1.0.pdf), Rev 1.0, 2018.
7. D. G. Gilmore (ed.), *Spacecraft Thermal Control Handbook, Volume 1: Fundamental Technologies*, The Aerospace Press, 2002.

*Cover photo: ["ISS-51 CubeSat deployment - A pair of CubeSats"](https://commons.wikimedia.org/wiki/File:ISS-51_CubeSat_deployment_-_A_pair_of_CubeSats.jpg), NASA, public domain, via Wikimedia Commons.*
