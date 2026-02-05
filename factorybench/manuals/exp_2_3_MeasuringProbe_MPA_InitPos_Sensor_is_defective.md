# Troubleshooting Manual: MPA_InitPos_Sensor_is_defective

**Fault ID:** exp_2_3
**Subsystem:** MeasuringProbe
**Scenario:** {'init': 'Measuring probe axis (MPA) is in one of the following states: A) Already at initial position (IP = true, m2IP = true, WP = false, m2WP = false) B) Moving toward initial position (IP = false, m2IP = true, WP = false, m2WP = false)', 'action': None, 'expected_result': 'A) IP remains true (MPA continues to be recognized at initial position) B) IP switches to true within a few seconds (MPA reaches and is recognized at initial position)', 'instead_failure': 'A) IP suddenly switches to false (spontaneous sensor failure - MPA is no longer detected at initial position despite not moving) B) IP never becomes true (MPA is not recognized at initial position after movement)'}

---

## 1. Fault Overview

This fault involves the measuring probe axis (MPA) failing to correctly indicate its initial position (MPA_InitPos) either spontaneously (sensor dropout while stationary) or after movement to initial position (failure to recognize arrival). This condition disrupts automated measurement cycles and may trigger system alarms, halting production until resolved.

## 2. System Impact

- **What stops working?**  
  The measuring probe cannot be activated for part measurement, preventing automated dimensional checks and process validation.
- **What safety systems activate?**  
  The system may inhibit axis movement and measurement routines; interlocks may prevent probe deployment.
- **Production consequences?**  
  Measurement-dependent operations are blocked, causing delays and possible scrap if undetected; downstream machining steps may be halted.

## 3. Observable Symptoms

**Via Alarms:**
- MPA_A_701124: "Measuring probe did not reach end position in time"
- MPA_A_701125: "Measuring probe axis both endposition sensor inputs indicate true"

**Via Sensor Readings:**
- MPA_InitPos:  
  - Expected: True when axis is at initial position  
  - Actual: False (either spontaneously or after movement)
- MPA_toInitPos:  
  - Expected: False when axis is stationary at initial position  
  - Actual: May remain True if axis keeps trying to reach initial position
- MPA_WorkPos:  
  - Expected: False when at initial position  
  - Actual: May show unexpected transitions
- MP_Inactive:  
  - Expected: True when probe is inactive  
  - Actual: May remain True, blocking measurement

**Via Visual/Physical Inspection:**
- Axis appears stationary at initial position, but system does not recognize it
- No audible movement when commanded to initial position (if already there)
- No visible probe deployment
- No unusual odors; possible indicator lights for fault/alarm

## 4. Root Cause Analysis

### 4.1 Primary Root Cause

**Cause:** Initial position sensor failure or wiring fault (MPA_InitPos signal dropout or not triggered)

**Mechanism:**  
Sensor fails to indicate axis arrival at initial position, either due to spontaneous electrical fault (stationary dropout) or failure to trigger after movement. This prevents system from recognizing probe readiness, blocks measurement routines, and triggers alarms.

**Probability:** High (based on manipulated variables and symptom specificity)

**Verification Steps:**
1. Check MPA_InitPos variable in diagnostics; confirm it remains False despite axis being at initial position.
2. Inspect physical sensor and wiring at initial position; verify signal continuity.
3. Manually move axis off and back to initial position; observe if MPA_InitPos ever switches to True.
4. If MPA_InitPos remains False, disconnect sensor and test with multimeter (expect closed circuit at initial position).
5. If sensor signal is present but not reaching controller, check I/O board input.

### 4.2 Secondary Causes

- **Cause:** Mechanical misalignment of axis or endstop
  - **Likelihood:** Medium
  - **Differentiating factors:** Axis physically does not reach sensor; sensor functional but not triggered.
- **Cause:** Controller input fault (PLC/I/O board failure)
  - **Likelihood:** Low
  - **Differentiating factors:** Sensor output correct, but controller does not register signal.
- **Cause:** Software logic error or configuration mismatch
  - **Likelihood:** Low
  - **Differentiating factors:** All hardware functional; issue only after software update or parameter change.

