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

## Agent Deployment

[Windows agent setup]

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
