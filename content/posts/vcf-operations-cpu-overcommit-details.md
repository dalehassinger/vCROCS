---
title: "VCF Operations | CPU|Overcommit Details"
date: 2025-06-04T04:00:00Z
author: "Dale Hassinger"
tags:
  - VCF
  - Operations
  - VMware vExpert
  - Dashboard
  - Super Metric
categories:
  - VCF Operations
image: "images/vcf-operations-cpu-overcommit-details-featured.png"
draft: false
---

**See your CPU|Overcommit at the Datacenter, Cluster and Host level.**



---

>My CPU usage is like my Energy Drink intake — dangerously high and probably not sustainable.

---

## CPU|Overcommit:  

Many of my blog posts stem from real-world questions I encounter. Recently, someone asked about the CPU|Overcommit ratio for a specific ESXi host. When I checked VCF Operations, I noticed that CPU|Overcommit metrics were available at the Datacenter and Cluster levels—but not at the Host level.  

To address this gap, I built a Super Metric to calculate the CPU|Overcommit ratio at the host level. You can find the Super Metric formula below.  

To make it easier to visualize CPU|Overcommit ratios across all levels—Datacenter, Cluster, and Host—I also created a custom dashboard. An example of this dashboard is shown below.  

CPU|Overcommit isn’t inherently a bad thing—in fact, it’s one of the main reasons we virtualize: to efficiently share physical resources across VMs. The key is finding a CPU|Overcommit ratio that works for your specific environment and workloads.  

That’s why I included the VM Details widget in the dashboard—to help you monitor important metrics like CPU|Ready (ms). Different workloads and use cases will tolerate different levels of overcommit, so keep an eye on performance indicators and adjust accordingly.  

By default, the CPU|Ready (ms) metric is not enabled in VMware Aria Operations. To activate it, navigate to Operations > Configuration > Policy Definition. Select your Default Policy, then click Edit Policy. Under Metrics and Properties, use the filter to search for "cpu|ready". Select Virtual Machine, expand Metrics, then CPU. You’ll find Ready (ms) listed as Deactivated—switch it to Activated and save your changes.  

Allow for a few collection cycles (typically 10–15 minutes), and the CPU Ready (ms) metric will become available for monitoring VM performance.  

---

## Dashboard Example:  


![](images/cpu-overcommit-01.png)  

---

## Super Metric:  



```SuperMetric

{‌ This Resource: ‌cpu|vcpus_allocated_on_all_powered_on_vms}/{‌This Resource: ‌cpu|corecount_provisioned }

```  

---

![](images/cpu-overcommit-02.png)  


---

## Lessons Learned:

* Host CPU|Overcommit isn't a metric available OOTB (Out of the Box).  
* You can download all the components used to build this dashboard from my [GitHub repository](https://github.com/dalehassinger/unlocking-the-potential/tree/main/VMware-Aria-Operations/Dashboards/CPU-Overcommit):
  - 7 Custom Views  
  - 1 Super Metric  
  - 1 Dashboard  
* Be sure to activate the CPU|Ready (ms) metric, as it’s required for displaying data in the VM Details widget.  

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