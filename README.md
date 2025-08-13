# Masterthesis – Node-RED Flows for Shop-Floor to Digital Twin Data Integration

## Overview
This repository contains the Node-RED flow definitions developed for the master’s thesis **“Design and Evaluation of a Reference Architecture for Data Transfer between Shop Floor and Digital Twins”**.  
The project implements a modular, low-code pipeline for acquiring data from production machines, standardizing it, and transmitting it to a Digital Twin implemented via the **Eclipse BaSyx Asset Administration Shell (AAS)**.

Three main data acquisition methods are supported:
- **File-based input** (e.g., CSV)
- **MQTT** (event-driven)
- **OPC UA** (polling)

All flows transform incoming raw data into a **predefined standardized structure**, perform optional aggregation, and send the results via HTTP to the BaSyx AAS.

---

## Repository Contents & Flow Descriptions

| File | Description |
|------|-------------|
| **flowsFileSystem.json** | Reads data from a local file system (CSV/JSON/XML). Triggers manually or on schedule. Minimal preprocessing needed due to already structured input. Generates request metadata and sends to BaSyx. |
| **flowsMQTT.json** | Event-driven flow that subscribes to an MQTT broker topic. Standardizes the message payload, performs aggregation (mean values, job duration), detects job changes, then sends completed job data to BaSyx. |
| **flowsOPCUA.json** | Polls data periodically from an OPC UA server. Processing logic is similar to the MQTT flow: standardization → aggregation → job change detection → send to BaSyx. |
| **flowsHTTP.json** | Handles the actual HTTP communication with the BaSyx AAS REST API. Formats payload into BaSyx-compliant JSON and posts it to the configured submodel endpoint. |
| **flowsTesting.json** | Contains test flows for validating transformations and HTTP requests without requiring live machine data. |

---

## Architecture & Module Mapping
Each flow implements the same four logical modules (sometimes combined within one flow):
1. **Data Acquisition Controller** – Connects to source (file, MQTT, OPC UA) and standardizes raw data.
2. **Data Aggregation Engine** – Performs mean calculations, duration measurement, or other aggregations.
3. **Request Preparation** – Formats standardized data into the JSON structure expected by BaSyx.
4. **AAS Communication Interface** – Sends HTTP POST requests to the BaSyx submodel endpoint.

---

## Required Node-RED Nodes
Only **one additional Node-RED package** is needed beyond the default installation:

```bash
cd ~/.node-red
npm install node-red-contrib-opcua
