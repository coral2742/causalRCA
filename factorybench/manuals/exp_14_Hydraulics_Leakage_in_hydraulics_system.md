# Troubleshooting Manual: Leakage_in_hydraulics_system

**Fault ID:** exp_14
**Subsystem:** Hydraulics
**Scenario:** {'init': 'Machine is in normal operation; both hydraulic pressure and oil level are within normal ranges and stable.', 'expected_result': 'Hydraulic pressure and oil level remain stable and above their respective thresholds during operation.', 'instead_failure': 'A hydraulic leak develops. First, a warning for low oil level is issued. Hydraulic pressure then continuously drops until its threshold is also breached.  Both values eventually fall below limits, triggering multiple fault alarms.'}

---

---
## 1. Fault Overview

This fault scenario involves a progressive hydraulic leak in the CNC vertical lathe’s hydraulic subsystem. The leak causes the hydraulic oil level to drop, first triggering a low-level warning, followed by a continuous decrease in hydraulic pressure that ultimately results in multiple system alarms and machine shutdown. Early detection and remediation are critical to prevent equipment damage and unplanned production downtime.

## 2. System Impact

- **What stops working?**  
  The hydraulic system loses pressure, causing all hydraulically actuated functions (e.g., tool clamping, turret rotation, chuck operation) to cease. The machine will enter a safe stop condition.
- **What safety systems activate?**  
  The system disables hydraulic actuation (Hyd_IsEnabled=False), stops the hydraulic pump (Hyd_Pump_isOff=True), and triggers both warnings and critical alarms to prevent further operation.
- **Production consequences?**  
  All machining operations halt. Workpieces may remain clamped or unclamped, depending on when the pressure loss occurred. Extended downtime may result if oil contamination or component damage occurs.

## 3. Observable Symptoms

**Via Alarms:**
- **Hyd_A_700205:** Warning—hydraulic oil level close to minimum
- **Hyd_A_700206:** Alarm—hydraulic oil level below minimum
- **Hyd_A_700202:** Alarm—hydraulic pressure out of norm

**Via Sensor Readings:**
- **Hyd_Level_Ok:** Changes from True → False as oil level drops
- **Hyd_Pressure:** Drops below operational threshold (see OEM specs; typically < 120 bar)
- **Hyd_Pump_On:** Remains True while system attempts to recover pressure
- **Hyd_Valve_P_Up:** True, indicating accumulator charge valve is open in attempt to restore pressure

**Expected vs. Actual:**
- **Expected:** Hyd_Level_Ok=True, Hyd_Pressure within 120–160 bar (nominal)
- **Actual:** Hyd_Level_Ok=False, Hyd_Pressure steadily decreasing, eventually < threshold

**Via Visual/Physical Inspection:**
- Visible oil pooling under machine or around hydraulic lines/components
- Noticeable drop in oil reservoir level (Hyd_Level sight glass)
- Possible hissing sound from leak location
- Oil odor near hydraulic cabinet

## 4. Root Cause Analysis

### 4.1 Primary Root Cause

**Cause:** Hydraulic leak (external or internal) causing loss of hydraulic oil

**Mechanism:**  
A leak in the hydraulic circuit (e.g., hose, fitting, seal) results in oil escaping from the system. As oil level drops, the Hyd_Level_Ok sensor triggers a warning (Hyd_A_700205), then an alarm (Hyd_A_700206). Continued operation with insufficient oil causes the hydraulic pump to draw air, reducing pressure (Hyd_Pressure falls), which triggers the pressure alarm (Hyd_A_700202). The system disables hydraulics to prevent damage.

**Probability:** High (based on manipulated variables and symptom sequence)

**Verification Steps:**
1. **Check Hyd_Level_Ok:**  
   - If False, confirm oil level in reservoir is below minimum mark.
2. **Inspect for visible leaks:**  
   - Examine all hydraulic lines, fittings, and actuator seals for oil residue or pooling.
3. **Monitor Hyd_Pressure:**  
   - If < 120 bar and not recovering with pump running, leak is likely.
4. **Check Hyd_Pump_On and Hyd_Valve_P_Up:**  
   - If both True but pressure not restored, leak is probable.
5. **Decision:**  
   - If leak is found, this is the primary cause.  
   - If no leak, proceed to secondary causes.

### 4.2 Secondary Causes

- **Cause:** Faulty Hyd_Level sensor (false low reading)
  - **Likelihood:** Medium
  - **Differentiating factors:** No actual oil loss; visual inspection shows normal reservoir level.
- **Cause:** Hydraulic pump failure (Hyd_Pump_Ok=False)
  - **Likelihood:** Low
  - **Differentiating factors:** Hyd_Pump_Ok alarm (Hyd_A_700208) may also be active; pressure loss not accompanied by oil loss.
- **Cause:** Clogged hydraulic filter (Hyd_Filter_Ok=False; Hyd_A_700207)
  - **Likelihood:** Low
  - **Differentiating factors:** Filter alarm active; pressure drop occurs, but oil level remains normal.

### 4.3 Causal Chain

