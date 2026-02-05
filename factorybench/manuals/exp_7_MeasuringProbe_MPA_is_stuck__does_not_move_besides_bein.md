# Troubleshooting Manual: MPA_is_stuck__does_not_move_besides_bein

**Fault ID:** exp_7
**Subsystem:** MeasuringProbe
**Scenario:** {'init': 'Measuring probe axis (MPA) is at working position (WP = true, m2WP = true, IP = false, m2IP = false)', 'action': 'The control system commands a move to the initial position (m2IP = true -> m2WP = false)', 'expected_result': 'WP switches to false as the MPA leaves the working position, and IP becomes true within a few seconds (MPA reaches and is recognized at initial position)', 'instead_failure': 'WP remains true - no movement has occurred'}

---

## 1. Fault Overview

This fault occurs when the measuring probe axis (MPA) is commanded to move from the working position to the initial position, but no movement takes place—the axis remains at the working position despite the command. This failure prevents the measuring probe from retracting, risking measurement errors and potential mechanical interference.

## 2. System Impact

- **Operational Impact:** The measuring probe remains deployed, preventing safe tool changes and potentially blocking subsequent machining operations.
- **Safety Systems:** Interlocks may prevent spindle or turret movement; alarms will inhibit further automation until resolved.
- **Production Consequences:** Machine cycle is halted, causing downtime and possible loss of measurement data integrity.

## 3. Observable Symptoms

**Via Alarms:**
- MPA_A_701124: "Measuring probe did not reach end position in time"
- Possible: MPA_A_701125 if both end position sensors indicate true

**Via Sensor Readings:**
- `MPA_WorkPos`: True (should switch to False when leaving working position)
- `MPA_InitPos`: False (should become True when reaching initial position)
- `MPA_toInitPos`: True (command issued)
- `MPA_toWorkPos`: False (command cleared)
- `MP_Inactive`: False (probe remains active)
- Expected: `MPA_WorkPos` switches to False, `MPA_InitPos` switches to True within seconds; Actual: `MPA_WorkPos` remains True, `MPA_InitPos` remains False

**Via Visual/Physical Inspection:**
- Measuring probe visibly remains extended into the work area
- No audible movement from axis motors/actuators
- No change in axis position indicator lights
- Possible warning lights or HMI messages

## 4. Root Cause Analysis

### 4.1 Primary Root Cause

**Cause:** Failure of the axis actuator or its control signal—axis drive does not respond to "move to initial position" command.

**Mechanism:** When `MPA_toInitPos` is set True, the axis should physically move, switching `MPA_WorkPos` to False and `MPA_InitPos` to True. If the actuator is unresponsive (mechanical jam, electrical fault, or control signal loss), the axis remains stationary, and the position sensors do not update.

**Probability:** High (most likely in context of no movement and persistent position readings)

**Verification Steps:**
1. Confirm `MPA_toInitPos` is True (command issued).
2. Check `MPA_WorkPos` remains True and `MPA_InitPos` remains False for >10 seconds after command.
3. Inspect actuator status: check for error codes, power supply, and control signal integrity.
4. Manually attempt axis movement (if safe)—listen for motor engagement.
5. If actuator does not respond, proceed to mechanical/electrical inspection.

### 4.2 Secondary Causes

- **Cause:** Position sensor failure (sensor stuck or wiring fault)
  - **Likelihood:** Medium
  - **Differentiating factors:** Axis may physically move, but sensor readings do not change; visual inspection shows axis retracted but sensors report working position.

- **Cause:** Software/PLC logic fault (command not actually issued to hardware)
  - **Likelihood:** Low
  - **Differentiating factors:** HMI or PLC logs show command not propagated; other axis commands may also fail.

- **Cause:** Interlock or safety override preventing axis movement
  - **Likelihood:** Low
  - **Differentiating factors:** Active safety alarms; other subsystems locked out.

### 4.3 Causal Chain

