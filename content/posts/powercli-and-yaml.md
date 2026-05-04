---
title: "PowerCLI and YAML"
date: 2025-03-08T05:00:00Z
author: "Dale Hassinger"
tags:
  - VCF
  - Automation
  - PowerShell
  - PowerCLI
  - VMware Code Coach
  - VMware vExpert
  - yaml
categories:
  - VCF Automation
image: "images/powercli-and-yaml-featured.png"
draft: false
---

**Streamlining PowerCLI Scripts: Using YAML for Variable Management**



---

>The true power of automation lies in simplicity—storing all your home lab variables in a single YAML file transforms complexity into clarity, making every deployment seamless and every configuration effortless.

---
## Why am I using a YAML file:  

Many automation tools and platforms rely on YAML files for configuration due to their readability and flexibility. In VMware VCF Automation—something I frequently work with—YAML is used extensively for Design Templates. To maintain consistency across my automation scripts, I centralized all my Home Lab variables in a single YAML file.  

To work with YAML in PowerShell, I’m using the "PowerShell-YAML" module, which provides a simple and efficient way to read and write YAML data. In this blog post, I’ll share several sample scripts demonstrating how easy it is to integrate YAML into PowerShell-based automation.  

All required variables for the PowerShell scripts are centralized in a single YAML configuration file for streamlined management.  

---

>For 2025, I’ve been invited to be a **VMware Community {code} Coach**! This year, my blogs will feature even more code examples to help others get started with automation, VMware solutions, and much more...

---

---
## Code Examples:  

**The sample YAML file contains all the variables for my home lab:**  
* You can see that I have defined variables for several products within my home lab  
* The YAML file is organized into major categories, such as NH, vCenter, Host101, and OPS, each representing a different area of configuration for my Lab  
* Within each top-level section, specific settings are defined using key-value pairs. For example, in NH, values like VIServer, VIUsername, and VIPassword store configuration details for a nested host setup  
* Some sections, like NH → Hosts, contain lists (denoted by -), where multiple items (e.g., ESXi hosts) are grouped together with attributes such as Hostname, IP, and Create
* Separate Functional Areas  
  * NH configures the nested ESXi environment  
  * vCenter stores vCenter connection details  
  * Host101 provides credentials for a single ESXi host  
  * OPS defines login details for VCF Operations  



```YAML

# NH is Nested Host
NH:
  VIServer: 192.168.6.100
  VIUsername: administrator@vcrocs.local
  VIPassword: VMware1!
  NestedESXiApplianceOVA: /Users/dalehassinger/Downloads/Nested_ESXi8.0u3c_Appliance_Template_v1.ova
  verboseLogFile: /Users/dalehassinger/Documents/GitHub/PS-TAM-Lab/VCF-Nested-Step-1-Host-Only-YAML.log
  NestedESXiMGMTvCPU: 12
  NestedESXiMGMTvMEM: 88
  NestedESXiMGMTCachingvDisk: 4
  NestedESXiMGMTCapacityvDisk: 500
  NestedESXiMGMTBootDisk: 32
  VMDatastore: ESX-04-2TB-02
  VMCluster: CLUSTER-03-VCF
  VMNetwork: VMs
  VMNetmask: 255.255.252.0
  VMGateway: 192.168.4.1
  VMDNS: 192.168.6.1
  VMNTP: time.google.com
  VMPassword: VMware1!
  VMDomain: vcrocs.local
  VMSyslog: 192.168.6.94

  Hosts:
    - Hostname: VCF-DDC-ESX178
      IP: 192.168.4.178
      Create: True
    - Hostname: VCF-DDC-ESX179
      IP: 192.168.4.179
      Create: False
    - Hostname: VCF-DDC-ESX180
      IP: 192.168.4.180
      Create: True

# vCenter Connection
vCenter:
  server: vcsa8x.vcrocs.local
  username: administrator@vcrocs.local
  password: VMware1!
  ignoreCertErrors: true

# A Single ESXi Host Connection
Host101:
  server: 192.168.6.101
  username: root
  password: VMware1!

# VCF Operations
OPS:
  opsURL: https://vao.vcrocs.local
  opsUsername: admin
  opsPassword: VMware1!
  authSource:  local

```

---

