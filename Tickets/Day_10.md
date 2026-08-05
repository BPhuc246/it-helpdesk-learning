#Ticket 1: None of customer's documents are printing

Possible problems:
+ Printer did not plug in
+ Different type of connections serial ( firewire, Bluetooth did not power on )
+ Did not install your printer in "Printers & Scanner" from Settings
+ Error at Imaging devices in Device Manager

Solutions:
-> For Device Manager: Delete the old one then click "Scan for hardware changes" to reinstall -> update driver
-> Search services.msc -> restart Printer Spooler



#Ticket 2: The device, \Device\Harddisk0\DR0, has a bad block ( Windows 1- 64bit OptiPlex 9020 )

OptiPlex 9020 -> a old dell computer


Troubleshooting: ( see more details on YouTube how to use chdsk )
( Warning: Do not do this on disk C, while using OS Dismount = Disconnect )
-> Run as administrator CMD: `chdsk  /r` -> locate bad sectors and recover readable information.
-> `chdsk /f`: fix errors -> It will be freezing for a moment

If can't fix -> maybe a bad hard rive that needs to be replaced, suggest an SSD storage to replace HDD.



#Ticket 3: Outlook searh issue 2013: Searching received emails like today's or yesterday's, outlook displaying the weeks old emails.

Possible problems:
+ Maybe outlook is getting errors -> ask administrator


