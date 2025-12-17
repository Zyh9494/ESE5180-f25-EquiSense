# EquiSense — Horse Wearable IoT (GNSS + LTE-M + Cloud + Reliability)

EquiSense is a wearable IoT prototype for horses that combines **GNSS tracking**, **LTE-M telemetry**, and **on-device sensing** to enable “find my horse / geofencing” style use cases plus environmental & motion analytics. The system includes an end-to-end cloud pipeline (AWS IoT) and device observability (Memfault) for fleet-style debugging.

> Academic team project built for UPenn ESE5180 (IoT Wireless, Security, & Scaling).

---

## Demo & Links
- Project website: https://ese5180.github.io/iot-venture-f25-helldivers/
- Wireless demo video: https://youtu.be/xRVHlD40wJ0
- Final demo video (Drive): https://drive.google.com/file/d/1hPCtE9E95GH7761vfB--U3-8jLXJnr4Y/view?usp=sharing
- BOM / cost model: https://docs.google.com/spreadsheets/d/1zmwnLL9aSm3jkdN8P-3BPP1QO4xDeBbsMbr3Et-MiHA/edit?gid=2020998309#gid=2020998309

---

## What it does (in 30 seconds)
- **GNSS + LTE-M integration:** Acquire GNSS fixes, package telemetry, and publish to cloud over LTE-M.
- **Cloud telemetry:** Periodically publishes a JSON payload (e.g., `app_version`, `uptime`, balance state, environmental data) to AWS IoT.
- **Fleet visibility:** Multiple devices appear in Memfault with identifiers + software version for basic fleet management.
- **Observability & debugging:** Captures crashes and uploads **coredumps** to Memfault; reports critical runtime metrics (e.g., GNSS lock/search/fix state).
- **Secure update demo:** Demonstrated a **signed firmware update** workflow using AWS IoT Jobs + S3 (prototype demo).

---

## My Role (Yuhe Zhang)
I owned key **system integration + device reliability + hardware** deliverables:

- **GNSS + LTE-M system integration:** Coordinated GNSS positioning with LTE-M connectivity/telemetry so the device can reliably acquire fixes and publish data under intermittent network conditions (task scheduling, retry/backoff behavior, cold-start recovery).
- **Memfault coredump & observability:** Integrated Memfault to enable **automatic coredump capture + upload** and added critical runtime reporting (including GNSS status as a device health signal).
- **Device management:** Implemented device status/version reporting used for fleet-style visibility and remote debugging workflows.
- **Enclosure design & 3D printing:** Designed and iterated the wearable enclosure (fit, mounting constraints, cable routing, access to charging/debug), prototyped via 3D printing.
- **Power design:** Designed the battery/power path and performed power budgeting; worked on reducing consumption by coordinating GNSS/LTE duty cycling and sleep behavior.

---

## System Architecture (high-level)
**Device** (nRF9151 DK + sensors + battery)  
→ **LTE-M**  
→ **AWS IoT Core** (MQTT / Shadow / Jobs)  
→ downstream visualization / logging  
→ **Memfault** (coredumps + heartbeat metrics)

---

## Tech Stack
**Firmware / Embedded**
- Zephyr RTOS
- LTE-M + GNSS on Nordic nRF91 (nRF9151 DK)
- Sensors over I2C (IMU + environmental)

**Cloud**
- AWS IoT Core (MQTT)
- Device Shadow (device state/telemetry payloads)
- AWS IoT Jobs + S3 (signed update demo)

**Observability**
- Memfault: coredumps + issues tracking + heartbeat metrics (device health)

**Testing / Workflow**
- Unit tests for balance detection under `tests/sensor_shell`

---

## Repository Layout (typical)
> Folder names may evolve; these are the main components used in the project.

- `aws_iot/` — AWS IoT connectivity, shadow payload schema, jobs/FOTA scripts & docs
- `tests/sensor_shell` — unit tests for balance-detection logic (Twister)
- `images/` — diagrams, screenshots, and hardware photos

---

## Quick Start (for reviewers)
### 1) Setup Zephyr (one-time)
Follow Zephyr’s standard `west` setup for your environment (Zephyr SDK + Python deps).

### 2) Build & flash (example)
```bash
# from repo root (adjust paths/board to match your setup)
west build -b nrf9151dk/nrf9151 aws_iot
west flash