**Sample Script to create Nested ESXi VMs:**  
* The initial lines of code ensure that the necessary PowerShell modules are installed before executing the script  
* The PowerShell code then reads the Home-Lab-Config.yaml file, loads its content as a raw string, and converts it from YAML format into a structured PowerShell object stored in $cfg
* The PowerShell code accesses the properties within the NH section of the $cfg object, which was previously loaded from a YAML configuration file
* The code references the NH section of the YAML configuration, accessing its VIServer value, where NH represents a structured key within the YAML file, organizing configuration settings into hierarchical sections.



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
$cfgFile = "/Users/dalehassinger/Documents/GitHub/PS-TAM-Lab/Home-Lab-Config.yaml"
$cfg = Get-Content -Path $cfgFile -Raw | ConvertFrom-Yaml

# Function to display configuration details
Function Show-ConfigValue {
    param (
        [string]$label,
        [string]$value,
        [switch]$newLine
    )
    Write-Host -NoNewline -ForegroundColor Green "${label}: "
    Write-Host -ForegroundColor White $value
    if ($newLine) { Write-Host "`n" }
}

# Display Nested ESXi Build Details
Write-Host -ForegroundColor Magenta "`nNested ESXi Build Details:`n"

Write-Host -ForegroundColor Yellow "---- vCenter Server used to Build Nested ESXi Host(s) ----"
Show-ConfigValue "vCenter Server Address" $cfg.NH.VIServer -newLine
Show-ConfigValue "VM Network" $cfg.NH.VMNetwork
Show-ConfigValue "VM Storage" $cfg.NH.VMDatastore
Show-ConfigValue "VM Cluster" $cfg.NH.VMCluster -newLine

Write-Host -ForegroundColor Yellow "---- ESXi Configuration for VCF Management Domain ----"
$hostnames = ($cfg.NH.Hosts | Where-Object { $_.Create } | ForEach-Object { $_.Hostname }) -join ", "
Show-ConfigValue "ESXi VM Name(s)" $hostnames
$hostips = ($cfg.NH.Hosts | Where-Object { $_.Create } | ForEach-Object { $_.ip }) -join ", "
Show-ConfigValue "IP Address(s)" $hostips
Show-ConfigValue "vCPU" $cfg.NH.NestedESXiMGMTvCPU
Show-ConfigValue "vMEM" "$($cfg.NH.NestedESXiMGMTvMEM) GB"
Show-ConfigValue "Caching VMDK" "$($cfg.NH.NestedESXiMGMTCachingvDisk) GB"
Show-ConfigValue "Capacity VMDK" "$($cfg.NH.NestedESXiMGMTCapacityvDisk) GB" -newLine

Show-ConfigValue "Netmask" $cfg.NH.VMNetmask
Show-ConfigValue "Gateway" $cfg.NH.VMGateway
Show-ConfigValue "DNS" $cfg.NH.VMDNS
Show-ConfigValue "NTP" $cfg.NH.VMNTP
Show-ConfigValue "Syslog" $cfg.NH.VMSyslog -newLine

# Build Start Time
$StartTime = Get-Date

# Function to log events
Function New-LogEvent {
    param([string]$message)
    $timeStamp = Get-Date -Format "MM-dd-yyyy HH:mm:ss"
    Write-Host "[$timeStamp] $message" -ForegroundColor Green
    "[$timeStamp] $message" | Out-File -Append -LiteralPath $cfg.NH.verboseLogFile
}

# Connect to vCenter
New-LogEvent "Connecting to vCenter: $($cfg.NH.VIServer)..."
$viConnection = Connect-VIServer $cfg.NH.VIServer -User $cfg.NH.VIUsername -Password $cfg.NH.VIPassword -WarningAction SilentlyContinue -Protocol https -Force

# Get datastore, cluster, and random ESXi host
$datastore = Get-Datastore -Server $viConnection -Name $cfg.NH.VMDatastore | Select-Object -First 1
$cluster = Get-Cluster -Server $viConnection -Name $cfg.NH.VMCluster
$vmhost = $cluster | Get-VMHost | Get-Random -Count 1

