# Troubleshooting Manual: Leakage_in_coolant_system

**Fault ID:** exp_22
**Subsystem:** Coolant
**Scenario:** {'init': 'Machine is in normal operation; coolant flow is stable.', 'expected_result': 'Coolant circulates properly through the system, keeping the machine at a stable temperature. Coolant level remains within normal operating range. No system faults occur.', 'instead_failure': 'The high pressure pump fails and coolant level in tank drops below the minimum threshold.'}

---

## 1. Fault Overview

This fault occurs when the high pressure pump in the coolant subsystem fails, resulting in the coolant level in the main tank dropping below the minimum threshold. The failure disrupts stable coolant circulation, risking machine overheating and potential damage to critical components.

## 2. System Impact

- **What stops working?**  
  The high pressure coolant flow ceases, and the system cannot maintain required coolant levels or pressure for safe operation.
- **What safety systems activate?**  
  Motor protection switches for the high pressure pump and low pressure pump may engage; level supervision alarms trigger to prevent overflow or dry running.
- **Production consequences?**  
  Machining operations halt due to insufficient cooling, risking thermal damage to workpieces and tooling, and causing unplanned downtime.

## 3. Observable Symptoms

**Via Alarms:**
- HP_A_700304: "Coolant high pressure (HP) pump motor protection switch triggered"
- CLT_A_700310: "Coolant tank fill level is below minimum"
- LP_A_700301 (possible): "Coolant low pressure (LP) pump motor protection switch triggered"

**Via Sensor Readings:**
- HP_Pump_Ok: False (should be True during normal operation)
- HP_Pump_isOff: True (should be False if pump is running)
- CLT_Level_lt_Min: True (should be False; indicates tank below minimum)
- LP_Pump_Ok: May be False if LP pump also affected
- CLF_Filter_Ok, F_Filter_Ok: Typically remain True unless secondary faults

**Expected vs. Actual Ranges:**
- HP_Pump_Ok: Expected = True; Actual = False
- CLT_Level_lt_Min: Expected = False; Actual = True

**Via Visual/Physical Inspection:**
- Coolant tank appears low or empty
- No audible operation from high pressure pump (normally a distinct hum)
- Possible warning lights on HMI or control panel
- Machine temperature rising abnormally
- No visible coolant flow at tool interface

## 4. Root Cause Analysis

### 4.1 Primary Root Cause
**Cause:** Failure of the high pressure pump (HP_Pump_Ok = False)  
**Mechanism:** The high pressure pump fails, either electrically or mechanically, stopping coolant circulation. Without replenishment, the coolant level in the tank drops below minimum, triggering CLT_Level_lt_Min = True and associated alarms.  
**Probability:** High

**Verification Steps:**
1. Check HP_Pump_Ok status on HMI or diagnostic panel; should read False.
2. Confirm HP_Pump_isOff = True (pump not running).
3. Inspect CLT_Level_lt_Min; should read True (tank below minimum).
4. Listen for absence of pump operation; verify no coolant flow.
5. If HP_Pump_Ok = False and CLT_Level_lt_Min = True, primary cause is confirmed.

### 4.2 Secondary Causes

- **Cause:** Low pressure pump failure (LP_Pump_Ok = False)
  - **Likelihood:** Medium
  - **Differentiating factors:** LP_Pump_Ok = False, LP_A_700301 alarm active, but HP_Pump_Ok may still be True.
- **Cause:** Coolant leak in tank or piping
  - **Likelihood:** Medium
  - **Differentiating factors:** Physical evidence of coolant outside containment, CLT_Level_lt_Min = True, but HP_Pump_Ok may be True.
- **Cause:** Blocked filter or fleece (CLF_Filter_Ok = False or F_Filter_Ok = False)
  - **Likelihood:** Low
  - **Differentiating factors:** CLF_A_700307 or F_A_700313 alarms active, but pump status may remain Ok.

### 4.3 Causal Chain

