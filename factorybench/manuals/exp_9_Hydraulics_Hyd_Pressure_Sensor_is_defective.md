# Troubleshooting Manual: Hyd_Pressure_Sensor_is_defective

**Fault ID:** exp_9
**Subsystem:** Hydraulics
**Scenario:** {'init': 'Machine is in normal operation; hydraulic pressure level sensor is functioning normally and reporting a constant, stable pressure reading.', 'expected_result': 'Pressure stays constant at its true value, with no unexpected fluctuations in the level sensor output.', 'instead_failure': 'The defective pressure‑level sensor outputs random values outside the normal range—spiking both above and below the true pressure—until it falsely triggers a pressure fault alarm. Operator acknowledges the fault and terminates the experiment.'}

---

## 1. Fault Overview

This fault scenario involves a defective hydraulic pressure-level sensor on the CNC vertical lathe, which intermittently outputs random, out-of-range values. Such erratic sensor behavior can falsely trigger hydraulic pressure alarms, leading to unnecessary system shutdowns and production interruptions.

## 2. System Impact

- **What stops working?**  
  The hydraulic subsystem may be disabled, halting all hydraulic-dependent machine functions, including clamping, tool change, and axis movement.
- **What safety systems activate?**  
  The system triggers pressure-related alarms (e.g., Hyd_A_700202), disables hydraulic actuation, and may command the accumulator charge valve (Hyd_Valve_P_Up) and hydraulic pump (Hyd_Pump_On) in response.
- **Production consequences?**  
  Machine operation is interrupted, causing unplanned downtime and potential loss of workpieces in process.

## 3. Observable Symptoms

**Via Alarms:**
- **Hyd_A_700202:** "Hydraulic pressure out of norm"
- (Possible secondary alarms if pressure excursions persist or interact with other subsystems)

**Via Sensor Readings:**
- **Hyd_Pressure:**  
  - *Expected:* Stable value within normal operating range (as per machine spec, e.g., 120–160 bar)
  - *Actual:* Rapid, random fluctuations above and below normal range, not matching physical system state
- **Hyd_Valve_P_Up:**  
  - Unexpected cycling (True/False) as system attempts to compensate for false low readings
- **Hyd_Pump_On:**  
  - May toggle unexpectedly in response to false pressure readings

**Via Visual/Physical Inspection:**
- No visible hydraulic leaks or component failures
- Hydraulic pump and accumulator may cycle audibly more frequently than normal
- No abnormal smells (e.g., burnt oil) or visible overheating

## 4. Root Cause Analysis

### 4.1 Primary Root Cause

**Cause:** Defective hydraulic pressure-level sensor (Hyd_Pressure sensor outputs random values)

**Mechanism:**  
The sensor intermittently outputs values outside the normal range, causing the control system to interpret these as true hydraulic pressure faults. This triggers Hyd_A_700202, commands the accumulator charge valve (Hyd_Valve_P_Up) and hydraulic pump (Hyd_Pump_On) unnecessarily, and disables the hydraulic subsystem.

**Probability:** High (given manipulated variables and observed symptoms)

**Verification Steps:**
1. **Review Hyd_Pressure trend:**  
   - Use HMI or diagnostic tool to plot Hyd_Pressure over time.
   - *Thresholds:* Normal = 120–160 bar; Fault = rapid excursions outside this range.
2. **Check for correlation:**  
   - If Hyd_Pressure spikes/drops do NOT correspond to actual machine events (e.g., no load changes), suspect sensor.
3. **Inspect sensor wiring and connector:**  
   - If secure, proceed to sensor replacement.
4. **Substitute with known-good sensor:**  
   - If readings stabilize, confirm root cause.

### 4.2 Secondary Causes

- **Cause:** Hydraulic pressure relief valve malfunction  
  - **Likelihood:** Low  
  - **Differentiating factors:** Physical pressure would also fluctuate; audible fluid bypassing; possible leaks.
- **Cause:** Electrical interference or grounding issue in sensor wiring  
  - **Likelihood:** Medium  
  - **Differentiating factors:** Fluctuations may coincide with operation of other high-power equipment; check with oscilloscope.
- **Cause:** Faulty control module analog input  
  - **Likelihood:** Low  
  - **Differentiating factors:** Other analog sensors on same module may also show erratic readings.

### 4.3 Causal Chain

