---
title: "One way to do Automation in a Zero Trust Environment"
date: 2020-04-19T04:00:00Z
author: "Dale Hassinger"
tags:
  - PowerCLI
  - PowerShell
  - vRA
  - NSX
  - vRealize Automation
  - VMware Aria
categories:
  - VCF Automation
image: "images/one-way-to-do-automation-in-a-zero-trust-environment-featured.png"
draft: false
---

**Automation with Zero Trust**



---

## PowerShell Code

There are two PowerCLI commands that have become my best friends in a Zero Trust Environment.

* Invoke-VMScript - Use to run scripts on VMs.
* Copy-VMGuestFile - Use to copy files to/from VMs.

In this example you can use Invoke-VMScript to run commands on a VM to install Trend Deep Security. All commands run with VM in a zero trust state.

---

{{< highlight powershell >}}

$VMName = 'VM Name'

#Copy file to the VM
$PSText = 'wget https://server01.vCrocs.info/software/agent/Ubuntu_18.04/x86_64/agent.deb'
Invoke-VMScript -VM $VMName -ScriptType Bash -ScriptText $PSText -GuestCredential $SSHcred | Select-Object -ExpandProperty ScriptOutput

#Run the install of the agent that was copied.
$PSText = 'sudo -S <<< "Password1" sudo apt install ./agent.deb'
Invoke-VMScript -VM $VMName -ScriptType Bash -ScriptText $PSText -GuestCredential $SSHcred | Select-Object -ExpandProperty ScriptOutput

#Run command to reset the agent
$PSText = 'sudo -S <<< "Password1" sudo /opt/ds_agent/dsa_control -r'
Invoke-VMScript -VM $VMName -ScriptType Bash -ScriptText $PSText -GuestCredential $SSHcred | Select-Object -ExpandProperty ScriptOutput

#Run command to config the agent
$PSText = 'sudo -S <<< "Password1" sudo /opt/ds_agent/dsa_control -a dsm://server01.vCrocs.info:4120/'
Invoke-VMScript -VM $VMName -ScriptType Bash -ScriptText $PSText -GuestCredential $SSHcred | Select-Object -ExpandProperty ScriptOutput

#Run command to get service staus.
$PSText = 'service ds_agent status'
$DeepSecurityServiceStatus = Invoke-VMScript -VM $VMName -ScriptType Bash -ScriptText $PSText -GuestCredential $SSHcred | Select-Object -ExpandProperty ScriptOutput

{{< /highlight >}}

---

In this example you can use Copy-VMGuestFile to copy a file to a VM and use invoke-VMScript to run commands on a VM to install agent. All commands run with VM in a zero trust state.

---

{{< highlight powershell >}}
$VMName = 'VM Name'

Copy-VMGuestFile -Source G:\splunkforwarder-8.0.1-6db836e2fb9e-linux-2.6-amd64.deb -Destination /home/vcrocsadmin -VM $VMNAME -LocalToGuest -GuestCredential $SSHcredsmadmin

#Run the install of the agent that was copied.
$PSText = 'sudo -S <<< "Password1" sudo apt install ./splunkforwarder-8.0.1-6db836e2fb9e-linux-2.6-amd64.deb'
Invoke-VMScript -VM $VMName -ScriptType Bash -ScriptText $PSText -GuestCredential $SSHcred | Select-Object -ExpandProperty ScriptOutput

#Run command to config the agent
$PSText = 'sudo -S <<< "Password1" sudo /opt/splunkforwarder/bin/splunk enable boot-start --accept-license --answer-yes --no-prompt --seed-passwd Password1'
Invoke-VMScript -VM $VMName -ScriptType Bash -ScriptText $PSText -GuestCredential $SSHcred | Select-Object -ExpandProperty ScriptOutput

#Run command to config the agent
$PSText = 'sudo -S <<< "Password1" sudo /opt/splunkforwarder/bin/splunk set deploy-poll server01.vCrocs.info:8089'
Invoke-VMScript -VM $VMName -ScriptType Bash -ScriptText $PSText -GuestCredential $SSHcred | Select-Object -ExpandProperty ScriptOutput

#Run command to get service staus.
$PSText = 'sudo -S <<< "Password1" service splunkd status'
Invoke-VMScript -VM $VMName -ScriptType Bash -ScriptText $PSText -GuestCredential $SSHcred | Select-Object -ExpandProperty ScriptOutput

{{< /highlight >}}

---

* If you found this Blog article useful and it helped you, Buy me a coffee to start my day.  

<center>
<script type="text/javascript" src="https://cdnjs.buymeacoffee.com/1.0.0/button.prod.min.js" data-name="bmc-button" data-slug="dalehassinger" data-color="#FFDD00" data-emoji=""  data-font="Cookie" data-text="Buy me a coffee" data-outline-color="#000000" data-font-color="#000000" data-coffee-color="#ffffff" ></script>
</center>

---