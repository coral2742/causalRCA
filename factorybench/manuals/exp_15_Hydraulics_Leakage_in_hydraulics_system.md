# Troubleshooting Manual: Leakage_in_hydraulics_system

**Fault ID:** exp_15
**Subsystem:** Hydraulics
**Scenario:** {'init': 'Machine is in normal operation; oil temperature is within the normal range (below 70°C), and hydraulic pressure is stable and above threshold.', 'expected_result': 'Oil temperature and hydraulic pressure remain within normal operating limits throughout the process.', 'instead_failure': 'A developing  cooling issues causes oil temperature to rise above 70°C, triggering a temperature warning. Hydraulic pressure then begins to drop, while the temperature continues to rise. In some runs, the pressure drops first; in others, the oil temperature exceeds the critical 80°C threshold first. Eventually, both conditions breach their respective limits, triggering fault alarms. The operator acknowledges the errors and terminates the experiment.'}

---

---
# Troubleshooting Manual Section: Hydraulic Overtemperature and Pressure Loss  
**Fault ID:** exp_15

---

## 1. Fault Overview

This fault scenario involves a simultaneous rise in hydraulic oil temperature above normal operating limits and a drop in hydraulic pressure on the CNC vertical lathe. These conditions can trigger multiple alarms and, if unaddressed, may lead to system shutdowns or equipment damage. Prompt identification and remediation are critical to maintain machine safety and production continuity.

---

## 2. System Impact

- **What stops working?**  
  Hydraulic actuation ceases once temperature exceeds 80°C or pressure drops below the safe threshold, resulting in loss of all hydraulically powered functions (e.g., chuck clamping, tool turret movement).
- **What safety systems activate?**  
  The system triggers temperature and pressure alarms (Hyd_A_700203, Hyd_A_700204, Hyd_A_700202), disables hydraulic enable (Hyd_IsEnabled=False), and may shut down the hydraulic pump (Hyd_Pump_isOff=True).
- **Production consequences?**  
  Machining operations halt, workpieces may remain clamped or unclamped, and extended downtime is possible due to required cooling and troubleshooting.

---

## 3. Observable Symptoms

**Via Alarms:**
- **Hyd_A_700203:** "Hydraulic oil temperature > 70°C (Warning)"
- **Hyd_A_700204:** "Hydraulic oil temperature > 80°C (Alarm – System Stop)"
- **Hyd_A_700202:** "Hydraulic pressure out of norm (Alarm)"
- **Hyd_A_700205/206:** (If oil level is also affected)

**Via Sensor Readings:**
- **Hyd_Temp_lt_70:** False (temperature ≥ 70°C)
- **Hyd_Temp_lt_80:** False (temperature ≥ 80°C)
  - *Expected:* True (oil < 70°C/80°C)
  - *Actual:* False (oil > 70°C/80°C)
- **Hyd_Pressure:** Drops below system threshold (refer to machine spec; e.g., < 12 bar)
  - *Expected:* Stable, above minimum threshold
  - *Actual:* Decreasing, possibly fluctuating below threshold
- **Hyd_Pump_On:** May cycle or remain on as system attempts to compensate

**Via Visual/Physical Inspection:**
- Unusually hot hydraulic tank or lines (caution: risk of burns)
- Possible audible pump strain or cavitation noises
- Oil odor (overheated oil may emit a burnt smell)
- No visible leaks, but possible increased vapor or mist near tank

---

## 4. Root Cause Analysis

### 4.1 Primary Root Cause

**Cause:** Hydraulic cooling system failure or degradation (e.g., blocked cooler, failed fan, low coolant flow)

**Mechanism:**  
A failure in the cooling circuit prevents adequate heat dissipation from the hydraulic oil. As oil temperature rises, viscosity decreases, reducing pump efficiency and causing hydraulic pressure to fall. Overheating may also trigger thermal protection, shutting down the pump and disabling hydraulics.

**Probability:** High

**Verification Steps:**
1. **Check Hyd_Temp_lt_70 and Hyd_Temp_lt_80:**  
   - If both are False, oil temperature > 80°C (critical).
2. **Inspect Hyd_Pressure:**  
   - Confirm if pressure is below threshold (see spec; e.g., < 12 bar).
3. **Examine cooling system components:**  
   - Is cooling fan operational?  
   - Is the oil cooler blocked or dirty?  
   - Is coolant flow adequate (if water-cooled)?
4. **Check for related alarms:**  
   - Hyd_A_700203/204 for temperature; Hyd_A_700202 for pressure.
5. **If cooling system failure confirmed:**  
   - Proceed to remediation.

### 4.2 Secondary Causes

- **Cause:** Hydraulic pump degradation or failure  
  - **Likelihood:** Medium  
  - **Differentiating factors:** Hyd_Pump_Ok=False, Hyd_Pump_isOff=True, possible Hyd_A_700208 (motor protection alarm).
- **Cause:** Low hydraulic oil level  
  - **Likelihood:** Medium  
  - **Differentiating factors:** Hyd_Level_Ok=False, Hyd_A_700205/206 triggered, visible low oil in tank.
- **Cause:** Clogged hydraulic filter  
  - **Likelihood:** Low  
  - **Differentiating factors:** Hyd_Filter_Ok=False, Hyd_A_700207 triggered, filter differential pressure high.

