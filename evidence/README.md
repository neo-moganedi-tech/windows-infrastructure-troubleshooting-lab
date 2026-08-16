# Evidence

This directory contains selected screenshots captured during the Windows Infrastructure Troubleshooting Lab

The evidence documents the configuration, testing, troubleshooting, root-cause identification, and resolution of the first controlled incident

The screenshots were captured during the lab rather than collected retrospectively to maintain a clear record of the system state at each stage

---

## Evidence Overview

| File | Description |
|---|---|
| `01_HTTP_Firewall_Rule_Healthy.png` | Shows the HTTP inbound Windows Firewall rule in its known-good state before the troubleshooting incident was introduced |
| `02_INC001_Network_Test_Failure.png` | Shows the failed connectivity tests from the physical host after the incident was introduced, demonstrating that remote access to the server was no longer successful |
| `03_INC001_Firewall_Root_Cause.png` | Shows the HTTP inbound firewall rule in its disabled state, providing evidence of the configuration responsible for the loss of remote HTTP access |
| `04_INC001_Resolution.png` | Shows successful connectivity after the firewall rule was re-enabled, providing evidence that the corrective action restored service |

---

## INC-001 — Internal Web Server Unreachable

### 01 — HTTP Firewall Rule: Known-Good State

**File:** `01_HTTP_Firewall_Rule_Healthy.png`

This screenshot documents the HTTP inbound firewall rule before the incident was introduced

It establishes the known-good baseline against which the later failure can be compared

The rule was enabled and configured to allow HTTP traffic

---

### 02 — Network Test: Failure State

**File:** `02_INC001_Network_Test_Failure.png`

This screenshot documents the connectivity failure observed from the physical Windows host

The tests demonstrated that the host could no longer successfully communicate with the server over the expected HTTP service path

The evidence supports the investigation by showing the observed failure rather than simply recording that the website was unavailable

---

### 03 — Firewall Rule: Root Cause

**File:** `03_INC001_Firewall_Root_Cause.png`

This screenshot documents the HTTP inbound firewall rule after the controlled fault was introduced

The rule was disabled as part of the troubleshooting scenario

This provided the configuration evidence used to identify the Windows Firewall rule as the root cause of the remote HTTP connectivity failure

---

### 13 — Incident Resolution

**File:** `04_INC001_Resolution.png`

This screenshot documents the successful recovery after the HTTP inbound firewall rule was re-enabled

The successful connectivity test and restored IIS access confirmed that the corrective action resolved the incident

---

## Evidence Methodology

The evidence for each incident follows a simple troubleshooting sequence:

Known-Good State - Fault Introduced - Failure Observed - Root Cause Identified - Corrective Action - Successful Recovery
