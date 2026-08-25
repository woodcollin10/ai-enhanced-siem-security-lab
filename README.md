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

## Wazuh Deployment

Wazuh was deployed on an Ubuntu Server virtual machine to serve as the centralized SIEM and security monitoring platform for the lab environment.

The Wazuh installation assistant was used to deploy the Wazuh Manager, Wazuh Indexer, and Wazuh Dashboard components.

The installation was initiated using:

sudo bash ./wazuh-install.sh -a

<img width="1000" height="764" alt="Wazuh" src="https://github.com/user-attachments/assets/44c98517-00c3-4a61-931e-d93ca7efb9a4" />

### Installation Troubleshooting

The initial Wazuh installation encountered several issues that required troubleshooting before the deployment could be completed.

#### 1. Existing Wazuh Installation

The installation assistant initially reported that Wazuh Manager was already installed:

```text
ERROR: Wazuh manager already installed.
```

To determine whether the Wazuh Manager package was still installed, the following command was used:

```bash
dpkg -l | grep wazuh
```

The output showed that `wazuh-manager` version `4.14.7-1` was installed.

#### 2. Port Conflicts

The installation assistant then reported that ports `1515` and `55000` were already being used by other processes:

```text
ERROR: Port 1515 is being used by another process.
ERROR: Port 55000 is being used by another process.
```

The processes using these ports were identified with:

```bash
sudo ss -ltnp | grep -E ':1515|:55000'
```

The output showed that existing Wazuh processes were listening on the affected ports.

The associated process IDs were identified with:

```bash
ps -fp 53616 53583
```

The processes were then stopped:

```bash
sudo kill 53583 53616
```

The ports were checked again to verify that the processes were no longer listening:

```bash
sudo ss -ltnp | grep -E ':1515|:55000'
```

#### 3. Wazuh Package Removal Failure

After stopping the existing processes, an attempt was made to remove the existing Wazuh Manager installation:

```bash
sudo apt remove --purge wazuh-manager -y
```

The removal initially failed because the Wazuh package's post-removal script attempted to access a directory that no longer existed:

```text
find: '/var/ossec/api/': No such file or directory
```

The package's removal script was inspected to determine what was causing the failure.

A backup of the original post-removal script was created:

```bash
sudo cp /var/lib/dpkg/info/wazuh-manager.postrm /var/lib/dpkg/info/wazuh-manager.postrm.bak
```

The problematic command was then modified so that the package could be removed without attempting to access the missing Wazuh directory:

```bash
sudo sed -i '/wazuh-control stop/c\ true' /var/lib/dpkg/info/wazuh-manager.postrm
```

The package removal was attempted again.

#### 4. Forcefully Purging the Wazuh Package

The Wazuh Manager package was still registered with the system even after the previous removal attempt. The package was therefore forcefully purged:

```bash
sudo dpkg --purge --force-all wazuh-manager
```

The output confirmed that the Wazuh Manager package was removed:

```text
Removing wazuh-manager (4.14.7-1) ...
Purging configuration files for wazuh-manager (4.14.7-1) ...
```

A warning indicated that `/var/ossec` was not completely removed because the directory was not empty. The remaining directory contents were checked:

```bash
sudo ls -la /var/ossec
```

The directory contained only the remaining `etc` directory.

#### 5. Verifying the Previous Installation Was Removed

The system was checked again for remaining Wazuh packages:

```bash
dpkg -l | grep wazuh
```

The system was also checked for processes using the Wazuh ports:

```bash
sudo ss -ltnp | grep -E ':1515|:55000'
```

These checks were used to confirm that the previous Wazuh Manager installation and associated processes had been removed.

#### 6. Reattempting the Installation

After the previous installation was removed and the port conflicts were resolved, the Wazuh installation assistant was run again:

```bash
sudo bash ./wazuh-install.sh -a
```

The installation completed successfully and the Wazuh web interface became available.

#### 7. Verifying Wazuh Services

After installation, the Wazuh Manager service was checked:

```bash
sudo systemctl status wazuh-manager
```

The service reported:

```text
Active: active (running)
```

The Wazuh Indexer and Wazuh Dashboard were also verified:

```bash
sudo systemctl is-active wazuh-manager wazuh-indexer wazuh-dashboard
```

The services returned an active status, confirming that the Wazuh environment was operational.

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