```
[High Pressure Pump Failure (HP_Pump_Ok=False)] → [High Pressure Pump Turns Off (HP_Pump_isOff=True)] → [Coolant Circulation Stops] → [Coolant Tank Level Drops Below Minimum (CLT_Level_lt_Min=True)] → [Alarms HP_A_700304 and CLT_A_700310 Triggered]
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- Action: Check HP_Pump_Ok status on HMI.
- Expected result: HP_Pump_Ok = True.
- If abnormal: Go to Step 2.
- If normal: Go to Step 5.

**Step 2: Check Alarms**
- Action: Review active alarms for HP_A_700304 and CLT_A_700310.
- Expected result: No active alarms.
- If abnormal: Go to Step 3.
- If normal: Go to Step 5.

**Step 3: Inspect Coolant Level**
- Action: Check CLT_Level_lt_Min variable and visually inspect tank.
- Expected result: CLT_Level_lt_Min = False; tank at nominal level.
- If abnormal: Go to Step 4.
- If normal: Go to Step 5.

**Step 4: Verify Pump Operation**
- Action: Listen for pump operation; check HP_Pump_isOff.
- Expected result: Audible pump operation; HP_Pump_isOff = False.
- If abnormal: Confirm high pressure pump failure; proceed to remediation.
- If normal: Investigate for secondary causes.

**Step 5: Check for Secondary Causes**
- Action: Review LP_Pump_Ok, CLF_Filter_Ok, F_Filter_Ok, and inspect for leaks.
- Expected result: All variables = True; no leaks.
- If abnormal: Address secondary cause per relevant fault section.

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Power down the CNC lathe and engage lock-out/tag-out procedures.
2. Depressurize coolant system by opening bleed valves.
3. Isolate the high pressure pump electrically and mechanically.
4. Document all sensor readings, alarms, and physical observations.

### 6.2 Repair Procedure

**Required:**
- Tools: Insulated screwdriver set, multimeter, torque wrench (20 Nm), coolant-safe gloves, spill containment kit.
- Parts: High Pressure Pump (P/N: COOL-PUMP-HP-001), Motor Protection Switch (if required, P/N: COOL-SWITCH-HP-002)
- Personnel: Certified maintenance technician, electrical qualification required.
- Estimated time: 2 hours

**Steps:**
1. Disconnect power supply to high pressure pump.
2. Remove pump mounting bolts (torque spec: 20 Nm).
3. Detach electrical connectors and coolant lines (note orientation).
4. Remove faulty pump and inspect for mechanical/electrical failure.
5. Install replacement pump (P/N: COOL-PUMP-HP-001), ensuring correct alignment and secure mounting (torque: 20 Nm).
6. Reconnect electrical connectors and coolant lines.
7. Replace motor protection switch if damaged (P/N: COOL-SWITCH-HP-002).
8. Restore power and check for leaks.
9. Reset alarms and clear fault history on HMI.

### 6.3 Verification Tests

After repair:
1. Power up system and command HP pump to run.
2. Confirm HP_Pump_Ok = True, HP_Pump_isOff = False.
3. Check CLT_Level_lt_Min = False; coolant tank at nominal level.
4. Run functional coolant circulation test for 10 minutes; observe for alarms and leaks.

### 6.4 Return to Service

- Calibrate coolant level sensors if disturbed.
- Update maintenance log with fault and repair details.
- Monitor HP_Pump_Ok and CLT_Level_lt_Min for 1 hour post-repair.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- Risk of electrical shock: always disconnect power and verify zero voltage.
- Pressurized coolant hazard: depressurize system before opening any lines.
- Wear PPE: coolant-safe gloves, safety goggles, anti-slip footwear.
- Lock-out/tag-out required for all electrical and mechanical work.
- Coolant may be hot; verify temperature before handling components.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect high pressure pump and motor protection switch every 500 operating hours.
- Monitor HP_Pump_Ok and CLT_Level_lt_Min variables weekly.
- Replace pump seals and check for wear at each scheduled maintenance.
- Clean coolant tank and check for debris every 3 months.

## 9. Related Faults

**This fault can cause:**
- Overheating of spindle and tooling
- LP pump overload (if system compensates)
- Machine stoppage due to coolant starvation

**This fault can be caused by:**
- Electrical supply failure to pump
- Motor protection switch trip
- Severe coolant leak

**Similar symptoms but different causes:**
- LP_Pump_Ok = False (low pressure pump failure)
- CLF_A_700307 (filter clogged, but pump may still run)
- F_A_700313 (fleece empty, but coolant level may remain normal)

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_22
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at