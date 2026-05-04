---
title: "vCommunity MP | Windows Servers Services"
date: 2025-11-08T05:00:00Z
author: "Dale Hassinger"
tags:
  - VCF
  - Operations
  - VMware vExpert
  - Dashboard
  - vCommunity-MP
categories:
  - VCF Operations
image: "images/vcommunity-mp-windows-servers-services-featured.png"
draft: false
---

**Use the vCommunity MP to monitor Windows Server Services agent-less.**

>A community-built VMware Cloud Foundation Operations Management Pack using Python and the VCF Operations Integration SDK to extend monitoring, reporting, and dashboard capabilities.

---

## vCommunity VCF Operations MP:  

[vCommunity MP | GitHub repository | Link](https://github.com/vmbro/VCF-Operations-vCommunity):

Head over to the vCommunity MP GitHub repository and review the System Requirements carefully—you’ll need a Cloud Proxy plus a few other prerequisites in place.  

Check out Brock Peterson's vCommunity MP blogs, which cover many of the details. [Click Here](https://www.brockpeterson.com/search?q=vcommunity+management+pack)  

In this blog, I’ll be focusing on Microsoft Windows Server Services.
-	I’ll demonstrate how you can monitor any Windows Service.
-	The vCommunity MP uses an agent-less approach to retrieve Windows Service status.
-	For example, I’ll cover monitoring Active Directory, RDS, Defender AV, Veeam, and Print Spooler services.


---

## Dashboard Example:  

- I like using the Scoreboard widget to display the service status.  
  <span style="color:green; font-weight:bold;">Green</span> is good,  
  <span style="color:red; font-weight:bold;">Red</span> is bad.

![](images/windows-services-02.png)  

- I don’t want the Print Spooler service running, which is why its status is shown in <span style="color:red; font-weight:bold;">Red</span> when it’s running.  

---

## Management Packs Configuration:  

- Add all the service names you want to monitor in the windows_service_list.xml section
- My example **Dashboard** and **View** rely on the following services: `spooler`, `sshd`, `TermService`, `NTDS`, and `WinDefend`.  
  Adjust the Dashboard and View to match the specific services you want to monitor.

![](images/windows-services-01.png)  

```xml
<windowsServices>
  <!--Remove comments if you want to monitor Windows Services.-->
  <!--You can add any service name as follows-->
  <!-- Dhcp -->
  <!-- WinDefend -->
  Dnscache,
  MpsSvc,
  spooler,
  sshd,
  TermService,
  DNS,
  NTDS,
  WinDefend,
  VeeamBackupSvc
</windowsServices>

```  

---

## View Configuration:  

- You can create a view to filter specific Windows Server Services.
- These views can be used in dashboards, reports, or simply viewed on their own.

Steps to define the View:  

![](images/windows-services-03.png)  

How to Filter for a Specific Service:  

![](images/windows-services-04.png)  

The final view displays only the VMs that are running Active Directory.:  

![](images/windows-services-05.png)  

---
## Lessons Learned:

- Make sure to have a Cloud Proxy in place before installing this MP.  
- You can monitor any Windows Service. All you need to do is add the service name to the windows_service_list.xml using the Management Packs Configuration. 
- No agents are required to gather the metrics and properties. I repeat, this is done Agent-less.  
- To download the Dashboard and View listed above | [Click Here](https://github.com/dalehassinger/unlocking-the-potential/tree/main/VMware-Aria-Operations/Management-Packs/vCommunity)  
- The Group of people that created the vCommunity MP have done an amazing job!  

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