# Troubleshooting Manual: Leakage_in_coolant_system

**Fault ID:** exp_27
**Subsystem:** Coolant
**Scenario:** {'init': 'Machine is in normal operation; coolant level is stable and above the minimum threshold; high‑pressure pump motor is running normally.', 'expected_result': 'Coolant level remains above the minimum threshold and the pump motor continues operating without interruption.', 'instead_failure': 'Defective or worn seals cause coolant to leak, so tank level falls below the minimum threshold and triggers a MIN‑level alarm. Continued low flow leads to pump cavitation/overload, causing the high‑pressure pump motor protection to trip and shut the motor off. Operator acknowledges the faults and terminates the experiment.'}

---

## 1. Fault Overview

This fault scenario involves a loss of coolant due to defective or worn seals, resulting in the coolant tank level falling below the minimum threshold. This triggers a MIN-level alarm and, if unaddressed, leads to pump cavitation and overload, causing the high-pressure pump motor protection to trip and shut down the pump. Prompt troubleshooting is essential to prevent equipment damage and production downtime.

## 2. System Impact

- **What stops working?**  
  The high-pressure coolant pump motor shuts off, halting coolant delivery at high pressure. The system cannot maintain required cooling and lubrication for machining operations.

- **What safety systems activate?**  
  The motor protection switch for the high-pressure pump activates, and the system triggers alarms for low coolant level and pump faults, preventing further operation until resolved.

- **Production consequences?**  
  Machining operations are interrupted, risking tool damage, poor part quality, and potential delays in production schedules.

## 3. Observable Symptoms

**Via Alarms:**
- CLT_A_700310: "Coolant tank fill level is below minimum"
- HP_A_700304: "Coolant high pressure (HP) pump motor protection switch triggered"

**Via Sensor Readings:**
- CLT_Level_lt_Min = True (actual: True; expected: False)
- HP_Pump_Ok = False (actual: False; expected: True)
- HP_Pump_isOff = True (actual: True; expected: False)
- LT_Level_Ok may show abnormal if overflow occurs due to pump shutoff

**Via Visual/Physical Inspection:**
- Visible coolant leakage around tank seals, hoses, or fittings
- Wet floor or pooling coolant near the tank base
- Unusual noises from the pump (cavitation sounds) prior to shutdown
- Possible burnt odor from pump motor if overloaded

## 4. Root Cause Analysis

### 4.1 Primary Root Cause

**Cause:** Defective or worn seals causing coolant leakage from the tank  
**Mechanism:** Seal failure allows coolant to escape, lowering tank level below the minimum threshold. The low level triggers CLT_Level_lt_Min=True, activating CLT_A_700310. Continued low coolant leads to pump cavitation and overload, tripping the HP pump motor protection (HP_A_700304) and shutting off the pump (HP_Pump_Ok=False, HP_Pump_isOff=True).  
**Probability:** High (based on scenario and manipulated variables)

**Verification Steps:**
1. Inspect tank and associated seals for visible leaks or wetness.
2. Check CLT_Level_lt_Min sensor reading; confirm True (below minimum).
3. Verify CLT_A_700310 and HP_A_700304 alarms are active.
4. Confirm HP_Pump_Ok=False and HP_Pump_isOff=True.
5. If seals are visibly damaged or leaking, this is the confirmed cause.

### 4.2 Secondary Causes

- **Cause:** Coolant supply line rupture or loose fitting  
  - **Likelihood:** Medium  
  - **Differentiating factors:** Leakage may be from lines rather than tank; inspect all connections.

- **Cause:** Faulty coolant level sensor (false low reading)  
  - **Likelihood:** Low  
  - **Differentiating factors:** No actual coolant loss; tank visually full; sensor reading does not match physical inspection.

- **Cause:** Operator error (incomplete tank fill after maintenance)  
  - **Likelihood:** Low  
  - **Differentiating factors:** No evidence of leakage; recent maintenance records indicate incomplete fill.

### 4.3 Causal Chain

