# HongKaide DM9100 (STM32F405 + HR_C6000 + AT1846S/RDA1846S + ATGM336H) — Calibration README

This README documents the “factory / pre-fine-calibration” parameters visible in your CPS “Test Mode” screen and proposes a repeatable calibration procedure and measurement methods.

It is written to be actionable with common RF test gear, but some fields remain radio-vendor-specific (naming matches typical AT1846S/RDA1846S + HR_C6000 designs, yet the exact register mapping of your OEM firmware is not fully observable from the UI alone).

## Sources (primary)
- AT1846S datasheet (architecture / RF ranges / audio path) citeturn2view0  
- AT1846S Programming Guide 1.4 (frequency programming, reference clock selection, squelch, VOX, deviation, volume control concepts) citeturn7view0  
- RDA1846S datasheet (similar RF transceiver family: VHF 134–174, UHF 400–520, DSP features, PA driver output level) citeturn1view2  
- HR_C6000 User Manual (translated, via ManualsLib) (DMR baseband, 4FSK modem, recommended low-IF around 450/455 kHz, framing and modem behavior) citeturn2view1  

---

## Test equipment (minimum practical)
1. RF service monitor (or spectrum analyzer + RF signal generator) with:
   - Frequency error measurement (Hz)
   - Deviation meter (kHz)
   - SINAD / audio analyzer (optional but very useful)
2. RF power meter (or calibrated power sensor / SA with power measurement)
3. 50 Ω dummy load rated above your radio’s maximum power
4. Audio generator (1 kHz) and AC millivoltmeter / analyzer (optional)
5. Stable power supply (or fully charged battery + consistent wiring)

---

## Recommended calibration order (high-level)
1. Reference / crystal selection (TCXO/XTAL settings, IF crystal)
2. Frequency accuracy (TX and RX frequency offsets)
3. RF tuning offsets per band partition (VHF/UHF low–mid–high)
4. TX power drive tables per band/partition (low, mid, high power profiles)
5. TX deviation (analog FM deviation low/mid/high)
6. Squelch thresholds (level 3 and 9 per band/partition)
7. Mic gains & modulation depth
8. VOX thresholds
9. Battery voltage calibration

Why this order: frequency reference errors contaminate everything (deviation, IF alignment, demod, squelch behavior).

---

# Parameter-by-parameter documentation

## A) Global / common block (left-most “Test Mode” column)

### 1) Internal Minimum Frequency
- Meaning: Lower bound used by the software for frequency-related computations / UI validation.
- Expected: UHF radios show ~400 MHz; VHF block shows ~136 MHz.
- How to set: Normally fixed. Only adjust if the OEM firmware incorrectly constrains your allowed band edges.

### 2) Internal Maximum Frequency
- Same as above, upper bound (e.g., UHF ~470 MHz, VHF ~174 MHz).

### 3) TCXO Crystal
- Meaning: Whether an external TCXO reference is used (or the internal crystal oscillator path is assumed).
- Chip impact: AT1846S/RDA1846S supports multiple XTALs and can accept TCXO bypass depending on configuration. citeturn1view2turn2view0  
- How to set / verify:
  - Measure frequency error of TX carrier at a known frequency (e.g., 433.000000 MHz) after warm-up.
  - If the radio uses a TCXO, drift should be lower and warm-up faster.

---

## B) UHF power drive table (5 “segments”)

In your UI these appear as:
- UHF Low Power Drive — Band Segment 1..5
- UHF Mid Power Drive — Band Segment 1..5
- UHF High Power Drive — Band Segment 1..5

### What “Segment 1..5” most likely means
[suggestion] Rename “Segment” to Band Subrange (or Band Partition).

- Interpretation: the OEM firmware partitions the band into 5 frequency subranges (e.g., from 400–470 MHz) and stores one PA-drive setting per subrange to flatten output across frequency.
- Not stages: these are almost certainly not “amplifier stages”; they are per-frequency-table entries for the same RF chain.

### What it acts on (signal chain)
- On AT1846S/RDA1846S class chips, the RF IC provides a low-power RF output (few dBm) to an external PA chain. citeturn1view2  
- Your radio then uses two external transistors (driver + final) to reach handheld power levels.
- The “Drive” value typically sets:
  - RF IC TX output level and/or
  - external bias/attenuator/DAC value feeding the PA chain (OEM-specific).

