#Ticket 1: Can't open mailbox of office 365

Potential issues:
+ License issue -> Office 365 license expired, removed, or not assigned
+ Internet not working
+ Local Outlook profile corruption
+ Mailbox storage limit reached ( Very large Inbox, Sent Items, Calendar, or Deleted Items,...)

- Trouble shooting steps:
Verify Internet connection.
Try logging into Outlook Web App (OWA).
Check Microsoft 365 license.
Check mailbox quota.
Create a new Outlook profile if needed.
Repair or recreate the OST file.


#Ticket 2: Outlook slow, when user archived outlook msg, it got slowness in access outlook, even though he upgraded 4 GB to 8 GB ram or uninstall, then he noticed that two archive on c drive & another in F drive


Possible problems to check:
+ Some process cause high CPU usage
+ Check size of PST file 
+ Remove duplicate or unused archive
+ If F drive is an HDD, external or network location -> moving active archive to a local SSD

- Troubleshooting steps
Check CPU, RAM, and Disk usage in Task Manager.
Check PST file sizes.
Verify whether both archive PST files are attached.
Remove unused or duplicate archive files (after confirming they are no longer needed).
Run SCANPST.EXE if corruption is suspected.
If the active archive is on a slow drive, move it to a local SSD.
Test Outlook in Safe Mode (outlook.exe /safe).

#Ticket 3: How to remote customer's computer whose is not connected to VPN but connected to her home network.

- Possible options include:
BeyondTrust (Bomgar) or your company's remote support solution.
TeamViewer, AnyDesk, or Quick Assist (if approved by company policy).
Zoom or Microsoft Teams screen sharing if only guidance is needed.
If your company uses Dameware (often pronounced "Dameware"), send the remote support invitation/link or session request if your environment supports Internet-based connections.

- Troubleshooting process
Verify the user has Internet access.
Confirm which remote support tool is approved by the company.
Send the remote support invitation or session link.
Obtain the user's permission.
Connect and troubleshoot the issue.
Disconnect and document the work performed.



