---
title: "VCF Operations Dashboard | ESXi Tiered Memory"
date: 2024-10-05T04:00:00Z
author: "Dale Hassinger"
tags:
  - ESXi
  - Tiered Memory
  - VCF Operations Management Pack Builder
  - VCF Operations
  - Dashboard
  - FastAPI
  - PowerShell
categories:
  - VCF Operations
image: "images/vcf-operations-dashboard-esxi-tiered-memory-featured.png"
draft: false
---

**How to show ESXi Tiered Memory Metrics in VCF Operations.**



---

>Using VCF Operations Management Pack Builder alongside a product like FastAPI will elevate your observability to a whole new level...

---

Here’s a use case that demonstrates the powerful capabilities of the **VCF Operations Manager Pack Builder** in your environment. With the introduction of **Tiered Memory** in ESXi 8.0.3, VMware hasn’t yet integrated tiered memory metrics visibility for VMs using tiered memory into **VCF Operations**. However, **William Lam** has provided a **Blog** outlining the necessary commands to retrieve the tiered memory metrics. [Click here to see Blog](https://williamlam.com/2024/09/how-much-virtual-machine-memory-is-using-nvme-tiering.html)  

To build an Operations Management Pack (MP) that captures this data, you need to utilize APIs. In my approach, I used **FastAPI** to execute a **PowerShell Script** that connects to vCenter to gather a list of ESXi hosts. The script then establishes an SSH connection to each host, retrieves the VM tiered memory metrics, and sends the results back to FastAPI. These metrics are then made available for Management Pack Builder, allowing you to incorporate the new metrics into the existing VM object within Operations.  

Once the VM tiered memory metrics are available in Operations, you can create some impressive dashboards to display this information. These dashboards provide a clear, visual representation of how tiered memory is being utilized across your environment, giving you deeper insights and making it easier to monitor and optimize performance.  

Using a tool like FastAPI to provide additional metrics to Operations opens up a world of possibilities. Once you understand the steps involved, your thought process becomes your most powerful observability tool. This approach empowers you to extend the capabilities of Operations, allowing you to gather and visualize critical metrics that might not be available out of the box.  

To get started, you’ll need to set up a server with FastAPI installed. In my lab, I created a Rocky Linux 9.4 VM and installed the latest version of FastAPI. Additionally, I installed the latest version of PowerShell along with the VMware PowerCLI and Posh-SSH modules, enabling the necessary functionality for this setup.  

To create and edit code on my Rocky Linux server, I prefer using the Remote SSH add-in for VSCode, which I have installed on my Apple MacBook. This approach allows me to avoid using the graphical version of Rocky Linux Server, keeping the setup lean and mean. I use PowerShell on Linux servers all the time—I’ve loved it ever since PowerShell became cross-platform.  

While working on this blog and sharing links to the discussed resources, I came across another  post by William Lam that also explains how to retrieve Host Tiered Memory settings. I adapted William's script to work with MPB and added the new properties to my FastAPI Management Pack. [Click here to see Blog](https://williamlam.com/2024/10/useful-nvme-tiering-reporting-using-vsphere-8-0-update-3-apis.html)  

I encourage you to install the VCF Operations Management Pack Builder appliance and download the MP export file linked at the end of this blog. My goal is for you to understand how Management Packs (MPs) work. While simply using a pre-built MP is easy, taking the time to learn how this MP works will inspire you with new ideas for integrating properties and metrics from other products into your enterprise environment.  

---

## FastAPI  
The steps I used to install FastAPI on Rocky Linux Server 9.4:  

* A very simple process to install FastAPI and get started.  

  
```Bash

# Update the System:
sudo dnf update -y

# Install Python:
sudo dnf install python3 -y

# Install Pip (Python Package Manager):
sudo dnf install python3-pip -y

# Install FastAPI and Uvicorn:
pip3 install fastapi uvicorn

# Create a Sample FastAPI Application:
nano app.py

# Add the following content to app.py:
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"Hello": "World"}


# You can run your app using uvicorn:
uvicorn app:app --host 0.0.0.0 --port 8000

# Your FastAPI app should now be accessible at:
http://<your_server_ip>:8000

```  


## FastAPI application python code:  

**URLs used in my Lab:**
* For VM Tiered Memory you would use http://192.168.6.83:8000/tieredVM  
* You can have many urls. See the sample FastAPI Python code.  
  * To return simple json information use http://192.168.6.83:8000/  
  * To run a PowerShell script to get Host Tiered Memory settings http://192.168.6.83:8000/tieredHost  
* I converted the data to json in the PowerShell Script. I wanted FastAPI to just return plaintext. See the Python code on how to do this.  

  
```Python

import subprocess
from fastapi import FastAPI
from fastapi.responses import PlainTextResponse

app = FastAPI()

@app.get("/")
def read_root():
    return {"Hello": "Welcome to the FastAPI Server by vCROCS!"}

@app.get("/tieredVM")
async def run_powershell_script():
    try:
        # Run the PowerShell script and capture the output
        result = subprocess.run(["pwsh", "-File", "/root/tieredVM.ps1"], capture_output=True, text=True)
        
        # Check if there's an error in the execution
        if result.returncode != 0:
            return PlainTextResponse(content=result.stderr, status_code=500)

        # Return the raw output from the PowerShell script without modification
        return PlainTextResponse(content=result.stdout.strip())
    
    except Exception as e:
        return PlainTextResponse(content=str(e), status_code=500)

 
@app.get("/tieredHost")
async def run_powershell_script():
    try:
        # Run the PowerShell script and capture the output
        result = subprocess.run(["pwsh", "-File", "/root/tieredHost.ps1"], capture_output=True, text=True)
        
        # Check if there's an error in the execution
        if result.returncode != 0:
            return PlainTextResponse(content=result.stderr, status_code=500)

        # Return the raw output from the PowerShell script without modification
        return PlainTextResponse(content=result.stdout.strip())
    
    except Exception as e:
        return PlainTextResponse(content=str(e), status_code=500)

```  

---

## PowerShell Script  

PowerShell script to collect the VM Tiered Memory metrics from the ESXi Hosts:  

  
```PowerShell

# The Posh-SSH and VMware PowerCLI modules are required to run this script
# Import the Posh-SSH and VMware PowerCLI modules
#Import-Module Posh-SSH
#Import-Module VMware.PowerCLI

# Define parameters for the Connect-VIServer cmdlet using splatting. Use a more secure method for passwords.
$ConnectionParams = @{
    Server   = "192.168.6.100"
    User     = "administrator@vcrocs.local"
    Password = "VMware1!"
    Force    = $true
}

# Connect to the vCenter Server
$vCenter = Connect-VIServer @ConnectionParams

# Get all ESXi hosts in the vCenter
$esxiHosts = Get-VMHost

# Create an empty array to store combined results
$combinedResults = @()

# Loop through each host
foreach ($esxiHost in $esxiHosts) {
    # Define ESXi SSH connection details
    $server = $esxiHost.Name
    $username = "root"
    $password = "VMware1!"  # Use a more secure method for passwords

    # Convert the password to a SecureString and create a PSCredential object
    $securePassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($username, $securePassword)

    # Try to establish an SSH session, skip host if connection fails
    Try {
        #Write-Host "Attempting to connect to $server..."
        $sshSession = New-SSHSession -ComputerName $server -Credential $credential -AcceptKey -ErrorAction Stop
        #Write-Host "Connected to $server."
    }
    Catch {
        #Write-Host "Failed to connect to $server. Skipping host..." -ForegroundColor Yellow
        Continue
    }

    # If SSH session is successful, execute the commands
    if ($sshSession) {
        # Define and execute the first SSH command to get the VMX Cartel IDs and Display Names
        $vmCommand = "esxcli --formatter csv vm process list | cut -d ',' -f 2,5"
        $vmResult = Invoke-SSHCommand -SessionId $sshSession.SessionId -Command $vmCommand
        $VMXCartelID = $vmResult.Output | ConvertFrom-Csv

        # Define and execute the second SSH command to get memory statistics
        $memCommand = 'memstats -r vmtier-stats -u mb -s name:memSize:active:tier0Consumed:tier1Consumed'
        $memResult = Invoke-SSHCommand -SessionId $sshSession.SessionId -Command $memCommand

        # Process the memory statistics output
        $lines = $memResult.Output -split "`n" | ForEach-Object { $_.Trim() } | Where-Object {
            $_ -notmatch '^-{2,}|Total|Start|No.|VIRTUAL|Unit|Selected'
        }

        # Regex pattern for memory statistics
        $pattern = '^(?<name>\S+)\s+(?<memSize>\d+)\s+(?<active>\d+)\s+(?<tier0Consumed>\d+)\s+(?<tier1Consumed>\d+)$'

        # Array to store parsed memory stats
        $tieredMEM = @()

        # Parse the memory stats
        foreach ($line in $lines) {
            if ($line -match $pattern) {
                $tieredMEM += [pscustomobject]@{
                    Name               = $matches['name']
                    vm_MemSize_MB      = [int]$matches['memSize']
                    vm_Active_Mem_MB   = [int]$matches['active']
                    "vm_Tier0_RAM_MB"  = [int]$matches['tier0Consumed']
                    "vm_Tier1_NVMe_MB" = [int]$matches['tier1Consumed']
                }
            }
        }

        # Remove 'vm.' prefix from each VM Name
        $tieredMEM | ForEach-Object { $_.Name = $_.Name -replace '^vm\.', '' }

        # Create a hashtable for easy mapping of VMX Cartel IDs to Display Names
        $vmNameMap = @{}
        foreach ($entry in $VMXCartelID) {
            $vmNameMap[$entry.VMXCartelID] = $entry.DisplayName
        }

        # Replace the Name field in $tieredMEM with the corresponding DisplayName
        foreach ($vm in $tieredMEM) {
            if ($vmNameMap.ContainsKey($vm.Name)) {
                $vm.Name = $vmNameMap[$vm.Name]
            }
        }

        # Filter out rows where the Name starts with "vCLS-"
        $tieredMEM = $tieredMEM | Where-Object { $_.Name -notlike "vCLS-*" }

        # Append the results from this host to the combined results array
        $combinedResults += $tieredMEM

        # Close the SSH session for this host
        $removeSession = Remove-SSHSession -SessionId $sshSession.SessionId
    }
}