### How to measure and adjust
1. Select a test frequency in each segment (low edge / mid / high edge).
2. Key TX into a dummy load, measure:
   - RF power (W)
   - spectral purity / harmonics (if available)
3. Adjust each segment so output power is consistent across the band for that power profile (Low/Mid/High), while avoiding distortion / spectral regrowth and PA overheating.

Expected outcome: a relatively flat power vs. frequency curve per power profile.

---

## C) 4FSK calibration 1..5 (4FSK校正1..5)

- Meaning: DMR 4FSK modem calibration constants (likely HR_C6000 modem/IF path).
- Why 1..5: likely per-frequency subrange again.
- Chip relationship: HR_C6000 integrates a 4FSK modem and typically expects a low-IF input around 450/455 kHz. citeturn2view1  

How to measure and adjust:
- Requires a DMR-capable service monitor or a reference receiver that can measure frequency error / BER.
- Practical method without a full analyzer:
  1. Fix frequency accuracy first (TX Frequency Offset Calibration).
  2. Transmit DMR and compare BER / audio quality on a known-good base station/hotspot at several frequencies.
  3. Adjust in small steps; prioritize BER stability across the band.

[Unverified] Without the vendor’s mapping of “Calibration 1..5” to exact HR_C6000 registers, treat these as empirical constants and proceed carefully.

---

## D) UHF Analog Deviation (U段模拟频偏 L/M/H)
- Meaning: Target FM deviation shaping per band partition and/or channel bandwidth setting. citeturn7view0  

How to measure and adjust:
1. Feed a stable 1 kHz tone (or radio internal tone).
2. Measure deviation in kHz on service monitor.
3. Adjust Low/Mid/High so deviation matches your intended spec at low/mid/high band frequencies.

Expected outcome: consistent deviation across band and minimal audio distortion.

---

## E) Volume Calibration — Low Byte / High Byte (音量校准低字节 / 高字节)
- Meaning: RX audio gain calibration constants (DAC/volume table endpoints).
- AT1846S supports analog/digital volume control in the RX path. citeturn2view0turn7view0  

How to measure and adjust:
- Inject a fixed RF level and deviation; measure speaker output (AC mVrms) for a given UI volume step.
- Adjust endpoints so max volume is not clipping and the scale feels reasonable.

---

## F) RX Frequency Offset Calibration (接收频率校正)
- Meaning: RX centering/AFC correction.
- Measurement: tune a known-accurate carrier; optimize SINAD/distortion at nominal frequency.

---

## G) RF Tuning Offset — Low/Mid/High Band (电调谐低/中/高)
[suggestion] Better labels:
- RF Tuning Offset — Low band partition
- RF Tuning Offset — Mid band partition
- RF Tuning Offset — High band partition

Meaning: likely a VCO/AFC/LO tuning trim per band partition.

How to measure and adjust:
- At representative frequencies: verify stable lock, RX sensitivity, and minimal spurs; adjust for best overall.

---

## H) TX Frequency Offset Calibration (发射频率校正)
- Meaning: TX frequency correction applied to synthesizer programming.
- Measurement: key carrier; measure Hz error; adjust until within your target tolerance.

---

## I) Digital Squelch Level
- Meaning: DSP squelch threshold (likely global scalar). See also detailed squelch tables (Level 3/9) later.

---

## J) IF Crystal Frequency (中频晶体)
- Meaning: Reference for the IF/baseband plan.
- HR_C6000 context: low-IF around 450/455 kHz is common in one-layer mode designs. citeturn2view1  

---

## K) Battery Voltage Calibration
- Meaning: ADC calibration so displayed battery voltage matches a DMM.
- Method: apply known voltage; adjust until match.

---

## L) RX Test Frequency — Low/Mid/High Band (测试接收频率低/中/高端)
Meaning: factory anchor frequencies used by the calibration routine.

[suggestion] Rename to:
- Factory RX Cal Frequency — Low/Mid/High

---

## M) Mic Gain — Stage 1 analog (Mic第一级增益)
Meaning: analog microphone preamp gain.

Measure/adjust:
- Monitor deviation and distortion; set so loud speech does not clip but reaches target deviation.

---

## N) Mic Gain — Stage 2 digital (Mic第二级增益)
Meaning: digital gain post-ADC/DSP; use to normalize after stage 1 is conservative.

