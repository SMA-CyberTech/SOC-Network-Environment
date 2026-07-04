# SOC-Network-Environment

## Objective

The aim was to simulate credential dumping attacks via Powershell, targetting a workstation connected to a network that is being monitored by a Security Incident and Event Management tool. The simulated attack takes place after the attacker has already gained access to the workstation.

### Tools Used

- Wazuh, an open source SIEM that has been deployed to monitor our network endpoints.
- Atomic Red Framework acted as our attack script for credential dumping via powershell.
- Sysmon for detailed log ingestion.
- Ubuntu Linux is the OS that was used to host the Wazuh services while Windows 10 was used for the workstation.

## Summary

<img width="542" height="344" alt="Diagram" src="https://github.com/user-attachments/assets/442ea6aa-471e-4352-bc9e-b1d5b7e18c16" />



*Ref 1: Network Diagram*

An attacker proceeds to run a powershell script in order to steal credentials from a compromised workstation. The workstation has been configured with Sysmon and the Sysmon logs are being forwarded to the Wazuh SIEM via Wazuh's agent. A SOC analyst can then log into a browser with credentials for Wazuh and look at the SIEM's dashboard for any suspisious events. Sysmon is able to detect the powershell script process.

In this project two virtual machines were utilised for the compromised workstation and the Wazuh services. The workstation used Windows 10 as it's operating system while Wazuh's services ran on an Ubuntu Linux operating system

<img width="660" height="496" alt="Screenshot 2026-06-09 163048" src="https://github.com/user-attachments/assets/4a3fff32-0727-44ff-ade4-3086b3729743" />

*Ref 2: Sysmon Event Results*

Sysmon is a powerful Windows tool from Microsoft that runs as a service and logs detailed system activity to the Windows Event Log. The reason Sysmon is being utilised is because it provides more in depth telemetry compared to generic Windows logs. The quality of event data is crucial.

<img width="622" height="299" alt="Screenshot 2026-06-09 163341" src="https://github.com/user-attachments/assets/d040c65e-2d4f-4840-af44-d728c28156e9" />

*Ref 3: Sysmon Configuration*

Configuration for Sysmon ingestion takes place inside the Windows 10 endpoint's ossec.conf file which can be opened via notepad. Note that administration privileges are required to edit the file. The specific details on what to input into the file can be found in the Sysmon documentation.


<img width="739" height="287" alt="Screenshot 2026-06-09 161904" src="https://github.com/user-attachments/assets/2d2c3be6-29d5-4c17-b818-35b850bf4bef" />


*Ref 4: Atomic Red Framework*

For this attack to work, the attacker would have to disable the anti-virus software. Atomic Red Team is a collection of tests developed by Red Canary that simulate attacks and malicious behaviour via powershell. The T1059-001 credential dumping test was used on the  Windows 10 endpoint to create an alert in the SIEM for further investigation. Credential dumping is a technique attackers use to steal login information from a compromised OS or memory.


