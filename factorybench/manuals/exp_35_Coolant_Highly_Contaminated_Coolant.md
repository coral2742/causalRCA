# Troubleshooting Manual: Highly_Contaminated_Coolant

**Fault ID:** exp_35
**Subsystem:** Coolant
**Scenario:** {'init': 'Machine is in normal operation; Hydac primary filter is clean, high‑pressure pump is running normally.', 'expected_result': 'Coolant flows freely through the Hydac filter; pump maintains stable pressure and flow; no alarms or trips occur.', 'instead_failure': 'As debris accumulates, the Hydac filter becomes fully blocked, starving the pump inlet. The pump protection triggers, shutting off the pump.'}

---

## 1. Fault Overview

This fault occurs when the Hydac primary coolant filter in the CNC vertical lathe becomes fully blocked by accumulated debris, resulting in starvation of the high-pressure pump inlet. The pump protection system then triggers, shutting off the high-pressure pump to prevent damage. This fault disrupts coolant flow, risking overheating and tool wear during machining.

## 2. System Impact

- **What stops working?**  
  The high-pressure coolant pump (HP_Pump) ceases operation, halting high-pressure coolant delivery to the cutting zone.
- **What safety systems activate?**  
  The pump motor protection switch engages, and the expert system disables the pump (HP_Pump_isOff=True) to prevent mechanical damage.
- **Production consequences?**  
  Machining operations may be interrupted due to insufficient cooling, risking tool failure, workpiece damage, and unplanned downtime.

## 3. Observable Symptoms

**Via Alarms:**
- CLF_A_700307: "Coolant filter is clogged"
- HP_A_700304: "Coolant high pressure (HP) pump motor protection switch triggered"

**Via Sensor Readings:**
- CLF_Filter_Ok: False (should be True in normal operation)
- HP_Pump_Ok: False (should be True)
- HP_Pump_isOff: True (should be False during operation)
- Coolant pressure readings: Below operational threshold (actual: near zero; expected: nominal system pressure)

**Via Visual/Physical Inspection:**
- No coolant flow from high-pressure nozzles
- Audible change: Pump stops running; possible relay click or motor trip sound
- Possible odor of overheated components if running dry
- Filter housing may feel unusually warm due to restricted flow

## 4. Root Cause Analysis

### 4.1 Primary Root Cause

**Cause:** Complete blockage of the Hydac primary coolant filter by debris  
**Mechanism:** Debris accumulation restricts coolant flow, starving the high-pressure pump inlet. The pump draws vacuum, triggering the motor protection switch (HP_A_700304), which disables the pump (HP_Pump_isOff=True) and sets HP_Pump_Ok=False.  
**Probability:** High (most likely cause given symptoms and causal chain)

**Verification Steps:**
1. Check CLF_Filter_Ok status in HMI or diagnostic panel; should read False if blocked.
2. Confirm CLF_A_700307 alarm is active.
3. Inspect HP_Pump_Ok; should be False.
4. Measure coolant pressure at pump outlet; expected: near zero if blocked.
5. If CLF_Filter_Ok=True, investigate secondary causes.

### 4.2 Secondary Causes

- **Cause:** High-pressure pump mechanical failure (e.g., seized bearings)
  - **Likelihood:** Medium
  - **Differentiating factors:** HP_Pump_Ok=False but CLF_Filter_Ok=True; no filter alarm.
- **Cause:** Electrical fault in pump motor protection circuit
  - **Likelihood:** Low
  - **Differentiating factors:** HP_A_700304 triggers without filter blockage; CLF_Filter_Ok=True.
- **Cause:** Incorrect fleece filter installation causing upstream restriction
  - **Likelihood:** Low
  - **Differentiating factors:** F_A_700313 alarm may be present; F_Filter_Ok=False.

### 4.3 Causal Chain