---

## O) IF Range — Low / High (加中频范围低 / 高)
[suggestion] Better English:
- IF Search Range — Low
- IF Search Range — High

[Unverified] Likely bounds for an IF/AFC search window during calibration.

---

## P) Firmware Version (版本号)
Human-readable firmware/parameter-set revision.

---

# VHF block (“UV dual-band radio — VHF parameters”)

## Q) TX power setting (dropdown)
Meaning: selects which power profile is used during calibration/test.

## R) VHF Low/Mid/High Power Drive — Band Segment 1..5
Same concept as UHF drive tables, but for 136–174 MHz.

## S) Internal Minimum/Maximum Frequency (VHF block)
Band boundaries for VHF (e.g., 136.0–174.0).

## T) RX Test Frequency — VHF Low/Mid/High
Factory calibration anchor frequencies for VHF (e.g., 137/144/173 MHz).

## U) 4FSK Calibration 1..5 (VHF block)
Same concept as earlier 4FSK; may be separate because VHF needs its own calibration.

## V) VHF Analog Deviation — Low/Mid/High
Same as UHF deviation but for VHF.

---

# VOX block (声控等级 1..9)
Meaning: threshold table for VOX activation levels.

How to calibrate:
- Use a consistent test speech level; set Level 1 to be sensitive but not trigger on noise, Level 9 to require loud speech.

AT1846S supports VOX and squelch functions in its DSP feature set. citeturn2view0turn7view0  

---

# Right-most block: “RDA1846 RF module parameters” (RDA1846模块参数)

[suggestion] If hardware is AT1846S, rename title to “AT1846S/RDA1846S RF module parameters”.

## 1) Analog Deviation (模拟频偏)
Master deviation scalar.

## 2) Modulation Correction Deviation (修正频偏)
Compensation to reduce deviation error across frequency/temperature.

## 3) Digital Correction Deviation (数工频偏)
Compensation for digital (4FSK) modulation path.

[Unverified] Exact meaning depends on whether DMR modulation is injected as AF/baseband into the RF chip or via a dedicated low-IF path.

## 4) RX-11H / TX-11H
[suggestion] Better labels:
- RX Register 0x11 Adjustment
- TX Register 0x11 Adjustment

[Unverified] Function depends on the specific RF chip’s programming guide.

## 5) Crystal Frequency (1846晶体)
Meaning: selected reference clock (e.g., 26 MHz). citeturn1view2  

## 6) RX Power Split Frequency (接收功率分界频率)
[suggestion] Better label: RX Gain Step Boundary Frequency.

[Unverified] Board-specific: boundary where RX chain changes gain profile by frequency.

## 7) MIC Analog Gain (Reg 0x0A)
## 8) MIC Digital Gain (Reg 0x41)
## 9) MIC Modulation Level (Reg 0x44)
Your UI embeds the register IDs.

[suggestion] Final labels:
- MIC Analog Gain (Reg 0x0A)
- MIC Digital Gain (Reg 0x41)
- MIC FM Modulation Level (Reg 0x44)

Practical calibration:
- Set mic stage-1 analog first, then these registers to reach target deviation with acceptable distortion; validate narrow/wide FM.

## 10) Squelch tables (UHF/VHF, Level 3 and Level 9, low/mid/high partitions)
Meaning: per-band, per-partition squelch thresholds.

How to measure:
- Inject a carrier, vary RF level until squelch opens/closes; tune Level 3 to open at weaker signal than Level 9.

Expected outcome: predictable squelch progression across user squelch settings.

---

# “November 2024 values” area (2024年11月数值 + buttons)
- 2024年11月数值 → November 2024 values
- 写入 (checkbox) → Write enable / Allow write
- 读取 → Read
- 写入 (button) → Write
- 保存 → Save
- 取消 → Cancel

---

# Known gaps / what we still need to reverse
1. Which RF IC is physically populated: AT1846S vs RDA1846S (UI says RDA; your note says AT1846S).
2. Mapping of “Drive” and “Tuning Offset” fields to RF IC registers vs external DAC/PWM/GPIO bias network.
3. Exact HR_C6000 “4FSK Calibration 1..5” register mapping.

[suggestion] Most efficient next step: log I2C/SPI traffic between STM32 and the RF IC during Read/Write operations (logic analyzer). That yields ground-truth register mapping quickly.
