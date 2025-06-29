+++
date = '2025-06-25T20:38:35-04:00'
draft = false
title = 'Monitor Your Machines!'
+++

I have basic vizualization set up but admittedly not much monitoring. Checking my system one Saturday evening I found surprisingly high power draw on my UPS. Almost everything on my UPS is arm based with the exception of my Proxmox host. A quick look through my VM inventory found the culprit, my Prometheus anomaly detection VM has I/O and CPU utilization through the roof since around 2am.

<!--more-->

![high power usage](images/high_power_usage.png)


![high vm usage](images/vm_usage.png)

Investigating in the VM shows the high usage coming from rsyslogd.

![high rsyslogd usage](images/high_rsyslogd_cpu_usage.png)

`sudo less /var/log/syslog`

That'll do it...

![filesystem filled](images/drive_filled.png)

It might help if I has actually partitioned the drives correctly

![bad partitioning](images/bad_partitioning.png)

Here we need to extend the logical volume group: `sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv`

And also extend the filesystem: `sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv`

![happy filesystem](images/happy_filesystem.png)
