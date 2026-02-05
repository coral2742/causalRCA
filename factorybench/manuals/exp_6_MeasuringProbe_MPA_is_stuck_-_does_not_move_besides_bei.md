# Troubleshooting Manual: MPA_is_stuck_-_does_not_move_besides_bei

**Fault ID:** exp_6
**Subsystem:** MeasuringProbe
**Scenario:** {'init': 'Measuring probe axis (MPA) is at initial position (IP = true, m2IP = true, WP = false, m2WP = false)', 'action': 'The control system commands a move to the working position (m2WP = true -> m2IP = false)', 'expected_result': 'IP switches to false as the MPA leaves the initial position, and WP becomes true within a few seconds (MPA reaches and is recognized at working position)', 'instead_failure': 'IP remains true - no movement has occurred / the axis is stuck'}

---

## 1. Fault Overview

This fault occurs when the measuring probe axis (MPA) fails to move from its initial position to the working position after being commanded by the control system. The axis remains stuck at the initial position (MPA_InitPos=True, MPA_WorkPos=False) despite receiving a command to move (MPA_toWorkPos=True), preventing the probe from performing measurement operations. This interruption can halt automated quality checks and disrupt production flow.

## 2. System Impact

- **What stops working?**  
  The measuring probe cannot reach the working position, so automated workpiece measurement is unavailable. Downstream processes relying on probe data may be blocked.
- **What safety systems activate?**  
  System interlocks may prevent spindle or tool movement to avoid collision with the stuck probe axis. Relevant alarms (e.g., MPA_A_701124) may trigger, activating warning lights and audible alerts.
- **Production consequences?**  
  Production is interrupted until the probe axis is restored. Workpieces cannot be measured, risking quality assurance failures and potential scrap or rework.

## 3. Observable Symptoms

**Via Alarms:**
- **MPA_A_701124:** "Measuring probe did not reach end position in time"
- **MPA_A_701125:** "Measuring probe axis both endposition sensor inputs indicate true" (possible if sensors malfunction)

**Via Sensor Readings:**
- **MPA_InitPos:** True (remains True when it should switch to False)
- **MPA_WorkPos:** False (should become True within seconds of command)
- **MPA_toWorkPos:** True (commanded, but axis does not respond)
- **MPA_toInitPos:** False (as expected after command)
- **MP_Inactive:** True (probe remains inactive)
- Expected: MPA_InitPos switches to False, MPA_WorkPos switches to True  
  Actual: MPA_InitPos remains True, MPA_WorkPos remains False

**Via Visual/Physical Inspection:**
- Probe axis remains in retracted/hidden position; no movement observed
- No audible sound of axis motor or actuator movement
- Possible warning lights on operator panel
- No visible mechanical obstructions, but axis does not respond to command

## 4. Root Cause Analysis

### 4.1 Primary Root Cause
**Cause:** Failure of the axis actuator or drive system (mechanical jam, electrical fault, or control signal interruption)
**Mechanism:** The command to move to working position (MPA_toWorkPos=True) is issued, but the axis does not physically move due to actuator failure, drive error, or jam. Sensors continue to report initial position (MPA_InitPos=True), and working position is never reached (MPA_WorkPos=False), triggering timeout alarms.
**Probability:** High (most likely given manipulated variables and observed symptoms)

**Verification Steps:**
1. **Check MPA_toWorkPos:** Confirm variable is True (command issued).
2. **Check MPA_InitPos and MPA_WorkPos:** Confirm MPA_InitPos remains True, MPA_WorkPos remains False after command.
3. **Inspect actuator status:** Check for error codes or drive faults in actuator control panel.
4. **Manual movement test:** Attempt to move axis manually (if safe) to check for mechanical jam.
5. **Decision points:**  
   - If actuator error present, proceed to actuator diagnostics.  
   - If no error, check for mechanical obstruction or sensor fault.

### 4.2 Secondary Causes
- **Cause:** End position sensor malfunction (sensor stuck or shorted)
  - **Likelihood:** Medium
  - **Differentiating factors:** Both MPA_InitPos and MPA_WorkPos may read True simultaneously; check for MPA_A_701125 alarm.

- **Cause:** Control system output failure (PLC or relay not energizing actuator)
  - **Likelihood:** Medium
  - **Differentiating factors:** No actuator error; output signal absent; verify with multimeter or PLC diagnostics.

- **Cause:** Physical obstruction (foreign object or debris blocking axis movement)
  - **Likelihood:** Low
  - **Differentiating factors:** Visual inspection reveals obstruction; actuator attempts to move but is blocked.

