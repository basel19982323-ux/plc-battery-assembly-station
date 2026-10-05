# Example preventive-maintenance plan

> This is an **example** of how the data this program collects could drive maintenance on a real station of this kind. The intervals are illustrative: real ones come from the manufacturers' manuals and the plant's own failure history.

## Data the PLC already collects

| Data | Where | Use |
|---|---|---|
| Cycles since last service | `Station_DB.CyclesSinceService` | **Usage-based** service trigger (demo: every 20 cycles) |
| Total cycles | `Station_DB.CyclesTotal` | Production count, MTBF calculations |
| Last cycle time | `Station_DB.LastCycleTime` | **Condition monitoring:** a slowly growing cycle time often means wear (belt slip, sticky cylinder) |
| Conveyor starts / running seconds | `Station_DB.Conveyor.Operations`, `.OperatingTime_s` | Motor and belt wear, relubrication intervals |
| Clamp operations / ON time | `Station_DB.Clamp.Operations`, `.OperatingTime_s` | Cylinder seal and valve life (rated in switching cycles) |
| Fault history | HMI alarm log (time stamps) | Find repeat faults → root cause analysis |

## Plan

| Component | Task | Trigger (example) | Type |
|---|---|---|---|
| Conveyor belt | Check tension and tracking, clean | Every service interval | Usage-based |
| Conveyor motor / gearbox | Check noise, temperature, oil or grease | By `Conveyor.OperatingTime_s` | Usage-based |
| Sensor B1 (module at station) | Clean the lens, check alignment and LED | Every service interval, plus after any *Conveyor jam* | Usage + condition |
| Clamp cylinder | Check for air leaks, smooth movement, seals | By `Clamp.Operations` | Usage-based |
| Valve Y1 / air supply | Check pressure, drain the water separator | Weekly | Time-based |
| Sensor B2 (clamp closed) | Check the mounting and LED, test the switching point | Every service interval, plus after any *Clamp sensor fault* | Usage + condition |
| E-stop circuit | Functional test of every E-stop | Per the safety assessment (e.g. each shift or week) | Time-based, mandatory |
| Whole station | Compare cycle time with the baseline (7.3 s); investigate if > +10 % | Continuous | Condition-based |

After service: press **SERVICE DONE** on the HMI. This resets the counter and clears the yellow lamp. Record the work in the CMMS (e.g. IBM Maximo).

## KPIs these counters support

- **MTBF** (mean time between failures) = operating time ÷ number of faults
- **MTTR** (mean time to repair) = from alarm time stamp to restart
- **OEE** availability = run time ÷ planned time
