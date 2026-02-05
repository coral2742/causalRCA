# Troubleshooting Manual: Leakage_in_hydraulics_system

**Fault ID:** exp_12
**Subsystem:** Hydraulics
**Scenario:** {'init': 'Machine is in normal operation; oil level is within the normal range and stable.', 'expected_result': 'Oil level remains constant within the specified limits, with no unexpected drop.', 'instead_failure': 'Oil level falls continuously due to a leak, eventually dropping below the threshold and low‑level alarms are triggered.'}

---

---
## 1. Fault Overview

This fault describes a scenario where the hydraulic oil level in the CNC vertical lathe falls continuously due to a leak, eventually dropping below the minimum threshold. As a result, low-level warnings and alarms are triggered, potentially leading to system shutdown. Early detection and correction are critical to prevent damage to hydraulic components and avoid unplanned production downtime.

## 2. System Impact

- **What stops working?**  
  The hydraulic subsystem will automatically disable itself once the oil level drops below the minimum threshold, halting all hydraulically actuated machine functions.
- **What safety systems activate?**  
  Low-level warnings (Hyd_A_700205) and critical low-level alarms (Hyd_A_700206) are triggered. The system disables the hydraulic pump (Hyd_Pump_On=False) to prevent pump damage.
- **Production consequences?**  
  The machine will stop all operations requiring hydraulics, resulting in a full production halt until the issue is resolved and oil level is restored.

## 3. Observable Symptoms

**Via Alarms:**
- **Hyd_A_700205:** Warning – Hydraulic oil level is close to minimum.
- **Hyd_A_700206:** Alarm – Hydraulic oil level is below minimum; hydraulics subsystem disabled.

**Via Sensor Readings:**
- **Hyd_Level_Ok:** Changes from True to False as oil level drops.
- **Hyd_Pump_On:** Changes from True to False after Hyd_A_700206 triggers.
- **Hyd_Pressure:** May show a gradual decrease as oil level falls, then a rapid drop when the pump is disabled.
- **Expected vs. actual:**  
  - *Normal:* Hyd_Level_Ok=True, Hyd_Pressure within operational range  
  - *Fault:* Hyd_Level_Ok=False, Hyd_Pressure below normal, Hyd_Pump_On=False

**Via Visual/Physical Inspection:**
- Visible oil leakage under the machine or around hydraulic lines, fittings, or reservoir.
- Oil puddles, wetness, or sheen on/under the hydraulic tank or piping.
- Possible audible hissing (if leak is pressurized) or dripping sounds.
- Smell of hydraulic oil near the machine.

## 4. Root Cause Analysis

### 4.1 Primary Root Cause

**Cause:** Hydraulic oil leak (external or internal) leading to loss of oil volume.

**Mechanism:**  
A leak in the hydraulic circuit allows oil to escape, causing the oil level in the reservoir to fall. Once the level drops below the warning threshold, Hyd_A_700205 is triggered. If the leak continues, the level falls below the minimum threshold, triggering Hyd_A_700206, which disables the hydraulic pump (Hyd_Pump_On=False) to prevent damage.

**Probability:** High (given continuous level drop and visible leak).

**Verification Steps:**
1. **Check Hyd_Level_Ok:**  
   - If False, proceed to step 2.
2. **Review alarm history:**  
   - Confirm Hyd_A_700205 and Hyd_A_700206 triggered in sequence.
3. **Inspect for visible leaks:**  
   - Look for oil under machine, at hoses, fittings, tank.
4. **Check oil reservoir level:**  
   - Compare actual oil level to minimum mark.
5. **Monitor Hyd_Pressure:**  
   - If Hyd_Pressure is low and Hyd_Pump_On=False, leak is likely.
6. **Decision:**  
   - If leak found and oil level below minimum, primary cause confirmed.
   - If no leak found, proceed to secondary causes.

### 4.2 Secondary Causes

- **Cause:** Faulty oil level sensor (Hyd_Level sensor failure)
  - **Likelihood:** Medium
  - **Differentiating factors:** No visible leak; oil level physically within range; sensor reading inconsistent with visual check.

- **Cause:** Air entrainment or cavitation causing false low-level reading
  - **Likelihood:** Low
  - **Differentiating factors:** Bubbles in oil, erratic sensor readings, no actual oil loss.

- **Cause:** Sudden large consumption or internal transfer of oil (e.g., accumulator failure)
  - **Likelihood:** Low
  - **Differentiating factors:** Rapid drop in level without external leak; check accumulator and internal components.

### 4.3 Causal Chain

