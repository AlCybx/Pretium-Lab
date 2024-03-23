# Pretium-Lab

## Scenario

he Security Operations Center at Defense Superior are monitoring a customer’s email gateway and network traffic (Crimeson LLC). One of the SOC team identified some anomalous traffic from Josh Morrison’s workstation, who works as a Junior Financial Controller. When contacted Josh mentioned he received an email from an internal colleague asking him to download an invoice via a hyperlink and review it. The email read:

There was a rate adjustment for one or more invoices you previously sent to one of our customers. The adjusted invoices can be downloaded via this [link] for your review and payment processing. If you have any questions about the adjustments, please contact me.

Thank you.

Jacob Tomlinson, Senior Financial Controller, Crimeson LLC.

The SOC team immediately pulled the email and confirmed it included a link to a malicious executable file. The Security Incident Response Team (SIRT) was activated and you have been assigned to lead the way and help the SOC uncover what happened.

You have NetWitness and Wireshark in your toolkit to help find out what happened during this incident.

### Tools Used

- Wireshark.
- TShark.
- NetWitness.
- CyberChef.

## Questions

# Question 1
* What is the full filename of the initial payload file?
To pinpoint the file transfer, we examine packets in the capture that suggest such activity, possibly occurring via protocols like HTTP, FTP, or SMB. Subsequently, we scrutinize these packets to pinpoint those containing file data. Once identified, we right click, navigate to 'Follow' > 'TCP Stream' to uncover the filename, such as 'INVOICE_2021937.pdf.bat
<img width="1140" alt="Screenshot 2024-03-23 at 12 38 11" src="https://github.com/AlCybx/Pretium-Lab/assets/161755166/bbe27ad1-4df9-4780-94ae-e95b061c575a">

# Question 2
* What is the name of the module used to serve the malicious payload?
The module responsible for delivering the malicious payload is found within the same interface where we discovered the file name. Once the module is identified, we conduct a quick search on Google to find our answer.
<img width="907" alt="Screenshot 2024-03-23 at 12 57 59" src="https://github.com/AlCybx/Pretium-Lab/assets/161755166/7c3afb37-1d22-4cc8-9b7c-748ab20acda0">
<img width="1387" alt="Screenshot 2024-03-23 at 12 59 10" src="https://github.com/AlCybx/Pretium-Lab/assets/161755166/48de1bce-87b5-4003-99bf-d89b1150b644">

# Question 3
* Analysing the traffic, what is the attacker's IP address?
The attacker's IP address can be located within the source IP field of the sent file.
<img width="1361" alt="Screenshot 2024-03-23 at 13 14 18" src="https://github.com/AlCybx/Pretium-Lab/assets/161755166/602a04fb-a384-435b-a576-c71654569673">

# Question 4
* Now that you know the payload name and the module used to deliver the malicious files, what is the URL that was embedded in the malicious email?
To locate the URL embedded in the malicious email, select the sent file, scroll down, and choose "HyperText Transfer Protocol" from the dropdown menu. The URL can then be found within this section.
<img width="1361" alt="Screenshot 2024-03-23 at 13 22 29" src="https://github.com/AlCybx/Pretium-Lab/assets/161755166/7282351f-f360-43ee-bf26-86a4b7de25ca">

# Question 5
* Find the PowerShell launcher string (you don’t need to include the base64 encoded script)
The simplest method is to revisit the HTTP stream or TCP stream that carried the payload. Once there, we can identify the PowerShell command utilized.
<img width="1137" alt="Screenshot 2024-03-23 at 13 40 49" src="https://github.com/AlCybx/Pretium-Lab/assets/161755166/62419852-15a4-4d35-b442-541eedf7ee04">

# Question 6
* What is the default user agent being used for communications?
In that same window, we can discover the default user agent utilized for communications.
<img width="1133" alt="Screenshot 2024-03-23 at 13 47 02" src="https://github.com/AlCybx/Pretium-Lab/assets/161755166/3cd3ea4c-42ed-4081-baa9-d3e5089ae57e">

