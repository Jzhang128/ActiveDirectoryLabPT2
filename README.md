<h1>Active Directory Lab Part 2: Group Policy & Duo Implementation</h1>

<h2>Description</h2>
In this part of the lab, we will focus on securing our domain controller by using Duo multi-factor authenication and setting group policy to minimize risk to workstations within our domain. Group policy is a security tool that allows administrators to implement specific configuration on users and computers within a domain. In this part of the tutorial, we will add a policy that automatically locks the user workstation after a minute of inactivity. In the second portion of the tutorial, we will leverage Duo MFA to add another layer of protection to our environment with "something we have" in addition to the admin credential login("something we know").
<br />

<h2>Prerequisite </h2>

- <b>Lab environment Setup. See Part 1.</b>
- <b>Create Duo account with free trial tier.</b>
<h2>Tutorial:</h2>

<p align="center">
<h3>Implementing Group Policy</h3>

1. Connect to the domain controller(DC) through virtualbox that was previously setup.

<br />
<br />
2. Open Server Manager and under “Tools” select “Group Policy Management”  
<img src="https://i.imgur.com/LWNr8IC.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
3. Expand the “Domains” folder and expand the domain name. Select the organization unit(OU) where you want to apply the group policy. In this case, right click the “Users” OU and click “Create a GPO in this domain, and Link it here…”
<img src="https://i.imgur.com/DnG97ZC.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
4. A new GPO pop up will appear and allow you to name it. We will call it “Computer Lockout”. 

<br />
<br />
5. Right click on the new GPO and click edit.  
<img src="https://i.imgur.com/nFnIoZG.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
6. Under User Configuration, expand the “Administrative Template” folder and expand the control panel folder. 

<br />
<br />
7. Select “Personalization”.  
<img src="https://i.imgur.com/bSFBXLh.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
8. Right click “Enable screen saver” and press “Edit”. Select “Enable” and press “Apply” and “Ok”. Do the same with the “Password protect the screensaver” setting. 
<img src="https://i.imgur.com/ZwxjTh6.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
9. The last setting we will enable is “Screen saver timeout”. Enable that setting as well, and specify the value “60 seconds” before the screen saver is launched. Click “Apply” and “Ok”. 
<img src="https://i.imgur.com/ZvnoKwq.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
10. It may take a few minutes for the new policy to take effect. Alternatively, you can manually force the policy by logging into the “Client” VM we created and open the command prompt as an administrator. Then type the command “gpupdate /force”.
<br />
<br />

<h3>Duo 2-Factor Authentication For Window Server/VM</h3>
1. Browse to https://duo.com/ and click on the  “Free Trial” button. Run through the requested information to start your free trial.
<br />
<br />
2. After setting up your free Duo trial, We need to add users to our Duo console. To do this with Duo free tier, find the “Groups” tab on the left side and click “Add Group”.
<img src="https://i.imgur.com/xRQFpEd.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
3. Enter a name and description for the Group. Once you’ve entered both a name and a description, hit the “Add Group” button.
<img src="https://i.imgur.com/NocpoOU.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
4. Next, we will add users manually by going to the “Users” section. Click “Add Users”.
<img src="https://i.imgur.com/vLn70vz.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
5. Enter a username that matches the logon name of the user in Active directory. In my homelab, the user is adm_jzhang. Scroll down and enter your email address and add the user to the group we created in the previous step(VM Admin Group).
<br />
<br />
6. Once changes have been saved, scroll back to the top and click the “Send Enrollment Email”. This email will provide the user to download Duo and activate it. 
<img src="https://i.imgur.com/y5pMkeD.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
7. Download the Duo Mobile app on your smartphone. Click on the enrollment link and scan the QR code from your smartphone to activate. You can verify if your device is enrolled by going under the user account you created earlier(adm_jzhang).
<img src="https://i.imgur.com/BaUrRAz.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
8. Go back to the Duo admin site. Locate the “Applications” section on the left column and  click “Protect an Application”.
<img src="https://i.imgur.com/GXOxCC1.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
9. Search for “Microsoft RDP” since we will be adding 2FA to our window system. 
Click Protect. 
<img src="https://i.imgur.com/0FIJSce.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
10. Once you click “Protect”, Duo will generate an integration key, a secret key and an API hostname. Make sure to save this information securely!
<br />
<br />
11. Scroll down and find Global Policy. Click “Edit Global Policy”. 
<img src="https://i.imgur.com/EPFWj7t.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
12. By default, “Require Enrollment” is selected. Let’s change it to “Deny Access” – Any user that is not enrolled in Duo will be denied access to the system you are securing. Click on Save Policy.
<img src="https://i.imgur.com/o1XtAWa.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
13. Next, we need to install the Duo Windows Client on our Virtual Machine. We will login to our Domain Controller VM.
<br />
<br />
14. Open a browser and paste this website: https://dl.duosecurity.com/duo-win-login-latest.exe
<br />
<br />
15. The executable will download and we will run through the installation. Click “Next” from the welcome page.
<br />
<br />
16. Enter the API Hostname from the previous step(Step 9). Click the “Next” button.
<img src="https://i.imgur.com/e1f62QO.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
17. Enter both the integration key and secret keys that were generated from Duo from the previous step(Step 9). Click “Next”. 
<img src="https://i.imgur.com/a3mbjzq.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
18. Check the “Use auto push to authenticate if available”. This will automatically trigger the app on your smartphone to prompt. Click “Next”.
<img src="https://i.imgur.com/5DWf3ON.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
19. We will leave “Smart card support” disabled. Click “Next”.
<br />
<br />
20. We will also leave “Enable UAC” disabled. Click “Next”.
<br />
<br />
21. Click “Install”. Once installation is finished, we can test out what this looks like when signing in with an enrolled Duo user. 
<br />
<br />
22. Once you enter your username and password, the Duo client screen appears on the screen. You should receive a notification on your smartphone to “Approve” the login. 
<img src="https://i.imgur.com/fvY7A9a.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />
23. Let’s try logging into the same VM with a user that isn’t enrolled in Duo. Once you enter those credentials, you’re automatically denied.
<img src="https://i.imgur.com/B4dnW4a.png" height="65%" width="65%" alt="Disk Sanitization Steps"/>
<br />
<br />

</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
