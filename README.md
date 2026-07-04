# SOC-Network-Environment

## Objective

The aim was to simulate credential dumping attacks via Powershell, targetting a workstation connected to a network that is being monitored by a Security Incident and Event Management tool. The simulated attack takes place after the attacker has already gained access to the workstation.

### Tools Used

- Wazuh, an open source SIEM that has been deployed to monitor our network endpoints.
- Atomic Red Framework acted as our attack script for credential dumping.
- Sysmon for detailed log ingestion.
- Ubuntu Linux is the OS that was used to host the Wazuh services.

## Summary

<img width="542" height="344" alt="Diagram" src="https://github.com/user-attachments/assets/442ea6aa-471e-4352-bc9e-b1d5b7e18c16" />



*Ref 1: Network Diagram*

An attacker proceeds to run a powershell script in order to steal credentials from a compromised workstation. The workstation has been configuredwith Sysmon, the Sysmon logs are being forwarded to the Wazuh SIEM via the Wazuh agent. A SOC analyst can then log into a browser with credentials for Wazuh and look at the SIEM's dashboard for any suspisious events. Sysmon is able to detect the powershell script process.

In this project two virtual machines were utilised for the compromised workstation and the Wazuh services. The workstation used Windows 10 as it's operating system while Wazuh's services ran on an Ubuntu Linux operating system

<img width="660" height="496" alt="Screenshot 2026-06-09 163048" src="https://github.com/user-attachments/assets/4a3fff32-0727-44ff-ade4-3086b3729743" />
*Ref 2: Sysmon Event Results*

Sysmon is a powerful Windows tool from Microsoft that runs as a service and logs detailed system activity to the Windows Event Log. The reason Sysmon is used is because it provides more in depth telemetry compared to generic Windows logs. The quality of event data is crucial.


<img width="419" height="132" alt="Screenshot 2026-06-27 165252" src="https://github.com/user-attachments/assets/fbb35ddd-4151-434a-b536-81ad89677670" />

*Ref 4: SPL Alert Prompt*

This Splunk prompt tells the dashboard to display Events from the ad_logs index containing Event Code 4625 from the Windows 10 endpoint, where said events appear more than 3 times.


<img width="601" height="254" alt="Screenshot 2026-06-27 165239" src="https://github.com/user-attachments/assets/9faaa790-397d-4f82-a7f3-b68e7939cfda" />

*Ref 4: Alert Settings*

With only the prompt, we would run into too many false positives as we need to add more specificity to the alert. What if someone accidently logged in with the wrong credentials once every week? This is why we have set the alert to trigger when there are 3+ events within the last 5 minutes of the present time (*/5 * * * *).

<img width="716" height="255" alt="Screenshot 2026-07-03 150336" src="https://github.com/user-attachments/assets/9c9c3c70-0257-48a4-b783-4068f49c4140" />

*Ref 5: Slack integration with Shuffle*

As seen in [Ref 4](https://github.com/user-attachments/assets/9faaa790-397d-4f82-a7f3-b68e7939cfda), after the event is triggered we input Shuffle's webhook into the alert so that data can be forwarded to Slack. 


<img width="1365" height="244" alt="Screenshot 2026-06-27 165559" src="https://github.com/user-attachments/assets/37bb981c-f7f7-4587-88c1-a997f320b882" />

*Ref 5: Slack integration with Shuffle*

This is the result of the Splunk --> Shuffle --> Slack integration. Once the link is clicked the user is taken straight to the Splunk dashboard to investigate the triggered alert.
