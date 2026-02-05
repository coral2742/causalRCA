# Troubleshooting Manual: Highly_Contaminated_Coolant

**Fault ID:** exp_28
**Subsystem:** Coolant
**Scenario:** {'init': 'Machine is in normal operation; coolant is within spec and both Hydac and fleece filters are clean.', 'expected_result': 'Coolant flows freely through both filters; pump maintains normal pressure and no alarms occur.', 'instead_failure': 'Due to highly contaminated coolant, the Hydac filter clogs first and then the fleece filter blocks completely. Coolant circulation stops, causing the low‑pressure (ND) protection switch to trip and shut down the pump. Operator acknowledges the fault and terminates the experiment.'}

---

## 1. Fault Overview

This fault scenario involves sequential clogging of both the Hydac (fine particle) and fleece filters in the coolant subsystem of a CNC vertical lathe. As a result, coolant circulation ceases, triggering the low-pressure pump protection switch and shutting down the pump. This fault is critical because it halts coolant flow, risking tool damage, overheating, and production downtime.

## 2. System Impact

- **What stops working?**  
  Coolant flow through both filters is blocked, causing the low-pressure pump to shut down and halting coolant delivery to the cutting zone.
- **What safety systems activate?**  
  The low-pressure pump motor protection switch trips (LP_A_700301), and the system disables the pump to prevent further damage. Associated alarms are triggered for filter clogging and fleece depletion.
- **Production consequences?**  
  Machining operations are interrupted, risking tool overheating, surface finish degradation, and potential workpiece damage. Production throughput is reduced until the fault is resolved.

## 3. Observable Symptoms

**Via Alarms:**
- CLF_A_700307: "Coolant filter is clogged"
- F_A_700313: "Fleece supply in coolant is empty and needs replacement"
- LP_A_700301: "Coolant low pressure (LP) pump motor protection switch triggered"

**Via Sensor Readings:**
- CLF_Filter_Ok: False (should be True)
- F_Filter_Ok: False (should be True)
- LP_Pump_Ok: False (should be True)
- LP_Pump_On: False (should be True during operation)
- HP_Pump_isOff: True (expected if LP_Pump_On is commanded)
- Coolant pressure readings: 0 bar (expected: 2–6 bar during normal operation)

**Via Visual/Physical Inspection:**
- No visible coolant flow at nozzles
- Filters appear saturated or discolored
- Fleece filter compartment empty or loaded with debris
- Possible burnt odor from pump if overheated
- Audible click or trip from pump protection switch

## 4. Root Cause Analysis

### 4.1 Primary Root Cause

**Cause:** Sequential clogging of the Hydac filter followed by complete blockage of the fleece filter due to highly contaminated coolant.

**Mechanism:**  
Contaminated coolant causes rapid accumulation of debris in the Hydac filter, reducing flow and triggering CLF_Filter_Ok=False. As flow further decreases, the fleece filter becomes overloaded and blocks completely (F_Filter_Ok=False). The resulting lack of coolant flow causes the low-pressure pump to operate dry, tripping its motor protection switch (LP_Pump_Ok=False), and shutting down the pump.

**Probability:** High (directly observed in digital twin experiment exp_28)

**Verification Steps:**
1. Check CLF_Filter_Ok and F_Filter_Ok variables; both should read False for >5 seconds.
2. Confirm LP_Pump_Ok is False and LP_A_700301 alarm is active.
3. Inspect filter and fleece physically for clogging/depletion.
4. If both filters are blocked and pump protection switch is tripped, primary cause is confirmed.

### 4.2 Secondary Causes

- **Cause:** Electrical failure in low-pressure pump motor  
  - **Likelihood:** Medium  
  - **Differentiating factors:** LP_Pump_Ok=False, but CLF_Filter_Ok and F_Filter_Ok remain True; no filter alarms.
- **Cause:** Coolant tank level below minimum (CLT_Level_lt_Min=True)  
  - **Likelihood:** Low  
  - **Differentiating factors:** CLT_A_700310 alarm active; filters not clogged; tank physically empty.
- **Cause:** Blockage in coolant lines downstream of filters  
  - **Likelihood:** Low  
  - **Differentiating factors:** Filters appear clean; line blockage detected via pressure drop after filters.

### 4.3 Causal Chain