```
[Hydac filter blockage] → [CLF_Filter_Ok=False] → [CLF_A_700307 alarm] → [Pump inlet starvation] → [HP_Pump_Ok=False] → [HP_A_700304 alarm] → [HP_Pump_isOff=True] → [No coolant flow]
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- Action: Check for active alarms CLF_A_700307 and HP_A_700304.
- Expected result: No alarms present.
- If abnormal (alarms present): Go to Step 2.
- If normal: Go to Step 5 (system is healthy).

**Step 2: Filter Status Verification**
- Action: Review CLF_Filter_Ok variable.
- Expected result: True.
- If False: Go to Step 3.
- If True: Go to Step 4.

**Step 3: Physical Filter Inspection**
- Action: Inspect Hydac filter for visible blockage or excessive debris.
- Expected result: Filter is clean.
- If blocked: Replace filter (see Section 6.2).
- If clean: Go to Step 4.

**Step 4: Pump Status Check**
- Action: Check HP_Pump_Ok and HP_Pump_isOff variables.
- Expected result: HP_Pump_Ok=True, HP_Pump_isOff=False.
- If HP_Pump_Ok=False and HP_Pump_isOff=True: Suspect pump or electrical fault; investigate motor protection circuit.
- If HP_Pump_Ok=True: Investigate upstream coolant supply issues.

**Step 5: Restore Operation**
- Action: Clear alarms, verify coolant flow resumes, monitor for recurrence.

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Power down the machine and engage lock-out/tag-out procedures.
2. Depressurize the coolant system by opening bleed valves.
3. Isolate the coolant subsystem electrically and mechanically.
4. Document all active alarms, sensor readings, and physical observations.

### 6.2 Repair Procedure

**Required:**
- Tools: 24mm socket wrench, torque wrench (preset to 30 Nm), filter puller, catch basin, nitrile gloves
- Parts: Hydac filter element (P/N: COOL-FILT-HYD-002)
- Personnel: Trained maintenance technician (Level II or higher)
- Estimated time: 45 minutes

**Steps:**
1. Verify system is powered down and depressurized.
2. Remove filter housing bolts using 24mm socket; torque spec for reassembly: 30 Nm.
3. Extract blocked Hydac filter using filter puller; avoid spillage.
4. Inspect housing for debris; clean with lint-free cloth.
5. Install new filter element (P/N: COOL-FILT-HYD-002); ensure correct orientation and seal integrity.
6. Reassemble housing; tighten bolts to 30 Nm.
7. Reconnect system, restore power, and repressurize coolant circuit.
8. Reset alarms via HMI; verify CLF_Filter_Ok=True.
9. Inspect for leaks and confirm proper installation.

### 6.3 Verification Tests

After repair:
1. Start coolant subsystem; observe for 5 minutes.
2. Confirm CLF_Filter_Ok=True, HP_Pump_Ok=True, HP_Pump_isOff=False.
3. Check coolant flow at high-pressure nozzles; flow should be steady.
4. Ensure no alarms (CLF_A_700307, HP_A_700304) are present.

### 6.4 Return to Service

- Calibrate coolant pressure sensors if disturbed during repair.
- Update maintenance log with fault details, actions taken, and replaced parts.
- Monitor CLF_Filter_Ok and HP_Pump_Ok for at least one production cycle.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- Risk of high-pressure coolant spray; always depressurize before opening filter housing.
- Wear PPE: safety goggles, nitrile gloves, protective apron.
- Lock-out/tag-out electrical and hydraulic sources before repair.
- Beware of hot surfaces and residual coolant; allow system to cool prior to service.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect Hydac filter every 250 operating hours or monthly, whichever comes first.
- Monitor CLF_Filter_Ok variable daily via HMI.
- Replace filter element when differential pressure exceeds manufacturer threshold or at scheduled intervals.
- Record filter changes and debris findings in maintenance log.

## 9. Related Faults

**This fault can cause:**
- HP_A_700304 (pump motor protection trip)
- Tool overheating and accelerated wear
- CLT_A_700310 (coolant tank level drop due to leaks)

**This fault can be caused by:**
- Poor coolant quality (excessive debris)
- Inadequate upstream filtration (fleece filter failure)
- Extended operation without scheduled filter maintenance

**Similar symptoms but different causes:**
- HP_Pump_Ok=False due to pump mechanical failure (not filter blockage)
- CLF_A_700307 triggered by sensor malfunction
- No coolant flow due to low tank level (CLT_Level_lt_Min=True, CLT_A_700310)

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_35
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at

---