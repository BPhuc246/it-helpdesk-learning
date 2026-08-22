#Ticket 1: Customer can't see his share drive in his desktop from Window 10 pro

Problem: In explorer it may be disconnected by remote computer or remote server is down

Troubleshooting: reinstall + add network drive again




#Ticket 2: Customer's laptop is at capacity, unable to run more than one application at a time, really slow opening applications

+ It may be no more spaces in drive, check explorer, then delete some unused files
+ Open and check the task manager, is there any program use too much memory or CPU, if it is unnecessary, just delete it



#Ticket 3: When ping a website with no reply, what is this issue ?

+ Make sure website is still online
+ Check if it is blocked by firewall from inbounds rule
+ Your location must be blocked by website



#Ticket 4: Computer is too hot and making noise while running

+ Check the Task Manager, look for how many hours your computer has been worked, is there any process take too much CPU or weird program.
+ Check computer and make sure it is clean from dirty



#Ticket 5: Customer is able to ping the printer's IP address and can access the web portal through browser, but it can't install it to any PC, he tried to add it by the device's installation software and also the windows wizard software. He also have restated printer but still stay the same

-> Make sure that the IP address of that printer is static and port is open, not block by firewall



#Ticket 6: Customer has an issue when he tried to access VPN portal via IP address on Google Chrome or Firefox, the error is on both web browsers, Google Chrome: "This site can't provide a secure connection ( IP address uses an unsupported protocol ) Err_SSL_version_or_cipher_mistmatch", Firefox: "Secure connection failed (an error occurred during connection to open address)"


Troubleshooting:
+ Ping the name website -> Copy the IP address it is pinging and paste it in google or firefox -> continue to <IP address>
=> It must be the website certificate expired or the frontend of website needs to be fixed























































































