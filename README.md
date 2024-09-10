<h1>Examining alerts, logs, and rules with Suricata</h1>



<h2>Description</h2>
In this lab activity, I explored the components of a rule using Suricata. I had the opportunity to trigger a rule and examine the output in Suricata. I used the Bash shell to complete these steps. 
<br />


<h2>Practical experience gained in this Lab</h2>

- <b>Creating custom rules and run them in Suricata</b> 
- <b>Monitoring traffic captured in a packet capture file</b>
- <b>Examining the fast.log and eve.json output</b> 


<h2>Environments Used </h2>

- <b>Qwiklabs</b> 

<h2>Lab walk-through:</h2>

<h2>Task 1: Examine a custom rule in Suricata </h2>
The `/home/analyst` directory contains a `custom.rules` file that defines the network traffic rules, which Suricata captures. <br /> <br />
In this task, I explored the composition of the Suricata rule defined in the `custom.rules` file.
 <br/> <br />
(1) First, I used the command "cat custom.rules" to display the rule in the custom.rules file.  <br/> <br />
(2) The action is the first part of the signature. It determines the action to take if all conditions are met. The file contains a single alert as the action. The alert keyword instructs to alert on selected network traffic. The IDS will inspect the traffic packets and send out an alert in case it matches.  <br/> <br />
(3) The next part of the signature is the header. The header defines the signature’s network traffic, which includes attributes such as protocols, source and destination IP addresses, source and destination ports, and traffic direction.  <br/> <br />

(4) The next field after the action keyword is the protocol field. <br/> <br />
In our example, the protocol is `http`, which determines that the rule applies only to HTTP traffic. The parameters to the protocol `http` field are `$HOME_NET any -> $EXTERNAL_NET any`. The arrow indicates the direction of the traffic coming from the `$HOME_NET` and going to the destination IP address `$EXTERNAL_NET`.  <br/> <br />

(5) `$HOME_NET` is a Suricata variable defined in `/etc/suricata/suricata.yaml` that I can use in my rule definitions as a placeholder for my local or home network to identify traffic that connects to or from systems within your organization.  <br/> <br /> 

In this lab `$HOME_NET` is defined as the 172.21.224.0/20 subnet.

The word `any` means that Suricata catches traffic from any port defined in the `$HOME_NET` network. 

(6) The many available rule options allowed me to customize signatures with additional parameters. Configuring rule options helps narrow down network traffic so I can find exactly what I'm looking for. <br/> <br />

- The `msg:` option provides the alert text. In this case, the alert will print out the text `“GET on wire”`, which specifies why the alert was triggered.
- The `flow:established,to_server` option determines that packets from the client to the server should be matched. (In this instance, a server is defined as the device responding to the initial SYN packet with a SYN-ACK packet.)
- The `content:"GET"` option tells Suricata to look for the word `GET` in the content of the `http.method` portion of the packet.
- The `sid:12345` (signature ID) option is a unique numerical value that identifies the rule.
- The `rev:3` option indicates the signature's revision which is used to identify the signature's version. Here, the revision version is 3.
<p align="center">
<img src="https://imgur.com/PSq88T8.png" height="80%" width="80%" alt="Disk Sanitization Steps"/> 
<br /> <br />

<h2>Task 2: Trigger a custom rule in Suricata </h2>
In this task, I triggered the custom Suricata rule and examine the alert logs that Suricata generates.
<br/> <br />
(1) First, I used the command "ls -l /var/log/suricata" to list the files in the /var/log/suricata folder. <br/> <br/>
(2) Next, I used the command "sudo suricata -r sample.pcap -S custom.rules -k none" to analyze the network traffic contained in sample.pcap using the Suricata engine with custom detection rules defined in the custom.rules file, while ignoring checksums. <br/> <br/>

- The `r sample.pcap` option specifies an input file to mimic network traffic. In this case, the `sample.pcap` file.
- The `S custom.rules` option instructs Suricata to use the rules defined in the `custom.rules` file.
- The `k none` option instructs Suricata to disable all checksum checks.

(3) Then, I used the command "ls -l /var/log/suricata" to list the files in the /var/log/suricata folder again. 
(4) Finally, I used the command "cat /var/log/suricata/fast.log" to display the fast.log file generated by Suricata. 
<br /> <br /> <p align="center">
<img src="https://imgur.com/rDFEmJj.png" height="80%" width="80%" alt="Disk Sanitization Steps"/> 
<br /> <br />

<h2>Task 3: Examine eve.json output </h2>
In this task, I examined the additional output that Suricata generates in the eve.json file.
 <br/> <br/>
(1) First, I used the command "cat /var/log/suricata/eve.json" to display the entries in the eve.json file. <br/> <br/>
(2) Next, I used the command "jq . /var/log/suricata/eve.json | less" to display the entries in an improved format. <br/> <br/>

The jq command above extracts the fields specified in the list in the square brackets from the JSON payload. The fields selected are the timestamp (.timestamp), the flow id (.flow_id), the alert signature or msg (.alert.signature), the protocol (.proto), and the destination IP address (.dest_ip).
<p align="center">
<img src="https://imgur.com/jZ6xDDc.png" height="80%" width="80%" alt="Disk Sanitization Steps"/> 
<br /> <br />
(3) Finally, I used the command "jq -c "[.timestamp,.flow_id,.alert.signature,.proto,.dest_ip]" /var/log/suricata/eve.json" to extract specific event data from the eve.json file.
<br/> <br/> <p align="center">
<img src="https://imgur.com/Fs3FCJe.png" height="80%" width="80%" alt="Disk Sanitization Steps"/> 
<br /> <br />