# Iterate over each Nested ESXi Host in YAML
foreach ($nestedhost in $cfg.NH.Hosts) {
    if (-not $nestedhost.Create) {
        New-LogEvent "Skipping host: $($nestedhost.Hostname) as per configuration."
        continue
    }

    # Check if the VM already exists
    if (Get-VM -Name $nestedhost.Hostname -ErrorAction SilentlyContinue) { 
        New-LogEvent "Nested ESXi Host: $($nestedhost.Hostname) already exists. Skipping deployment."
        continue
    }

    # Configure VM settings
    $VMHostName = "$($nestedhost.Hostname).$($cfg.NH.VMDomain)"

    $ovfconfig = Get-OvfConfiguration $cfg.NH.NestedESXiApplianceOVA
    $networkMapLabel = ($ovfconfig.ToHashTable().keys | Where {$_ -Match "NetworkMapping"}).replace("NetworkMapping.","").replace("-","_").replace(" ","_")
    $ovfconfig.NetworkMapping.$networkMapLabel.value = $cfg.NH.VMNetwork
    $ovfconfig.common.guestinfo.hostname.value = $VMHostName
    $ovfconfig.common.guestinfo.ipaddress.value = $nestedhost.IP
    $ovfconfig.common.guestinfo.netmask.value = $cfg.NH.VMNetmask
    $ovfconfig.common.guestinfo.gateway.value = $cfg.NH.VMGateway
    $ovfconfig.common.guestinfo.dns.value = $cfg.NH.VMDNS
    $ovfconfig.common.guestinfo.domain.value = $cfg.NH.VMDomain
    $ovfconfig.common.guestinfo.ntp.value = $cfg.NH.VMNTP
    $ovfconfig.common.guestinfo.syslog.value = $cfg.NH.VMSyslog
    $ovfconfig.common.guestinfo.password.value = $cfg.NH.VMPassword
    $ovfconfig.common.guestinfo.ssh.value = $true

    New-LogEvent "Deploying Nested ESXi VM: $($nestedhost.Hostname) with IP $($nestedhost.IP) ..."
    $vm = Import-VApp -Source $cfg.NH.NestedESXiApplianceOVA -OvfConfiguration $ovfconfig -Name $nestedhost.Hostname -Location $cfg.NH.VMCluster -VMHost $vmhost -Datastore $datastore -DiskStorageFormat thin -Force

    New-LogEvent "Updating vCPU: $($cfg.NH.NestedESXiMGMTvCPU) & vMEM: $($cfg.NH.NestedESXiMGMTvMEM) GB ..."
    Set-VM -VM $vm -NumCpu $cfg.NH.NestedESXiMGMTvCPU -CoresPerSocket $cfg.NH.NestedESXiMGMTvCPU -MemoryGB $cfg.NH.NestedESXiMGMTvMEM -Confirm:$false | Out-File -Append -LiteralPath $cfg.NH.verboseLogFile

    New-LogEvent "Updating vSAN Disks (Boot: $($cfg.NH.NestedESXiMGMTBootDisk) GB, Cache: $($cfg.NH.NestedESXiMGMTCachingvDisk) GB, Capacity: $($cfg.NH.NestedESXiMGMTCapacityvDisk) GB)..."
    Get-HardDisk -VM $vm -Name "Hard disk 1" | Set-HardDisk -CapacityGB $cfg.NH.NestedESXiMGMTBootDisk -Confirm:$false | Out-File -Append -LiteralPath $cfg.NH.verboseLogFile
    Get-HardDisk -VM $vm -Name "Hard disk 2" | Set-HardDisk -CapacityGB $cfg.NH.NestedESXiMGMTCachingvDisk -Confirm:$false | Out-File -Append -LiteralPath $cfg.NH.verboseLogFile
    Get-HardDisk -VM $vm -Name "Hard disk 3" | Set-HardDisk -CapacityGB $cfg.NH.NestedESXiMGMTCapacityvDisk -Confirm:$false | Out-File -Append -LiteralPath $cfg.NH.verboseLogFile

    New-LogEvent "Powering On $($nestedhost.Hostname) ..."
    $vm | Start-Vm -RunAsync | Out-Null
}

# Disconnect from vCenter
New-LogEvent "Disconnecting from vCenter..."
Disconnect-VIServer -Server * -Confirm:$false

$EndTime = Get-Date
$duration = [math]::Round((New-TimeSpan -Start $StartTime -End $EndTime).TotalMinutes,2)

New-LogEvent "VCF Lab Nested ESXi Hosts Build Complete!"
New-LogEvent "StartTime: $StartTime"
New-LogEvent "EndTime: $EndTime"
New-LogEvent "Duration: $duration minutes to deploy Nested ESXi Hosts"

