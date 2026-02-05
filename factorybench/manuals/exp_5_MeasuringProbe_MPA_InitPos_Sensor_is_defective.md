# Troubleshooting Manual: MPA_InitPos_Sensor_is_defective

**Fault ID:** exp_5
**Subsystem:** MeasuringProbe
**Scenario:** {'init': 'Measuring probe axis (MPA) is at initial position (IP = true, m2IP = true, WP = false, m2WP = false)', 'action': 'The control system commands a move to the working position (m2WP = true -> m2IP = false)', 'expected_result': 'IP switches to false as the MPA leaves the initial position, and WP becomes true within a few seconds (MPA reaches and is recognized at working position)', 'instead_failure': 'IP incorrectly remains true even after movement - the system falsely believes the MPA is still at initial position, despite it having moved'}

---

## 1. Fault Overview

This fault occurs when the measuring probe axis (MPA) is commanded to move from its initial position to the working position, but the system incorrectly continues to report that the axis is at the initial position (MPA_InitPos=True), even after movement. This misreporting disrupts automated measurement cycles and can prevent further operations, risking both process accuracy and machine safety.

## 2. System Impact

- **What stops working?**  
  Automated measuring routines halt because the system believes the probe has not left its initial position, blocking measurement and subsequent machining steps.
- **What safety systems activate?**  
  Interlocks may prevent spindle start or axis movement, and alarms (e.g., MPA_A_701124, MPA_A_701125) may trigger, disabling further probe or axis commands.
- **Production consequences?**  
  Workpieces are not measured, leading to production delays, possible scrap due to unverified dimensions, and increased downtime for troubleshooting.

## 3. Observable Symptoms

**Via Alarms:**
- **MPA_A_701124:** "Measuring probe did not reach end position in time"
- **MPA_A_701125:** "Measuring probe axis both endposition sensor inputs indicate true"

**Via Sensor Readings:**
- **MPA_InitPos:** Remains True (should switch to False after movement)
- **MPA_WorkPos:** Remains False (should switch to True after movement)
- **MPA_toWorkPos:** True (commanded to working position)
- **MPA_toInitPos:** False (no longer commanded to initial position)
- **Expected:** MPA_InitPos=False, MPA_WorkPos=True within seconds of command
- **Actual:** MPA_InitPos=True, MPA_WorkPos=False

**Via Visual/Physical Inspection:**
- Axis physically moves away from initial position (visible on machine)
- Measuring probe not in initial (hidden) position, but system HMI shows "Initial Position: True"
- No abnormal sounds or smells unless mechanical jam is present

## 4. Root Cause Analysis

### 4.1 Primary Root Cause

**Cause:** Faulty or stuck initial position sensor (MPA_InitPos signal remains True despite axis movement)  
**Mechanism:** The sensor or its wiring fails to update when the axis leaves the initial position, causing the control system to falsely report the axis as still at initial position. This blocks measurement routines and triggers alarms due to conflicting sensor states.  
**Probability:** High (most likely given manipulated variables and symptom pattern)

**Verification Steps:**
1. Inspect MPA_InitPos sensor physically for damage or misalignment.
2. Check live sensor reading at the PLC/HMI:  
   - If MPA_InitPos=True while axis is visibly NOT in initial position, sensor is stuck or wiring is faulty.
3. Manually move axis between positions and observe if MPA_InitPos toggles.
   - If not, confirm sensor failure.
4. Check continuity of sensor wiring (should be <5Ω resistance).
5. If sensor toggles correctly, proceed to secondary causes.

### 4.2 Secondary Causes

- **Cause:** PLC input module fault (not registering sensor state change)
  - **Likelihood:** Medium
  - **Differentiating factors:** Sensor physically works (LED toggles), but HMI/PLC does not update.
- **Cause:** Mechanical jam or obstruction preventing axis from fully leaving initial position
  - **Likelihood:** Low
  - **Differentiating factors:** Axis does not physically move as commanded; audible mechanical resistance.
- **Cause:** Software logic error (incorrect mapping of sensor states)
  - **Likelihood:** Low
  - **Differentiating factors:** All hardware functions, but HMI/PLC logic misreports position.

