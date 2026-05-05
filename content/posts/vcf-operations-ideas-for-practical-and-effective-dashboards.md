---
title: "VCF Operations | Ideas for Practical and Effective Dashboards"
date: 2024-10-29T04:00:00Z
author: "Dale Hassinger"
tags:
  - VCF
  - Dashboards
  - Operations
  - Samples
  - Ideas
categories:
  - VCF Operations
image: "images/vcf-operations-ideas-for-practical-and-effective-dashboards-featured.png"
draft: false
---

**Sample Dashboard Designs to review first thing in the morning while drinking your Coffee or Energy Drink.**



---

>If you have an apple and I have an apple and we exchange these apples, then you and I will still each have one apple. But if you have an idea and I have an idea and we exchange these ideas, then each of us will have two ideas. — George Bernard Shaw

---

Out of the box (OOTB), VCF Operations provides many great dashboards. Some questions I frequently get from customers are:  

* "What else can we monitor with VCF Operations?"  
* "How do I show settings that are most important to my environment?"  
* "How do other customers use VCF Operations?"

I always recommend that customers create a list of their (5) favorite dashboards and pin them to the VCF Operations homepage. That way, each morning, as you enjoy your coffee or energy drink, you can get an at-a-glance view of your infrastructure environment.  

When designing dashboards, always consider using colors to enhance their functionality—green indicates all is well, while red signals issues that may need attention.  

In this blog post, I’ll share some screenshots of dashboards that my team or myself have created and used or helped customers implement in their environments. As you review these examples, think about combining sections from different dashboards to create one that covers everything important to you. This way, you’ll have a go-to dashboard you’ll want to check every morning with your favorite wake me up drink in hand.  

The main goal of this blog post is to provide visual inspiration for what's achievable with Operations Dashboards.  

---

## Dashboard Ideas:  

* Environment  
* Configuration  
* Status  
* Counts  
* Mobile Friendly    

---

## Dashboard Screen Shots:  

<small>**Dashboard: VM Rightsizing**  
* Always one of my favorite use cases  
* Shows recommended size for CPU and Memory  
* Include the metrics to show why CPU Count should be increased or decreased
* Include the metrics to show why Memory Amount should be increased or decreased</small>  

![](images/dashboard-01.png)  

---

<small>**Dashboard: Environment Overview**  
* Show Total Inventory Counts  
* Include Alerts that should be reviewed  
* VMware Tools Versions  
* VM OS  
* Hosts Health, Connection State, Power State, Version, Maintenance Mode Status, etc...  
* Show Host Health in descending order to get you attention</small>  

![](images/dashboard-02.png)  

---

<small>**Dashboard: Environment Overview**  
* Show Weather at your data center physical locations  
* Include Health, Maintenance Mode, Workload, Snaps, Etc...  
* Object Relationship shows what other objects could be affect VM performance  
</small>  

![](images/dashboard-03.png)  

---

<small>**Dashboard: Environment Overview**  
* Designed to work on a mobile phone  
* Use Color to get your attention  
* Green is Good  
* Red is Bad</small>  

![](images/dashboard-04.png)  

---

<small>**Dashboard: Environment Overview**  
* Same Dashboard as above but on a mobile device.</small>  

![](images/dashboard-05.png)  

---

<small>**Dashboard: Security Advisories**  
* This Dashboard was created in conjunction with a custom Management Pack Builder MP
</small>  

![](images/dashboard-06.png)  

---

<small>**Dashboard: CPU Core Counts**  
* This Dashboard makes it easy to show current CPU Core counts to help with licensing
</small>  

![](images/dashboard-07.png)  

---

<small>**Dashboard: vCenters**  
* This Dashboard was designed to monitor vCenter appliance VMs only. VMs that are running vCenter.
* Shows vCenter VM Volumes and Performance
* Includes vCenter Host details at bottom of Dashboard
</small>  

![](images/dashboard-08.png)  

---

<small>**Dashboard: Virtual Machines**  
* Similar to some of the other Dashboards but this one includes some network metrics
* Example of what I said at beginning of this Blog, add or remove metrics to make the Dashboard work for you
</small>  

![](images/dashboard-09.png)  

---

<small>**Dashboard: Edge Sites**  
* Show only items that are located in an edge site
* Use vCenter Tags to specify site location. vCenter Tags make it easy to group together objects.
* Look at the bottom widget in this Dashboard. In recent versions of Operations, they allow Ancestor/Descendants in views. You can show VM and Host metrics on a single line. That can be very powerful in troubleshooting.
</small>  

![](images/dashboard-10.png)  

---

<small>**Dashboard: vCenter Clusters**  
* Use TOP-n widgets to show high cpu and memory usage first 
* Set the metrics in the views to use colors to represent high or low values  
* I like how new versions of Operations have the colored squares next to metric values  
</small>  

![](images/dashboard-12.png)  

---

<small>**Dashboard: SaaS Status**  
* A lot of SaaS products offer a status web site. 
* Use the text display widget to show web site information
</small>  

![](images/dashboard-11.png)  

---

## Lessons Learned:

* Always think about using colors in your Dashboard Designs
* I love to use donut charts. You can show a lot of data in a small area of the Dashboard.
* The text display widget can be used to show data like Weather, Zoom Status, etc...

---

## Links to resources that also show useful Dashboard examples: 

* [Brock Peterson Blog Site](https://www.brockpeterson.com) - Shout out to Brock for some of these examples and ideas.  
* Shout out to Christopher Kusek for some of these examples and ideas.  

---

I created a Google NotebookLM Podcast based on the content of this blog. While it may not be entirely accurate, is any podcast ever 100% perfect, even when real people are speaking? Take a moment to listen and share your thoughts with me!  

[vCROCS Deep Dive Podcast | VCF Operations | Ideas for Practical and Effective Dashboards](https://youtu.be/OGDfvYJ4hhM)  

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