```

---

![](images/powercli-yaml-01.gif)  

---

**Sample Script to connect to vCenter using variables from a YAML file:**  


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
$cfgFile = "/Users/dalehassinger/Documents/GitHub/PS-TAM-Lab/Home-Lab-Config.yaml"
$cfg = Get-Content -Path $cfgFile -Raw | ConvertFrom-Yaml

# Connect to vCenter
$credential = New-Object PSCredential ($cfg.vCenter.username, (ConvertTo-SecureString $cfg.vCenter.password -AsPlainText -Force))
$vCenter = Connect-VIServer -Server $cfg.vCenter.server -Credential $credential -Force

Get-VM | Select-Object Name | Sort-Object Name

Disconnect-VIServer -Server * -Confirm:$false

```

---

**Sample Script to Connect to an ESXi Host Using Variables from a YAML File:**  


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
$cfgFile = "/Users/dalehassinger/Documents/GitHub/PS-TAM-Lab/Home-Lab-Config.yaml"
$cfg = Get-Content -Path $cfgFile -Raw | ConvertFrom-Yaml

# Connect to a ESXi Host
$credential = New-Object PSCredential ($cfg.Host101.username, (ConvertTo-SecureString $cfg.Host101.password -AsPlainText -Force))
$hostConnection = Connect-VIServer -Server $cfg.Host101.server -Credential $credential -Force

Get-VM | Select-Object Name | Sort-Object Name

Disconnect-VIServer -Server * -Confirm:$false

```

---

**Sample Script to Connect to a VCF Operations Appliance Using Variables from a YAML File:**  


```PowerShell

# Ensure required modules are installed before proceeding
$requiredModules = @("powershell-yaml", "VMware.PowerCLI")
foreach ($module in $requiredModules) {
    if (-not (Get-Module -ListAvailable -Name $module)) {
        Write-Host "$module is not installed. Install it using 'Install-Module $module'." -ForegroundColor Red
        exit
    }
}

# Define YAML configuration file path
$cfgFile = "/Users/dalehassinger/Documents/GitHub/PS-TAM-Lab/Home-Lab-Config.yaml"

# Read YAML configuration into PowerShell object
$cfg = Get-Content -Path $cfgFile -Raw | ConvertFrom-Yaml

# Extract Aria Operations (OPS) credentials from the YAML file
$opsURL      = $cfg.OPS.opsURL
$opsUsername = $cfg.OPS.opsUsername
$authSource  = $cfg.OPS.authSource
$opsPassword = $cfg.OPS.opsPassword

# ----- Obtain Aria Operations authentication token -----
$authUri = "$opsURL/suite-api/api/auth/token/acquire?_no_links=true"

# Construct the request body as a hashtable
$bodyHashtable = @{
    username   = $opsUsername
    authSource = $authSource
    password   = $opsPassword
}

# Convert the hashtable to JSON format
$body = $bodyHashtable | ConvertTo-Json

# Execute REST API call to retrieve the authentication token
$tokenResponse = Invoke-RestMethod -Uri $authUri -Method Post -Headers @{
    "accept"       = "application/json"
    "Content-Type" = "application/json"
} -Body $body -SkipCertificateCheck

# Extract the token from the API response
$authorization = "OpsToken " + $tokenResponse.token

# ----- Retrieve the VM Operations identifier -----
$resourceUri = "$opsURL/suite-api/api/resources?resourceKind=HostSystem&page=0&pageSize=1000&_no_links=true"

# Execute REST API call to fetch ESXi host system resources
$resourceResponse = Invoke-RestMethod -Uri $resourceUri -Method Get -Headers @{
    "accept"        = "application/json"
    "Authorization" = $authorization
} -SkipCertificateCheck

# Extract resource list and convert to JSON for better readability
$identifierList = $resourceResponse.resourceList | ConvertTo-Json -Depth 10

# Convert the JSON string back to a PowerShell object
$data = $identifierList | ConvertFrom-Json

# Display the names of all retrieved ESXi hosts
$data.resourceKey.name

```

---

## Lessons Learned:

* I enjoy bringing consistency to my scripts by leveraging a YAML file, similar to the approach used in automation platforms I work with.  
* All the scripts in this blog were created and tested on a Mac using PowerShell.  
* Don’t dismiss PowerShell just because it was created by Microsoft. Embrace it to enhance your automation journey!  
* Integrating PowerShell with YAML has been something I’ve wanted to explore for a while. I’m glad I took the time to develop the necessary code and share it with the vCommunity.  

---

I created a Google NotebookLM Podcast based on the content of this blog. While it may not be entirely accurate, is any podcast ever 100% perfect, even when real people are speaking? Take a moment to listen and share your thoughts with me!  

[vCROCS Deep Dive Podcast | PowerCLI and YAML](https://youtu.be/0qF9ONBxcwA)  

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