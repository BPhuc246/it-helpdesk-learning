#Ticket 1: A printer failed to print double sided.

Printers & scanners from Settings -> Look for option to enable or disable single side or double-sided printing



#Ticket 2: Customer's computer is slow

Troubleshooting:
+ Open Task Manager -> Change which process take a lot of CPU, Performance ( utilization, hours of work ), how many Memory remaining.
+ Open explorer to check if is there any disk nearly full ( red color )



#Ticket 3: Customer is unable to login to his computer, customer got a bad timestamp error. His time is incorrect as well. 

Bad timestamp error: may caused by logging into Duo Authentication for Windows Logon
Fix by some CMD in or tools  



#Ticket 4: Customer installed office 2016, created a team meeting in calendar tab, when he clicked on calendar tab in outlook, create a new meeting, it gives an error: "Sorry, you need to sign out if Teams and sign back in again before yo can schedule your meeting", he repaired it but it stay the same


Potential solution:
+ Log out of any used office 265 applications ( like word, excel ,... )
+ Repair plugin only if possible
+ Flush appdata from appdata folder



#Ticket 5: Backup is not working, error: Event log Application: \\192.168.44.2\Backup\VOL-b002-i001-cd.spi Bad decryption password

-> Dealing with backup has error bad decryption password -> SFTP ( Secure File Transfer Protocol )
-> SFTP does encrypted SSH (Secure Shell) connection (usually on Port 22), providing both data encryption and file management capabilities.
Solution: Make sure that you have set up a private and public key both from client and server.
Source to generate key: PuTTYgen



#Ticket 6: During remote connection, the connection is successful, but it says password is required

-> Click "More choices" to select a different account -> use your domain account login ( make sure it is admin ) or local admin account



#Ticket 7: Customer have printer but he have dots on the printed paper

-> dots on the printed paper: could be caused by paspar dust, glue from label,..., or something stuck and stick to these areas on the drum and creates black dots on the printed pages.

-> Clear the drum



#Ticket 8: Window update is frozen downloading and update stay at 66%, Unable to get new updates ( HP Windows 11 Home, 16GB, HD/SSD hybrid storage ). Customer tried the update troubleshooting but did not fix anything. 
It happened when he linking Galaxy S21 to his PC.

Images:
Update Windows stay 66% with status is Downloading
Can't access Microsoft Edges: "Microsoft Edges can't read and write to its data directory"

+ Downloading stay at 66% but customer is still connecting with wifi -> so customer's PC did not lose connection

+ Open task manager -> to check is there anything is installing that prevent window update
-> If you can stop it and install window first
 
+ Look for Window tool use that to update





















