# Troubleshooting Manual: Leakage_in_hydraulics_system

**Fault ID:** exp_8
**Subsystem:** Hydraulics
**Scenario:** {'init': 'Machine is in normal operation and hydraulic pressure is ok.', 'instead_failure': 'A leak causes hydraulic pressure to fall continuously; once pressure drops below the threshold (Hyd_Pressure < ca. 6600), the system detects a low‑pressure fault and raises an alarm.'}

---

---
## 1. Fault Overview

This fault scenario involves a continuous hydraulic oil leak in the CNC vertical lathe, resulting in a steady drop of hydraulic pressure below the operational threshold. The system detects this abnormal pressure condition and triggers a low-pressure alarm, which can halt machine operation and compromise both safety and productivity.

## 2. System Impact

- **What stops working?**  
  Hydraulic-actuated functions (e.g., tool clamping/unclamping, axis movement, chuck operation) become unavailable or unreliable due to insufficient pressure.
- **What safety systems activate?**  
  The system automatically disables hydraulic operations and may command the hydraulic pump and accumulator charge valve to attempt pressure recovery. If unsuccessful, the machine transitions to a safe state.
- **Production consequences?**  
  All machining operations are interrupted, resulting in unplanned downtime and potential loss of workpieces in process.

## 3. Observable Symptoms

**Via Alarms:**
- **Hyd_A_700202:** "Hydraulic pressure out of norm" (triggered when Hyd_Pressure < threshold for ≥5 seconds)
- Possible secondary alarms if leak persists:
  - **Hyd_A_700205:** "Hydraulic oil level close to minimum" (if oil loss is significant)
  - **Hyd_A_700206:** "Hydraulic oil level below minimum"

**Via Sensor Readings:**
- **Hyd_Pressure:** Drops below operational threshold (typically < 6600 units; normal range: 6600–7500)
- **Hyd_Valve_P_Up:** True (accumulator charge valve commanded open to restore pressure)
- **Hyd_Pump_On:** True (hydraulic pump commanded on)
- **Hyd_Level_Ok:** May transition to False if leak is severe/prolonged

**Via Visual/Physical Inspection:**
- Visible hydraulic oil pooling under machine or at hose/fitting locations
- Wet or oily surfaces on hydraulic lines, connectors, or reservoir
- Possible hissing sound from leak site
- Noticeable drop in reservoir oil level
- Oil odor near the hydraulic subsystem

## 4. Root Cause Analysis

### 4.1 Primary Root Cause

**Cause:** Hydraulic oil leak (external or internal) causing pressure loss  
**Mechanism:** The leak allows hydraulic fluid to escape, reducing system pressure below the set threshold. The control system detects this via Hyd_Pressure and triggers Hyd_A_700202. The system attempts to compensate by activating the pump and accumulator valve, but cannot restore pressure due to ongoing fluid loss.  
**Probability:** **High** (based on manipulated variables and scenario description)

**Verification Steps:**
1. **Check Hyd_Pressure:**  
   - Confirm Hyd_Pressure < 6600 (threshold).  
   - If true, proceed.
2. **Inspect for Alarms:**  
   - Verify Hyd_A_700202 is active.  
   - Check for Hyd_A_700205 or Hyd_A_700206 (if oil loss is advanced).
3. **Visual Inspection:**  
   - Examine hydraulic lines, fittings, and reservoir for visible leaks or oil accumulation.
   - If leak is found, confirm as root cause.
4. **Check Hyd_Level_Ok:**  
   - If False, oil level is low due to leak.
5. **Decision:**  
   - If leak is confirmed and alarms match, root cause is validated.
   - If no leak is found, proceed to secondary causes.

### 4.2 Secondary Causes

- **Cause:** Hydraulic pump failure (Hyd_Pump_Ok=False)
  - **Likelihood:** Medium
  - **Differentiating factors:** Hyd_Pump_Ok=False, but no visible leaks; Hyd_Pump_isOff may be True.
- **Cause:** Clogged hydraulic filter (Hyd_Filter_Ok=False)
  - **Likelihood:** Low-Medium
  - **Differentiating factors:** Hyd_A_700207 alarm active; filter housing may be under vacuum; pressure drop across filter.
- **Cause:** Accumulator charge valve malfunction (Hyd_Valve_P_Up stuck or not opening)
  - **Likelihood:** Low
  - **Differentiating factors:** Hyd_Valve_P_Up=False when pressure is low; no leak or pump fault.

### 4.3 Causal Chain