# Convert the combined result to json
$returnData = $combinedResults | ConvertTo-Json -Compress

return $returnData

# Disconnect from vCenter
Disconnect-VIServer -Confirm:$false

```  

---

PowerShell script to collect the Host Tiered Memory Properties:  

  
```PowerShell

# Most of this Code from William Lam Blog Post
# https://williamlam.com/2024/10/useful-nvme-tiering-reporting-using-vsphere-8-0-update-3-apis.html
# I changed the code to test in my Lab. Select all hosts 8.0.3 or greater. Convert results to json to work with MPB.

# Define parameters for the Connect-VIServer cmdlet using splatting
$ConnectionParams = @{
    Server   = "192.168.6.100"
    User     = "administrator@vcrocs.local"
    Password = "VMware1!"
    Force    = $true
}

# Connect to the vCenter Server
$vCenter = Connect-VIServer @ConnectionParams

$vmHosts = Get-VMhost | Where-Object {$_.Version -ge "8.0.3"} | Sort-Object Name

$results = @()
foreach ($vmhost in $vmHosts) {
    $hostID = $vmhost.Id
    $hostID = $hostID.Replace("HostSystem-", "")
    
    $tieringType = $vmhost.ExtensionData.Hardware.memoryTieringType

    $totalMemory = [math]::round($vmhost.ExtensionData.Hardware.MemorySize /1GB,2).ToString() + " GB"
    $tieringRatio = ($vmhost | Get-AdvancedSetting Mem.TierNvmePct).Value.toString() + "%"
    
    $tieringEnabled = $false
    if($tieringType -eq "softwareTiering") {
        $tieringEnabled = $true

        $dramTotal = [math]::round(($vmhost.ExtensionData.Hardware.MemoryTierInfo | Where-Object {$_.Name -eq "DRAM"}).Size /1GB,2).ToString() + " GB"
        $nvmeTotal = [math]::round(($vmhost.ExtensionData.Hardware.MemoryTierInfo | Where-Object {$_.Name -eq "NVMe"}).Size /1GB,2).ToString() + " GB"

        $storageSystem = Get-View $vmhost.ExtensionData.ConfigManager.StorageSystem
        $nvmeDevice = ($storageSystem.StorageDeviceInfo.ScsiLun | where {$_.UsedByMemoryTiering -eq $true}).CanonicalName

    } else {
        $dramTotal = $totalMemory
        $nvmeTotal = 0
        $nvmeDevice = "N/A"
    }

    $tmp = [pscustomobject] @{
        Id = $hostID
        VMHost = $vmhost.Name
        TieringEnabled = $tieringEnabled
        TieringRatio = $tieringRatio
        DRAMMemory = $dramTotal
        NVMeMemory = $nvmeTotal
        TotalSystemMemory = $totalMemory
        NVMeDevice = $nvmeDevice
    }
    $results+=$tmp
}