```
[Coolant contamination] → [Hydac filter clogging (CLF_Filter_Ok=False)] → [Fleece filter blockage (F_Filter_Ok=False)] → [Low-pressure pump protection switch trips (LP_Pump_Ok=False)] → [Pump shutdown & coolant flow stops]
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- Action: Check active alarms on HMI.
- Expected result: No coolant subsystem alarms.
- If abnormal (CLF_A_700307, F_A_700313, LP_A_700301 active): Go to Step 2.
- If normal: Go to Step 5 (other causes).

**Step 2: Sensor Status Check**
- Action: Read CLF_Filter_Ok, F_Filter_Ok, LP_Pump_Ok variables.
- Expected result: All True.
- If any False: Go to Step 3.
- If all True: Go to Step 5.

**Step 3: Physical Inspection**
- Action: Inspect Hydac and fleece filters for clogging/depletion.
- Expected result: Filters clean, fleece present.
- If filters blocked/fleece depleted: Go to Step 4.
- If filters clean: Go to Step 5.

**Step 4: Pump Protection Switch**
- Action: Inspect low-pressure pump motor protection switch.
- Expected result: Switch not tripped.
- If tripped: Confirm primary root cause; proceed to remediation.
- If not tripped: Go to Step 5.

**Step 5: Alternate Fault Investigation**
- Action: Check coolant tank level (CLT_Level_lt_Min), electrical connections, and coolant lines.
- Expected result: All normal.
- If abnormal: Address according to secondary causes.

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Power down the CNC lathe and engage lock-out/tag-out procedures.
2. Depressurize coolant lines and isolate coolant subsystem.
3. Document all active alarms, sensor readings, and physical observations.

### 6.2 Repair Procedure

**Required:**
- Tools: Filter wrench, fleece replacement tool, torque wrench (10 Nm), inspection flashlight, multimeter
- Parts:
  - Hydac filter element (P/N: COOL-FILT-HYD-002)
  - Fleece roll (P/N: COOL-FLEECE-001)
  - Low-pressure pump protection switch (if damaged, P/N: COOL-PUMP-LP-003)
- Personnel: Trained maintenance technician, coolant subsystem certified
- Estimated time: 60–90 minutes

**Steps:**
1. Remove Hydac filter housing; extract clogged filter element.
2. Install new Hydac filter (torque to 10 Nm, ensure O-ring seated).
3. Open fleece compartment; remove spent fleece roll.
4. Install new fleece roll, ensuring correct feed direction.
5. Inspect low-pressure pump protection switch; reset if tripped, replace if damaged.
6. Reassemble all components; check for leaks and proper seating.
7. Restore power and coolant pressure; verify no alarms on HMI.

### 6.3 Verification Tests

After repair:
1. Command low-pressure pump ON; observe LP_Pump_Ok=True, LP_Pump_On=True.
2. Check CLF_Filter_Ok=True and F_Filter_Ok=True for >30 seconds.
3. Confirm coolant flow at nozzles and pressure in range (2–6 bar).
4. Monitor for absence of CLF_A_700307, F_A_700313, and LP_A_700301 alarms.

### 6.4 Return to Service

- Calibrate coolant pressure sensors if replaced.
- Update maintenance log and fault report.
- Monitor coolant subsystem for 1 hour post-repair for recurrence.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- Coolant under pressure can cause injury; always depressurize before opening lines.
- Use PPE: chemical-resistant gloves, safety goggles, and protective clothing.
- Lock-out/tag-out electrical supply before servicing pumps.
- Beware of hot surfaces and residual coolant temperature.
- Dispose of used filters and fleece according to hazardous waste protocols.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect Hydac and fleece filters every 100 operating hours.
- Replace filters/fleece when differential pressure exceeds 1.5 bar or visual contamination is present.
- Schedule coolant quality analysis monthly.
- Maintain coolant tank cleanliness and monitor wear indicators.

## 9. Related Faults

**This fault can cause:**
- Overheating of cutting tools (potential tool breakage)
- Workpiece surface damage due to lack of coolant

**This fault can be caused by:**
- Introduction of excessive particulate matter into coolant tank
- Failure to replace filters/fleece at recommended intervals

**Similar symptoms but different causes:**
- LP_A_700301 due to electrical pump failure (not filter clogging)
- CLT_A_700310 due to low coolant tank level (not filter blockage)

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_28
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at

---