### 4.3 Causal Chain

```
[Cooling System Failure] → [Oil Temperature Rises] → [Oil Viscosity Drops] → [Hydraulic Pressure Drops] → [Hyd_A_700203/204, Hyd_A_700202 Triggered]
```

---

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- **Action:** Review active alarms (Hyd_A_700203, Hyd_A_700204, Hyd_A_700202)
- **Expected result:** No active alarms during normal operation
- **If abnormal:** Go to Step 2
- **If normal:** Monitor system, no action needed

**Step 2: Check Sensor Readings**
- **Action:** Read Hyd_Temp_lt_70, Hyd_Temp_lt_80, and Hyd_Pressure
- **Expected result:** Hyd_Temp_lt_70=True, Hyd_Temp_lt_80=True, Hyd_Pressure above threshold
- **If abnormal:** Go to Step 3

**Step 3: Inspect Cooling System**
- **Action:** Verify oil cooler/fan operation, check for blockages, confirm coolant flow
- **If cooling system not functioning:** Go to Step 4
- **If cooling system OK:** Go to Step 5

**Step 4: Check Hydraulic Pump and Oil Level**
- **Action:** Inspect Hyd_Pump_Ok, Hyd_Pump_isOff, Hyd_Level_Ok
- **If pump or oil level abnormal:** Investigate and repair as per respective fault procedures
- **If normal:** Cooling system failure confirmed

**Step 5: Inspect Hydraulic Filter**
- **Action:** Check Hyd_Filter_Ok, Hyd_A_700207
- **If filter clogged:** Replace filter
- **If filter OK:** Escalate to advanced diagnostics

---

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Power down the machine and activate lock-out/tag-out (LOTO) procedures.
2. Allow hydraulic system to cool; depressurize system per OEM guidelines.
3. Isolate the cooling circuit to prevent further heat buildup.
4. Document all active alarms, sensor readings, and system state.

### 6.2 Repair Procedure

**Required:**
- **Tools:** Infrared thermometer, multimeter, hydraulic pressure gauge, cleaning brushes, wrenches (metric), PPE
- **Parts:**  
  - Oil cooler (P/N: HYD-GEN-COOL-001)  
  - Cooling fan (P/N: HYD-GEN-FAN-002)  
  - Hydraulic oil (P/N: HYD-GEN-OIL-003)  
  - Filter element (if required, P/N: HYD-FILT-ELM-004)
- **Personnel:** Certified hydraulic technician
- **Estimated time:** 2–4 hours

**Steps:**
1. **Verify zero energy state** (LOTO, depressurized, cooled to <40°C).
2. **Inspect and clean oil cooler:**  
   - Remove debris, flush if necessary.
   - Check for internal blockages.
3. **Test cooling fan operation:**  
   - Replace if non-functional.
4. **Check coolant flow:**  
   - For water-cooled systems, inspect flow and temperature differential.
5. **Replace failed components:**  
   - Install new cooler or fan as needed.
   - Replace hydraulic oil if overheated (check for discoloration or burnt odor).
6. **Inspect and replace filter if indicated.**
7. **Reassemble and torque all fittings to spec (refer to OEM manual, e.g., 35 Nm for cooler lines).**
8. **Top up oil to correct level.**

### 6.3 Verification Tests

After repair:
1. **Restart system and monitor Hyd_Temp_lt_70, Hyd_Temp_lt_80, Hyd_Pressure.**
2. **Check for absence of Hyd_A_700203/204/202 alarms.**
3. **Run functional test:**  
   - Operate hydraulic actuators under load for 15 minutes.
   - Confirm temperature stabilizes below 70°C, pressure remains above threshold.

### 6.4 Return to Service

- **Calibration:** Verify temperature and pressure sensor calibration per OEM procedure.
- **Documentation:** Update maintenance logs with actions taken, parts replaced, and sensor readings.
- **Monitoring:** Observe system for 1 hour post-repair for recurrence of symptoms.

---

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- Risk of severe burns from hot hydraulic oil and components (>80°C).
- Always wear insulated gloves, safety glasses, and protective clothing.
- Strictly follow lock-out/tag-out (LOTO) procedures before opening any hydraulic or cooling circuit.
- Depressurize system fully before disconnecting lines.
- Beware of residual pressure and hot oil spray when opening fittings.

---

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect oil cooler and fan every 500 operating hours; clean as needed.
- Replace hydraulic oil every 2000 hours or per oil analysis.
- Check and replace filter element every 1000 hours or when Hyd_A_700207 triggers.
- Monitor Hyd_Temp_lt_70 and Hyd_Pressure trends weekly via system diagnostics.

---

## 9. Related Faults

**This fault can cause:**
- Hydraulic pump failure (if run at high temperature/low pressure)
- Accelerated seal and hose degradation

**This fault can be caused by:**
- Cooling fan failure
- Blocked oil cooler
- Low oil level (Hyd_A_700205/206)
- Filter clogging (Hyd_A_700207)

**Similar symptoms but different causes:**
- Hyd_A_700208 (Pump motor protection trip)
- Hyd_A_700206 (Oil level below minimum)
- Hyd_A_700207 (Filter clogged)

---

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_15
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at

---