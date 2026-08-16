# Windows Infrastructure Troubleshooting Lab

## 1. Project Overview

This project is a hands on Windows Server infrastructure lab designed to develop practical skills in server administration, networking, IIS, Windows Firewall, PowerShell diagnostics, and structured troubleshooting

The lab is built using a Windows Server 2022 virtual machine running on Oracle VirtualBox. The environment provides an isolated platform for configuring infrastructure, introducing controlled faults, investigating incidents, applying corrective actions, and verifying successful recovery

The project focuses on understanding 'why an infrastructure problem occurs', rather than simply applying a fix

---

## 2. Objectives

The objectives of this lab are to:

- Build and configure a Windows Server 2022 virtual machine
- Configure and understand NAT and Host-only networking
- Configure and verify server IP addressing
- Install and administer IIS
- Test local and remote network connectivity
- Use PowerShell for network diagnostics
- Troubleshoot Windows Firewall configuration
- Apply a structured troubleshooting methodology
- Document incidents, root causes, corrective actions, and verification
- Maintain technical evidence throughout the lab

---

## 3. Lab Environment

### Host Machine

- Windows laptop
- Intel Core i5-1235U
- 8 GB RAM
- x64-based system
- 167 GB available storage

### Virtualisation

- Oracle VirtualBox 7.2.14

### Virtual Machine

| Configuration | Value |
|---|---|
| Operating System | Windows Server 2022 Standard Evaluation |
| Installation | Desktop Experience |
| Hostname | WIN-SRV01 |
| Memory | 3 GB |
| CPU | 2 processors |
| Virtual Disk | 60 GB |

---

## 4. Network Configuration

The virtual machine uses two network adapters.

| Adapter | Configuration | IPv4 Address | Purpose |
|---|---|---|---|
| Adapter 1 | NAT | 10.0.2.15 | External/Internet connectivity |
| Adapter 2 | Host-only | 192.168.56.101 | Private communication with host |

The NAT adapter provides external connectivity to the virtual machine

The Host-only adapter provides an isolated private network between the physical host and the virtual machine. This network is used to test remote access to services running on WIN-SRV01

The Host-only adapter does not require a default gateway for communication within the private lab network

---

## 5. Initial Network Verification

The initial network configuration was verified using PowerShell.

The following checks were performed:

- IP configuration
- External IP connectivity
- DNS name resolution
- Host-only network configuration
- Remote connectivity to the server

The baseline tests confirmed that the server had valid network connectivity before troubleshooting scenarios were introduced

---

## 6. IIS Configuration

Internet Information Services (IIS) was installed on WIN-SRV01 using Server Manager

The IIS installation was verified locally using:
http://localhost

The IIS welcome page loaded successfully

Remote access was then tested from the physical Windows host using:
http://192.168.56.101

## 7. Troubleshooting Methodology

The troubleshooting process used in this lab follows a layered approach

When an infrastructure service becomes unavailable, the investigation begins with the lowest-level relevant checks and progressively moves toward the application layer

The general approach is:

Client
  ↓
Network connectivity
  ↓
IP configuration
  ↓
Security controls / Firewall
  ↓
TCP port
  ↓
Service
  ↓
Application

The purpose of this approach is to isolate the failure rather than immediately changing configuration.

Each incident is documented using:

- Problem identification
- Known-good baseline
- Fault introduction
- Testing and observation
- Root-cause analysis
- Corrective action
- Verification

Lessons learned
## 8. Incident INC-001 — Internal Web Server Unreachable

**8.1 Incident Overview**

Incident ID: INC-001

System: WIN-SRV01

Service: IIS

Status: Resolved

Reported Problem

The IIS website became inaccessible from the physical Windows host

The website had previously been confirmed as operational and accessible through the server's Host-only network address

**8.2 Known-Good State**

Before introducing the fault:

IIS was installed and running
http://localhost successfully displayed the IIS welcome page
The physical host successfully accessed http://192.168.56.101
The HTTP inbound Windows Firewall rule was enabled

This established a known-good baseline

**8.3 Fault Introduction**

A controlled fault was introduced by disabling the Windows Firewall rule:

World Wide Web Services (HTTP Traffic-In)

The rule was disabled rather than deleted so that the original configuration could be restored after the investigation.

**8.4 Observed Symptoms**

After the firewall rule was disabled:

The IIS website could no longer be accessed from the physical host
Ping testing from the host timed out
TCP connectivity testing to port 80 failed

The following PowerShell command was used:
Test-NetConnection 192.168.56.101 -Port 80

The result was:
TcpTestSucceeded : False

**8.5 Troubleshooting Process**

Test 1 — Local IIS Availability

The IIS website was tested from WIN-SRV01:

http://localhost

Result: Successful.

This established that the IIS service itself was still functioning.

Test 2 — Server IP Configuration

The server's Host-only adapter was checked using:

ipconfig

The address remained:

192.168.56.101

This ruled out loss of the expected Host-only IP configuration.

Test 3 — Local Host-only Connectivity

The server tested its own Host-only address:

ping 192.168.56.101

Result: Successful.

Test 4 — Local TCP Port Test

The server tested TCP port 80:

Test-NetConnection 192.168.56.101 -Port 80

Result:

TcpTestSucceeded : True

This demonstrated that the IIS service was accessible locally through the Host-only address and that TCP port 80 was accepting connections locally.

Test 5 — Remote TCP Connectivity

The physical Windows host tested the server:

Test-NetConnection 192.168.56.101 -Port 80

Result:

TcpTestSucceeded : False

The investigation therefore focused on the remote network and security path rather than IIS itself.

**8.6 Root Cause**

The World Wide Web Services (HTTP Traffic-In) Windows Firewall inbound rule had been disabled.

This prevented inbound HTTP traffic to TCP port 80 from reaching the IIS service from the physical host.

The IIS service itself was functioning correctly.

**8.7 Corrective Action**

The World Wide Web Services (HTTP Traffic-In) firewall rule was re-enabled.

The existing rule was restored rather than recreated.

**8.8 Verification**

After re-enabling the firewall rule:

TCP port 80 became reachable from the physical host.
The IIS website became accessible again.
Remote HTTP access to 192.168.56.101 was restored.

The incident was therefore confirmed as resolved.

**8.9 Lessons Learned**

This incident demonstrated the importance of troubleshooting infrastructure problems systematically

A service being unavailable remotely does not necessarily mean that the service itself has failed

In this case:

IIS locally → Working
Server IP → Correct
TCP 80 locally → Working
Remote TCP 80 → Failed
Firewall rule → Disabled

The evidence allowed the fault to be isolated to the Windows Firewall configuration

The incident reinforced the value of:

- Establishing a known-good baseline
- Testing locally and remotely
- Using PowerShell network diagnostics
- Checking security controls before modifying application configuration
- Verifying the fix after remediation
- Documenting the troubleshooting process

## 9. Evidence

Evidence is being captured throughout the lab to document configuration, testing, troubleshooting, and recovery

The primary evidence for INC-001 includes:

Evidence	Description
- 01_HTTP_Firewall_Rule_Healthy.png -	Known-good HTTP firewall configuration
- 02_INC001_Network_Test_Failure.png - Failed remote connectivity tests
- 03_INC001_Firewall_Root_Cause.png	- Disabled HTTP firewall rule
- 04_INC001_Resolution.png - Successful recovery after remediation