# Question 7
* You are seeing a lot of HTTP traffic. What is the name of a process where malware communicates with a central server asking for instructions at set time intervals?
The payload includes PowerShell code encoded in base64. The simplest method is to copy the encoded text and decode it using CyberChef (gchq.github.io). Alternatively, you can use PowerShell for decoding. Utilize Generic Code Beautify and Regular Expression to tidy up the output. Within the PowerShell code, a hardcoded User Agent can be found.
<img width="1176" alt="Screenshot 2024-03-23 at 14 01 20" src="https://github.com/AlCybx/Pretium-Lab/assets/161755166/ed08786c-f05f-4f14-83d3-c2c93a5b6159">

# Question 8
* What is the URI containing ‘login’ that the victim machine is communicating to?
The URI of the login process can be discovered by navigating and selecting the 'login process'. Subsequently, scrolling down and clicking on 'HyperText Control Protocol' allows us to locate the URI of the login process
<img width="1366" alt="Screenshot 2024-03-23 at 14 06 41" src="https://github.com/AlCybx/Pretium-Lab/assets/161755166/ba1cde9e-17d5-403a-bc35-e1442fe0195e">

# Question 9
* What is the name of the popular post-exploitation framework used for command-and-control communication?
After conducting some research in MITRE ATT&CK, it was discovered that EMPIRE aligns with our findings during the investigation.
<img width="1078" alt="Screenshot 2024-03-23 at 14 26 29" src="https://github.com/AlCybx/Pretium-Lab/assets/161755166/cb52dc31-1394-49b4-b3d5-2adb38ab6782">

# Question 10
* It is believed that data is being exfiltrated. Investigate and provide the decoded password.
<img width="1137" alt="Screenshot 2024-03-23 at 14 40 58" src="https://github.com/AlCybx/Pretium-Lab/assets/161755166/3dc5a772-bb73-4f8a-aca6-bbb48f75aed1">

Towards the end of the .pcap file, there is a significant amount of ICMP traffic occurring rapidly, with packets being sent in milliseconds. Unlike standard ping packets which are typically 32 bytes, these packets are 43 bytes for requests and 60 bytes for replies.

Upon closer inspection, it's noted that the last character of each packet changes with each request. Further examination reveals that these characters resemble '==', indicating padding for base64 encoded commands, a method previously observed from the attacker.

To analyze this, I utilized tshark, Wireshark's CLI, which facilitated the extraction of data from these ping packets. By reading in the .pcap file and printing fields for the attacker's machine, I was able to capture the ICMP replies (mirroring the victim's requests) and save them into a .txt file.

<img width="157" alt="Screenshot 2024-03-23 at 14 46 11" src="https://github.com/AlCybx/Pretium-Lab/assets/161755166/c6798bdf-b957-448e-aacd-4078769a9d7c">
Extracted data as hex from ICMP packets

C:\Users\BTLOTest\Desktop\Investigation> tshark.lnk -r
C:\Users\BTLOTest\Desktop\Investigation\LAB.pcap -T fields -e data
ip.src==192.168.1.9 >> data.txt

Navigate to the lower section of the file and locate the column consisting of two characters, representing data transmitted in the ICMP reply in hexadecimal format. Confirm that this data matches what was observed in Wireshark packets (e.g., 3d in hex corresponds to =).

Select and copy this data (excluding the entire file) and paste it into CyberChef. Proceed to convert the string from hexadecimal, decode it from base64, and apply the Regular Expression filter to enhance the output.

The resulting output will reveal the password "Youthink0ucAnc4tchm3$$" for the user account.
<img width="1231" alt="Screenshot 2024-03-23 at 14 50 34" src="https://github.com/AlCybx/Pretium-Lab/assets/161755166/1172de1e-4d7c-44fa-8055-7bf16aa00329">

# Question 11
* What is the account’s username?
The accounts username can also be located in CyberChef.
<img width="1224" alt="Screenshot 2024-03-23 at 14 53 24" src="https://github.com/AlCybx/Pretium-Lab/assets/161755166/25bd828c-221f-4f2e-bd16-f9076fb17bcc">




