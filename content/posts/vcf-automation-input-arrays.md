---
title: "VCF Automation | Input Arrays"
date: 2025-01-31T05:00:00Z
author: "Dale Hassinger"
tags:
  - VCF
  - Automation
  - PowerShell
  - PowerCLI
  - VMware Code Coach
  - VMware vExpert
categories:
  - VCF Automation
image: "images/vcf-automation-input-arrays-featured.png"
draft: false
---

**How to use Input Arrays with ABX Actions.**



---

>Don’t automate for the sake of automation—automate to simplify, accelerate, and improve.

---

<small>**Note: VCF Automation 8.18.1 was used to create this Blog**</small>  

I recently received a question about leveraging an Array Input from a VCF Automation Template and processing the array data within an ABX Action. Below, you'll find example code and screenshots demonstrating the approach. If you're working with array inputs in your automation workflows, this guide should be helpful!  

---

**Screenshots demonstrating the use of VCF Automation Input Arrays:**

* <small>**Example manual Deployment that has an Array Input**</small>


![](images/array-01.png)  

---

* <small>**Example Custom Form using an Array Input**</small>

![](images/array-10.png)  

---

**Screen Shot of the YAML Code for Input Array:**


* <small>**Define the input type as array**</small>
* <small>**Use the array input in the "Cloud.vSphere.Machine" - "properties:" section**</small>
* <small>**All properties values will get passed in the ABX Action**</small>


![](images/array-02.png)  

---

**Screen Shot of the Inputs:**

* <small>**If you haven't worked with YAML array inputs before, use the Inputs screens in VCF Automation to assist in formatting the YAML correctly. This will ensure the data is structured properly before being passed to the ABX Action.**</small>

![](images/array-03.png)  

---

**Screen Shot of the Array Creation with Inputs:**

* <small>**Example screen of creating an Array**</small>

![](images/array-04.png)  

---

**Screen Shot of the Subscription to execute an ABX Action during an VM Build:**

* <small>**In this example I am running an ABX Action during Pre Allocation of a new VM Build**</small>

![](images/array-05.png)  

---

**Screen Shot of the Array Creation with Inputs:**

* <small>**Here's an example of PowerShell code that takes a YAML array string passed to an ABX Action, cleans it up, and converts it into a PowerShell array**</small>
* <small>**This is the most critical step in this blog post. Take the time to understand how the YAML string is transformed into a PowerShell array, as this enables you to leverage the array values for any automation tasks needed in your server build process.**</small>
* <small>**The PowerShell code processes the YAML string by replacing unnecessary characters, ensuring proper formatting, and then splitting the string on commas to create a PowerShell array. This transformation allows the data to be easily iterated and used within the ABX Action.**</small>


![](images/array-06.png)  

---

**Screen Shot of the YAML Array Values:**

* <small>**This example demonstrates how the YAML array format is passed to the ABX Action as a string. Understanding this format is essential for parsing and utilizing the array data effectively within your automation logic.**</small>

![](images/array-07.png)  

---

**Screen Shot of the PowerShell Array Code Ouput:**

* <small>**Example screen of PowerShell code output**</small>

![](images/array-08.png)  

---

**Screen Shot of ABX successful run:**

* <small>**Shows that the ABX Action was successfully run.**</small>

![](images/array-09.png)  

---


## VCF Automation Design Template  

**Example Template Yaml Code:**  

* <small>**Code from the screen shots above.**</small>  


