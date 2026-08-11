#Ticket 1: Explain and How to configure a firewall ?


Explanation: This is done automatically by with group policy that is controlled by domain controller. ( Active directory )

-> Find "Windows Defender Firewall with Advanced Security" and run as administrator:
Inbound Rules: rules that if an application is allowed to receive inbound connection. ( receive data rules ).

-> Can also add new rule ( Inbound or Outbound ) in here such as block to google.com by:
Click new rules -> select Program -> Paste the path of google ( not URL -> Browser -> Then find path of google in explorer ) -> Choose action "Block the connection" -> Select when does this rule apply -> Complete

Outbound Rules: rules that connection being sent out from your computer if you are tying to send data from computer it will be blocked. 



#Ticket 2: We have an office span with about 10 people, and the wifi keeps dropping for some people. It happens randomly for people and it happens maybe 2-4 times a day. Customer suspected that it's because there's too many logging on to the same wifi network but not sure


Troubleshooting setps:
-> Access the router
Open cmd -> `ipconfig /all` -> Look at "Default gateway" ipv4 in the Ethernet adapter Ethernet ->  Then access it to website -> Find connectivity -> check local network, you can see maximum number of users and can change it, then apply.



#Ticket 3: Libre office not opening after installing, customer can't open any files with this issue.


- Libre office is a free and open-source office from OpenOffice.
Troubleshooting: 
+ Uninstall then reinstall
+ Check virus protection in settings to make sure that you did not download a malware
+ Check .xls file and .doc file, if these extensions are compatible with libre office.



#Ticket 4: Customer is working from home at the moment and he need to connect to the company via VPN. He is online, he can use Google and receive his private mails but connection via VPN is not working. Server is online, password correct but no connection to the company is built up.


- If you work from home and google works + can't connect VPN -> It must be proxy problem.

- Proxy is an intermediary computer or software application that sits between a user (the Client) and the destination on the internet (the Target Server): 

+ Outbound filtering (Forward Proxy): Schools or corporate networks use proxies to block access to specific domains (e.g., social media or malicious sites).

+ Inbound filtering (Reverse Proxy): Protects web applications by inspecting incoming traffic to block SQL injections, cross-site scripting (XSS), or Distributed Denial of Service (DDoS) attacks.




























