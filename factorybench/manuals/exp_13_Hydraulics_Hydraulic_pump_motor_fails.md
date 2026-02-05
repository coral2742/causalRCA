# Troubleshooting Manual: Hydraulic_pump_motor_fails

**Fault ID:** exp_13
**Subsystem:** Hydraulics
**Scenario:** {'init': 'Machine is in normal operation; hydraulic pump motor is available.', 'instead_failure': 'The hydraulic pump motor fails spontaneously during operation, causing condition monitoring to trigger.'}

---

## 1. Fault Overview

This fault scenario describes a spontaneous failure of the hydraulic pump motor during normal CNC vertical lathe operation. Such a failure interrupts hydraulic subsystem function, leading to immediate system alarms and potential machine stoppage. Prompt diagnosis and remediation are critical to restore production and prevent secondary equipment damage.

## 2. System Impact

- **What stops working?**  
  The hydraulic pump ceases operation, resulting in loss of hydraulic pressure and disabling all hydraulically actuated functions (e.g., clamping, tool change, axis locking).
- **What safety systems activate?**  
  The system triggers alarms, disables hydraulics (Hyd_IsEnabled=False), and may automatically stop the machine to prevent damage.
- **Production consequences?**  
  Production halts until the hydraulic subsystem is restored; prolonged downtime may result if not addressed promptly.

## 3. Observable Symptoms

**Via Alarms:**
- Hyd_A_700202: "Hydraulic pressure out of norm"
- Hyd_A_700208: "Hydraulic pump motor protection switch triggered"
- Hyd_A_700206: "Hydraulic oil level below minimum" (possible secondary)
- Hyd_A_700203: "Hydraulic temperature > 70°C" (possible secondary)

**Via Sensor Readings:**
- Hyd_Pump_Ok: **False** (expected: True)
- Hyd_Pump_isOff: **True** (expected: False during operation)
- Hyd_Pump_On: **True** (system is commanding pump ON, but pump is OFF)
- Hyd_Pressure: **Below normal operating range** (expected: [system-specific setpoint], actual: near zero or rapidly falling)
- Hyd_IsEnabled: **False** (if safety disables hydraulics)

**Via Visual/Physical Inspection:**
- Hydraulic pump motor is not running (no vibration or sound)
- Possible tripped motor protection switch or blown fuse
- No hydraulic oil movement in sight glass or return line
- No abnormal odors (unless electrical failure caused burning smell)
- No visible leaks (unless failure caused seal damage)

## 4. Root Cause Analysis

### 4.1 Primary Root Cause

**Cause:** Spontaneous failure of the hydraulic pump motor (Hyd_Pump_Ok=False)

**Mechanism:**  
A sudden motor failure (e.g., electrical fault, thermal overload, or mechanical seizure) causes the hydraulic pump to stop. The system continues to command the pump ON (Hyd_Pump_On=True), but the motor does not respond (Hyd_Pump_isOff=True). Hydraulic pressure (Hyd_Pressure) drops below threshold, triggering Hyd_A_700202. The motor protection switch may also trigger, causing Hyd_A_700208.

**Probability:** High

**Verification Steps:**
1. **Check Hyd_Pump_Ok:**  
   - If **False**, proceed to Step 2.
2. **Check Hyd_Pump_On:**  
   - Should be **True** (system is commanding ON).
3. **Check Hyd_Pump_isOff:**  
   - If **True** while Hyd_Pump_On=True, pump is not running.
4. **Check Hyd_A_700208:**  
   - If **True**, motor protection switch has triggered.
5. **Check Hyd_Pressure:**  
   - If below [system setpoint] for >5s, confirms Hyd_A_700202.
6. **Physical inspection:**  
   - Verify pump motor is not running (no sound/vibration).
   - Check motor protection switch/fuse status.
7. **Decision:**  
   - If all above, primary cause confirmed.  
   - If not, proceed to secondary causes.

### 4.2 Secondary Causes

- **Cause:** Hydraulic pump motor protection switch tripped due to overcurrent or overheating  
  - **Likelihood:** Medium  
  - **Differentiating factors:** Hyd_Pump_Ok may be False, Hyd_A_700208=True, but may reset after cooling; check for prior high load or blocked pump.

- **Cause:** Hydraulic filter clogged, causing pump overload and shutdown  
  - **Likelihood:** Low  
  - **Differentiating factors:** Hyd_A_700207=True, Hyd_Filter_Ok=False, possible gradual pressure drop before failure.

- **Cause:** Hydraulic oil level below minimum, causing pump to run dry and trip protection  
  - **Likelihood:** Low  
  - **Differentiating factors:** Hyd_A_700206=True, Hyd_Level_Ok=False, visible low oil level.

### 4.3 Causal Chain

```
[Hydraulic pump motor failure] → [Hyd_Pump_Ok=False & Hyd_Pump_isOff=True] → [Hyd_Pressure drops below setpoint] → [Hyd_A_700202: Pressure out of norm alarm] → [Hyd_A_700208: Motor protection switch alarm]
```

## 5. Diagnostic Procedure

**Step 1: Initial Assessment**
- Action: Check Hyd_Pump_Ok status in HMI/diagnostics.
- Expected result: **True**
- If abnormal (**False**): Go to Step 2.
- If normal: Go to Step 5 (check for intermittent or sensor fault).