```yaml

formatVersion: 2
name: Rocky-Basic
outputs:
  __deploymentOverview:
    value: |
      **The following vSphere Virtual Machine has been provisioned with VMware VCF Automation.**  

      **Original Build Specs Used:**  
      IP: ${input.IP}  
      Memory(MB): ${input.totalMemoryMB}  
      CPU (Count): ${input.cpuCount}  
      Core (Count): ${input.coreCount}  

      **Connect to Server using Cockpit:**  
      http://${input.IP}:9090  

      **Check out these Blogs for Updates/Tips/Tricks on the VMware VCF Operations/Automation Products:**  
      **Brock Peterson:** https://www.BrockPeterson.com  
      **Dale Hassinger:** https://www.vCROCS.info  
      **Cosmin Trif:** https://www.cosmin.gq  

      **Link to vCROCS VCF Operations:**  
      https://vao.vcrocs.local  

      **Link to vCROCS VCF Operations for Logs:**  
      https://vaol.vcrocs.local  
#cloud-config
inputs:
  CustomizationSpec:
    type: string
    description: Customization Specification
    default: LINUX
    title: CustomizationSpec
  VMName:
    type: string
    title: VM Name
    minLength: 1
    maxLength: 15
    default: Rocky-Array-202
  IP:
    type: string
    default: 192.168.5.202
  totalMemoryMB:
    type: integer
    title: Memory(MB)
    default: 1024
  cpuCount:
    type: integer
    title: CPU (count)
    default: 1
  coreCount:
    type: integer
    title: Core (count)
    default: 1
  folderName:
    type: string
    title: vCenter Folder
    default: Rocky-Linux
    enum:
      - Rocky-Linux
      - ESXi-01-VMs
      - ESXi-02-VMs
  VolumeGB:
    type: string
    title: 'Volume Size GB:'
    default: '20'
  vmAdminUsers:
    type: array
    items:
      type: string
    title: 'Admin Users:'
    minItems: 1
    maxItems: 4
    default:
      - dale.hassinger
    description: VM Admin Users
resources:
  Network_VMs:
    type: Cloud.vSphere.Network
    properties:
      name: PG-VMs
      networkType: existing
      constraints:
        - tag: Network:VM
  vCenter_Rocky:
    type: Cloud.vSphere.Machine
    properties:
      image: ROCKY9
      #flavor: SM
      totalMemoryMB: ${input.totalMemoryMB}
      cpuCount: ${input.cpuCount}
      coreCount: ${input.coreCount}
      name: ${input.VMName}
      fqdn: ${input.VMName}.vcrocs.local
      folderName: Rocky-Linux
      vmAdminUsers: ${input.vmAdminUsers}
      storage:
        bootDiskCapacityInGB: ${input.VolumeGB}
      remoteAccess:
        authentication: usernamePassword
        username: root
        password: ${secret.administrator}
      customizationSpec: ${input.CustomizationSpec}
      constraints:
        - tag: VPZ:VM
      networks:
        - network: ${resource.Network_VMs.id}
          assignment: static
          address: ${input.IP}

```  

---

## VCF Automation Design Template  

**Example ABX Action Code:**  

* <small>**Code from the screen shots above.**</small>
* <small>**My example code simply outputs the values to demonstrate how to create the array and validate that the code works. Once you have this structure in place, you can extend the `foreach` section to automate any tasks you need.**</small>

 
```PowerShell

function handler($context, $inputs) {

    if(!$inputs.customProperties.vmAdminUsers){
        $vmAdminUsers = "NA"
    }else{
        $vmAdminUsers = $inputs.customProperties.vmAdminUsers
    }
    
    Write-Host "STR vmAdminUsers:" $vmAdminUsers
    
    # Input string
    #$vmAdminUsers = '[\"dale.hassinger\",\"brock\",\"chris\"]'
    
    
    # Convert the string into an array
    $Array = $vmAdminUsers -replace '[\[\\\"\]]', '' -split ','
    
    # Output the array
    # Iterate through each item in the array and display its value
    foreach ($Item in $Array) {
        Write-Host "Username: $Item"
    }

    Write-Host "Array:" $Array
    Write-Host "Array Count:"$Array.Count

  return $inputs
}

```  

---

## Lessons learned:

* Using Array Inputs are a great way to use multiple values for processes needed to build new VMs.  
* After you take the YAML array string and turn it into a PowerShell array, you can use the values to automate anything you can script.  

---

I created a Google NotebookLM Podcast based on the content of this blog. While it may not be entirely accurate, is any podcast ever 100% perfect, even when real people are speaking? Take a moment to listen and share your thoughts with me!  

[vCROCS Deep Dive Podcast | VCF Automation | Input Arrays](https://youtu.be/8jPQHbtK1s8)  

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