```
[Hydraulic oil leak] → [Hyd_Pressure drops below threshold] → [Hyd_A_700202 alarm triggers] → [Pump and accumulator valve attempt recovery] → [Pressure remains low, system disables hydraulics]
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- **Action:** Check active alarms on HMI.
- **Expected result:** No alarms in normal operation.
- **If abnormal:** Hyd_A_700202 active → Go to Step 2.
- **If normal:** Monitor system; no action required.

**Step 2: Pressure Verification**
- **Action:** Read Hyd_Pressure value.
- **Expected result:** ≥ 6600.
- **If abnormal:** < 6600 → Go to Step 3.
- **If normal:** Investigate intermittent alarm; check for sensor faults.

**Step 3: Leak Inspection**
- **Action:** Visually inspect hydraulic lines, reservoir, and fittings for oil leaks.
- **Expected result:** No visible oil outside system.
- **If abnormal:** Leak found → Go to Step 4.
- **If normal:** Go to Step 5.

**Step 4: Oil Level Check**
- **Action:** Check Hyd_Level_Ok and physical reservoir sight glass.
- **Expected result:** Hyd_Level_Ok=True, oil at proper level.
- **If abnormal:** Hyd_Level_Ok=False, oil low → Confirm leak as root cause.
- **If normal:** Leak may be internal; further investigation required.

**Step 5: Secondary Cause Evaluation**
- **Action:** Check Hyd_Pump_Ok, Hyd_Filter_Ok, Hyd_Valve_P_Up.
- **Expected result:** All True.
- **If abnormal:**  
  - Hyd_Pump_Ok=False → Investigate pump failure.  
  - Hyd_Filter_Ok=False → Inspect/replace filter.  
  - Hyd_Valve_P_Up=False → Check valve operation.

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Engage E-stop and power down machine.
2. Depressurize hydraulic system using manufacturer procedure.
3. Isolate hydraulic subsystem (lock-out/tag-out main hydraulic disconnect).
4. Contain and clean up any spilled oil (use absorbent pads).
5. Document alarm history, sensor readings, and leak location.

### 6.2 Repair Procedure

**Required:**
- **Tools:** Wrenches (metric), hydraulic line clamps, oil drain pan, torque wrench, inspection mirror, flashlight
- **Parts:**  
  - Replacement hose or fitting (P/N: HYD-GEN-001, HYD-GEN-002, etc. as per BOM)
  - Hydraulic oil (P/N: HYD-GEN-OIL-001)
  - Seals/O-rings (P/N: HYD-GEN-003)
- **Personnel:** Certified hydraulic technician
- **Estimated time:** 1–2 hours (minor leak); 3–4 hours (major component replacement)

**Steps:**
1. Identify and mark leak source.
2. Remove affected hydraulic line/fitting using appropriate wrenches.
3. Inspect mating surfaces for wear or scoring; clean thoroughly.
4. Install new line/fitting/seal (torque to 40 Nm unless otherwise specified).
5. Refill reservoir with hydraulic oil to proper level.
6. Bleed air from system per service manual.
7. Reconnect all sensors and verify no loose connections.
8. Remove lock-out/tag-out devices.

### 6.3 Verification Tests

After repair:
1. Power up machine and enable hydraulics.
2. Monitor Hyd_Pressure (should stabilize ≥ 6600).
3. Check Hyd_Level_Ok (should be True).
4. Confirm no active alarms (Hyd_A_700202, Hyd_A_700205, Hyd_A_700206).
5. Perform functional test of all hydraulic actuators (clamp/unclamp, axis movement).
6. Inspect for new leaks during operation.

### 6.4 Return to Service

- Calibrate pressure sensors if disturbed during repair.
- Update maintenance and repair logs with fault details, parts used, and actions taken.
- Monitor hydraulic pressure and oil level for at least 30 minutes post-repair.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- Hydraulic oil under pressure can cause severe injury—always depressurize before opening system.
- Wear PPE: safety goggles, oil-resistant gloves, and protective clothing.
- Follow lock-out/tag-out procedures on all electrical and hydraulic sources.
- Beware of hot oil and surfaces; hydraulic oil may exceed 70°C.
- Clean up spills immediately to prevent slip hazards.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect all hydraulic lines, hoses, and fittings for wear or leaks every 500 operating hours.
- Replace seals and hoses at manufacturer-recommended intervals.
- Check and record Hyd_Pressure and Hyd_Level_Ok daily.
- Change hydraulic oil and filter per maintenance schedule (typically every 2000 hours).
- Monitor for early warning alarms (Hyd_A_700205).

## 9. Related Faults

**This fault can cause:**
- Hyd_A_700205 (oil level close to minimum)
- Hyd_A_700206 (oil level below minimum)
- Loss of hydraulic actuator function

**This fault can be caused by:**
- Upstream mechanical damage (e.g., impact to lines)
- Improper maintenance (loose fittings, degraded seals)

**Similar symptoms but different causes:**
- Hyd_Pump_Ok=False (pump failure)
- Hyd_A_700207 (filter clogged)
- Sensor malfunction (false Hyd_Pressure reading)

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_8
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at

---