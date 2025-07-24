# Azure-Sentinel-SIEM-Honeypot Project
This project simulates a real-world Security Operations Center (SOC) environment. A vulnerable Windows virtual machine (honeypot) is deployed in Azure to attract brute-force login attempts. Security logs are forwarded to Microsoft Sentinel (SIEM), where logs are enriched with geolocation data, visualized on an attack map, and used to trigger automated incident creation.
<br>

Key objectives include threat detection, log analysis, IP enrichment, automated response, and security event correlation using Microsoft’s cloud-native security tools.


# Lab Checklist

# Part 1: Create the Honeypot (Azure Virtual Machine)
Create a New Windows 10 VM

In Azure Portal, search for Virtual Machines.
Create a new Windows 10 VM (choose an appropriate size based on your available credits).
Configure Network Security Group (NSG) to allow all inbound traffic to simulate a vulnerable VM.
Disable the Windows Firewall: 
<br>
Start -> wf.msc -> Properties -> All Off.

Log into the VM:
Use the username and password you created when deploying the VM.

# Part 2: Logging into the VM and Inspecting Logs
Simulate Failed Login Attempts:
Fail 3 login attempts using an invalid username (e.g., employee).

Inspect Logs:
Log into the VM and open Event Viewer.
Navigate to Security Logs and check for Event ID 4625 (failed logon events).

![Logging into the VM and Inspecting Logs](project-screenshots/Logging%20into%20the%20VM%20and%20Inspecting%20Logs.PNG)

Create a Central Log Repository (Log Analytics Workspace):
We'll later forward logs to Microsoft Sentinel via the Log Analytics Workspace (LAW).

# Part 3: Log Forwarding and Querying with KQL
Create a Log Analytics Workspace (LAW)
In the Azure Portal, create a Log Analytics Workspace.

Create a Sentinel Instance:
Connect the LAW to Microsoft Sentinel.

Configure the "Windows Security Events via AMA" Connector:
Set up Data Collection Rules (DCR) to forward logs from your VM to Sentinel.

Query Logs in Sentinel:
Use Kusto Query Language (KQL) to query the failed logon events (Event ID 4625) in Sentinel:
```spl
kql

SecurityEvent
| where EventID == 4625
```

![Query for failed logins](project-screenshots/Query%20for%20failed%20logins.PNG)


# Part 4: Log Enrichment with GeoIP Data
Import GeoIP Watchlist:

Download the GeoIP watchlist: geoip-summarized.csv.
In Sentinel, create a Watchlist and import the CSV file with approximately 54,000 rows of IP address information.

![Microsoft Sentinel Watchlist Creation](project-screenshots/Microsoft%20Sentinel%20Watchlist%20Creation.PNG)

Enrich Logs with GeoIP Data:

Use the following KQL to enrich the logs with geographical information:
```spl
kql

let GeoIPDB_FULL = _GetWatchlist("geoip");
SecurityEvent
| where EventID == 4625
| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network)
```

Observe the Enriched Logs:

![Query adding map location](project-screenshots/Query%20adding%20map%20location.PNG)

View the geographical data of failed login attempts, identifying where attacks are originating.

# Part 5: Attack Map Creation
Create a Sentinel Workbook:
Create a new Workbook in Sentinel.
Delete the prepopulated elements and add a Query element.

Insert JSON for Attack Map:
Go to the Advanced Editor tab in the Workbook and paste the map.json for visualizing failed login attempts on a map.

Observe the Interactive Map:
Visualize failed login attempts with enriched GeoIP data to see where attacks are coming from.

![Windows VM Attack Map](project-screenshots/Windows%20VM%20Attack%20Map.PNG)

# Part 6: Incident Rule Configuration
Enable Built-in Analytics Rule for Incident Detection:
Go to Microsoft Sentinel → Analytics.
Search for the "Multiple Failed Authentication Attempts" rule and enable it.

Customize Incident Detection:
Modify the rule to detect multiple failed login attempts from the same IP address within a defined time window.

Create Incident Alerts:
Ensure that the rule is set to create an incident whenever it detects a suspicious activity such as multiple failed logins.

![Microsoft Sentinel Incidents](project-screenshots/Microsoft%20Sentinel%20Incidents.PNG)


# Skills Demonstrated

<br>

| Tools & Technologies          | Description                                        |
| ----------------------------- | -------------------------------------------------- |
| Microsoft Azure               | Virtual Machines, Resource Groups, Subscriptions   |
| Microsoft Sentinel (SIEM)     | Cloud-native SIEM for security analytics           |
| Azure Firewall & NSG          | Traffic filtering and network segmentation         |
| Log Analytics Workspace (LAW) | Centralized log collection and querying            |
| Azure Monitor & DCR           | Telemetry collection and rule-based log forwarding |
| Watchlists                    | IP threat intelligence and enrichment              |
| KQL                           | Used for querying and analyzing logs               |
| Visualization                 | Workbooks for graphical representation of attacks  |


<br>
<br>


| Security Skill               | Description                                   |
| ---------------------------- | --------------------------------------------- |
| Security Event Monitoring    | Tracking security logs using Sentinel         |
| Brute-force Detection        | Simulated attacks using failed logins         |
| Threat Detection & Response  | Alerts triggered from suspicious activity     |
| Log Correlation              | Linking multiple events to detect patterns    |
| IP Geolocation & Enrichment  | Enhancing logs with watchlist data            |
| Firewall & NSG Configuration | Allowing/denying traffic to simulate exposure |
        
<br>


# Final Result


• Simulated brute-force login attacks
<br>
• Logs collected via Log Analytics Workspace (LAW)
<br>
• Analyzed events using KQL queries
<br>
• IP addresses enriched via Azure Watchlists
<br>
• Attack map built in Sentinel Workbooks
<br>
• Created automated incidents for suspicious activity

