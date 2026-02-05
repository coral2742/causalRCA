# Troubleshooting Manual: Hydraulic_oil_cooler_defective

**Fault ID:** exp_10
**Subsystem:** Hydraulics
**Scenario:** {'init': 'Machine is in normal operation; oil temperature is within the normal range and stable.', 'expected_result': 'Oil temperature remains within the specified limits with no unexpected increase.', 'instead_failure': 'Due to a defective cooler, oil temperature rises continuously at varying rates across runs until it exceeds the threshold; high‑temperature alarms are triggered.'}

---

---
## 1. Fault Overview

This fault scenario involves a continuous and abnormal rise in hydraulic oil temperature due to a defective oil cooler within the CNC vertical lathe’s hydraulic subsystem. If unaddressed, elevated oil temperature can lead to system shutdowns, accelerated component wear, and potential safety hazards.

## 2. System Impact

- **What stops working?**  
  The hydraulic subsystem may automatically shut down when oil temperature exceeds safety thresholds, halting all hydraulically actuated machine functions (e.g., tool clamping, axis movement).
- **What safety systems activate?**  
  The system triggers high-temperature warnings (Hyd_A_700203) and alarms (Hyd_A_700204), and may force a stop to prevent equipment damage.
- **Production consequences?**  
  Unplanned downtime, possible loss of in-process workpieces, and increased risk of secondary faults due to thermal stress.

## 3. Observable Symptoms

**Via Alarms:**
- Hyd_A_700203: “Hydraulic oil temperature exceeds 70°C (Warning)”
- Hyd_A_700204: “Hydraulic oil temperature exceeds 80°C (Alarm, system stop)”

**Via Sensor Readings:**
- `Hyd_Temp_lt_70`: False (indicates temperature >70°C; warning state)
- `Hyd_Temp_lt_80`: False (indicates temperature >80°C; system stop)
- Actual oil temperature (if available): Rising trend, exceeding normal operating range (typically 40–65°C)

**Via Visual/Physical Inspection:**
- Oil cooler may feel abnormally hot to the touch (use IR thermometer, do not touch directly)
- Possible increased noise from hydraulic components due to oil thinning
- Faint burnt oil odor near hydraulic tank or lines

## 4. Root Cause Analysis

### 4.1 Primary Root Cause

**Cause:** Defective oil cooler (failure to dissipate heat from hydraulic oil)  
**Mechanism:** The oil cooler fails to transfer heat away from the hydraulic oil, causing a continuous temperature rise. As the oil temperature exceeds 70°C, the system triggers a warning (Hyd_A_700203); above 80°C, it triggers an alarm (Hyd_A_700204) and may halt hydraulic operations to prevent damage.  
**Probability:** High (based on manipulated variables in digital twin experiment exp_10)

**Verification Steps:**
1. **Check alarm history:** Confirm Hyd_A_700203 and Hyd_A_700204 have been triggered.
2. **Review `Hyd_Temp_lt_70` and `Hyd_Temp_lt_80`:** Both should be False during the fault.
3. **Measure actual oil temperature:** Use the HMI or a calibrated sensor; confirm temperature >80°C.
4. **Inspect oil cooler:**
   - Ensure coolant flow (if water-cooled) or fan operation (if air-cooled).
   - Check for blockages, leaks, or fan failure.
5. **Decision point:**  
   - If oil temperature is high and cooler is not functioning → Proceed to repair cooler.  
   - If cooler is functioning but temperature still high → Investigate secondary causes.

### 4.2 Secondary Causes

- **Cause:** Hydraulic pump overloading (excessive recirculation or bypass flow)
  - **Likelihood:** Medium
  - **Differentiating factors:** High pump current, abnormal `Hyd_Pressure` readings, possible Hyd_A_700202 alarm.
- **Cause:** Low hydraulic oil level (inadequate heat capacity)
  - **Likelihood:** Low
  - **Differentiating factors:** Hyd_A_700205 or Hyd_A_700206 alarms, `Hyd_Level_Ok` = False.
- **Cause:** Contaminated or degraded hydraulic oil (reduced thermal conductivity)
  - **Likelihood:** Low
  - **Differentiating factors:** Oil appears dark, smells burnt, or shows high particulate content; `Hyd_Filter_Ok` may be False.

### 4.3 Causal Chain