```
[Defective Hyd_Pressure Sensor] → [Random Out-of-Range Hyd_Pressure Readings] → [Hyd_A_700202 Alarm Triggered] → [Hydraulic Subsystem Disabled / Unnecessary Valve & Pump Cycling] → [Production Halt]
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- Action: Check HMI for active alarms (Hyd_A_700202) and review Hyd_Pressure trend.
- Expected result: Hyd_Pressure stable, no alarms.
- If abnormal: Go to Step 2  
- If normal: Resume operation; monitor.

**Step 2: Sensor Reading Validation**
- Action: Compare Hyd_Pressure reading to mechanical gauge (if available).
- Expected result: Both readings match.
- If mismatch: Go to Step 3  
- If match: Investigate hydraulic system for actual pressure faults.

**Step 3: Sensor Wiring and Connector Check**
- Action: Inspect sensor wiring and connector for damage/corrosion.
- If damaged: Repair/replace wiring, retest.
- If intact: Go to Step 4

**Step 4: Substitute Sensor**
- Action: Replace Hyd_Pressure sensor with known-good unit (P/N: HYD-GEN-PRS-001).
- If readings stabilize: Fault resolved.
- If not: Go to Step 5

**Step 5: Control Module Input Test**
- Action: Test analog input channel with simulator or move sensor to another channel.
- If fault follows sensor: Sensor is root cause.
- If fault stays with channel: Control module issue.

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Power down hydraulic system and machine main disconnect.
2. Depressurize hydraulic system per OEM procedure.
3. Lock-out/tag-out (LOTO) all energy sources.
4. Isolate the defective sensor circuit.
5. Document alarm history, sensor readings, and physical findings.

### 6.2 Repair Procedure

**Required:**
- Tools: Insulated screwdriver set, 17mm open-end wrench, multimeter, HMI/diagnostic laptop
- Parts: Hydraulic pressure sensor (P/N: HYD-GEN-PRS-001), replacement connector (if needed)
- Personnel: Maintenance technician (hydraulics certified)
- Estimated time: 45 minutes

**Steps:**
1. Confirm system is powered down and depressurized (verify zero pressure on gauge).
2. Disconnect sensor wiring harness.
3. Remove defective Hyd_Pressure sensor using 17mm wrench.
4. Inspect sensor port for contamination; clean if necessary.
5. Install new sensor (P/N: HYD-GEN-PRS-001); torque to 25 Nm.
6. Reconnect wiring harness; ensure secure, corrosion-free connection.
7. Power up system; check for leaks at sensor port.
8. Clear all alarms from HMI.
9. Calibrate sensor if required (see Section 6.4).
10. Document part replacement in maintenance log.

### 6.3 Verification Tests

After repair:
1. Power on hydraulic system; observe Hyd_Pressure reading.
2. Confirm Hyd_Pressure stabilizes within 120–160 bar (or per spec).
3. Cycle hydraulic functions; verify no false alarms (Hyd_A_700202 remains inactive).
4. Check for leaks and proper operation of accumulator and pump.

### 6.4 Return to Service

- Calibrate new sensor per OEM procedure (zero and span adjustment if required).
- Update maintenance and calibration records.
- Monitor Hyd_Pressure readings for 30 minutes under normal operation.
- Advise operators to report any recurrence immediately.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- High-pressure hydraulic fluid can cause serious injury—always depressurize before service.
- Wear PPE: safety glasses, gloves, and protective clothing.
- Follow lock-out/tag-out (LOTO) procedures for all energy sources.
- Be aware of hot surfaces and potential oil spray during sensor removal.
- Never bypass or disable safety interlocks.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect Hyd_Pressure sensor and wiring every 3 months.
- Verify sensor calibration annually.
- Check for signs of oil ingress or connector corrosion during each PM.
- Replace sensor at manufacturer’s recommended interval or if drift is detected.

## 9. Related Faults

**This fault can cause:**
- Unnecessary accumulator charging cycles (Hyd_Valve_P_Up cycling)
- Hydraulic pump overuse or wear
- Production stoppage due to false alarms

**This fault can be caused by:**
- Electrical surges damaging sensor electronics
- Poor grounding or EMI in sensor wiring

**Similar symptoms but different causes:**
- Actual hydraulic pressure loss (e.g., pump failure, relief valve stuck open)
- Control module analog input failure
- Severe hydraulic leaks

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_9
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at

---