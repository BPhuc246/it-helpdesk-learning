#Ticket 1: Need access to shared drive

--> Make sure that added by user sent that shared drive


#Ticket 2: Email is not working ? 
( There are no error response or screenshot error so -\_(@_@)_/-

--> Ask to connect remote user's computer


#Ticket 3: Error from outlook: "We Couldn't find a mailbox for this account. Either they don't have a mailbox or don't have a license assigned." )

This error almost always comes down to one of two things: 
+ The account lacks a valid Exchange Online license
+ Microsoft 365 hasn't finished provisioning the mailbox yet

1. Verify and Assign an Exchange License

-> Open M365 Admin Center: admin.microsoft.com -> Locate the User -> Check Licenses and Apps
( Select the Licenses and apps tab. Ensure an active license with Exchange is assigned (e.g., Business Standard, Exchange Online Plan 1/2, Enterprise E3). Expand Apps at the bottom and make sure Exchange Online is explicitly checked. ) -> Save and Wait ( It can take anywhere from 5 to 30 minutes for Exchange Online to finish creating the mailbox in the background. )

2. Other Potential IT Causes & Fixes

Newly Created Account ( Account was made < 30 mins ago. )
-> Exchange creation queues can take up to 24 hours in rare edge cases. Wait 15–30 minutes before re-testing.

Hybrid On-Premises Sync ( User created in local AD without Exchange attributes. )
-> Ensure RemoteRecipientType is set to 4 (ProvisionMailbox) via local Exchange PowerShell or Exchange Management Console before syncing with Microsoft Entra Connect.

Guest/Shared Account Conflict ( Trying to sign in via standard Webmail. )
-> Verify the UPN matches their primary email address, not an alias or guest account domain (user@domain.com vs. user_domain.com#EXT#@tenant.onmicrosoft.com).

Stuck Provisioning State ( License is assigned, but status reads "Pending". )
-> Unassign the license, wait 5 minutes, reassign it, and click Save.