```
[Hydraulic Oil Leak] → [Oil Level Drops] → [Hyd_Level_Ok=False] → [Hyd_A_700205 triggers] → [Continued Leak] → [Hyd_A_700206 triggers] → [Hyd_Pump_On=False] → [Hydraulics Disabled]
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- Action: Check Hyd_Level_Ok status and alarm history.
- Expected result: Hyd_Level_Ok=True, no recent Hyd_A_700205/206.
- If abnormal: Go to Step 2.
- If normal: Resume operation; monitor.

**Step 2: Visual Inspection**
- Action: Inspect for oil leaks under/around machine, reservoir, hoses, and fittings.
- Expected result: No oil outside system.
- If abnormal: Go to Step 3.
- If normal: Go to Step 4.

**Step 3: Reservoir Level Check**
- Action: Open reservoir access, check actual oil level against minimum mark.
- Expected result: Oil at or above minimum.
- If below minimum: Confirm leak; proceed to remediation.
- If at/above minimum: Go to Step 4.

**Step 4: Sensor Verification**
- Action: Test Hyd_Level sensor operation (simulate level change if possible).
- Expected result: Sensor responds accurately.
- If abnormal: Replace sensor; see secondary causes.
- If normal: Go to Step 5.

**Step 5: Systematic Pressure Test**
- Action: Pressurize system, observe for drops in Hyd_Pressure or new leaks.
- Expected result: Pressure holds, no leaks.
- If abnormal: Locate and repair leak.
- If normal: Fault may be intermittent; monitor closely.

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Power down the machine and engage lock-out/tag-out (LOTO) procedures.
2. Depressurize the hydraulic system using the designated bleed valve.
3. Contain and clean up any spilled oil to prevent slip hazards.
4. Isolate the affected hydraulic circuit if possible.
5. Document alarm history, sensor readings, and physical findings.

### 6.2 Repair Procedure

**Required:**
- Tools: Wrenches (metric/imperial), oil catch basin, absorbent pads, inspection mirror, torque wrench, replacement hoses/fittings, cleaning supplies.
- Parts:  
  - Hydraulic hose (P/N: HYD-GEN-001)  
  - Fitting (P/N: HYD-GEN-002)  
  - Oil level sensor (if needed, P/N: HYD-LEVEL-003)  
  - Hydraulic oil (P/N: HYD-GEN-004)
- Personnel: Technician trained in hydraulic maintenance.
- Estimated time: 1–3 hours (depending on leak location).

**Steps:**
1. Verify system is powered down and depressurized.
2. Identify and mark leak location.
3. Remove faulty hose/fitting using proper wrenches; collect residual oil.
4. Install replacement part (e.g., hose P/N: HYD-GEN-001) to manufacturer’s torque spec (e.g., 45 Nm for ½" fitting).
5. Clean area thoroughly; ensure no debris enters system.
6. Refill reservoir with hydraulic oil to correct level (P/N: HYD-GEN-004).
7. If sensor was replaced, install new sensor (P/N: HYD-LEVEL-003) and connect wiring.
8. Inspect all connections for proper fit and torque.
9. Dispose of contaminated oil and materials per environmental regulations.

### 6.3 Verification Tests

After repair:
1. Power up machine and enable hydraulics.
2. Check Hyd_Level_Ok=True, Hyd_A_700205/206 not present.
3. Observe Hyd_Pressure stabilizes within normal range.
4. Visually inspect for leaks during operation.
5. Run a functional test of all hydraulic actuators.

### 6.4 Return to Service

- Calibrate oil level sensor if replaced (follow OEM procedure).
- Update maintenance and repair logs with details of fault and corrective action.
- Monitor oil level and pressure for at least 1 hour of operation to confirm no recurrence.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- Hydraulic oil under pressure can cause serious injury; always depressurize before opening any connections.
- Wear chemical-resistant gloves, safety goggles, and protective clothing (PPE).
- Strictly follow lock-out/tag-out (LOTO) procedures before maintenance.
- Hot oil and surfaces may cause burns; verify temperature is below 40°C before handling.
- Clean up spills immediately to prevent slip hazards and environmental contamination.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect all hydraulic hoses, fittings, and reservoir for leaks every 250 operating hours.
- Monitor Hyd_Level_Ok and Hyd_A_700205/206 for early warning signs.
- Replace hoses and seals showing wear, cracks, or bulging.
- Maintain a log of oil additions; unexplained losses may indicate a developing leak.
- Schedule annual sensor calibration and system pressure test.

## 9. Related Faults

**This fault can cause:**
- Hyd_Pump damage (if run dry)
- System-wide hydraulic actuator failures
- Contamination of shop floor/environment

**This fault can be caused by:**
- Upstream: Overpressure events, improper hose installation, vibration loosening fittings

**Similar symptoms but different causes:**
- Faulty oil level sensor (Hyd_Level_Ok false positive)
- Air entrainment causing false low-level reading
- Accumulator failure causing sudden oil level drop

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_12
- Expert graph node references: cause_start_at, alarms_detected_at, alarms_resolved_at, diagnosis_at, cause_end_at

---