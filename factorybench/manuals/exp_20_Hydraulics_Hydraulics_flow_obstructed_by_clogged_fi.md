# Troubleshooting Manual: Hydraulics_flow_obstructed_by_clogged_fi

**Fault ID:** exp_20
**Subsystem:** Hydraulics
**Scenario:** {'init': 'Machine is in normal operation; the return filter is clean, hydraulic flow is unobstructed, and the motor operates normally.', 'expected_result': 'Hydraulic fluid flows freely through the return line; the return filter remains unblocked, and the motor continues to run without issue.', 'instead_failure': 'Contamination in the system causes the return filter to become progressively blocked, increasing back pressure. Eventually, this results in hydraulic motor failure.'}

---

## 1. Fault Overview

This fault describes a progressive blockage of the hydraulic return filter (Hyd_Filter) due to system contamination, eventually leading to increased back pressure and hydraulic motor failure. Early detection and remediation are critical, as this fault can cause unplanned downtime, equipment damage, and safety hazards.

## 2. System Impact

- **What stops working?**  
  Hydraulic fluid circulation is impeded, leading to reduced or lost hydraulic function. The hydraulic pump motor may trip or fail due to overpressure.
- **What safety systems activate?**  
  The system triggers alarms (e.g., Hyd_A_700207 for filter clog, Hyd_A_700208 for pump motor protection), disables hydraulics (Hyd_IsEnabled=False), and may stop machine operation to prevent further damage.
- **Production consequences?**  
  Machine operation halts, resulting in production stoppage and potential loss of workpieces or process integrity.

## 3. Observable Symptoms

**Via Alarms:**
- **Hyd_A_700207:** "Hydraulic filter for returning oil is clogged"
- **Hyd_A_700208:** "Hydraulic pump motor protection switch triggered"
- **Hyd_A_700202:** "Pressure monitoring of hydraulics registered hydraulic pressure out of norm" (possible secondary alarm)
- **Hyd_A_700205/700206:** "Hydraulic oil level close to/below minimum" (if leak or overflow occurs)

**Via Sensor Readings:**
- **Hyd_Filter_Ok:** False (expected True under normal operation)
- **Hyd_Pressure:** Elevated or fluctuating above normal range (see system specs for thresholds)
- **Hyd_Pump_Ok:** May transition to False if motor fails
- **Hyd_Pump_isOff:** May become True if protection trips
- **Hyd_Valve_P_Up:** May cycle more frequently as system attempts to compensate for pressure loss

**Expected vs. actual ranges:**
- **Hyd_Pressure:** Normal operating range (see system manual, e.g., 120–140 bar); actual may exceed upper limit or oscillate
- **Hyd_Filter_Ok:** Should be True; actual is False during fault

**Via Visual/Physical Inspection:**
- Unusual noise from hydraulic pump (cavitation, straining)
- Excessive heat at filter housing or pump
- Visible contamination or discoloration in hydraulic oil
- Leaks or seepage around filter assembly
- Burnt smell from motor or oil

## 4. Root Cause Analysis

### 4.1 Primary Root Cause

**Cause:** Progressive blockage of the hydraulic return filter (Hyd_Filter) due to contamination  
**Mechanism:** Contaminants accumulate in the return filter, increasing back pressure. This triggers Hyd_A_700207 (filter clogged), which disables hydraulics (Hyd_IsEnabled=False). Continued operation under these conditions causes the hydraulic pump motor to overwork, eventually tripping the motor protection switch (Hyd_A_700208) and potentially causing motor failure.  
**Probability:** High (based on manipulated variables and scenario description)

**Verification Steps:**
1. Check **Hyd_Filter_Ok** variable: Should be False for at least 5 seconds if filter is clogged.
2. Confirm **Hyd_A_700207** alarm is active.
3. Inspect **Hyd_Pressure**: Should be above normal range or unstable.
4. Check **Hyd_Pump_Ok** and **Hyd_Pump_isOff**: If motor protection has tripped, Hyd_Pump_Ok=False and Hyd_Pump_isOff=True.
5. Physically inspect return filter for blockage or contamination.
   - If filter is visibly clogged or pressure across filter is high, confirm root cause.

### 4.2 Secondary Causes

- **Cause:** Hydraulic oil degradation (sludge formation)
  - **Likelihood:** Medium
  - **Differentiating factors:** Oil appears dark, sludgy, or smells burnt; filter may not be visibly blocked but oil analysis shows high particulate count.
- **Cause:** Incorrect filter installation or bypass valve stuck closed
  - **Likelihood:** Low
  - **Differentiating factors:** Recent maintenance; filter element may be new but not seated properly; bypass valve does not open under high pressure.
- **Cause:** Hydraulic pump internal failure (debris generation)
  - **Likelihood:** Low
  - **Differentiating factors:** Metallic particles in filter; Hyd_Pump_Ok may toggle before filter alarm; abnormal pump noise before filter clog.