```
[Axis actuator/control failure] → [Axis does not move] → [Position sensors remain unchanged] → [MPA_WorkPos=True, MPA_InitPos=False, MPA_A_701124 alarm]
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- Action: Check `MPA_toInitPos` status in HMI/PLC.
- Expected result: True after command.
- If abnormal: Go to Step 2.
- If normal: Go to Step 3.

**Step 2: Command Signal Verification**
- Action: Inspect PLC output and wiring to actuator.
- Expected result: Command signal present at actuator.
- If abnormal: Repair signal path, then retest.
- If normal: Go to Step 3.

**Step 3: Actuator Response Check**
- Action: Observe/mechanically test axis movement.
- Expected result: Axis moves within 5 seconds.
- If abnormal: Go to Step 4.
- If normal: Go to Step 5.

**Step 4: Position Sensor Validation**
- Action: Manually move axis (if possible) and observe `MPA_WorkPos`/`MPA_InitPos`.
- Expected result: Sensor states change with axis position.
- If abnormal: Replace/repair sensors.
- If normal: Go to Step 5.

**Step 5: Safety Interlock Review**
- Action: Check for active safety interlocks or alarms.
- Expected result: No active interlocks.
- If abnormal: Resolve interlock condition.
- If normal: Fault resolved.

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Power down the machine and engage lock-out/tag-out.
2. Depressurize pneumatic/hydraulic systems if applicable.
3. Isolate the measuring probe axis from control system.
4. Document sensor readings, alarm history, and axis position.

### 6.2 Repair Procedure

**Required:**
- Tools: Multimeter, insulated screwdrivers, torque wrench (5 Nm), axis position gauge
- Parts: P/N: MPRO-AXIS-ACT-001 (axis actuator), P/N: MPRO-SENS-POS-002 (position sensor), P/N: MPRO-CABL-CNT-003 (control cable)
- Personnel: Certified CNC technician
- Estimated time: 90 minutes

**Steps:**
1. Remove axis cover (torque bolts to 5 Nm on reinstallation).
2. Disconnect actuator power and control cables.
3. Remove actuator; check for mechanical jams or wear.
4. Install new actuator (P/N: MPRO-AXIS-ACT-001), ensuring alignment and clearance per spec (0.5 mm).
5. Reconnect cables; verify secure connections.
6. Replace position sensor if faulty (P/N: MPRO-SENS-POS-002).
7. Reinstall axis cover; torque bolts to spec.
8. Restore power and control connections.
9. Clear alarms and reset system.

### 6.3 Verification Tests

After repair:
1. Command axis to initial position; observe movement.
2. Confirm `MPA_WorkPos` switches to False, `MPA_InitPos` switches to True within 5 seconds.
3. Run full measuring probe cycle; verify all position changes and alarm-free operation.

### 6.4 Return to Service

- Perform axis calibration per manufacturer protocol.
- Update maintenance and repair logs.
- Monitor axis operation for 1 hour post-repair for abnormal readings or alarms.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- Hazard of unexpected axis movement—always power down and lock-out/tag-out before service.
- Risk of electrical shock—use insulated tools and PPE (gloves, safety glasses).
- Moving parts—keep hands clear of axis during testing.
- Pneumatic/hydraulic pressure—depressurize before disconnecting lines.
- High temperatures possible near actuator—allow cooling before handling.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect axis actuator and position sensors every 500 operating hours.
- Monitor for abnormal noises or vibration during axis movement.
- Check cable integrity and connections monthly.
- Schedule full functional test of measuring probe axis every 3 months.

## 9. Related Faults

**This fault can cause:**
- Measuring probe collision with tool or workpiece
- Downstream axis movement faults
- Measurement data errors

**This fault can be caused by:**
- Upstream power supply failure
- Control system logic errors
- Safety interlock activation

**Similar symptoms but different causes:**
- Position sensor stuck (axis moves, but readings do not change)
- PLC output failure (command never reaches actuator)
- Cover actuator jam (probe axis blocked by cover)

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_7
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at

---