**Step 2: Command Check**
- Action: Verify Hyd_Pump_On and Hyd_Pump_isOff.
- Expected result: Hyd_Pump_On=True, Hyd_Pump_isOff=False during operation.
- If Hyd_Pump_On=True and Hyd_Pump_isOff=True: Go to Step 3.
- If not: Check control logic.

**Step 3: Alarm Review**
- Action: Check for Hyd_A_700208 (motor protection switch) and Hyd_A_700202 (pressure out of norm).
- If either alarm is active: Go to Step 4.
- If not: Check wiring and sensor integrity.

**Step 4: Physical Inspection**
- Action: Inspect hydraulic pump motor for operation, check protection switch/fuse.
- If motor is not running and protection switch is tripped: Reset switch, attempt restart.
- If switch/fuse trips again: Motor or pump likely defective—proceed to repair.

**Step 5: Sensor/Control Check**
- Action: Inspect wiring, connectors, and control relays for faults.
- If faults found: Repair as needed.
- If no faults: Escalate for advanced diagnostics.

## 6. Remediation

### 6.1 Immediate Actions

**BEFORE any repair work:**
1. Power down the machine and engage lock-out/tag-out (LOTO) procedures.
2. Depressurize the hydraulic system using the manual bleed valve.
3. Isolate the hydraulic pump circuit to prevent accidental start.
4. Document all current alarms, sensor readings, and HMI messages.

### 6.2 Repair Procedure

**Required:**
- Tools: Insulated screwdriver set, multimeter, torque wrench (40 Nm), oil catch pan, pump puller
- Parts: Hydraulic pump motor (P/N: HYD-PUMP-001), Motor protection switch (P/N: HYD-GEN-002), Fuses (P/N: HYD-GEN-003)
- Personnel: Certified maintenance technician (hydraulics/electrical)
- Estimated time: 2–3 hours

**Steps:**
1. Verify LOTO is in place and system is depressurized.
2. Remove pump motor electrical connections; label wires for reassembly.
3. Test motor windings with multimeter (resistance should be per spec, e.g., 2.5–3.5 Ω phase-to-phase).
4. If motor is faulty, unbolt and remove motor (torque bolts to 40 Nm during reinstallation).
5. Inspect pump coupling and shaft for wear or damage; replace if necessary.
6. Install new motor (P/N: HYD-PUMP-001), ensuring alignment and correct torque.
7. Reconnect electrical wiring as labeled.
8. Replace motor protection switch (P/N: HYD-GEN-002) and fuses (P/N: HYD-GEN-003) if required.
9. Check hydraulic oil level; top up if needed.
10. Remove all tools and materials from work area.

### 6.3 Verification Tests

After repair:
1. Remove LOTO and restore power.
2. Command hydraulic pump ON via HMI.
3. Observe Hyd_Pump_Ok=True, Hyd_Pump_isOff=False, Hyd_Pump_On=True.
4. Monitor Hyd_Pressure: should rise to normal operating range within 10 seconds.
5. Confirm no active alarms (Hyd_A_700202, Hyd_A_700208).
6. Run a full hydraulic function test (clamp/unclamp, axis lock, etc.).

### 6.4 Return to Service

- Calibrate pressure sensors if disturbed during repair.
- Update maintenance logs and fault records.
- Monitor hydraulic subsystem for 1 hour post-repair for recurrence of alarms or abnormal readings.

## 7. Safety Warnings

⚠️ **CRITICAL SAFETY INFORMATION:**
- High voltage and stored hydraulic energy—serious injury risk.
- Always wear PPE: safety glasses, insulated gloves, steel-toe boots.
- Lock-out/tag-out (LOTO) before opening electrical or hydraulic enclosures.
- Ensure system is fully depressurized before disconnecting any hydraulic lines.
- Beware of hot surfaces if recent overheating occurred.

## 8. Preventive Maintenance

**To prevent recurrence:**
- Inspect hydraulic pump motor and protection switch every 1,000 operating hours.
- Check for abnormal vibration, noise, or heat during weekly rounds.
- Test motor protection switch function quarterly.
- Replace hydraulic filter (P/N: HYD-FILTER-001) per schedule or when Hyd_Filter_Ok=False.
- Maintain oil level and quality; top up as required.

## 9. Related Faults

**This fault can cause:**
- Hyd_A_700202: Pressure out of norm
- Hyd_A_700206: Hydraulic oil level below minimum (if pump failure causes leaks)
- Hyd_A_700203: Hydraulic temperature > 70°C (if pump stalls)

**This fault can be caused by:**
- Hyd_A_700207: Hydraulic filter clogged (leading to pump overload)
- Hyd_A_700206: Oil level below minimum (pump runs dry)
- Electrical supply faults

**Similar symptoms but different causes:**
- Hyd_A_700207: Filter clogged (Hyd_Filter_Ok=False)
- Control relay failure (Hyd_Pump_On=False despite command)
- Sensor wiring fault (false Hyd_Pump_Ok reading)

## 10. Technical Notes

- Document revision: 1.0
- Last updated: February 2026
- Based on: Digital twin experiment exp_13
- Expert graph node references: cause_start_at, alarms_detected_at, diagnosis_at, cause_end_at, alarms_resolved_at

---