# Rectangular Waveguide — Two-Port S-Parameters

A rectangular waveguide (a=1.0 m, b=0.75 m, L=0.5 m, PEC walls, air-filled), excited at port 1 with the TE10 mode and swept across 139 frequencies from 30–350 MHz to measure S11/S21 at port 2.

Full writeup: [`informe_puerto.pdf`](informe_puerto.pdf) (3 pages) · day-by-day notes: [`bitacora_puertos.pdf`](bitacora_puertos.pdf)

## Setup

- Software: COMSOL Multiphysics, *Electromagnetic Waves, Frequency Domain*, 3D.
- Port 1 (z=0): TE10, 1 W in. Port 2 (z=L): TE10, measuring transmission.
- Frequency sweep: 139 points, 30–350 MHz.

## What it found

**Theoretical TE10 cutoff: f_c = c/(2a) = 149.896 MHz.** COMSOL's own transmission data places the cutoff between 149.5 and 150.0 MHz — matching to within the 0.5 MHz frequency-mesh resolution used in the sweep.

**Above cutoff:** transmission is essentially perfect (S21 ≈ 0 dB, S11 < −89 dB), with three small, predictable S11 perturbations exactly at the cutoffs of the next modes (TE01 at 199.9 MHz, TE11 at 249.8 MHz, TE20 at 299.8 MHz) — evanescent coupling near the port as each higher mode starts to turn on, even though only TE10 is being driven.

**Below cutoff, the S-parameter is genuinely undefined — not "near zero."** A port normalizes S11/S21 by the *real* power its reference mode carries; below its own cutoff, TE10 is evanescent everywhere and carries only reactive power, so that normalization divides by (approximately) zero. This was confirmed to not be a port-type artifact: the full sweep was repeated with both ports switched from analytic *Rectangular* ports to *Numeric* ports (with an added *Boundary Mode Analysis* step each) — the result was identical, S21 exactly 0+0j across all 61 sub-cutoff frequencies either way.

**What actually happens below cutoff, seen directly in the field:** holding P_in = 1 W fixed and reading the raw field (`emw.normE`) instead of S21, the solver has to inflate the field to a physically meaningless magnitude trying to satisfy "1 W of real power in a mode that can only carry reactive power" — a 6-order-of-magnitude jump between the evanescent regime (~10⁷ V/m) and the propagating regime (tens of V/m, matching theory). A line-graph cut along the guide's center confirms the field decays faster per meter the further below cutoff the drive frequency is, consistent with the closed-form evanescent attenuation constant α(f) = (2π/c)·√(f_c² − f²).

| Mode | f_c (theoretical) |
|---|---|
| TE10 | 149.896 MHz |
| TE01 | 199.862 MHz |
| TE11 | 249.827 MHz |
| TE20 | 299.792 MHz |
