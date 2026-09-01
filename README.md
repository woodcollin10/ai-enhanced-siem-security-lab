# AI-Enhanced SIEM & Security Monitoring Project

## Overview 

<img width="1000" height="698" alt="Network Diagram" src="https://github.com/user-attachments/assets/844bea16-ce6d-40bb-83bb-4047e542dbc5" />

### Systems
- Ubuntu Server — Wazuh SIEM
- Windows Server — Server/Endpoint
- Windows 11 — Workstation/Endpoint

## Project Objectives

- Deploy and configure Wazuh SIEM
- Configure endpoint monitoring
- Collect and analyze security logs
- Create and test detection rules
- Simulate security events
- Investigate generated alerts
- Incorporate AI into security analysis
- Document findings and troubleshooting

## 1) Wazuh Deployment

Wazuh was deployed on an Ubuntu Server virtual machine to serve as the centralized SIEM and security monitoring platform for the lab environment.

The Wazuh installation assistant was used to deploy the Wazuh Manager, Wazuh Indexer, and Wazuh Dashboard components.

<img width="800" height="564" alt="Wazuh" src="https://github.com/user-attachments/assets/44c98517-00c3-4a61-931e-d93ca7efb9a4" />

### Installation Troubleshooting

The initial Wazuh deployment encountered a storage issue that resulted in an incomplete installation. The installation process had already placed some Wazuh components on the Ubuntu Server before the available disk space was exhausted. This required troubleshooting the storage issue, cleaning up the incomplete installation, and performing a fresh installation.

#### 1. Insufficient Storage

The first issue encountered during the Wazuh installation was insufficient disk space on the Ubuntu Server.

Disk usage was checked using:

```bash
df -h
```

The output showed that the available storage was insufficient for the Wazuh installation to complete successfully.

Because Wazuh had already installed some components before the storage was exhausted, simply rerunning the installation was not enough. The partially installed components needed to be cleaned up before attempting a fresh installation.

#### 2. Cleaning Up the Incomplete Installation

The existing Wazuh installation and related files were investigated to determine what had already been installed.

Installed Wazuh packages were checked using:

```bash
dpkg -l | grep wazuh
```

The system was also checked for Wazuh processes and services that may have remained active from the incomplete installation.

The goal was to completely remove the partially installed Wazuh components so that the installation could be restarted from a clean state.

#### 3. Resolving the Storage Issue

The Ubuntu Server was cleaned up to provide sufficient storage for the complete Wazuh deployment.

Disk usage was verified again using:

```bash
df -h
```

Once sufficient storage was available, the remaining Wazuh installation components and processes from the failed installation were removed.

#### 4. Cleaning Up Wazuh Services and Packages

The system was checked for existing Wazuh services and packages before proceeding with the reinstall.

Wazuh services were examined using:

```bash
sudo systemctl status wazuh-manager
```

Existing Wazuh processes and network ports were also checked to ensure that the incomplete installation was no longer actively running.

Ports commonly used by the Wazuh Manager, including `1515` and `55000`, were checked using:

```bash
sudo ss -ltnp | grep -E ':1515|:55000'
```

Any remaining Wazuh processes associated with the incomplete installation were stopped and the existing Wazuh Manager package was removed.

#### 5. Fresh Wazuh Installation

After resolving the storage problem and cleaning up the incomplete installation, the Wazuh installation was started again from a clean state.

The all-in-one installation was performed using:

```bash
sudo bash ./wazuh-install.sh -a
```

This time, the installation completed successfully.

#### 6. Verifying the Installation

After the installation completed, the Wazuh services were checked to confirm that the deployment was operational:

```bash
sudo systemctl is-active wazuh-manager wazuh-indexer wazuh-dashboard
```

The services returned an `active` status, confirming that the Wazuh Manager, Indexer, and Dashboard were successfully running.

The Wazuh Dashboard was then accessed through a web browser, confirming that the installation was functioning correctly.

## 2) Agent Deployment

The Ubuntu server was configured as the centralized Wazuh server for the lab environment. The Windows systems were then configured as Wazuh agents and pointed to the Ubuntu server so that security events and system information could be collected and monitored through the Wazuh dashboard.

### Ubuntu Wazuh Server

The Ubuntu server was established as the central Wazuh management server. After confirming that the Wazuh services were running, the server's IP address was used as the destination for the Windows agents.

The Wazuh dashboard was accessed from the Windows environment to verify connectivity with the server and begin the agent deployment process.

