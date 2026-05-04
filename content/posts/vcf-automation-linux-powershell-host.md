---
title: "VCF Automation | Linux PowerShell Host"
date: 2025-03-15T04:00:00Z
author: "Dale Hassinger"
tags:
  - VCF
  - Automation
  - PowerShell
  - PowerCLI
  - VMware Code Coach
  - VMware vExpert
  - Linux
categories:
  - VCF Automation
image: "images/vcf-automation-linux-powershell-host-featured.png"
draft: false
---

**How to use a Rocky Linux PowerShell Host with VCF Automation Orchestrator. Linux ❤️ PowerShell!**



---

>Streamline VMware Automation with VCF Automation, a Rocky Linux PowerShell host, custom workflows, and the simplicity of PowerCLI.

---
## I like using PowerShell on Linux in my Lab:  

In most cases, a PowerShell Host for VCF Automation Orchestrator is set up on a Windows Server, which works seamlessly. Having spent much of my career working with Microsoft Windows Server, I’m well-versed in its capabilities. However, in my lab, where resource efficiency is a priority, I prefer to run Linux VMs whenever possible. I keep these Linux VMs lean and mean for maximum performance.  

I set out to explore how a Rocky Linux VM could function as a PowerShell Host. By default, VCF Automation Orchestrator includes a workflow called “Run SSH Command”, designed to execute SSH commands on a server. I began testing with this built-in workflow and customized it to better align with my requirements.  

To keep the setup simple, I wanted to create a single workflow where I could specify the PS script path and name. The Linux VM details—such as hostname, username, password, and port—would remain static.  

>For 2025, I’ve been invited to be a **VMware Community {code} Coach**! This year, my blogs will feature even more code examples to help others get started with automation, VMware solutions, and much more...

---

## Running PowerShell from the CLI and Executing a Script:


```Bash
pwsh -file /home/administrator/Home-Lab-PS.ps1

# If you want to use Script Parameters
pwsh -file /home/administrator/Home-Lab-PS.ps1 -vmName "VM-01" -PowerState "On"

```

---

## Workflow Design:  

![](images/linux0ps-host-01.gif)  

---


* [Click to download the workflow](https://github.com/dalehassinger/unlocking-the-potential/blob/main/VMware-Aria-Automation/Workflows/SSH-Run-PowerShell-Script.package)
* Import the Package into Orchestrator

---

**Java Script used in the Workflow to SSH to the Rocky Linux PowerShell Host and run a PS Script:**  

  
```JavaScript
var session = null;
try {
	if (port) {
		session = new SSHSession(hostNameOrIP, username, port);
	} else {
		System.log("A port value is not provided! Using default port 22");
		session = new SSHSession(hostNameOrIP, username);
	}

	if (passwordAuthentication){
		System.log("Connecting with password");
	} else {
		if (path == null || path == ""){
			System.log("using default");
			path = defaultKeyPairPath;
		}
		System.log("Connecting with key pair (" + path + ")");
		password = passphrase;
	}

	session.connectWithPasswordOrIdentity(passwordAuthentication, password, path);
	System.log("Connected!");

	System.log("Executing '" + cmd + "' using encoding '" + (encoding ? encoding : "Default System Encoding") + "'");
	session.setEncoding(encoding);
	session.executeCommand(cmd, true);

	output = session.getOutput();

	System.log("Output: '" + output + "'");

} catch (e) {
	throw "Unable to execute command: " + e;
} finally {
	if (session) {
		session.disconnect();
	}
}

```

---

## Code Examples:  

* YAML file to store values to be used with scripts


```YAML
# vCenter Connection Values
vCenter:
  server: vcsa8x.vcrocs.local
  username: administrator@vcrocs.local
  password: VMware1!
  ignoreCertErrors: true
```

---

**Sample PowerShell Script:**  
* Sample script that I used to show that Rocky Linux can be used as a PowerShell Host


```PowerShell
# Ensure required modules are installed before proceeding
$requiredModules = @("powershell-yaml", "VMware.PowerCLI")
foreach ($module in $requiredModules) {
    if (-not (Get-Module -ListAvailable -Name $module)) {
        Write-Host "$module is not installed. Install it using 'Install-Module $module'." -ForegroundColor Red
        exit
    }
}

# Read YAML configuration
$cfgFile = "Home-Lab-Config.yaml"
$cfg = Get-Content -Path $cfgFile -Raw | ConvertFrom-Yaml

Write-Host "Executing Script"

# Connect to vCenter
Write-Host "Connecting to vCenter..."

try {
    $credential = New-Object PSCredential ($cfg.vCenter.username, (ConvertTo-SecureString $cfg.vCenter.password -AsPlainText -Force))
    $vCenter = Connect-VIServer -Server $cfg.vCenter.server -Credential $credential -Force

    Write-Host "Running commands..."

    $vmNames = Get-VM | Select-Object Name, PowerState | Sort-Object Name

    $vmNames.Name

    Disconnect-VIServer -Server * -Confirm:$false

    Write-Host "Disconnecting from vCenter..."

    return "Script Completed"
}
catch {
    Write-Host "An error occurred: $_"
    return "Error: $_"
}

```

---

* VCF Automation Catalog with an Option to run the Orchestrator Workflow that was created to run the script on Rocky Linux  

![](images/linux0ps-host-02.png)  

---

* Orchestrator Workflow as a Catalog Item  
* This is a simple example to give you ideas. The script that will run could bascially Automate anything  
* Script Parameters become fields on a custom form
* If you can write a script for the process, you can Automate it with Orchestrator  

![](images/linux0ps-host-03.png)  

---

## Install PowerShell on Rocky Linux:


```Bash

# Install PowerShell on Rocky Linux
sudo dnf install -y powershell

# Ensure OpenSSH is installed and running
sudo dnf install -y openssh-server
sudo systemctl enable --now sshd

# Create a PowerShell subsystem entry in the SSH daemon config
echo "Subsystem powershell /usr/bin/pwsh -sshs -NoProfile" | sudo tee -a /etc/ssh/sshd_config

# Restart the SSH service
sudo systemctl restart sshd

```

---

## Lessons Learned:

* If you like to use Linux for Servers, this is a good use case for running PowerShell scripts  
* If you have existing PowerShell scripts that run on a Windows Server, most scripts should run on Linux with little to no modifications  
* I enjoy bringing consistency to my scripts by leveraging a YAML file, similar to the approach used in automation platforms I work with  
* All the scripts in this blog were created and tested on a Mac using PowerShell  
* Don’t dismiss PowerShell just because it was created by Microsoft. Embrace it to enhance your automation journey!  

---

In my blogs, I often emphasize that there are multiple methods to achieve the same objective. This article presents just one of the many ways you can tackle this task. I've shared what I believe to be an effective approach for this particular use case, but keep in mind that every organization and environment varies. There's no definitive right or wrong way to accomplish the tasks discussed in this article.

---

Always test new setups and processes, like those discussed in this blog, in a lab environment before implementing them in a production environment.

---

If you found this blog helpful, consider buying me a coffee to kickstart my day.  

<center>
<script type="text/javascript" src="https://cdnjs.buymeacoffee.com/1.0.0/button.prod.min.js" data-name="bmc-button" data-slug="dalehassinger" data-color="#FFDD00" data-emoji=""  data-font="Cookie" data-text="Buy me a coffee" data-outline-color="#000000" data-font-color="#000000" data-coffee-color="#ffffff" ></script>
</center>

---