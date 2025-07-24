# Azure-Sentinel-SIEM-Honeypot
Simulate a real-world Security Operations Center (SOC) scenario by deploying a Windows honeypot, collecting and analyzing threat logs with Microsoft Sentinel, enriching data using a watchlist, visualizing attack sources, and generating automated incidents based on suspicious activity.

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

# Lab Checklist

# Part 1: Create the Honeypot (Azure Virtual Machine)
Create a New Windows 10 VM

In Azure Portal, search for Virtual Machines.
Create a new Windows 10 VM (choose an appropriate size based on your available credits).
Configure Network Security Group (NSG) to allow all inbound traffic to simulate a vulnerable VM.
Disable the Windows Firewall: 
Start -> wf.msc -> Properties -> All Off.

Log into the VM:
Use the username and password you created when deploying the VM.

# Part 2: Logging into the VM and Inspecting Logs
Simulate Failed Login Attempts:
Fail 3 login attempts using an invalid username (e.g., employee).

Inspect Logs:
Log into the VM and open Event Viewer.
Navigate to Security Logs and check for Event ID 4625 (failed logon events).

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

# Part 4: Log Enrichment with GeoIP Data
Import GeoIP Watchlist:

Download the GeoIP watchlist: geoip-summarized.csv.
In Sentinel, create a Watchlist and import the CSV file with approximately 54,000 rows of IP address information.

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

View the geographical data of failed login attempts, identifying where attacks are originating.

# Part 5: Attack Map Creation
Create a Sentinel Workbook:

Create a new Workbook in Sentinel.

Delete the prepopulated elements and add a Query element.

Insert JSON for Attack Map:

Go to the Advanced Editor tab in the Workbook and paste the map.json for visualizing failed login attempts on a map.

Observe the Interactive Map:

Visualize failed login attempts with enriched GeoIP data to see where attacks are coming from.

# Part 6: Incident Rule Configuration
Enable Built-in Analytics Rule for Incident Detection:

Go to Microsoft Sentinel → Analytics.

Search for the "Multiple Failed Authentication Attempts" rule and enable it.

Customize Incident Detection:

Modify the rule to detect multiple failed login attempts from the same IP address within a defined time window.

Create Incident Alerts:

Ensure that the rule is set to create an incident whenever it detects a suspicious activity such as multiple failed logins.


# Final Result:


# Skills Demonstrated
Skill Area	Description:
SIEM Integration	Configured Microsoft Sentinel with centralized log ingestion
Incident Detection	Automated brute-force detection rule with incident creation
Log Analysis (KQL)	Queried and filtered Windows Security logs
Threat Enrichment	Correlated attacker IPs with geolocation data using watchlists
Security Monitoring	Built real-time dashboards using Sentinel Workbooks
Visualization: Creating Workbooks for graphical representation of attacks.
Incident Detection: Configuring incident rules to monitor for suspicious activity.

