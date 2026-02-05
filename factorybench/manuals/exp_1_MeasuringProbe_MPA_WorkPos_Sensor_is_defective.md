# Troubleshooting Manual: MPA_WorkPos_Sensor_is_defective

**Fault ID:** exp_1
**Subsystem:** MeasuringProbe
**Scenario:** {'init': 'Measuring probe axis (MPA) is in one of the following states: A) Already at working position (WP = true, m2WP = true, IP = false, m2IP = false) B) Moving toward working position (WP = false, m2WP = true, IP = false, m2IP = false)', 'action': None, 'expected_result': 'A) WP remains true (MPA continues to be recognized at working position) B) WP switches to true within a few seconds (MPA reaches and is recognized at working position)', 'instead_failure': 'A) WP suddenly switches to false (spontaneous sensor failure - MPA is no longer detected despite not moving) B) WP never becomes true (MPA is not recognized at working position after movement)'}

---

## 1. Fault Overview

This fault involves the measuring probe axis (MPA) failing to be recognized at its working position, either spontaneously (sensor drop-out while stationary) or after movement (never detected at working position). This disrupts automated measurement cycles, risking incorrect part qualification and production delays.

## 2. System Impact

- **What stops working?**  
  Automated measurement routines halt; the measuring probe cannot perform dimensional checks.
- **What safety systems activate?**  
  System interlocks may prevent further axis movement; alarms inhibit probe activation.
- **Production consequences?**  
  Workpieces cannot be measured, causing process interruptions, possible scrap, and reduced throughput.

## 3. Observable Symptoms

**Via Alarms:**
- MPA_A_701124: "Measuring probe did not reach end position in time"
- MPA_A_701125: "Measuring probe axis both endposition sensor inputs indicate true"

**Via Sensor Readings:**
- `MPA_WorkPos`: Expected = True (when probe at working position); Actual = False (unexpectedly drops or never becomes True)
- `MPA_toWorkPos`: Expected = True during movement; Actual = True, but `MPA_WorkPos` remains False
- `MPA_InitPos`: Expected = False at working position; Actual = False (consistent)
- `MP_Inactive`: May remain True (probe not activated due to position error)

**Via Visual/Physical Inspection:**
- Probe appears stationary at working position but system does not recognize it
- No audible movement after axis command, but alarms persist
- No visible damage, but probe cover may be closed or open as expected

## 4. Root Cause Analysis

### 4.1 Primary Root Cause
**Cause:** Failure of the working position sensor (MPA_WorkPos) or its wiring  
**Mechanism:** Sensor does not signal "True" when probe is at working position, causing system to believe probe is absent or not positioned  
**Probability:** High

**Verification Steps:**
1. Check `MPA_WorkPos` variable in diagnostics; should be True when probe is physically at working position.
2. Manually move probe to working position; observe if `MPA_WorkPos` transitions to True.
3. If `MPA_WorkPos` remains False, inspect sensor wiring and connections for continuity (use multimeter, threshold < 1Ω).
4. If wiring is intact, replace sensor and retest.
5. If `MPA_WorkPos` transitions to True after sensor replacement, confirm root cause.

### 4.2 Secondary Causes

- **Cause:** Axis drive failure (probe never reaches working position)
  - **Likelihood:** Medium
  - **Differentiating factors:** `MPA_toWorkPos` is True, but probe physically does not move; audible drive errors; other axis variables abnormal

- **Cause:** PLC logic fault or software error
  - **Likelihood:** Low
  - **Differentiating factors:** All sensors and wiring test OK; variable transitions do not match physical state; may require software diagnostics

- **Cause:** Mechanical obstruction (probe blocked from reaching position)
  - **Likelihood:** Medium
  - **Differentiating factors:** Physical resistance, probe cannot be moved by hand, possible cover interference (`MPC_close`/`MPC_Closed` states abnormal)

### 4.3 Causal Chain