```
[Hydraulic Leak] → [Oil Level Drops] → [Hyd_Level_Ok=False] → [Hyd_A_700205/206] → [Pump draws air] → [Hyd_Pressure drops] → [Hyd_A_700202] → [Hyd_IsEnabled=False]
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- **Action:** Review active alarms on HMI.
- **Expected result:** No alarms; all Hyd_*_Ok variables True.
- **If abnormal:** Go to Step 2.
- **If normal:** Resume operation.

**Step 2: Check Oil Level**
- **Action:** Inspect hydraulic reservoir; verify Hyd_Level_Ok status.
- **Expected result:** Oil at or above minimum; Hyd_Level_Ok=True.
- **If abnormal (Hyd_Level_Ok=False):** Go to Step 3.
- **If normal:** Go to Step 4.

**Step 3: Inspect for Leaks**
- **Action:** Visually check all hydraulic lines, fittings, and components for oil leaks.
- **Expected result:** No visible leaks.
- **If leak found:** Proceed to Remediation (Section 6).
- **If no leak:** Go to Step 4.

**Step 4: Check Hydraulic Pressure**
- **Action:** Observe Hyd_Pressure value.
- **Expected result:** ≥ 120 bar (or OEM spec).
- **If < 120 bar:** Go to Step 5.
- **If normal:** Check Hyd_Filter_Ok and Hyd_Pump_Ok.

**Step 5: Check Pump and Filter Status**
- **Action:** Verify Hyd_Pump_Ok and Hyd_Filter_Ok.
- **Expected result:** Both True.
- **If abnormal:** Investigate pump/filter faults per respective procedures.
- **If normal:** Suspect sensor fault or intermittent leak; consult maintenance.

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Engage emergency stop; power down machine.
2. Depressurize hydraulic system per OEM procedure.
3. Isolate hydraulic subsystem (close reservoir isolation valve if equipped).
4. Place absorbent pads under suspected leak area.
5. Document alarm history, sensor readings, and take photographs of leak.

### 6.2 Repair Procedure

**Required:**
- **Tools:** Metric wrench set, hydraulic line plug kit, oil spill cleanup kit, torque wrench (10–100 Nm), inspection mirror, flashlight.
- **Parts:**  
  - Replacement hose/fitting/seal (as identified on inspection)  
    - Example: P/N: HYD-GEN-012 (hose), P/N: HYD-GEN-045 (seal)
  - Hydraulic oil (OEM grade, quantity as required)
- **Personnel:** Certified hydraulic technician
- **Estimated time:** 1–3 hours (depending on leak location)

**Steps:**
1. Identify and mark leak source.
2. Remove affected hydraulic line/component using appropriate wrenches.
3. Replace faulty part (e.g., hose P/N: HYD-GEN-012) with new, ensuring correct orientation and torque (refer to OEM torque chart; typical fitting: 40 Nm).
4. Inspect mating surfaces for damage; clean as needed.
5. Reinstall line/component; torque to specification.
6. Refill reservoir with hydraulic oil to correct level.
7. Bleed air from system per OEM procedure (typically by cycling pump with return line open).
8. Inspect for further leaks during static pressure test (Hyd_Pressure ≥ 120 bar).
9. Clean up any spilled oil; dispose of waste per local regulations.

### 6.3 Verification Tests

After repair:
1. Power on and enable hydraulics (Hyd_IsEnabled=True).
2. Confirm Hyd_Level_Ok=True and Hyd_Pressure within 120–160 bar.
3. Cycle all hydraulic actuators; check for smooth operation and absence of alarms.
4. Observe for leaks during operation for at least 10 minutes.

### 6.4 Return to Service

- Calibrate Hyd_Level sensor if replaced.
- Update maintenance and repair logs with part numbers and actions taken.
- Monitor hydraulic system for 1 hour post-repair for recurrence of symptoms.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- Hydraulic oil under pressure can cause injection injuries—NEVER inspect leaks with hands.
- Always wear PPE: safety goggles, nitrile gloves, oil-resistant apron, safety boots.
- Follow lock-out/tag-out procedures before opening any hydraulic circuit.
- Allow system to cool if Hyd_Temp_lt_70/80=False; oil may be hot (>70°C).
- Clean up all oil spills immediately to prevent slip hazards.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect all hydraulic hoses, fittings, and seals every 500 operating hours.
- Check for oil residue or seepage during daily pre-shift walkaround.
- Replace hoses and seals at manufacturer-recommended intervals or at first sign of wear.
- Maintain oil level within marked range; top off only with approved hydraulic oil.

## 9. Related Faults

**This fault can cause:**
- Hydraulic pump cavitation (if air enters system)
- Actuator malfunction (e.g., loss of clamping force)
- Contaminated oil alarms if leak allows ingress

**This fault can be caused by:**
- Overpressure events rupturing hoses
- Improper maintenance (e.g., loose fittings)
- Aging or degraded seals

**Similar symptoms but different causes:**
- Hyd_A_700207 (hydraulic filter clogged): Pressure drops, but oil level remains normal
- Hyd_A_700208 (pump motor protection): Pump stops, but oil level and filter OK

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_14
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at

---