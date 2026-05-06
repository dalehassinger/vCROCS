---
title: "VMware Aria Operations | Broadcom Services | Management Pack"
date: 2024-04-01T04:00:00Z
author: "Dale Hassinger"
tags:
  - VMware
  - VMware Aria Operations
  - Management Pack
  - Broadcom Services
  - Management Pack Builder
categories:
  - VCF Operations
image: "images/vmware-aria-operations-broadcom-services-management-pack-featured.png"
draft: false
---

**How to use the VMware Aria Operations Management Pack Builder for Day 2 monitoring**



---

## VMware Aria Operations  

VMware Aria Operations 8.16.0 and Management Pack Builder 1.1.2 was used for this Blog Post. When new versions of VMware Aria Operations or the MP Builder are released, the code or process may need to be changed. 

---

![](images/mp-builder-01.png)  

---


I wanted to share how I created a VMware Aria Operations Management Pack to use with the Broadcom Service Status web site, [status.broadcom.com](https://status.broadcom.com). This web site shows the current status of all Broadcom Services, Broadcom Software Group or VMware Software. As a VCF Specialist, my first thought when I saw that this web site provided API access, was to create a Aria Operations Management Pack.  

When Aria Operations is pulling in the status of the Broadcom Services with a Management Pack, that will enable you to create Alerts, Dashboards, Views, Reports, etc... that are important to your environment. If you are a VMware customer, monitoring the "VMware Cloud Services" may be important to you. I am going to show how I created the Management Pack using the MP Builder and some of the custom items I created in Aria Operations. I like to use Aria Operations as the "Single Pane of Glass" for monitoring.  

Please keep this in mind after reading the details of the Blog Article. The Management Pack Builder is Designed to allow you to bring data into Aria Operations, using an API, that is important to your Environment. This is a simple example of how Powerful the MP Builder can be. Other items that could be monitored with a MP could be: Temperatures of Networking closets or Data Centers (if you have a temperature senor with API capabilities), weather conditions around your physical DC locations (There are Web Sites that allow API weather updates), Storage/Compute/Networking that doesn't have a VMware provided MP, etc... So many use cases!  

---

**See how the items show in Object Browser:**
![](images/mp-builder-02.png)  

---

**Dashboard to show the Broadcom Service Statuses:**
![](images/mp-builder-03.png)  

---

**Views created to use with the Dashboard:**
![](images/mp-builder-04.png)  

---

**YouTube Video to show how I created the Management Pack using MP Builder:**

---

![](images/mp-builder-05.png)  

---

![](images/mp-builder-06.png)  

---

![](images/mp-builder-07.png)  

---

![](images/mp-builder-08.png)  

---

![](images/mp-builder-09.png)  

---

![](images/mp-builder-10.png)  

---

![](images/mp-builder-11.png)  

---

![](images/mp-builder-12.png)  

---

![](images/mp-builder-13.png)  

---

![](images/mp-builder-14.png)  

---

![](images/mp-builder-15.png)  

---

![](images/mp-builder-16.png)  

---

![](images/mp-builder-17.png)  

---

![](images/mp-builder-18.png)  

---

![](images/mp-builder-19.png)  

---

![](images/mp-builder-20.png)  

---

![](images/mp-builder-21.png)  

---

![](images/mp-builder-22.png)  

---

![](images/mp-builder-23-1.png)  

---

![](images/mp-builder-23-2.png)  

---

![](images/mp-builder-23-3.png)  

---

![](images/mp-builder-24.png)  

---

![](images/mp-builder-25.png)  

---

![](images/mp-builder-26.png)  

---

![](images/mp-builder-27.png)  

---

## Objects:
![](images/mp-builder-28.png)  

---

I like to pick a icon that represents the Object.
![](images/mp-builder-29.png)  

---

Select all attributes per the screen shot.
![](images/mp-builder-30.png)  

---

Make sure all items are marked as property.
![](images/mp-builder-31.png)  

---

Make sure instance name is "Name" and Identifiers is "ID".
![](images/mp-builder-32.png)  

---

## Relationships:
![](images/mp-builder-33.png)  

---

## Events:
![](images/mp-builder-34.png)  

---

## Content:
![](images/mp-builder-35.png)  

---

## Configuration:
![](images/mp-builder-36.png)  

---

## Perform Collection:
![](images/mp-builder-37.png)  

---

## Perform Collection Complete:
![](images/mp-builder-38.png)  

---

## MP Build Complete:
Select Download to be able to add your your Aria Operations.
![](images/mp-builder-39.png)  

---






## Links to resources about VMware Aria Operations Management Pack Builder:
* [Brock Peterson Blog Post | vROps Management Pack Builder](https://www.brockpeterson.com/post/vrops-management-pack-builder).  
* [YouTube | VMware Aria Management Pack Builder v1.1](https://www.youtube.com/playlist?list=PLrFo2o1FG9n4gQ60pa8CjpTDbgpUz5Hrt).  
* [GitHub Repository where you can download the Manage Pack, Dashboard and Views](https://github.com/dalehassinger/unlocking-the-potential)




---

When I write my blogs, I always say there are many ways to accomplish the same task. This article is just one way that you could accomplish this task. I am showing what I felt was a good way to complete the use case but every organization/environment will be different. There is no right or wrong way to complete the tasks in this article.

---

* If you found this Blog article useful and it helped you, Buy me a coffee to start my day.  

<center>
<script type="text/javascript" src="https://cdnjs.buymeacoffee.com/1.0.0/button.prod.min.js" data-name="bmc-button" data-slug="dalehassinger" data-color="#FFDD00" data-emoji=""  data-font="Cookie" data-text="Buy me a coffee" data-outline-color="#000000" data-font-color="#000000" data-coffee-color="#ffffff" ></script>
</center>

---