### 4.3 Causal Chain

```
[Sensor failure at initial position] → [MPA_InitPos remains False] → [MPA_A_701124 alarm triggers] → [Measuring probe cannot activate]
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- Action: Check MPA_InitPos status in HMI diagnostics.
- Expected result: True when axis is at initial position.
- If abnormal (False): Go to Step 2.
- If normal (True): Go to Step 5.

**Step 2: Physical Position Verification**
- Action: Visually confirm axis is at initial position (reference marks, mechanical stops).
- Expected result: Axis stationary at initial position.
- If abnormal: Investigate axis drive/motion fault.
- If normal: Go to Step 3.

**Step 3: Sensor Function Test**
- Action: Inspect initial position sensor and wiring; test sensor output with multimeter.
- Expected result: Closed circuit when axis at initial position.
- If abnormal: Replace sensor (see Section 6.2).
- If normal: Go to Step 4.

**Step 4: Controller Input Check**
- Action: Check I/O board input for sensor signal; verify PLC registers change.
- Expected result: Input changes state when sensor activated.
- If abnormal: Replace/repair I/O board.
- If normal: Go to Step 5.

**Step 5: Software/Logic Verification**
- Action: Review recent software changes, parameter settings for axis position logic.
- Expected result: No recent changes or errors.
- If abnormal: Restore correct configuration.
- If normal: Fault resolved.

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Power down machine and engage lock-out/tag-out.
2. Depressurize pneumatic/hydraulic systems if present.
3. Isolate measuring probe subsystem electrically.
4. Document current sensor readings and alarm states.

### 6.2 Repair Procedure

**Required:**
- Tools: Multimeter, insulated screwdrivers, 2.5mm hex key, wire strippers, crimp tool
- Parts:  
  - Initial position sensor P/N: MEAS-AXIS-001  
  - Sensor cable P/N: MEAS-CABL-002  
  - I/O board P/N: CTRL-IO-003
- Personnel: Certified CNC maintenance technician
- Estimated time: 60-90 minutes

**Steps:**
1. Remove axis cover using 2.5mm hex key; retain fasteners.
2. Locate initial position sensor; disconnect cable.
3. Test sensor with multimeter (expect <5Ω at initial position).
4. If sensor faulty, replace with P/N: MEAS-AXIS-001; torque mounting screws to 1.5 Nm.
5. Inspect and replace cable if damaged (P/N: MEAS-CABL-002); ensure secure crimps.
6. If I/O board input faulty, replace with P/N: CTRL-IO-003; follow ESD precautions.
7. Reassemble cover; torque screws to 2.0 Nm.
8. Restore power and clear lock-out/tag-out.

### 6.3 Verification Tests

After repair:
1. Move axis off and back to initial position; observe MPA_InitPos variable switches True.
2. Confirm no MPA_A_701124 or MPA_A_701125 alarms.
3. Run measuring probe activation cycle; verify probe deploys and measures.

### 6.4 Return to Service

- Calibrate axis position sensor per OEM procedure.
- Update maintenance log and fault documentation.
- Monitor MPA_InitPos status for 1 production shift.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- Risk of electrical shock when accessing sensor wiring; isolate power.
- Moving axis can cause crush injuries; ensure axis is locked out.
- PPE: Safety glasses, insulated gloves, steel-toe boots.
- Lock-out/tag-out must be applied before any physical intervention.
- Beware of residual pneumatic/hydraulic pressure in actuator systems.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect initial position sensor and cable every 3 months.
- Monitor for wear, corrosion, or loose mounting.
- Verify sensor operation during scheduled maintenance.
- Replace sensor every 24 months or per OEM guidelines.

## 9. Related Faults

**This fault can cause:**
- Measuring probe activation failure (MP_Inactive remains True)
- Axis cover not commanded to close (MPC_close not triggered)

**This fault can be caused by:**
- Axis drive malfunction (axis does not reach initial position)
- PLC/I/O board failure

**Similar symptoms but different causes:**
- Axis mechanical jam (axis cannot reach initial position physically)
- Software configuration error (incorrect position thresholds)

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_2_3
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at