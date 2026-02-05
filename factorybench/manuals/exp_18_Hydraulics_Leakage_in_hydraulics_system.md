# Troubleshooting Manual: Leakage_in_hydraulics_system

**Fault ID:** exp_18
**Subsystem:** Hydraulics
**Scenario:** {'init': 'Machine is in normal operation; hydraulic oil level is stable and the hydraulic pump motor is running normally.', 'expected_result': 'Hydraulic oil level remains within acceptable limits, and the pump motor operates continuously without interruption.', 'instead_failure': 'Due to Leakage the oil level begins to fall continuously, eventually leading to motor failure.'}

---

## 1. Fault Overview

This fault scenario describes a progressive hydraulic oil leak in the CNC vertical lathe’s hydraulic subsystem, resulting in a continuous drop in oil level. If unaddressed, the falling oil level leads to hydraulic pump motor failure, causing system downtime and potential equipment damage.

## 2. System Impact

- **What stops working?**  
  The hydraulic pump motor (Hyd_Pump_Ok) fails, causing loss of hydraulic pressure and disabling all hydraulically actuated machine functions.
- **What safety systems activate?**  
  The system triggers multiple alarms (e.g., Hyd_A_700205, Hyd_A_700206, Hyd_A_700208), disables hydraulics (Hyd_IsEnabled=False), and may automatically stop machine operation to prevent further damage.
- **Production consequences?**  
  Machine operation halts, leading to unplanned downtime, possible part scrap, and risk of secondary faults if not promptly addressed.

## 3. Observable Symptoms

**Via Alarms:**
- Hyd_A_700205: Warning – Hydraulic oil level is close to minimum
- Hyd_A_700206: Alarm – Hydraulic oil level is below minimum
- Hyd_A_700208: Alarm – Hydraulic pump motor protection switch triggered

**Via Sensor Readings:**
- Hyd_Level_Ok: Changes from True to False as oil level drops
  - Expected: True (oil level within limits)
  - Actual: False (oil level low or below minimum)
- Hyd_Pump_Ok: Changes from True to False if pump motor fails
- Hyd_Pump_isOff: May become True if pump is disabled by protection
- Hyd_Pressure: Drops below normal operating range (refer to machine-specific setpoints)

**Via Visual/Physical Inspection:**
- Visible oil leakage beneath machine or around hydraulic lines/fittings
- Low oil level in hydraulic reservoir (Hyd_Level)
- Unusual noises from hydraulic pump (e.g., cavitation, rattling)
- Smell of hydraulic oil in machine area
- Possible overheating of pump motor

## 4. Root Cause Analysis

### 4.1 Primary Root Cause

**Cause:** Hydraulic oil leakage leading to insufficient oil level  
**Mechanism:** Continuous loss of oil reduces reservoir level, causing Hyd_Level_Ok to become False. If unaddressed, the pump draws air, leading to cavitation, overheating, and eventual pump motor failure (Hyd_Pump_Ok=False).  
**Probability:** High (based on manipulated variables and symptom progression)

**Verification Steps:**
1. Check Hyd_Level_Ok variable in HMI/diagnostics:
   - If False, proceed to step 2.
2. Inspect hydraulic reservoir visually:
   - Oil level below minimum mark confirms low level.
3. Examine area under/around machine for oil pooling or wetness.
4. Review Hyd_A_700205 and Hyd_A_700206 alarm history:
   - Confirm sequence of warnings and alarms.
5. Check Hyd_Pump_Ok status:
   - If False, and protection switch (Hyd_A_700208) is active, motor failure is likely due to low oil.
6. If oil leak is found and alarms match, primary cause is confirmed.

### 4.2 Secondary Causes

- **Cause:** Hydraulic filter clogging (Hyd_A_700207)
  - **Likelihood:** Medium
  - **Differentiating factors:** Hyd_Filter_Ok=False, Hyd_A_700207 alarm active, but oil level remains normal.
- **Cause:** Pump motor electrical failure (not related to oil level)
  - **Likelihood:** Low
  - **Differentiating factors:** Hyd_Level_Ok=True, no oil leak, but Hyd_Pump_Ok=False and Hyd_A_700208 active.
- **Cause:** Faulty level sensor (Hyd_Level)
  - **Likelihood:** Low
  - **Differentiating factors:** Visual oil level normal, but Hyd_Level_Ok=False and no actual leak observed.

### 4.3 Causal Chain