```
[Defective Oil Cooler] → [Inadequate Heat Dissipation] → [Oil Temperature Rises] → [Hyd_A_700203 Warning] → [Hyd_A_700204 Alarm/System Stop]
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- Action: Check for active alarms (Hyd_A_700203, Hyd_A_700204) and review oil temperature on HMI.
- Expected result: No alarms, oil temperature <70°C.
- If abnormal: Go to Step 2.
- If normal: Monitor system; no action required.

**Step 2: Confirm Sensor Readings**
- Action: Verify `Hyd_Temp_lt_70` and `Hyd_Temp_lt_80` status.
- Expected result: Both True.
- If abnormal (either False): Go to Step 3.
- If normal: Fault is intermittent; monitor for recurrence.

**Step 3: Inspect Oil Cooler Function**
- Action: Check for coolant flow (water/air), fan operation, and physical blockages.
- Expected result: Cooler is operational, no blockages.
- If abnormal: Go to Step 4.
- If normal: Go to Step 5.

**Step 4: Assess Oil Level and Quality**
- Action: Check `Hyd_Level_Ok` and visually inspect oil.
- Expected result: Oil level and quality within spec.
- If abnormal: Address oil level/quality issues.
- If normal: Go to Step 5.

**Step 5: Check for Pump Overload or Recirculation Issues**
- Action: Review `Hyd_Pressure`, `Hyd_Pump_Ok`, and listen for abnormal pump noise.
- Expected result: All within normal range.
- If abnormal: Investigate pump or valve faults.

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Power down the machine and engage lock-out/tag-out (LOTO) procedures.
2. Allow hydraulic system to depressurize and cool to below 40°C.
3. Isolate the hydraulic subsystem and document all alarm and sensor states.

### 6.2 Repair Procedure

**Required:**
- Tools: IR thermometer, multimeter, coolant flow meter, basic hand tools, PPE (thermal gloves, goggles)
- Parts: Oil cooler assembly (P/N: HYD-GEN-COOL-001), replacement coolant (if required), seals (P/N: HYD-GEN-SEAL-002)
- Personnel: Certified hydraulic technician
- Estimated time: 2–4 hours

**Steps:**
1. Disconnect and drain hydraulic lines to the oil cooler; collect oil for reuse or disposal.
2. Remove defective oil cooler; inspect mounting points and seals.
3. Install new oil cooler (P/N: HYD-GEN-COOL-001) with new seals (P/N: HYD-GEN-SEAL-002); torque mounting bolts to 25 Nm.
4. Reconnect hydraulic and coolant lines; ensure all connections are tight and leak-free.
5. Refill hydraulic oil to specified level; bleed air from system as per OEM procedure.
6. Restore power and check for leaks.
7. Verify coolant flow (if water/air cooled) and fan operation.
8. Document all actions in maintenance log.

### 6.3 Verification Tests

After repair:
1. Start hydraulic system; monitor oil temperature via HMI.
2. Confirm `Hyd_Temp_lt_70` and `Hyd_Temp_lt_80` both read True after 30 minutes of operation.
3. Ensure no recurrence of Hyd_A_700203 or Hyd_A_700204 alarms during functional test (simulate full load if possible).

### 6.4 Return to Service

- Calibrate oil temperature sensor if required.
- Update maintenance and repair records.
- Monitor oil temperature and alarms for at least 8 hours of operation post-repair.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- High-temperature oil can cause severe burns—always verify temperature before handling components.
- Wear thermal-resistant gloves, goggles, and protective clothing.
- Strictly follow lock-out/tag-out (LOTO) procedures before servicing.
- Depressurize system fully before disconnecting any hydraulic lines.
- Be aware of hot surfaces and potential for oil spray under pressure.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect oil cooler and coolant flow monthly.
- Clean cooler fins and check for blockages every 3 months.
- Replace oil cooler seals annually or at first sign of leakage.
- Monitor `Hyd_Temp_lt_70` and `Hyd_Temp_lt_80` trends weekly.
- Schedule full oil analysis and cooler performance check every 12 months.

## 9. Related Faults

**This fault can cause:**
- Premature hydraulic pump wear (possible Hyd_Pump_Ok=False)
- Hydraulic seal degradation and leaks
- System-wide shutdowns due to overtemperature

**This fault can be caused by:**
- Coolant supply failure (pump or fan malfunction)
- Blocked or fouled oil cooler
- Incorrect oil type or viscosity

**Similar symptoms but different causes:**
- High oil temperature from low oil level (Hyd_A_700205/Hyd_A_700206)
- Overheating due to excessive hydraulic load or pump recirculation (Hyd_A_700202)

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_10
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at

---