### 4.3 Causal Chain

```
[Contamination enters hydraulic system] → [Return filter progressively blocks] → [Back pressure increases] → [Hyd_A_700207 triggers] → [Hyd_IsEnabled=False] → [Pump motor overworks] → [Hyd_A_700208 triggers] → [Hyd_Pump_Ok=False] → [Hydraulic system shutdown]
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- Action: Check active alarms on HMI.
- Expected result: No active alarms.
- If abnormal (Hyd_A_700207 or Hyd_A_700208 present): Go to Step 2.
- If normal: Monitor for intermittent symptoms; check maintenance logs.

**Step 2: Filter Status Check**
- Action: Review **Hyd_Filter_Ok** variable.
- Expected result: True.
- If False: Go to Step 3.
- If True: Go to Step 4.

**Step 3: Physical Inspection**
- Action: Inspect return filter for blockage, discoloration, or contamination.
- Expected result: Filter is clean, oil is clear.
- If blocked/contaminated: Replace filter (see Section 6.2); Go to Step 5.
- If clean: Go to Step 4.

**Step 4: Hydraulic Pressure and Pump Status**
- Action: Check **Hyd_Pressure** and **Hyd_Pump_Ok**.
- Expected result: Pressure within normal range (e.g., 120–140 bar), Hyd_Pump_Ok=True.
- If pressure high/unstable or Hyd_Pump_Ok=False: Suspect secondary causes (see 4.2); escalate to maintenance lead.
- If normal: Intermittent issue; monitor closely.

**Step 5: Post-Repair Verification**
- Action: Reset alarms, restore system, and monitor variables.
- Expected result: No alarms, all variables normal.

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Engage emergency stop and power down the machine.
2. Depressurize hydraulic system per LOTO (lock-out/tag-out) procedure.
3. Isolate hydraulic subsystem to prevent contamination spread.
4. Document current alarms, sensor readings, and take photos of filter and oil condition.

### 6.2 Repair Procedure

**Required:**
- Tools: Filter wrench, oil catch pan, torque wrench (20–30 Nm), clean rags, PPE (gloves, goggles)
- Parts: Replacement return filter element (P/N: HYD-FILT-RET-001), hydraulic oil (P/N: HYD-OIL-ISO46-020)
- Personnel: Qualified maintenance technician (hydraulics certified)
- Estimated time: 60–90 minutes

**Steps:**
1. Confirm system is powered down and depressurized.
2. Place oil catch pan under filter housing.
3. Remove filter housing with filter wrench.
4. Extract used filter element; inspect for contamination type (record findings).
5. Clean filter housing and sealing surfaces.
6. Install new filter element (P/N: HYD-FILT-RET-001); torque housing bolts to 25 Nm.
7. Top up hydraulic oil if required (P/N: HYD-OIL-ISO46-020); ensure **Hyd_Level_Ok=True**.
8. Reinstall housing, check for leaks.
9. Dispose of used filter and oil per environmental regulations.
10. Reset system alarms and restore power.

### 6.3 Verification Tests

After repair:
1. Start hydraulic system; monitor **Hyd_Filter_Ok** (should remain True).
2. Check **Hyd_Pressure** (must stabilize within normal range).
3. Confirm **Hyd_Pump_Ok=True**, **Hyd_Pump_isOff=False** (if system is running).
4. Run functional test: cycle machine axes to verify hydraulic operation.
5. Check for absence of alarms (Hyd_A_700207, Hyd_A_700208).

### 6.4 Return to Service

- Calibrate pressure sensors if required.
- Update maintenance and repair logs.
- Monitor filter and pressure variables for at least 1 hour after restart.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- High-pressure hydraulic oil can cause serious injury—always depressurize before opening any hydraulic line or filter.
- Wear appropriate PPE: gloves, goggles, and protective clothing.
- Follow lock-out/tag-out (LOTO) procedures before starting work.
- Allow system components to cool if overheated; oil above 60°C can cause burns.
- Dispose of contaminated oil and filters according to hazardous waste regulations.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect return filter every 500 operating hours or per manufacturer’s schedule.
- Monitor **Hyd_Filter_Ok** and **Hyd_Pressure** for early signs of blockage.
- Replace hydraulic oil every 2000 hours or annually, whichever comes first.
- Use only approved hydraulic oil and filters.
- Train operators to recognize early warning signs (noise, heat, minor alarms).

## 9. Related Faults

**This fault can cause:**
- Hyd_A_700208 (pump motor protection trip)
- Hyd_A_700202 (pressure out of norm)
- Premature pump wear/failure

**This fault can be caused by:**
- Contaminated hydraulic oil
- Poor maintenance practices
- Ingress of foreign material during service

**Similar symptoms but different causes:**
- Hyd_A_700202 due to accumulator failure (not filter blockage)
- Hyd_A_700206 (oil level below minimum) due to leaks
- Hyd_A_700203/700204 (over-temperature) due to cooling failure

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_20
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at