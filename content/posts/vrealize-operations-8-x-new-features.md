---
title: "vRealize Operations 8.x - New Features"
date: 2021-02-20T05:00:00Z
author: "Dale Hassinger"
tags:
  - vROPS
  - vRealize Operations
  - Donut Charts
  - Top-N Charts
  - Ping Monitoring
  - VMware Aria
categories:
  - VCF Operations
image: "images/vrealize-operations-8-x-new-features-featured.png"
draft: false
---

**The New Features I use the most.**

---



---

1. Donut Charts.
   * This is one of my favorite new features.
   * The donut chart allows you to show a lot of information in a smaller space and makes the Dashboards look more modern and clean looking.
   * I have been replacing the Heat Map Widgets on some of my custom Dashboards with Donut Charts.
   * If you click on the colors of the Donut charts you will then get a list of the data that makes up that color.
2. Top-N Chart and Color Method.
   * Adding color to the Top-N makes it so much more usable. With the previous version where is was just blue it didn't grab your eye like adding the color.
   * Using the Top-N chart and the Donut chart together makes a GREAT looking Dashboard. See my example below.
3. Ping Monitoring.
   * This feature always us to monitor non VMware devices. In a short period of time I learned a lot about latency and packet drops in our environment.
4. Troubleshooting Workbench.
   * In previous versions of vROPS I would create Dashboards to help with trouble shooting.
   * With Trouble Shooting work bench I don't need to create those custom Dashboards anymore.
   * The included Trouble Shooting workbench is like what I did on steroids.

## Donut Chart and Top-N Example:

![](images/vrops8-01.png)  

This single Dashboard has a lot of info that would has taken several Dashboards to show same amount of data using Heat Maps. This would be a good example of a Dashboard that managers may want to see.

![](images/vrops8-05.png)  

## Donut Chart Config:
* Create a Distribution View. 
* Make the visualization a Donut Chart. 
* Use Manual distribution.
* Create Buckets and set the values/colors for the Buckets.
* Add the View to a Dashboard.

![](images/vrops8-02.png)  

![](images/vrops8-03.png)  

## Top-N Chart Config:
* Add Top-N Widget to Dashboard
* Define configuration and Input Transformation.
* Output Data:Color Method:Custom is where you define the color values.

![](images/vrops8-04.png)  

---

* If you found this Blog article useful and it helped you, Buy me a coffee to start my day.  

<center>
<script type="text/javascript" src="https://cdnjs.buymeacoffee.com/1.0.0/button.prod.min.js" data-name="bmc-button" data-slug="dalehassinger" data-color="#FFDD00" data-emoji=""  data-font="Cookie" data-text="Buy me a coffee" data-outline-color="#000000" data-font-color="#000000" data-coffee-color="#ffffff" ></script>
</center>

---