```
[Sensor Failure at Working Position] → [MPA_WorkPos remains False] → [MPA_A_701124 alarm triggers] → [Measurement cycle aborts]
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- Action: Check `MPA_WorkPos` value in system diagnostics.
- Expected result: True when probe at working position.
- If abnormal: Go to Step 2.
- If normal: Go to Step 5.

**Step 2: Physical Position Verification**
- Action: Visually confirm probe is at working position.
- Expected result: Probe physically at position.
- If abnormal: Go to Step 3.
- If normal: Go to Step 4.

**Step 3: Axis Movement Test**
- Action: Command axis to working position (`MPA_toWorkPos=True`).
- Expected result: Probe moves smoothly to position.
- If abnormal: Inspect axis drive and mechanical components.
- If normal: Go to Step 4.

**Step 4: Sensor and Wiring Check**
- Action: Inspect working position sensor and wiring for damage or loose connections.
- Expected result: No visible damage; continuity < 1Ω.
- If abnormal: Repair/replace sensor or wiring.
- If normal: Go to Step 5.

**Step 5: PLC/Software Diagnostics**
- Action: Review PLC logic and software for correct variable transitions.
- Expected result: Variables match physical state.
- If abnormal: Contact controls engineer for further diagnostics.
- If normal: Fault resolved.

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Power down the measuring probe subsystem and main axis drive.
2. Depressurize pneumatic/hydraulic systems if applicable.
3. Lock-out/tag-out electrical supply to probe axis.
4. Document current variable states and alarm history.

### 6.2 Repair Procedure

**Required:**
- Tools: Multimeter, insulated screwdrivers, sensor removal tool, torque wrench (5 Nm)
- Parts: Working position sensor (P/N: MPRO-AXIS-001), wiring harness (P/N: MPRO-AXIS-002)
- Personnel: Qualified maintenance technician (electrical/mechanical)
- Estimated time: 45 minutes

**Steps:**
1. Remove probe cover (if closed) using sensor removal tool.
2. Disconnect wiring harness from working position sensor.
3. Test continuity of wiring harness (< 1Ω); replace if faulty.
4. Remove faulty sensor; install new sensor (P/N: MPRO-AXIS-001), torque to 5 Nm.
5. Reconnect wiring harness, ensuring secure connections.
6. Reinstall probe cover; verify actuator operation (`MPC_close` and `MPC_Closed` True).
7. Power up subsystem and clear lock-out/tag-out.

### 6.3 Verification Tests

After repair:
1. Command axis to working position (`MPA_toWorkPos=True`).
2. Confirm `MPA_WorkPos` transitions to True within 5 seconds.
3. Run measurement cycle; confirm probe activates (`MP_Inactive=False`).
4. Check for absence of alarms (MPA_A_701124, MPA_A_701125).

### 6.4 Return to Service

- Calibrate working position sensor using system calibration procedure.
- Update maintenance log and fault documentation.
- Monitor `MPA_WorkPos` and related alarms for 1 production shift.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- Electrical shock hazard: Always lock-out/tag-out before sensor work.
- Moving axis hazard: Ensure axis is powered down and immobilized.
- PPE required: Safety glasses, insulated gloves, steel-toe boots.
- Beware of stored energy in pneumatic/hydraulic systems.
- Never bypass interlocks or safety covers during troubleshooting.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect working position sensor and wiring every 3 months.
- Monitor for loose connections and corrosion.
- Schedule sensor replacement every 24 months or 10,000 cycles.
- Log all sensor faults and repairs for trend analysis.

## 9. Related Faults

**This fault can cause:**
- Measurement cycle aborts
- Downstream process delays
- False scrap of good parts

**This fault can be caused by:**
- Upstream axis drive failures
- PLC logic errors
- Probe cover actuator faults

**Similar symptoms but different causes:**
- Axis fails to move due to drive fault
- Probe cover fails to open/close (`MPC_isOpen`, `MPC_close` abnormal)
- PLC variable mapping errors

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_1
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at

---