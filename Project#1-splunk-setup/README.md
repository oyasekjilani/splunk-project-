# Splunk Installation and Universal Forwarder Setup

## Objective

Build a basic SIEM lab using Splunk Enterprise, Sysmon, and Splunk Universal Forwarder to collect and analyze Windows logs.

## Environment

* Windows 10 LTSC
* Splunk Enterprise
* Splunk Universal Forwarder
* Sysmon

---

## Step 1: Download Splunk Enterprise

Download Splunk Enterprise from the official Splunk website and install it on the system.

After installation:

1. Start Splunk.
2. Open the web interface.
3. Create an administrator account.
4. Log in to Splunk.

---

## Step 2: Configure Receiving Port

Navigate to:

Settings → Forwarding and Receiving → Configure Receiving

Create a new receiving port:

```text
9997
```

Save the configuration.

---

## Step 3: Download and Install Sysmon

Download Sysmon from Microsoft Sysinternals.

Extract the ZIP file and open PowerShell as Administrator.

Install Sysmon:

```powershell
sysmon64.exe -i
```

Verify installation:

```powershell
Get-Service Sysmon64
```

---

## Step 4: Download and Install Splunk Universal Forwarder

Download Splunk Universal Forwarder from the official Splunk website.

Install the forwarder and provide:

```text
Deployment Server: None
Receiving Indexer: Splunk Server IP
Receiving Port: 9997
```

---

## Step 5: Configure Inputs

Open:

```text
C:\Program Files\SplunkUniversalForwarder\etc\system\local\
```

Create or edit:

```text
inputs.conf
```

Configuration:

```ini
[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = 0
index = main
```

---

## Step 6: Configure Outputs

Edit:

```text
outputs.conf
```

Configuration:

```ini
[tcpout]
defaultGroup = default-autolb-group

[tcpout:default-autolb-group]
server = 127.0.0.1:9997
```

Replace the IP address with your Splunk server IP if necessary.

---

## Step 7: Restart Universal Forwarder

Open Command Prompt as Administrator:

```cmd
splunk restart
```

Or restart the service:

```powershell
Restart-Service splunkforwarder
```

---

## Step 8: Verify Log Collection

Search for Sysmon logs:

```spl
index=* sourcetype="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational"
```

Check process creation events:

```spl
index=* EventCode=1
| table _time host User Image CommandLine
```

---

## Result

Successfully installed Splunk Enterprise, Sysmon, and Universal Forwarder. Windows Sysmon logs were collected and analyzed through Splunk.

