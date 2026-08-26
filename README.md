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
## Security Monitoring

[Logs, alerts, dashboards]

## Detection & Attack Simulation

[Test scenarios]

## AI Integration

[How AI is being incorporated]

## Incident Investigation

[Example investigations]

## Lessons Learned

[What you learned]