### 4.3 Causal Chain
```
[Axis actuator/drive failure] → [Axis does not leave initial position] → [MPA_InitPos remains True, MPA_WorkPos remains False] → [MPA_A_701124 alarm triggered, probe inactive]
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- Action: Check MPA_toWorkPos variable status in HMI/diagnostics.
- Expected result: True (command issued to move to working position).
- If abnormal: Command not issued; check control logic.  
- If normal: Go to Step 2.

**Step 2: Sensor Status Check**
- Action: Observe MPA_InitPos and MPA_WorkPos.
- Expected result: MPA_InitPos switches to False, MPA_WorkPos switches to True within 5 seconds.
- If abnormal: Both remain unchanged; go to Step 3.  
- If normal: Axis movement is functioning; investigate intermittent faults.

**Step 3: Actuator/Drive Diagnostics**
- Action: Inspect actuator control panel for fault codes or error lights.
- Expected result: No faults; actuator ready.
- If abnormal: Fault present; proceed to actuator troubleshooting.  
- If normal: Go to Step 4.

**Step 4: Manual Movement Test**
- Action: Attempt to move axis manually (using jog function or manual override, if safe).
- Expected result: Axis moves freely.
- If abnormal: Axis jammed; inspect for mechanical obstruction.  
- If normal: Go to Step 5.

**Step 5: Sensor Integrity Test**
- Action: Check end position sensor readings and wiring.
- Expected result: Only one position sensor active at a time.
- If abnormal: Both sensors active (MPA_A_701125); replace/repair sensor.  
- If normal: Fault may be intermittent; monitor for recurrence.

## 6. Remediation

### 6.1 Immediate Actions
**BEFORE any repair work:**
1. Press emergency stop; power down machine and lock out main disconnect.
2. Depressurize pneumatic/hydraulic lines to actuator.
3. Isolate probe axis from control system to prevent accidental movement.
4. Document all variable states, alarms, and sensor readings.

### 6.2 Repair Procedure

**Required:**
- Tools: Insulated screwdriver set, multimeter, torque wrench (10 Nm), Allen keys, actuator alignment jig
- Parts:  
  - Actuator assembly (P/N: MPRO-AXIS-001)  
  - End position sensor (P/N: MPRO-SENS-002)  
  - Control relay (P/N: MPRO-GEN-003)
- Personnel: Certified CNC maintenance technician
- Estimated time: 2-4 hours

**Steps:**
1. Remove axis cover (torque bolts to 8 Nm on reinstallation).
2. Disconnect actuator power and signal cables.
3. Unbolt actuator from axis frame (use alignment jig for reinstallation).
4. Inspect actuator for mechanical damage; replace if faulty (P/N: MPRO-AXIS-001).
5. Inspect end position sensors; replace if stuck or shorted (P/N: MPRO-SENS-002).
6. Check control relay output; replace if not energizing actuator (P/N: MPRO-GEN-003).
7. Reinstall actuator and sensors; torque all fasteners to spec.
8. Reconnect cables; verify wiring integrity.
9. Reinstall axis cover; verify proper clearance (2 mm minimum).

### 6.3 Verification Tests
After repair:
1. Power up system; clear all alarms.
2. Command axis to initial and working positions; observe MPA_InitPos and MPA_WorkPos transitions.
3. Confirm actuator movement and sensor response.
4. Run full probe measurement cycle; verify MP_Inactive switches to False in working position.

### 6.4 Return to Service
- Perform axis calibration routine per OEM instructions.
- Update maintenance log and fault documentation.
- Monitor axis operation for 1 hour; check for recurring alarms or abnormal sensor readings.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- Risk of crush injury from unexpected axis movement; always lock out/tag out power before servicing.
- Wear safety glasses, cut-resistant gloves, and steel-toe boots.
- Depressurize all pneumatic/hydraulic lines before disconnecting actuator.
- Beware of high-voltage components in actuator control panel.
- Do not bypass safety interlocks or operate with covers removed.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect actuator and sensors every 500 operating hours.
- Monitor for abnormal noise or vibration during axis movement.
- Check axis cover for proper closure and clearance monthly.
- Replace actuator and sensors at recommended intervals (see OEM schedule).
- Log all maintenance actions in system records.

## 9. Related Faults

**This fault can cause:**
- Probe measurement failures (MP_Inactive remains True)
- Downstream process delays or stoppages
- False quality assurance failures

**This fault can be caused by:**
- Control system output faults
- Sensor wiring shorts or open circuits
- Previous mechanical jams not fully resolved

**Similar symptoms but different causes:**
- MPA_A_701125: Both end position sensors indicate True (sensor fault)
- MPC_close/MPC_Closed faults: Axis cover not closing properly, blocking movement
- Control relay failure: Axis does not move despite correct commands

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_6
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at

---