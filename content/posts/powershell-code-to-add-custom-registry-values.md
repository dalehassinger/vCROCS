---
title: "Powershell code to add custom Registry values"
date: 2020-04-19T04:00:00Z
author: "Dale Hassinger"
tags:
  - PowerShell
  - PowerCLI
  - Registry
categories:
  - VCF Automation
image: "images/powershell-code-to-add-custom-registry-values-featured.png"
draft: false
---

**Add Windows Server Registry Values**



---

## PowerShell Code

Add Registry Values to Windows Server

---

{{< highlight powershell >}}

#vCrocs Registry Changes
invoke-command -computername $FQDNVMname -ScriptBlock {new-Item HKLM:\SOFTWARE\vCrocs -f }
invoke-command -computername $FQDNVMname -ScriptBlock {param ($SG) Set-ItemProperty hklm:\software\vCrocs -Name SupportGroup -Value "$SG" -Force} -ArgumentList $SG
invoke-command -computername $FQDNVMname -ScriptBlock {param ($APP) Set-ItemProperty hklm:\software\vCrocs -Name Application -Value "$APP" -Force} -ArgumentList $APP
invoke-command -computername $FQDNVMname -ScriptBlock {param ($LOC) Set-ItemProperty hklm:\software\vCrocs -Name Location -Value "$LOC" -Force} -ArgumentList $LOC
invoke-command -computername $FQDNVMname -ScriptBlock {param ($SL) Set-ItemProperty hklm:\software\vCrocs -Name ServiceLevel -Value "$SL" -Force} -ArgumentList $SL

{{< /highlight >}}

---

* If you found this Blog article useful and it helped you, Buy me a coffee to start my day.  

<center>
<script type="text/javascript" src="https://cdnjs.buymeacoffee.com/1.0.0/button.prod.min.js" data-name="bmc-button" data-slug="dalehassinger" data-color="#FFDD00" data-emoji=""  data-font="Cookie" data-text="Buy me a coffee" data-outline-color="#000000" data-font-color="#000000" data-coffee-color="#ffffff" ></script>
</center>

---