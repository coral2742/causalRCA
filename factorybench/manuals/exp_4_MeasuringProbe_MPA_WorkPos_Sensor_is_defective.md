# Troubleshooting Manual: MPA_WorkPos_Sensor_is_defective

**Fault ID:** exp_4
**Subsystem:** MeasuringProbe
**Scenario:** {'init': 'Measuring probe axis (MPA) is at working position (WP = true, m2WP = true, IP = false, m2IP = false)', 'action': 'The control system commands a move to the initial position (m2IP = true -> m2WP = false)', 'expected_result': 'WP switches to false as the MPA leaves working position, and IP switches to true within a few seconds (MPA reaches and is recognized at initial position)', 'instead_failure': 'WP incorrectly remains true even after movement, while IP becomes true - the system can no longer determine the actual position of the MPA'}

---

## 1. Fault Overview

This fault occurs when the measuring probe axis (MPA) is commanded to move from the working position (MPA_WorkPos=True) to the initial position (MPA_InitPos=True), but the system incorrectly continues to report MPA_WorkPos=True even after the axis has moved. This leads to ambiguity in the probe’s actual position, which can compromise measurement accuracy and system safety.

## 2. System Impact

- **Operational Impact:** The CNC lathe cannot reliably determine the measuring probe’s position, halting automated measurement cycles and potentially blocking further axis movements.
- **Safety Systems:** Interlocks may activate to prevent probe or workpiece damage; cover actuators (MPC_close) may not function as intended.
- **Production Consequences:** Measurement routines are interrupted, risking scrap parts, increased downtime, and possible damage to probe or workpiece due to position uncertainty.

## 3. Observable Symptoms

**Via Alarms:**
- MPA_A_701124: "Measuring probe did not reach end position in time."
- MPA_A_701125: "Measuring probe axis both endposition sensor inputs indicate true."

**Via Sensor Readings:**
- MPA_WorkPos: Remains TRUE even after axis is commanded away from working position.
- MPA_InitPos: Switches to TRUE as expected.
- MPA_toInitPos: TRUE during movement command.
- MPA_toWorkPos: FALSE during movement to initial position.
- MP_Inactive: May remain FALSE (probe considered active) when it should be inactive.
- Expected: MPA_WorkPos=FALSE, MPA_InitPos=TRUE after movement.
- Actual: MPA_WorkPos=TRUE, MPA_InitPos=TRUE (invalid state).

**Via Visual/Physical Inspection:**
- Axis physically located at initial position (probe hidden), but system display shows "Working Position."
- No audible movement after command (axis has stopped).
- No visible probe at measuring position.
- Cover actuator (MPC_close/MPC_Closed) may not engage as expected.

## 4. Root Cause Analysis

### 4.1 Primary Root Cause
**Cause:** Faulty or stuck working position sensor (MPA_WorkPos) continues to indicate TRUE after axis leaves working position.
**Mechanism:** The sensor fails to reset when the axis moves, causing the system to believe the probe is still in the working position, even as initial position sensor (MPA_InitPos) correctly indicates TRUE. This creates a logical conflict and triggers alarms.
**Probability:** High (most likely based on manipulated variables in digital twin exp_4).

**Verification Steps:**
1. Inspect MPA_WorkPos sensor status in control panel; confirm it remains TRUE after axis movement.
2. Manually move axis between positions; observe if MPA_WorkPos changes state.
3. Check wiring and connections to MPA_WorkPos sensor for damage or loose contacts.
4. If MPA_WorkPos remains TRUE regardless of axis position, sensor is faulty.
5. If sensor toggles correctly, proceed to secondary causes.

### 4.2 Secondary Causes

- **Cause:** PLC logic error or software bug causing MPA_WorkPos variable to remain latched.
  - **Likelihood:** Medium
  - **Differentiating factors:** Sensor physically toggles, but PLC status does not update; check PLC diagnostics/logs.

- **Cause:** Mechanical obstruction preventing axis from fully leaving working position.
  - **Likelihood:** Low
  - **Differentiating factors:** Axis movement is incomplete; visual inspection shows axis not fully at initial position.

- **Cause:** Simultaneous activation of both end position sensors (MPA_WorkPos=True AND MPA_InitPos=True).
  - **Likelihood:** Medium
  - **Differentiating factors:** Both sensors indicate TRUE; triggers MPA_A_701125 alarm.

