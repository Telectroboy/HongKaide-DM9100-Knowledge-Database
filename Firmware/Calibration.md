# HongKaide DM9100 – Calibration & Parameter Reference
## STM32F405 + AT1846S + HR_C6000 + ATGM336H
Reference values: November 2024

---

## Architecture Overview

- **MCU**: STM32F405  
  → contrôle, stockage calibration, interface CPS

- **RF Transceiver**: AT1846S (RDA1846 compatible)  
  → PLL, modulateur, démodulateur, IF, DAC/ADC audio

- **DMR Baseband**: HR_C6000  
  → traitement DMR, 4FSK, vocoder AMBE

- **GPS**: ATGM336H  
  → sans impact direct sur la calibration RF/audio

- **TX Chain**:
  AT1846S RF out → Driver transistor → Final PA transistor → Filters → Antenna

- **RX Chain**:
  [Inference]
  Deux entrées coaxiales distinctes → probablement VHF/UHF séparées
  → filtres dédiés → AT1846S RX

---

# 1. Internal Frequency Limits

## Internal Minimum Frequency (Global Limit)
## Internal Maximum Frequency (Global Limit)

**Function**  
Defines absolute PLL limits enforced by firmware.

**Component**  
STM32F405 + AT1846S PLL

**Measurement / Adjustment**  
❌ Do not adjust unless changing legal band limits.

**Expected Result**  
PLL lock always possible inside band.

---

# 2. TCXO Crystal

## TCXO Crystal

**Function**  
Reference oscillator for RF frequency stability.

**Component**  
TCXO → AT1846S PLL → HR_C6000 timing

**How to Measure**
- Transmit unmodulated carrier
- Measure with frequency counter or SDR locked to GPSDO

**How to Adjust**
- Use TX Frequency Offset Calibration
- TCXO value itself usually fixed

**Expected**
- Frequency error < ±200 Hz (DMR safe)
- < ±100 Hz preferred

---

# 3. RX / TX Frequency Offset Calibration

## RX Frequency Offset Calibration

**Function**  
Compensates RX local oscillator offset.

**Component**  
AT1846S RX PLL

**How to Measure**
- Inject precise RF carrier
- Observe demodulated frequency error

**How to Adjust**
- Adjust until RX audio/DSP shows zero offset

**Expected**
- Centered demodulation
- No AFC drift

---

## TX Frequency Offset Calibration

**Function**  
Compensates TX PLL offset.

**Component**
AT1846S TX PLL

**How to Measure**
- TX carrier on dummy load
- Frequency counter / SDR

**Expected**
- Carrier exactly on nominal frequency

---

# 4. RF Tuning Offset – Low / Mid / High Band

## RF Tuning Offset – Low Band
## RF Tuning Offset – Mid Band
## RF Tuning Offset – High Band

**Function**  
RF/PLL tracking compensation across band.

**Component**
AT1846S varactor / PLL loop

**How to Measure**
- RX sensitivity test at:
  - low band edge
  - band center
  - high band edge

**How to Adjust**
- Maximize RX sensitivity
- Ensure PLL lock stability

**Expected**
- Uniform RX sensitivity across band

⚠️ Bad setting → deaf RX on part of band

---

# 5. RX Test Frequencies

## RX Test Frequency – Low Band
## RX Test Frequency – Mid Band
## RX Test Frequency – High Band

**Function**
Factory reference frequencies used during calibration.

**Component**
Firmware only (no direct RF effect)

**How to Set**
- Choose representative frequencies:
  - Low ≈ band -5%
  - Mid ≈ center
  - High ≈ band +5%

---

# 6. TX Power Drive Tables (PA Control)

## UHF/VHF Low Power Drive – Band Segment 1–5
## UHF/VHF Mid Power Drive – Band Segment 1–5
## UHF/VHF High Power Drive – Band Segment 1–5

**Function**
Frequency compensation table for PA drive level.

**Component**
AT1846S DAC → Driver → PA transistors

**Important**
- NOT amplifier stages
- NOT user power levels
- Interpolated table by frequency

**How to Measure**
- 50 Ω dummy load
- Wattmeter

**How to Adjust**
1. Set user power level (Low/Mid/High)
2. Adjust segments 1 → 5
3. Achieve flat output power across band

**Expected**
- Constant RF power across frequency
- No PA overdrive

⚠️ Overdrive = distortion, PA damage

---

# 7. Analog FM Deviation

## UHF/VHF Analog Deviation – Low / Mid / High

**Function**
FM deviation control for analog voice.

**Component**
AT1846S modulator

**How to Measure**
- Inject 1 kHz audio tone
- Measure deviation (deviation meter or SDR)

**Targets**
- Narrow FM: ~2.5 kHz
- Wide FM: ~5.0 kHz

---

# 8. 4FSK Calibration (DMR)

## 4FSK Calibration 1–5

**Function**
DMR symbol deviation shaping.

**Component**
HR_C6000 → AT1846S modulator

**How to Measure**
- BER test
- DMR repeater decode quality

**How to Adjust**
- Reduce BER
- Avoid clipping or under-modulation

**Expected**
- Clean DMR decoding
- Stable network access

---

# 9. Audio RX Calibration

## Volume Calibration – Low Byte
## Volume Calibration – High Byte

**Function**
Digital RX audio gain.

**Component**
AT1846S ADC → HR_C6000 → DAC

**How to Adjust**
- Max volume without speaker distortion

---

# 10. Microphone & TX Audio

## MIC Analog Gain (Register 0x0A)

**Function**
Microphone preamplifier gain.

**Component**
AT1846S analog front-end

---

## MIC Digital Gain (Register 0x41)

**Function**
Post-ADC digital scaling.

---

## MIC Modulation Level (Register 0x44)

**Function**
Final TX modulation depth.

---

### Correct Adjustment Order (MANDATORY)

1. MIC Analog Gain  
2. MIC Digital Gain  
3. MIC Modulation Level  

**Expected**
- Natural voice
- No clipping
- Correct deviation

---

# 11. RX Power Split Frequency

## RX Power Split Frequency

**Function**
Frequency threshold selecting RX profile.

**Component**
AT1846S RX gain/AGC logic

**How to Adjust**
- Typically center of band
- Modify only if RX imbalance observed

---

# 12. RX-11H / TX-11H

## RX Register 0x11 Adjustment
## TX Register 0x11 Adjustment

**Function**
Direct AT1846S register access.

**Status**
⚠️ Factory-level parameter

**Action**
- Do NOT modify unless experimentally characterized

---

# 13. Analog Squelch Thresholds

## UHF/VHF Squelch Level – Low/Mid/High Band – Level 3 / 9

**Function**
RSSI-based squelch thresholds.

**Component**
AT1846S RSSI detector

**How to Adjust**
- Inject weak RF signal
- Level 3 = open squelch
- Level 9 = strict squelch

**Expected**
- No noise
- Fast opening on signal

---

# 14. VOX Levels 1–9

## VOX Level 1–9

**Function**
Voice-operated TX thresholds.

**Component**
Audio DSP (HR_C6000 / MCU)

**How to Adjust**
- Speak normally
- Avoid false triggering

---

# Final Notes

- This document replaces missing manufacturer calibration manual
- All adjustments should be logged (before / after)
- Never adjust multiple parameters at once

---

## Disclaimer

Some interpretations are marked [Inference] and based on:
- AT1846S behavior
- Industry-standard DMR radios
- Empirical RF practice

Proceed carefully.
