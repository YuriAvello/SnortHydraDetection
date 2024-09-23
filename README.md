<h1>Snort local rule to detect slow-rate Hydra attacks</h1>
<!--
 ### [YouTube Demonstration]()
-->
<h2>Description</h2>
This project is the one that I presented as a final project for my degree in MSc Cyber Security. I created three virtual machine:
- Ubuntu as a target machine where I activated the SSH service and installed Snort
- A Kali machine as the attacker
- Another Kali machine to simulate legit attempt to login into the SSH to test false positive.
<br />


<h2>Languages and Utilities Used</h2>

- <b>OpenSSH 3.0.13</b>
- <b>Snort 2.9.20</b> 
- <b>Bash</b>
- <b>Hydra Brute Force</b> 
  
<h2>Environments Used </h2>

- <b>Oracle VM Virtual Box 7.0</b> 
- <b>Ubuntu 24.04 LTS</b>
- <b>Kali Linux 2023.1</b> 

<h2>Program walk-through:</h2>

<p align="center">
Work in progress <br/>
 
<img src="SnortHydraDetection/01_snortstartscript.png" height="80%" width="80%" alt="Script to start Snort"/>
<br />

Work in progress <br/>
 
<img src="SnortHydraDetection/02_users.png" height="20%" width="20%" alt="Text file with usernames"/>
<br />

Work in progress <br/>
 
<img src="SnortHydraDetection/03_password.png" height="20%" width="20%" alt="Text file with passwords"/>
<br />

Work in progress <br/>
 
<img src="SnortHydraDetection/04_hydratest.png" height="80%" width="80%" alt="Hydra test without Snort started"/>
<br />

Work in progress <br/>
 
<img src="SnortHydraDetection/05_hydratest20threads.png" height="80%" width="80%" alt="Test Hydra with -T 20"/>
<br />

Work in progress <br/>
 
<img src="SnortHydraDetection/06_snortruleHydra.png" height="80%" width="80%" alt="Snort Local Rule"/>
<br />

Work in progress <br/>
 
<img src="SnortHydraDetection/07_hydradetection1.png" height="80%" width="80%" alt="First Snort Detection"/>
<br />

Work in progress <br/>
 
<img src="SnortHydraDetection/08_hydradetection2.png" height="80%" width="80%" alt="Snort second detection"/>
<br />

Work in progress <br/>
 
<img src="SnortHydraDetection/09_hydradetection3.png" height="80%" width="80%" alt="Snort False Positives"/>
<br />

Work in progress <br/>
 
<img src="SnortHydraDetection/10_hydradetection4.png" height="80%" width="80%" alt="Snort fourth detection"/>
<br />

Work in progress <br/>
 
<img src="SnortHydraDetection/11_hydraslow1.png" height="80%" width="80%" alt="Test slow hydra 1"/>
<br />

Work in progress <br/>
 
<img src="SnortHydraDetection/12_hydraslow2.png" height="80%" width="80%" alt="Test Slow Hydra 2"/>
<br />

Work in progress <br/>
 
<img src="SnortHydraDetection/13_hydradetection5.png" height="80%" width="80%" alt="Snort Fifth Detection"/>
<br />

Work in progress <br/>
 
<img src="SnortHydraDetection/14_hydraslow3.png" height="80%" width="80%" alt="Test Slow Hydra 3"/>
<br />

Work in progress <br/>
 
<img src="SnortHydraDetection/15_hydrafaildetection.png" height="80%" width="80%" alt="Snort Failed Detection"/>
<br />

Work in progress <br/>
 
<img src="SnortHydraDetection/16_hydradetection6.png" height="80%" width="80%" alt="Snort sixth detection"/>
<br />


 <!--


 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