```
Hydraulic oil leak → Oil level drops (Hyd_Level_Ok=False) → Hyd_A_700205/Hyd_A_700206 triggered → Pump draws air/cavitates → Pump motor overheats → Hyd_Pump_Ok=False, Hyd_A_700208 triggered → Hydraulic system disabled
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- Action: Check for active alarms (Hyd_A_700205, Hyd_A_700206, Hyd_A_700208)
- Expected result: No alarms; all clear
- If abnormal: Go to Step 2
- If normal: Resume operation, monitor system

**Step 2: Oil Level Verification**
- Action: Check Hyd_Level_Ok variable and visually inspect reservoir
- Expected result: Hyd_Level_Ok=True, oil at correct level
- If abnormal: Go to Step 3
- If normal: Go to Step 4

**Step 3: Leak Inspection**
- Action: Inspect for visible oil leaks under/around machine and hydraulic lines
- Expected result: No leaks
- If abnormal: Repair leak (see Section 6), then refill oil
- If normal: Go to Step 4

**Step 4: Pump Motor Status**
- Action: Check Hyd_Pump_Ok and Hyd_A_700208
- Expected result: Hyd_Pump_Ok=True, no motor protection alarm
- If abnormal: Investigate motor failure (may require replacement)

**Step 5: Filter and Sensor Check**
- Action: Check Hyd_Filter_Ok and Hyd_Level sensor for faults
- Expected result: Both True
- If abnormal: Replace filter or sensor as needed

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Power down the machine and engage lock-out/tag-out procedures.
2. Depressurize the hydraulic system per OEM instructions.
3. Contain and clean up any spilled oil using absorbent materials.
4. Isolate the leak source to prevent further oil loss.
5. Document alarm history, sensor readings, and physical findings.

### 6.2 Repair Procedure

**Required:**
- Tools: Wrenches (metric/imperial), oil drain pan, hydraulic line clamps, torque wrench, inspection mirror, absorbent pads
- Parts: Replacement hydraulic hose/seal/gasket (as identified), hydraulic oil (P/N: HYD-GEN-OIL-001), possible pump motor (P/N: HYD-PUMP-001)
- Personnel: Certified maintenance technician (hydraulic systems)
- Estimated time: 2–4 hours (depending on leak location and motor status)

**Steps:**
1. Identify and mark the leak location.
2. Remove affected hydraulic line, fitting, or component.
   - Torque all fittings to OEM specification (e.g., 45 Nm for 1/2" fittings).
3. Replace faulty part with OEM replacement (reference part number).
4. Inspect and clean surrounding area.
5. Reinstall components, ensuring all seals/gaskets are correctly seated.
6. Refill reservoir with correct hydraulic oil to specified level.
7. Bleed air from system per OEM procedure.
8. Reset any triggered motor protection switches.
9. Verify all connections for leaks at operating pressure.

### 6.3 Verification Tests

After repair:
1. Power up and enable hydraulics (Hyd_IsEnabled=True).
2. Monitor Hyd_Level_Ok, Hyd_Pump_Ok, Hyd_Pressure for normal values:
   - Hyd_Level_Ok=True
   - Hyd_Pump_Ok=True
   - Hyd_Pressure within specified range (see machine manual)
3. Confirm all alarms are cleared.
4. Run a functional test cycle to ensure normal operation.

### 6.4 Return to Service

- Calibrate level sensor if replaced.
- Update maintenance and repair logs with fault details and corrective actions.
- Monitor system for at least 1 hour for recurrence of leaks or alarms.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- Hydraulic oil under pressure can cause serious injury—always depressurize before opening any lines.
- Wear appropriate PPE: safety glasses, oil-resistant gloves, and protective clothing.
- Strictly follow lock-out/tag-out procedures before repair.
- Be aware of hot surfaces and oil—risk of burns.
- Clean up spills immediately to prevent slip hazards.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect hydraulic lines, fittings, and reservoir for leaks every 250 operating hours.
- Monitor Hyd_Level_Ok and investigate any drops immediately.
- Replace seals and hoses at recommended intervals (see OEM schedule).
- Check and record oil level at each shift start.
- Maintain a log of all hydraulic faults and repairs.

## 9. Related Faults

**This fault can cause:**
- Hydraulic pump cavitation and failure
- Overheating of pump motor
- Loss of machine axis control (if hydraulics are used for clamping/positioning)

**This fault can be caused by:**
- Upstream seal failure
- Excessive system pressure (leading to burst hoses)
- Poor maintenance practices

**Similar symptoms but different causes:**
- Hyd_A_700207 (hydraulic filter clogging)
- Electrical failure of pump motor (Hyd_A_700208 without oil loss)
- Faulty level sensor (false Hyd_Level_Ok=False)

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_18
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at

---