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

<p align="left">
Before launching Snort I needed to configure some parameters in the file etc/snort/snort.conf, such as network adapter and IP range addresses.
 
When Snort is launched, any alert will be printed to the console. For ease of use, I created a script that launches snort, redirects the output to the text file snortlog.txt, and once I interrupt the execution of snort, the command WC -l snortlog.txt will print in the console the number of lines generated (a.k.a detections). 
 
 <br />
<img src="SnortHydraDetection/01_snortstartscript.png" height="80%" width="80%" alt="Script to start Snort"/>
<br />

Then I created two text files with ten usernames and ten passwords to be used with the Hydra attack <br/>
 
<img src="SnortHydraDetection/02_users.png" height="20%" width="20%" alt="Text file with usernames"/>
<br />

<img src="SnortHydraDetection/03_password.png" height="20%" width="20%" alt="Text file with passwords"/>
<br />

Without launching Snort, I just ran a hydra attack as a test to ensure the attack would succeed.
```
Hydra -L users.txt -P password.txt ssh://10.0.2.12 -t 8
```
-t 8 indicates the number of simultaneous threads that can attempt to access the SSH port 22

<br/>
 
<img src="SnortHydraDetection/04_hydratest.png" height="80%" width="80%" alt="Hydra test without Snort started"/>
<br />

After starting Snort, I performed the same attack, but Snort did not detect any attacks. Unsure if the number of combinations and the pace of the attack would be enough to be detected, 
I increased the number of combinations to 400 and set the flag -T 20 to perform 20 simultaneous threads. <br/>
 
<img src="SnortHydraDetection/05_hydratest20threads.png" height="80%" width="80%" alt="Test Hydra with -T 20"/>
<br />

Here is the local rule that I implemented in an attempt to detect the attack: <br />
```
alert tcp $EXTERNAL_NET any -> $HOME_NET 22 (msg: "Possible SSH brute forcing!" ;\ flags: 5+; threshold: type both, track by_src, count 5, seconds 30; sid:1000002; rev: 1;)
```

[Reference](https://stackoverflow.com/questions/47742405/using-snort-suricata-i-want-to-generate-an-ssh-alert-for-every-failed-login-to)

This rule focuses on the number of inbound connections from the same source that are asking for a TCP connection to the SSH port 22. If more than five connection attempts in 30 seconds are detected, it will send a single alert. 
 <br/>
 
<img src="SnortHydraDetection/06_snortruleHydra.png" height="80%" width="80%" alt="Snort Local Rule"/>
<br />

With the local rules, Snort created one alert at a maximum once every 30, generating only 5 alerts while the brute force was running.  <br/>
 
<img src="SnortHydraDetection/07_hydradetection1.png" height="80%" width="80%" alt="First Snort Detection"/>
<br />

So, I proceeded to eliminate the threshold parameters, and out of 420 attacks from Hydra, Snort created 169 alerts, which equals 40.2% of the total attacks.  <br/>
 
<img src="SnortHydraDetection/08_hydradetection2.png" height="80%" width="80%" alt="Snort second detection"/>
<br />

So far, I have not considered the possibility of false positives. Without the threshold parameters, Snort could likely send an alert for a legitimate login attempt. <br />
Therefore, I repeated the experiment, and from a third VM, I created an SSH connection three times, pretending to be a legitimate user. The result is that Snort created three extra alerts, and inspecting the log file, I could find the three legitimate connections. I concluded that the local rule would not be viable without the threshold parameter.  <br/>
 
<img src="SnortHydraDetection/09_hydradetection3.png" height="80%" width="80%" alt="Snort False Positives"/>
<br />

To eliminate false positives, I reintegrated the parameter threshold: this time, the count of attempted connections is three instead of 5, and the time span is 3 seconds instead of 30. The idea is still to receive a certain number of alerts without the false positives.  <br/>
The new rules generated fewer alerts, 25, due to the threshold parameters, but they did not generate any false positives against the five SSH legitimate connections I executed. <br />


<img src="SnortHydraDetection/10_hydradetection4.png" height="80%" width="80%" alt="Snort fourth detection"/>
<br />

### Avoid detection
In this stage of the experiment, I tried to test Hydra with the slower setting, with fewer threads, to avoid the detection from Snort.
```
hydra -L users.txt -P password.txt ssh://10.0.2.12 -t 4
```
And Failed <br />
<img src="SnortHydraDetection/11_hydraslow1.png" height="80%" width="80%" alt="Test slow hydra 1"/>
<br />

```
hydra -L users.txt -P password.txt ssh://10.0.2.12 -t 2
```
<br/>
 
<img src="SnortHydraDetection/12_hydraslow2.png" height="80%" width="80%" alt="Test Slow Hydra 2"/>
<br />

Was detected only once <br/>
 
<img src="SnortHydraDetection/13_hydradetection5.png" height="80%" width="80%" alt="Snort Fifth Detection"/>
<br />

```
hydra -L users.txt -P password.txt ssh://10.0.2.12 -t 1
```
<br/>
 
<img src="SnortHydraDetection/14_hydraslow3.png" height="80%" width="80%" alt="Test Slow Hydra 3"/>
<br />

The previous attack was not detected by Snort <br/>

**Considerations:** The brute force attack with four threads took 1 minute and 25 seconds to complete and was detected nine times. With two threads, the completion time was 3 minutes and 8 seconds, more than double that of the one with four threads, but it was detected only once, which reduced the detection by almost 90%. The attack with only one thread took 10 minutes and 55 seconds and avoided detection.
 
<img src="SnortHydraDetection/15_hydrafaildetection.png" height="80%" width="80%" alt="Snort Failed Detection"/>
<br />

### Adapting the local rules 

I tested the local rule, increasing the period for detection to 10, 15, 30 and 60 seconds. <br />
With 60 seconds period for detection, Snort was able to send 6 alerts for the attack. <br/>
 
<img src="SnortHydraDetection/16_hydradetection6.png" height="80%" width="80%" alt="Snort sixth detection"/>
<br />

### Analysis and Evaluation
Increasing the time span of the threshold parameters helps in detecting the brute force attack, but is also prone to false positives. Giving more time to do three attempts from the same source could easily create false positives. But here is the thing: the dictionary files of brute force attacks contain thousands and thousands of entries that, combined with a few users' names, can create combinations in the order of hundreds of thousands. If a malicious actor wants to perform a brute force attack, performing an attempt every 5 seconds would take a long time to succeed, so much that it would act as a deterrent. <br />

Another consideration is that I used Snort exclusively in IDS mode (detection) and not in IPS mode (prevention). The prevention mode may have some rule that may drop packets and close connections when certain requirements are met. Furthermore, in a secured system there is not only a network intrusion detection system but many other tools that together complete each other. One example is the firewall, that in the case of experiment could have picked up the fast and massive attempts of Hydra in the first stage of the experiments. <br />

Furthermore, I had the impression that the rule making of Snort is a powerful tool, and in knowledgeable hands can really be an important resource against intrusion. Another aspect regarding the rule is that on Snort website is possible to download the community rules, a collection of advanced rules, always updates against the new threats. And for the big corporations there is a possibility to pay a subscription to get another set of exclusive rules. 

 <!--


 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
