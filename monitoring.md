# The case for monitoring
I have basic vizualization set up but admittedly not much monitoring. Checking my system one Saturday evening I found surprisingly high power draw on my UPS. Almost everything on my UPS is arm based with the exception of my Proxmox host. A quick look through my VM inventory found the culprit, my Prometheus anomaly detection VM has I/O and CPU utilization through the roof since around 2am.

![image](https://github.com/user-attachments/assets/f395f97c-3cd6-4046-975b-fcb2452b52db)

Investigating in the VM shows the high usage coming from rsyslogd.

![image](https://github.com/user-attachments/assets/7bde971c-514c-4f9e-ae4b-94bbb266abab)

`sudo less /var/log/syslog`

That'll do it...

![image](https://github.com/user-attachments/assets/11a4782f-bf25-48a5-8150-ef92d125a96d)

It might help if I has actually partitioned the drives correctly

![image](https://github.com/user-attachments/assets/ea1bcfb8-3d16-4b6f-8d6c-225c6f5663e9)

Here we need to extend the logical volume group: `sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv`

And also extend the filesystem: `sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv`

![image](https://github.com/user-attachments/assets/a0879155-5970-4553-88f0-7c98f1165c5c)
