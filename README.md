# Victron Virtual PV Inverter for Shelly 1PM / Plus 1PM
A Node‑RED flow that converts Shelly 1PM / Shelly Plus 1PM power and energy telemetry into a Victron Virtual PV Inverter on Venus OS (GX devices).
This allows Shelly‑based PV systems, booster chargers, or auxiliary DC/AC generation sources to appear in VRM, Remote Console, and GX dashboards exactly like a real PV inverter. Please [watch my video](https://youtu.be/_HkP70xJzSw) for a live demonstration and detailed explanation on this project.

## Installation

1. Import the JSON flow into Node‑RED  
2. Set your Shelly IP in the HTTP request node  
3. Follow the instructions in the comments of the flow above each node 
4. Deploy and reboot the GX device

### Shelly URL configuration (important)
Depending on which Shelly device you use, the HTTP request URL must be set correctly:
#### Shelly Gen‑1 (1PM, EM, 2.5, Plug‑S)
Use the classic `/status` endpoint:
`http://<shelly-ip>/status`
#### Shelly Plus / Pro / Gen2, Gen3 Gen4 devices (RPC API)
Use the RPC endpoint:
`http://<shelly-ip>/rpc/Shelly.GetStatus?id=0`
Make sure the HTTP request node in Node‑RED matches your device type.  
If you switch Shelly models later, update the URL accordingly.

## Project Description
This project provides a Node‑RED flow that turns Shelly power meters (Shelly 1PM, EM, 2.5, Plus 1PM, Plus Plug, Pro 1pm, etc.) into fully functional Victron Virtual PV Inverters on Venus OS (GX devices).
It allows Shelly‑measured AC or DC generation sources to appear inside:
- VRM Portal
- Remote Console
- GX device dashboards
- Energy flow diagrams
- Daily PV production graphs
- exactly like a real PV inverter.

This is especially useful for:
- Small AC‑coupled PV systems
- Auxiliary solar chargers
- DC boosters
- Micro‑inverters
- Off‑grid hybrid setups
- Any Shelly‑monitored energy source you want visible in VRM

## Why Shelly?
Shelly devices are excellent for measuring power and energy, but Victron GX devices cannot read Shelly data directly at the moment. The current Shelly integration in Venus OS is mostly for AC Loads only and a role assignment to PV Inverter is missing.
This Node‑RED flow acts as a bridge, converting Shelly telemetry into Victron’s D‑Bus Virtual Device format.

However, Shelly Gen‑1 devices expose energy in a non‑standard unit:
- `total` = Watt‑minutes (W·min) → NOT Wh
- `counters[0]` = Wh (today) → correct
- `counters[1]` = Wh (yesterday)
- `counters[2]` = Wh (day before)
If you feed total into VRM, your daily PV production becomes 60× too large (e.g., 250–300 kWh/day).
This project fixes that by using the correct daily Wh counter.

Shelly Plus/Pro devices use proper Wh units, and the flow handles those automatically.

## What this flow does
- Reads Shelly JSON via HTTP
- Detects Gen‑1 vs Plus/Pro automatically
- Extracts correct power and daily energy
- Converts Wh → kWh
- Removes inverter standby noise (<5 W)
- Publishes a Virtual PV inverter to Venus OS
- VRM displays correct daily PV production
- Works with multiple Shelly devices
- Works with multiple virtual PV inverters
The result is a clean, accurate PV inverter inside VRM that behaves like a real one.

## Who this project is for
This project is ideal for:
- Victron users who want Shelly‑based PV monitoring
- Off‑grid setups with AC‑coupled micro‑inverters
- DIY solar enthusiasts
- People using Shelly EM/1PM to measure PV output
- Anyone wanting VRM visibility for non‑Victron energy sources
- Integrators who need reliable daily PV totals
If you’re running Venus OS + Node‑RED + Shelly, this flow gives you a seamless integration.

## How it works (technical overview)
The flow:
- Polls Shelly /status or RPC API
- Extracts:
  - power
  - counters[0] (Gen‑1 daily Wh)
  - aenergy.total (Plus daily Wh)
- Converts Wh → kWh
- Applies a configurable standby cutoff
- Builds a Virtual PV inverter payload
- Publishes it to D‑Bus via the Virtual Device node
- VRM receives correct power + daily energy
This makes Shelly appear as a real PV inverter in Victron’s ecosystem.

## VRM behaviour (important)
VRM does not use DeviceInstance for virtual devices.
VRM assigns IDs based on device category, not your configuration.

This is normal and expected.

Names do change in VRM, but IDs do not.

## Included in this repository
virtual-pv-shelly-flow.json  
A ready‑to‑import Node‑RED flow.

README.md  
Setup instructions, troubleshooting, and background information.

## License
MIT
