# SOC-Network-Environment

## Objective

The aim was to simulate credential dumping attacks via Powershell, targetting a workstation connected to a network that is being monitored by a Security Incident and Event Management tool. The simulated attack takes place after the attacker has already gained access to the workstation.

### Tools Used

- Wazuh, an open source SIEM that has been deployed to monitor our network endpoints.
- Atomic Red Framework acted as our attack script for credential dumping.
- Sysmon for detailed log ingestion.
- Ubuntu Linux is the OS that was used to host the Wazuh services.

## Summary

<img width="542" height="344" alt="Diagram" src="https://github.com/user-attachments/assets/a077f298-f61e-4344-8af0-04ac1453115b" />

*Ref 1: Network Diagram*

An attacker proceeds to run a script\
In this project two virtual machines were utilised for the compromised workstation and the Wazuh services. The workstation used Windows 10 as it's operating system while Wazuh's services ran on an Ubuntu Linux operating system

Brute force attacks generate multiple log events of “failed log in attempts” and Windows logs this in it's Security group as Event ID 4625. This Event ID is crucial when creating an alert on Splunk. A service called Splunk Universal forwarder is installed onto the Windows endpoint pointing to the IP address that is hosting Splunk Enterprise. This service forwards the Security logs which can be viewed on Splunk’s Dashboard on a browser. 


<img width="700" height="472" alt="Screenshot 2026-06-27 164319" src="https://github.com/user-attachments/assets/66733f58-b4a4-427c-9687-7ce96a444718" />

*Ref 2: Windows Server Domain*

Before creating an Active Directory environment, Active Directory Domain Services (AD DS) had to be installed and configured. This is so that we could utilise the Domain feature and create users and groups. As seen above, we created a user named Kulle KO. Otsuka and added him to the Tech Team security group. Now we are able to sign into the domain using the credentials that we have associated with Kulle's account. The Windows 10 Client uses this account to sign into the domain "splunklab.local".

<img width="490" height="54" alt="Screenshot 2026-06-19 171537" src="https://github.com/user-attachments/assets/3f947838-3164-449b-8695-923f2cc5ea6b" />

*Ref 3: Splunk Universal Forwarder*

Splunk Enterprise has been installed and configured on an Ubuntu Linux virtual machine. The Splunk Universal forwarder is installed on the Windows 10 client pointing to the IP address of the Linux VM machine.

In order to create a specific index for Splunk to ingest data into, we needed to configure a specific file telling it what to name the index and what data to ingest. The process is to copy the file "C:\Program Files\SplunkUniversalForwarder\etc\system\default\inputs.conf" into "C:\Program Files\SplunkUniversalForwarder\etc\system\local\" if it is not there already.

I have editted the file with notepad and added this line:\
[WinEventLog://Security]\
index = ad_logs\
disabled = false

This tells Splunk Universal Forwarder to forward the Windows Security logs into an index named "ad_logs" that must not be disabled.


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
