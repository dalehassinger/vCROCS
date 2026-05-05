---
title: "VMware Aria Automation | Options for CPU|Memory values"
date: 2024-06-19T04:00:00Z
author: "Dale Hassinger"
tags:
  - VMware
  - VMware Aria Automation
  - Templates
categories:
  - VCF Automation
image: "images/vmware-aria-automation-options-for-cpu-memory-values-featured.png"
draft: false
---

**How to Allocate CPU and Memory for VMs Using T-Shirt Sizes or Custom Values**



---

>"Always deliver more than expected.” - Larry Page (Co-founder of Google)

---

## VMware Aria Automation | VM | CPU and Memory  

Recently, I was asked how to create new VMs using T-Shirt sizes and also have the flexibility to specify custom CPU and Memory values. Typically, in the YAML code of a Aria Automation Design Template, you use either the properties `cpuCount` and `totalMemoryMB` or the `flavor` property. You **cannot** use both within the same template for the same VM.

After considering the options, I decided to use the `cpuCount` and `totalMemoryMB` properties to specify VM CPU and memory, adding logic to the YAML code to determine values based on an input named Flavor.

**Flavor Options:**
* Small: 1 CPU, 2 GB memory
* Medium: 2 CPUs, 4 GB memory
* Large: 4 CPUs, 8 GB memory
* Other: Use CPU and Memory values specified in custom inputs `CPU` and `Memory`. See the Full Design Template YAML code below.

---

These two lines of YAML code demonstrate how to specify the `cpuCount` and `totalMemoryMB` properties. This is the key to enabling both T-Shirt sizes and custom values:

---

  

```YAML
cpuCount: "${(input.Flavor == 'small') ? 1 : (input.Flavor == 'medium') ? 2 : (input.Flavor == 'large') ? 4 : (input.Flavor == 'other') ? input.CPU : 1}"
totalMemoryMB: "${(input.Flavor == 'small') ? 2048 : (input.Flavor == 'medium') ? 4096 : (input.Flavor == 'large') ? 8192 : (input.Flavor == 'other') ? input.Memory * 1024 : 1024}"
```  

---

**Design Templates within Aria Automation:**

![](images/t-shirt-01.png)  

---

**Design Template and YAML Code:**


![](images/t-shirt-02.png)  

---

**Small Flavor Selected:**


![](images/t-shirt-03.png)  

---

**Other Flavor Selected:**


![](images/t-shirt-04.png)  

---

**Design Template Example YAML Code:**  


This full Design Template YAML code shows the inputs and how to specify the `cpuCount` and `totalMemoryMB` properties.  

  

```YAML
formatVersion: 1
inputs:
  CustomizationSpec:
    type: string
    description: Customization Specification
    default: Customization-Ubuntu-22
    title: CustomizationSpec
  VMName:
    type: string
    title: VM Name
    minLength: 1
    maxLength: 15
    default: RCKY-9-000
  IP:
    type: string
    default: 192.168.69.17
  Flavor:
    type: string
    title: Standard Size
    default: medium
    enum:
      - small
      - medium
      - large
      - other
  Memory:
    type: number
    title: Memory(GB)
    default: 1
    minimum: 1
    maximum: 512
  CPU:
    type: number
    title: CPU Count
    default: 1
    minimum: 1
    maximum: 32
resources:
  Cloud_vSphere_Network_1:
    type: Cloud.vSphere.Network
    properties:
      networkType: existing
      constraints:
        - tag: Network:vCenter-VMs
  Cloud_vSphere_Machine_1:
    type: Cloud.vSphere.Machine
    properties:
      name: ${input.VMName}
      image: vCenter-Rocky-9
      cpuCount: "${(input.Flavor == 'small') ? 1 : (input.Flavor == 'medium') ? 2 : (input.Flavor == 'large') ? 4 : (input.Flavor == 'other') ? input.CPU : 1}"
      totalMemoryMB: "${(input.Flavor == 'small') ? 2048 : (input.Flavor == 'medium') ? 4096 : (input.Flavor == 'large') ? 8192 : (input.Flavor == 'other') ? input.Memory * 1024 : 1024}"
      networks:
        - network: ${resource.Cloud_vSphere_Network_1.id}
          assignment: static
          address: ${input.IP}
```

---

**Flavors:**

In my lab, I already had some Flavor Mappings defined. To allow for larger sizes, I had to create a new Flavor Mapping for the maximum sizes I wanted to support. I couldn't specify CPU or memory sizes higher than the largest existing Flavor Mapping.  

---

**Warning I received while testing:**

![](images/t-shirt-05.png)  

---

**Flavor Mappings within Aria Automation:**

![](images/t-shirt-06.png)  

---

**Flavor Mapping to allow large VM Builds:**

![](images/t-shirt-07.png)  

---

**Summary:**

I hope this helps anyone looking to standardize new VM builds using T-Shirt sizes while also providing the flexibility to specify custom CPU and Memory values.  

---

## Aria Automation Version used for Blog Post:
VMware Aria Automation 8.17.0 was used for this Blog Post. When new versions of VMware Aria Automation are released, the code or process may need to be changed.  

---

In my blogs, I often emphasize that there are multiple methods to achieve the same objective. This article presents just one of the many ways you can tackle this task. I've shared what I believe to be an effective approach for this particular use case, but keep in mind that every organization and environment varies. There's no definitive right or wrong way to accomplish the tasks discussed in this article.

---

* If you found this blog article helpful and it assisted you, consider buying me a coffee to kickstart my day.  

<center>
<script type="text/javascript" src="https://cdnjs.buymeacoffee.com/1.0.0/button.prod.min.js" data-name="bmc-button" data-slug="dalehassinger" data-color="#FFDD00" data-emoji=""  data-font="Cookie" data-text="Buy me a coffee" data-outline-color="#000000" data-font-color="#000000" data-coffee-color="#ffffff" ></script>
</center>

---