### 4.3 Causal Chain
```
[Faulty MPA_WorkPos Sensor] → [MPA_WorkPos remains TRUE after movement] → [System cannot determine actual axis position] → [Alarms MPA_A_701124/MPA_A_701125 triggered, measurement cycle halted]
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- Action: Check MPA_WorkPos and MPA_InitPos status in HMI/control system.
- Expected result: After movement, MPA_WorkPos=FALSE, MPA_InitPos=TRUE.
- If abnormal (MPA_WorkPos=TRUE, MPA_InitPos=TRUE): Go to Step 2.
- If normal: Fault not present; investigate other issues.

**Step 2: Physical Sensor Check**
- Action: Inspect MPA_WorkPos sensor and wiring for damage or disconnection.
- If sensor/wiring faulty: Go to Step 4.
- If sensor/wiring OK: Go to Step 3.

**Step 3: PLC/Software Diagnostics**
- Action: Review PLC logic for MPA_WorkPos variable update; check for software errors.
- If PLC logic error found: Go to Step 5.
- If logic OK: Go to Step 4.

**Step 4: Mechanical Inspection**
- Action: Verify axis physically reaches initial position (no obstruction).
- If axis obstructed: Remove obstruction, retest.
- If axis moves freely: Go to Step 5.

**Step 5: Sensor Replacement/Logic Correction**
- Action: Replace faulty sensor or correct PLC logic as indicated by previous steps.

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Power down the CNC lathe and engage E-stop.
2. Depressurize pneumatic/hydraulic systems if applicable.
3. Lock-out/tag-out electrical supply to MeasuringProbe subsystem.
4. Document current sensor states and alarm history.

### 6.2 Repair Procedure

**Required:**
- Tools: Multimeter, insulated screwdriver, hex key set, PLC programming terminal.
- Parts: P/N: MEAS-AXIS-SENS-001 (MPA_WorkPos sensor), P/N: MEAS-AXIS-WIRE-002 (sensor wiring harness)
- Personnel: Senior technician with PLC experience
- Estimated time: 60–90 minutes

**Steps:**
1. Remove axis cover (MPC_isOpen=TRUE) using hex key; follow torque spec 8 Nm.
2. Locate MPA_WorkPos sensor; disconnect wiring harness.
3. Test sensor with multimeter (expect open circuit when axis not at working position).
4. Replace sensor (P/N: MEAS-AXIS-SENS-001) if faulty; reconnect harness (P/N: MEAS-AXIS-WIRE-002).
5. Inspect wiring for continuity; repair/replace as needed.
6. Reinstall axis cover; torque bolts to spec.
7. If PLC logic error found, connect programming terminal, correct ladder logic as per OEM documentation.
8. Power up system, clear alarms.

### 6.3 Verification Tests

After repair:
1. Command axis to move between working and initial positions; observe MPA_WorkPos and MPA_InitPos toggling correctly.
2. Confirm MPA_A_701124 and MPA_A_701125 alarms do not trigger.
3. Run a functional measurement cycle; verify probe activation/deactivation.

### 6.4 Return to Service

- Calibrate position sensors per OEM procedure.
- Update maintenance and repair logs.
- Monitor sensor readings for 8 hours of operation to confirm stability.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- Risk of axis movement during troubleshooting—engage E-stop and lock-out/tag-out before work.
- PPE: Safety glasses, insulated gloves, steel-toe boots required.
- Beware of pinch points near axis and cover actuator.
- Depressurize any pneumatic/hydraulic lines before sensor replacement.
- High voltage present in control panels—use insulated tools only.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect position sensors every 500 operating hours.
- Monitor for wear or contamination on sensor faces.
- Verify sensor toggling during routine maintenance cycles.
- Schedule PLC logic audits annually.

## 9. Related Faults

**This fault can cause:**
- Measurement probe collision due to position ambiguity.
- Cover actuator (MPC_close) failing to engage, exposing probe to damage.
- Downstream measurement errors and scrap parts.

**This fault can be caused by:**
- Upstream PLC logic faults.
- Electrical surges damaging sensor inputs.
- Mechanical misalignment of axis.

**Similar symptoms but different causes:**
- Axis fails to move due to drive fault (not sensor issue).
- Both end position sensors indicate TRUE (MPA_A_701125), but axis is physically stuck.

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_4
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at