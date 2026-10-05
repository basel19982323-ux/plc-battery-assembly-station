# Troubleshooting

## Part 1: problems I hit while building it

| Symptom | Cause | Fix |
|---|---|---|
| *"Download of export restricted software. You have no authorization…"* | Some Siemens downloads need a one-time manual export approval (can take days). | Click *Request permission* and wait, or use a product you can download now. I used **S7-PLCSIM Advanced V7.0**. |
| *"License transfer could not be performed because of missing license key medium"* (PLCSIM Advanced setup) | The installer looks for a USB licence stick. | **Skip license transfer**, then activate the **trial** licence on first start. |
| *"No valid License Key was found … Trial License Keys may be activated"* | First use of STEP 7 / PLCSIM Advanced / WinCC Advanced. | Select the product → **Activate**. Don't click Skip, or the feature won't work. |
| **Online → Download to device is greyed out** | TIA stayed *half-online* after an earlier connection, even though the message said *"Connection terminated"*. | Click **Go offline**, select PLC_1 again, then **Download to device**. |
| *"PLC_1 might not be a trustworthy device"* (self-signed certificate, IP doesn't match) | S7-1500 secure communication: the simulated PLC uses its own certificate. | For your own local simulation → **Connect**. On a real network, check the device before trusting it. |
| *"The target PLC_1 in the offline project is different from the target in the online project"* | The PLC is still empty (nothing downloaded yet). | Do a **Download to device**, not *Go online*. |
| Orange **!** next to PLC_1 in the project tree while online | TIA reports a difference or warning for the online PLC. I saw it after adding the HMI to the project. | Open *Online & diagnostics* to read the reason; if the project and the PLC differ, download PLC_1 again. |
| Compile warning *"Inputs or outputs are used that do not exist in the configured hardware"* | No DI/DQ modules are configured (simulation). | Expected. With hardware, add I/O modules and match the addresses. |
| HMI notice *"Too many tags (Powertags) have been configured … 128 PowerTags"* | A licence-size notice from the PC runtime simulator. This project uses 13 PowerTags. | Click **Noted**. It didn't stop the simulation. |
| Button left "pressed" in the watch table (e.g. Start = TRUE) | Modify writes a value and **keeps** it. | Always write TRUE → Modify now → FALSE → Modify now. |
| Typing long text into TIA tables went into the wrong cell | Autocomplete in the watch table and tag fields. | Type the name, wait for the suggestion list, press Enter to accept it, then Enter again. |

## Part 2: operator / technician alarm guide

| Alarm | What the PLC saw | Likely real-world causes | Checks | Reset |
|---|---|---|---|---|
| **EMERGENCY STOP pressed** | `EStop_OK` = FALSE (NC circuit open) | E-stop pressed, broken wire, loose terminal, safety relay tripped | Find which E-stop is pressed; check wiring and terminals; check the safety relay status | Release the E-stop → RESET → START |
| **CONVEYOR JAM – module not arrived** | Conveyor ON but B1 not ON within 8 s | Module stuck or fallen, belt slipping, motor/VFD fault, B1 sensor dirty or misaligned | Look at the conveyor; check motor runs and the VFD has no fault; clean and align B1 and check its LED | Clear the cause → RESET → START |
| **CLAMP SENSOR FAULT – no feedback** | Clamp ON but B2 not ON within 3 s | Low air pressure, valve coil fault, cylinder seal leak, B2 sensor misaligned or broken, cable damage | Check air pressure; operate the valve manually; watch the cylinder move; check the B2 LED and cable | Fix → RESET → START |
| **MAINTENANCE DUE – service the station** | `CyclesSinceService` ≥ interval | Planned service interval reached | Do the tasks in [maintenance-plan.md](maintenance-plan.md) | SERVICE DONE |

General troubleshooting order: **safety first** (lock out / tag out) → read the alarm → look at the machine → check the sensor LED → check power and air → check the PLC input/output status (watch table or I/O LEDs) → wiring → replace the part → test → record the root cause (5 Whys).
