# Two-Stage CMOS Op-Amp (LTspice)

This project is a simple two-stage CMOS op-amp built and simulated in LTspice.
The goal was to build the amplifier at transistor level, bias it correctly, and extract the main DC, AC, and transient specifications.

> **Note:** this version is **not Miller compensated**. The results below are for the final uncompensated version.

## Circuit overview

The op-amp is made of:

- **M1, M2**: NMOS differential input pair  
- **M3, M4**: PMOS current-mirror active load  
- **M5**: NMOS second stage (common-source)  
- **M6**: PMOS active load for the second stage  
- **I1**: 1 mA ideal tail current source  
- **VDD**: 5 V supply  
- **CL**: 10 pF output load for transient tests  

## MOS models

```spice
.model MYNMOS NMOS (LEVEL=1 VTO=0.7 KP=200u LAMBDA=0.02)
.model MYPMOS PMOS (LEVEL=1 VTO=-0.7 KP=100u LAMBDA=0.02)
```

## Transistor sizing

| Transistor | Role | Type | W | L |
|---|---|---|---:|---:|
| M1, M2 | Input differential pair | NMOS | 10 µm | 1 µm |
| M3, M4 | Active load / current mirror | PMOS | 20 µm | 1 µm |
| M5 | Second gain stage | NMOS | 1.1 µm | 2 µm |
| M6 | Second-stage load | PMOS | 20 µm | 1 µm |

## Main results

| Parameter | Result |
|---|---:|
| Supply voltage | 5 V |
| Output load used in transient test | 10 pF |
| DC gain from `.tf` | 1349.4 V/V |
| DC gain in dB | 62.6 dB |
| Approx. -3 dB bandwidth | ~300 kHz |
| DC output voltage | 3.77 V |
| Supply current | 1.498 mA |
| Power consumption | 7.49 mW |
| 10–90% rise time | 108 ns |
| Rise rate | 22.94 V/µs |
| 90–10% fall time | 124 ns |
| Fall rate | 20.05 V/µs |

---

## 1) DC operating point (`.op`)

The operating point confirms the internal biasing and gives the DC output voltage and supply current.

From the screenshot below:

- `V(vout) = 3.77327 V`
- `I(V1) = -0.00149834 A`

The minus sign on `I(V1)` is just LTspice sign convention. The circuit is drawing about **1.498 mA** from the 5 V supply.

So the DC power is:

```text
P = VDD × IDD
  = 5 V × 1.49834 mA
  ≈ 7.49 mW
```

![Operating point result](screenshots/operating_point.png)

---

## 2) Small-signal transfer result (`.tf`)

The `.tf` analysis gives the low-frequency gain directly.

From the screenshot below:

- `transfer_function = 1349.4`
- `output_impedance_at_v(vout) = 52644 ohms`

So the open-loop gain is:

```text
Av = 1349.4 V/V
```

In dB, that is approximately:

```text
20 log10(1349.4) ≈ 62.6 dB
```

![TF result](screenshots/tf_result.png)

---

## 3) Open-loop gain plot (`.ac`)

The AC plot confirms the same gain visually.
The low-frequency magnitude starts at about **62.6 dB**, which matches the `.tf` result.

From the plot, the gain stays flat at low frequency and then starts to roll off. The approximate **-3 dB bandwidth** is around **300 kHz**.

![AC gain plot](screenshots/ac_response.png)

---

## 4) Small-signal transient response

For the small-signal test, one input was kept around the DC bias point while a small sinusoidal signal was applied.
The output stays in its linear region and behaves like an amplified version of the input.

![Small-signal transient](screenshots/small_signal_transient.png)

---

## 5) Large-signal response

A larger input step was used to observe how quickly the output can move between two levels.
This gives a practical way to estimate the large-signal charging and discharging speed of the amplifier.

![Large-signal transient](screenshots/large_signal_response.png)

The 10–90% / 90–10% measurements are shown below:

- `trise = 108 ns`
- `rise_rate = 22.94 V/µs`
- `tfall = 124 ns`
- `fall_rate = 20.05 V/µs`

![Rise/fall measurements](screenshots/rise_fall_measurements.png)

---

## Comments

A few things stand out from the results:

- The op-amp reaches the target of **more than 60 dB gain**.
- The large-signal response is fast, with rise/fall rates around **20–23 V/µs**.
- The power consumption is relatively high for a small op-amp because the design uses a **1 mA ideal tail current source** and simple Level-1 models.
- This project is mainly a **learning / transistor-level design exercise**, not a final silicon-ready design.

## Files

This repository includes the README and screenshots used as evidence for the measured results.
The final LTspice schematic (`.asc`) can be added in the project root.