### 4.3 Causal Chain

```
[Initial position sensor stuck True] → [System believes axis is at initial position] → [Measurement routines blocked, alarms triggered] → [Production halted, operator alerted]
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- Action: Check MPA_InitPos and MPA_WorkPos status on HMI/PLC.
- Expected result: MPA_InitPos=False, MPA_WorkPos=True after axis movement.
- If abnormal: Go to Step 2
- If normal: Go to Step 5

**Step 2: Physical Inspection**
- Action: Observe axis position; compare to HMI/PLC status.
- If axis not at initial position but MPA_InitPos=True: Go to Step 3
- If axis at initial position: Re-attempt movement, monitor for jam.

**Step 3: Sensor Function Test**
- Action: Manually actuate initial position sensor (if accessible).
- Expected result: HMI/PLC status toggles with sensor actuation.
- If sensor does not toggle: Go to Step 4
- If sensor toggles: Suspect PLC input or software logic (Step 5)

**Step 4: Electrical Checks**
- Action: Test sensor wiring for continuity and correct voltage.
- If wiring faulty: Repair/replace as needed.
- If wiring good: Replace sensor (Step 6)

**Step 5: PLC/Software Verification**
- Action: Check PLC input module and software mapping for errors.
- If mapping incorrect: Correct in PLC program.
- If module faulty: Replace PLC input module.

**Step 6: Final Confirmation**
- Action: Cycle axis through positions, verify all sensor readings update correctly.

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Power down machine and engage lock-out/tag-out.
2. Depressurize pneumatic/hydraulic systems if present.
3. Isolate measuring probe subsystem electrically.
4. Document current variable states and alarm history.

### 6.2 Repair Procedure

**Required:**
- Tools: Multimeter, insulated screwdrivers, sensor alignment jig
- Parts: Initial position sensor (P/N: MPRO-AXIS-001), wiring harness (P/N: MPRO-GEN-002)
- Personnel: Certified CNC maintenance technician
- Estimated time: 45 minutes

**Steps:**
1. Remove axis cover (torx T30, 2.0 Nm torque).
2. Locate and disconnect initial position sensor wiring.
3. Test sensor for continuity (<5Ω); if failed, replace sensor.
4. Install new sensor (P/N: MPRO-AXIS-001), align to manufacturer spec (±0.2 mm clearance).
5. Reconnect wiring harness (P/N: MPRO-GEN-002), secure with cable ties.
6. Reinstall axis cover, torque bolts to 2.0 Nm.
7. Power up subsystem, check for correct sensor operation.

### 6.3 Verification Tests

After repair:
1. Command axis to initial and working positions; verify MPA_InitPos and MPA_WorkPos toggle correctly.
2. Confirm no alarms (MPA_A_701124, MPA_A_701125) are present.
3. Run functional measurement cycle; probe should activate and measure as expected.

### 6.4 Return to Service

- Calibrate sensor position per OEM procedure.
- Update maintenance log and fault report.
- Monitor subsystem for 1 hour post-repair for recurrence.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- Risk of unexpected axis movement; always lock-out/tag-out before accessing sensors.
- High voltage present in control cabinet—use insulated tools.
- Wear safety glasses and gloves when working near moving parts.
- Ensure pneumatic/hydraulic systems are depressurized before sensor removal.
- Do not bypass safety interlocks.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect position sensors every 3 months for wear or misalignment.
- Monitor sensor toggling in daily startup checks.
- Replace sensors showing intermittent readings or physical damage.
- Schedule wiring harness inspection every 6 months.

## 9. Related Faults

**This fault can cause:**
- Measurement probe fails to activate (MP_Inactive remains True)
- Axis cover fails to close (MPC_close not triggered)
- Downstream alarms in measuring routines

**This fault can be caused by:**
- Upstream PLC input faults
- Wiring harness degradation
- Mechanical jams in axis movement

**Similar symptoms but different causes:**
- Axis fails to move due to drive fault (not sensor-related)
- Software mapping error causing incorrect HMI display
- Both end position sensors stuck True (MPA_A_701125)

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_5
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at

---