$results | ConvertTo-Json -Compress

# Disconnect from vCenter
Disconnect-VIServer -Confirm:$false

```  

---

## VCF Operations:

* These metrics will allow you to create some great operational insights, including cool dashboards.  

---

![](images/tiered-01.png)  

---

## MP Builder Details:

* The following screen shots are all the details I used to create the MP
* I presented at VMware Explore 2024, and one of the sessions includes a video on MP Builder. I recommend checking it out to get more familiar with MPB. [Link to session | CODE1161LV](https://www.youtube.com/live/EcQNwO_skAA?si=xkLv3PvocHy5ka0f)  
* Brock Peterson has a great Blog on building a MP. [Link to Blog](https://www.brockpeterson.com/post/building-a-management-pack-with-the-aria-operations-management-pack-builder
)  
* After building and saving the MP, you need to import into VCF Operations Repository and add account. I show those details in my VMware Explore session video.   

---

![](images/tiered-02.png)  

---

![](images/tiered-03.png)  

---

![](images/tiered-04.png)  

---

![](images/tiered-05.png)  

---

![](images/tiered-06.png)  

---

![](images/tiered-07.png)  

---

![](images/tiered-08.png)  

---

![](images/tiered-09.png)  

---

![](images/tiered-10.png)  

---

![](images/tiered-11.png)  

---

![](images/tiered-12.png)  

---

![](images/tiered-13.png)  

---

![](images/tiered-14.png)  

---

![](images/tiered-15.png)  

---

![](images/tiered-16.png)  

---

![](images/tiered-17.png)  

---

![](images/tiered-18.png)  

---

![](images/tiered-19.png)  

---

![](images/tiered-20.png)  

---

![](images/tiered-21.png)  

---

## Lessons learned:

* Incorporating a FastAPI server adds an additional layer to your observability environment, but the capabilities it unlocks are remarkable. It enables you to gather and serve custom metrics, offering greater flexibility and control over your monitoring and performance analysis. This approach not only enhances visibility but also provides powerful tools to extend the insights available in your environment.
* Just because products don’t offer APIs out of the box for the metrics you need doesn’t mean you’re limited. You can always leverage custom scripts to gather the data you want. By combining tools like PowerShell, SSH, and FastAPI, you can extract and integrate those custom metrics into your observability platform, giving you the flexibility to get exactly the insights you need.



---

## Links to resources discussed is this Blog Post: 

* [Download Link for the MP Export File, Dashboard and Views](https://github.com/dalehassinger/unlocking-the-potential/tree/main/VMware-Aria-Operations/Management-Packs/FastAPI)  
* [FastAPI](https://fastapi.tiangolo.com/)  
* [Rocky Linux | Downloads](https://rockylinux.org/download)  
* [VCF Operations | Management Pack Builder | Download](https://marketplace.cloud.vmware.com/services/details/draft-vmware-aria-operation-management-pack-builder-1-1?slug=true)  
* [Brock Peterson Blogs | Great place to get Dashboard Ideas and see MPB examples](https://www.brockpeterson.com)  
---

In my blogs, I often emphasize that there are multiple methods to achieve the same objective. This article presents just one of the many ways you can tackle this task. I've shared what I believe to be an effective approach for this particular use case, but keep in mind that every organization and environment varies. There's no definitive right or wrong way to accomplish the tasks discussed in this article.

---

Always test new setups and processes, like those discussed in this blog, in a lab environment before implementing them in a production environment.

---

If you found this blog article helpful and it assisted you, consider buying me a coffee to kickstart my day.

<center>
<script type="text/javascript" src="https://cdnjs.buymeacoffee.com/1.0.0/button.prod.min.js" data-name="bmc-button" data-slug="dalehassinger" data-color="#FFDD00" data-emoji=""  data-font="Cookie" data-text="Buy me a coffee" data-outline-color="#000000" data-font-color="#000000" data-coffee-color="#ffffff" ></script>
</center>

---