```
Defective/Worn Seal → Coolant Leakage → Tank Level Drops Below Minimum → CLT_Level_lt_Min=True → CLT_A_700310 → Pump Cavitation/Overload → HP_Pump_Ok=False → HP_A_700304 → HP_Pump_isOff=True
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- Action: Check for active alarms (CLT_A_700310, HP_A_700304)
- Expected result: No alarms
- If abnormal: Go to Step 2
- If normal: Resume operation

**Step 2: Sensor Verification**
- Action: Read CLT_Level_lt_Min and HP_Pump_Ok variables
- Expected result: CLT_Level_lt_Min=False, HP_Pump_Ok=True
- If CLT_Level_lt_Min=True: Go to Step 3
- If HP_Pump_Ok=False: Go to Step 4

**Step 3: Visual Inspection**
- Action: Inspect tank, seals, and surrounding area for coolant leaks
- If leak found: Go to Step 5
- If no leak: Go to Step 6

**Step 4: Pump Status Check**
- Action: Inspect HP pump for signs of cavitation, overload, or damage
- If pump shows damage: Schedule repair
- If pump is normal: Continue investigation

**Step 5: Leak Source Confirmation**
- Action: Identify and mark defective seal(s)
- If seal is visibly worn/damaged: Proceed to remediation
- If seal appears intact: Inspect supply lines and fittings

**Step 6: Sensor Validation**
- Action: Manually verify coolant level in tank
- If level is above minimum: Suspect sensor fault; replace sensor
- If level is below minimum: Refill coolant and monitor for leaks

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Power down the CNC lathe and engage lock-out/tag-out procedures.
2. Depressurize coolant system by opening bleed valves.
3. Isolate the coolant subsystem to prevent further leakage.
4. Document current sensor readings, alarm states, and physical observations.

### 6.2 Repair Procedure

**Required:**
- Tools: Socket set, seal puller, torque wrench, inspection mirror, absorbent pads
- Parts: Replacement seal (P/N: COOL-TANK-SEAL-002), coolant fluid (P/N: COOL-FLUID-001)
- Personnel: Maintenance technician with coolant system experience
- Estimated time: 60–90 minutes

**Steps:**
1. Remove access panels to coolant tank.
2. Drain remaining coolant into approved container.
3. Remove defective seal using seal puller; clean mating surfaces.
4. Install new seal (P/N: COOL-TANK-SEAL-002), ensuring proper seating and alignment.
5. Torque seal retaining bolts to manufacturer spec (e.g., 12 Nm).
6. Refill tank with coolant fluid to specified level (see tank gauge).
7. Inspect for leaks by pressurizing system briefly.
8. Replace access panels and clean work area.
9. Reset alarms and clear fault history in control system.

### 6.3 Verification Tests

After repair:
1. Power up system and monitor CLT_Level_lt_Min; confirm False (level above minimum).
2. Check HP_Pump_Ok; confirm True.
3. Run coolant pumps at low and high pressure; observe for leaks and abnormal noises.
4. Confirm no active alarms (CLT_A_700310, HP_A_700304).

### 6.4 Return to Service

- Calibrate coolant level sensor if replaced.
- Update maintenance logs and fault documentation.
- Monitor coolant level and pump status for 30 minutes post-repair.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- Risk of electrical shock when working near pump motors; disconnect power.
- Coolant may be hot or under pressure—depressurize before opening any fittings.
- Use PPE: chemical-resistant gloves, safety goggles, and non-slip footwear.
- Follow lock-out/tag-out procedures for all electrical and fluidic systems.
- Avoid skin contact with coolant fluid; refer to MSDS for handling instructions.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect tank seals and fittings every 1,000 operating hours.
- Monitor for wear indicators (cracks, swelling, hardening) on seals.
- Replace seals proactively every 12 months or at first sign of degradation.
- Check coolant level sensors for accuracy during monthly maintenance.

## 9. Related Faults

**This fault can cause:**
- HP pump damage due to repeated cavitation
- Tool overheating or accelerated wear
- Lifting tank overflow if system compensates incorrectly

**This fault can be caused by:**
- Upstream coolant contamination (damaging seals)
- Excessive system pressure
- Poor installation of seals during prior maintenance

**Similar symptoms but different causes:**
- CLT_A_700310 triggered by faulty level sensor (not actual leak)
- HP_A_700304 triggered by electrical fault in pump motor
- Coolant loss due to supply line rupture, not tank seal failure

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_27
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at