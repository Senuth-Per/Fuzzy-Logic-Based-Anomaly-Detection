# Fuzzy-Logic-Based-Anomaly-Detection
Fuzzy Logic-Based Anomaly Detection and Correction in Smart Grid Systems

## ⚡ Smart Grid Anomaly Detection using Fuzzy Logic

### 📘 Introduction

Smart grid systems are critical for modern electricity distribution, aiming to enhance reliability, efficiency, and resilience. However, they face challenges such as voltage fluctuations, frequency instability, and load imbalances, which can lead to power outages and infrastructure damage.

This project presents the **design, implementation, and testing** of a **fuzzy logic-based anomaly detection and correction system** to improve the reliability of smart grid operations. The system monitors key electrical parameters:
- Voltage Deviation
- Frequency Variation
- Load Imbalance

It detects anomalies and applies corrective actions like:
- Load Balancing
- Power Factor Correction
- Frequency Regulation

The system is implemented in **Python** using the `skfuzzy` library, focusing on:
- Fuzzification
- Rule-based inference
- Defuzzification

Simulated test cases evaluate the system’s performance, with optimization strategies aimed at minimizing false positives/negatives and maximizing grid stability.

---

### 🧪 Methodology

#### 🔧 System Overview

The fuzzy logic system processes **three input parameters**:
- **Voltage Deviation (VD)**: Percentage deviation from nominal voltage (0–20%)
- **Frequency Variation (FV)**: Deviation from nominal frequency (-2 to 2 Hz)
- **Load Imbalance (LI)**: Percentage imbalance in load distribution (0–30%)

These inputs are:
1. **Fuzzified**
2. **Evaluated** against fuzzy rules
3. **Aggregated**
4. **Defuzzified** to produce an **Anomaly Severity Score (0–100)**

Based on the severity score, a **Mitigation Action** is recommended:
- Monitor
- Adjust
- Isolate

Specific adjustments (e.g., Power Factor Correction, Frequency Regulation, Load Balancing) are determined by the input combination.

---

### 🔣 Fuzzy Logic System Components

#### 1. Fuzzification

Inputs are fuzzified manually using `fuzz.interp_membership` to compute membership degrees:

| **Variable**           | **Fuzzy Set** | **Range/Description**                                     |
|------------------------|---------------|------------------------------------------------------------|
| Voltage Deviation (%)  | Low           | 0–5 (peaks at 0, drops to 0 by 5)                          |
|                        | Medium        | 4–10 (peaks at 7)                                          |
|                        | High          | 9–20 (peaks at 15, stays at 1 until 20)                    |
| Frequency Variation (Hz)| Stable        | -0.5 to 0.5 (peaks at 0)                                   |
|                        | Unstable      | -2 to -1 and 0.5 to 2 (two triangle sets)                  |
| Load Imbalance (%)     | Balanced      | 0–10 (peaks at 0, drops to 0 by 10)                        |
|                        | Unbalanced    | 8–30 (peaks at 15, stays at 1 until 30)                    |
| Anomaly Severity       | Low           | 0–40 (peaks at 0)                                          |
|                        | Moderate      | 30–70 (peaks at 50)                                        |
|                        | High          | 60–100 (peaks at 80, stays at 1 until 100)                 |
| Mitigation Action      | Monitor       | 0–30 (peaks at 0)                                          |
|                        | Adjust        | 20–60 (peaks at 40)                                        |
|                        | Isolate       | 50–100 (peaks at 75, stays at 1 until 100)                 |

Membership degrees represent the extent to which each input belongs to its sets (e.g., VD of 12% → 0.5 in High, 0 in others).

---

#### 2. Fuzzy Rule Processing

Rules are evaluated manually using:
- `np.fmin` for fuzzy AND
- `np.fmax` for fuzzy OR

Each rule combines membership degrees to determine the **activation levels** for severity levels. Rules are crafted to cover all meaningful input combinations, avoiding empty outputs.

#### 📋 Rule Table

| **Rule** | **VD**    | **FV**      | **LI**         | **Severity Output** |
|----------|-----------|-------------|----------------|---------------------|
| 1        | Low       | Stable      | Balanced       | Low                 |
| 2        | Medium    | Stable      | Balanced       | Moderate            |
| 3        | High      | Unstable    | Unbalanced     | High                |
| 4        | Medium    | Unstable    | Unbalanced     | Moderate            |
| 5        | Low       | Unstable    | Unbalanced     | Moderate            |
| 6        | High      | Stable      | Balanced       | Moderate            |
| 7        | High      | Stable      | Unbalanced     | Moderate            |
| 8        | High      | Unstable    | Balanced       | Moderate            |

The **aggregated output** is computed by:
- Scaling severity membership functions (`severity_low`, `severity_moderate`, `severity_high`) with activation levels
- Combining with `np.fmax` to generate the final fuzzy output
- **Defuzzification** yields a crisp score to determine the mitigation action.

---
