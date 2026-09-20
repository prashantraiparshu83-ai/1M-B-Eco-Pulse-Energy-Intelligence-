# EcoPulse AI

An edge-enabled agentic intelligence engine running on **IBM Granite models** that processes campus vision feeds, automates room power relays, extracts smart meter data, awards sustainability credit points, and generates executive energy summaries for Department HODs.

---

## Overview

EcoPulse AI is a campus energy management system with three core capabilities:

| Payload Type | Description |
|---|---|
| `VISION_FRAME` | Multimodal analysis of CCTV or digital meter camera feeds |
| `IOT_STATE_EVENT` | Agentic relay control based on occupancy trends |
| `END_OF_DAY_REPORT` | RAG-powered sustainability credit computation and HOD email generation |

---

## Architecture

```
Campus Cameras / Smart Meters
        │
        ▼
  VISION_FRAME Analysis  ──▶  Occupancy + kWh Entity Extraction
        │
        ▼
IOT_STATE_EVENT Engine   ──▶  MQTT Relay Triggers (POWER_ON / POWER_OFF)
        │
        ▼
END_OF_DAY_REPORT (RAG)  ──▶  Savings Metrics + Credit Points + HOD Email
```

---

## Payload Types & Output Contracts

### 1. `VISION_FRAME` — Multimodal Input Analysis

Processes image frames or camera feed metadata to detect human occupancy and read smart meter display values.

**Output JSON:**
```json
{
  "device_type": "cctv_camera" | "digital_meter",
  "room_id": "STRING",
  "occupancy_detected": true | false,
  "confidence": 0.0–1.0,
  "meter_kwh_reading": FLOAT_OR_NULL
}
```

---

### 2. `IOT_STATE_EVENT` — Agentic Control Workflow

Evaluates occupancy trends and issues real-time IoT relay actions via MQTT.

**Rules:**
- **Rule 1:** `occupancy_detected == false` for ≥ 60 seconds → `POWER_OFF` (lights, fans, AC)
- **Rule 2:** `occupancy_detected == true` and current state is `OFF` → immediate `POWER_ON`

**Output JSON:**
```json
{
  "action": "TRIGGER_RELAY",
  "room_id": "STRING",
  "target_power_state": "ON" | "OFF",
  "mqtt_topic": "campus/room/{room_id}/relay",
  "reason": "STRING"
}
```

---

### 3. `END_OF_DAY_REPORT` — RAG, Sustainability Credits & Summarization

Uses a retrieved baseline from the database vs. actual daily smart meter logs to compute energy savings, carbon reduction, and sustainability credits.

**Calculations:**
```
Energy Saved (kWh)              = Baseline Unattended Usage − Actual Monitored Usage
Estimated Cost Saved            = Energy Saved × Local Tariff Rate
CO₂ Reduced (kg)                = Energy Saved × 0.85 kg/kWh
Sustainability Credits Earned   = Energy Saved (kWh) × 10 Credits/kWh
Updated Total Credit Balance    = Existing Balance + Credits Earned Today
```

**Output:** Polished markdown executive email to the Department HOD containing:
1. Executive Energy & Carbon Savings Summary
2. Credit Points Breakdown (today's earnings + total balance)
3. Campus Green Marketplace Recommendations (redeemable eco-equipment discounts)

---

## Getting Started

See [`implement.txt`](implement.txt) for a step-by-step guide to deploying and integrating EcoPulse AI.

---

## Technology Stack

- **AI Engine:** IBM Granite (edge-deployed)
- **Transport:** MQTT (IoT relay messaging)
- **Data:** Smart meter logs + RAG baseline database
- **Vision:** CCTV / digital meter camera feeds