<img width="800" height="564" alt="desktopwazuhconf" src="https://github.com/user-attachments/assets/f78d3d3d-0c54-4dbb-b89c-650dbe3b5971" />

<img width="800" height="564" alt="serverwazuh" src="https://github.com/user-attachments/assets/1fc06b14-f300-4098-a903-41aeef44b174" />

<img width="800" height="564" alt="systems agents" src="https://github.com/user-attachments/assets/db1f2c8e-fdbf-49c1-8490-c1cfd1b4dbfe" />

### Windows Agent Deployment

From the Wazuh dashboard, the **Deploy new agent** option was selected to begin configuring the Windows endpoints.

The Windows operating system and the Wazuh server information were specified during the deployment process. Wazuh then generated the installation and configuration commands required to deploy the agent.

The generated PowerShell commands were copied and executed in an **Administrator PowerShell** session on each Windows machine. These commands installed the Wazuh agent and configured it to communicate with the Ubuntu Wazuh server.

After installation, the Wazuh service was verified using PowerShell:

```powershell
Get-Service -Name WazuhSvc
```
## 3) Security Monitoring & Detection

## Failed Authentication Detection

To test the security monitoring capabilities of the Wazuh environment, a failed authentication scenario was intentionally performed on the Windows 11 machine, DESKTOP01. Multiple incorrect password attempts were made to generate Windows security events that could be collected and analyzed by Wazuh.

### 1. Verifying the Failed Authentication Event

Windows Event Viewer was first used to verify that the failed authentication attempts were successfully recorded by the operating system.

The Security log was filtered for **Event ID 4625**, which represents a failed logon attempt. The Event Viewer results showed multiple instances of Event ID 4625, confirming that the authentication failures were being logged by Windows.

<img width="800" height="564" alt="Event Viewer" src="https://github.com/user-attachments/assets/5fd1180d-e86b-407a-9a5b-100ea4927092" />

The selected event shows:

- **Log Name:** Security
- **Event ID:** 4625
- **Task Category:** Logon
- **Keywords:** Audit Failure
- **Computer:** Desktop01.woodtech.com
- **Message:** "An account failed to log on."

This confirmed that the failed authentication activity was being recorded at the Windows endpoint before investigating the event through Wazuh.

### 2. Investigating the Event in Wazuh

After confirming the event in Windows Event Viewer, the Wazuh dashboard was used to determine whether the Wazuh agent successfully collected and reported the activity.

The **Threat Hunting → Events** section was opened for the DESKTOP01 agent. A search for **4625** was performed to locate the failed authentication events.

<img width="800" height="564" alt="passauthwazuh" src="https://github.com/user-attachments/assets/ba2438d9-8690-4d88-a646-d2ff269193aa" />

Wazuh identified multiple events associated with DESKTOP01. The results included:

- **Agent:** DESKTOP01
- **Agent ID:** 001
- **Event ID:** 4625
- **Rule ID:** 60122
- **Rule Level:** 5
- **Rule Description:** Logon Failure - Unknown user or bad password

This demonstrated that the Windows security event was successfully collected by the Wazuh agent and processed by the Wazuh manager.

### 3. Inspecting the Alert Details

The magnifying glass next to an event was selected to investigate the alert in greater detail.

<img width="800" height="564" alt="psswdauthmagnify" src="https://github.com/user-attachments/assets/f84fc1ef-d9b3-4a41-825c-1273504f3b9c" />

The expanded event view provides additional information associated with the Windows security event. This includes information such as the affected computer, workstation name, target domain, target username, Windows Security channel, Event ID, event record ID, and the detailed Windows event message.

For this event, the investigation showed:

- **Workstation:** DESKTOP01
- **Target Domain:** WOODTECH
- **Target Username:** Administrator
- **Windows Channel:** Security
- **Event ID:** 4625
- **Event Message:** "An account failed to log on."

The expanded event information provides an analyst with additional context needed to investigate the authentication failure and determine whether the activity represents normal user error or potentially suspicious behavior.

### Detection Result

This test demonstrated the complete detection process:

1. An intentional failed authentication attempt was performed on DESKTOP01.
2. Windows generated a Security Event ID 4625.
3. The Wazuh agent collected the event from the Windows endpoint.
4. Wazuh processed the event using its detection rules.
5. The event appeared in Wazuh Threat Hunting.
6. The alert was opened and investigated using the expanded event details.

This establishes the foundation for additional security monitoring and attack simulation scenarios within the lab environment.

## AI Integration

[How AI is being incorporated]

## Incident Investigation

[Example investigations]

## Lessons Learned

[What you learned]
