<h1>JWipe - Disk Sanitization</h1>

<h2>Description</h2>
Project consists of a simple PowerShell script that walks the user through "zeroing out" (wiping) any drives that are connected to the system. The utility allows you to select the target disk and choose the number of passes that are performed. The PowerShell script will configure a diskpart script file based on the user's selections and then launch Diskpart to perform the disk sanitization.
<br />


<h2>Languages and Utilities Used</h2>

- <b>PowerShell</b> 
- <b>Diskpart</b>

<h2>Environments Used </h2>

- <b>Windows 10</b> (21H2)

<h2>Tutorial:</h2>

<p align="center">
<h3>Implementing Group Policy</h3>
1. Group policy is a security tool that allows administrators to implement specific configuration on users and computers within a domain. In this part of the tutorial, we will add a policy that automatically locks the user workstation after a minute of inactivity. This helps prevent potential attackers from accessing sensitive information when users forget to lock their computer when away from their work area. 
<br />
<br />
2. Connect to the domain controller(DC) through virtualbox that was previously setup.

<br />
<br />
3. Open Server Manager and under “Tools” select “Group Policy Management”  
<img src="https://i.imgur.com/tcTyMUE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
4. Expand the “Domains” folder and expand the domain name. Select the organization unit(OU) where you want to apply the group policy. In this case, right click the “Users” OU and click “Create a GPO in this domain, and Link it here…”
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
5. A new GPO pop up will appear and allow you to name it. We will call it “Computer Lockout”. 

<br />
<br />
6. Right click on the new GPO and click edit.  
<img src="https://i.imgur.com/JL945Ga.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
7. Under User Configuration, expand the “Administrative Template” folder and expand the control panel folder. 

<br />
<br />
8. Select “Personalization”.  
<img src="https://i.imgur.com/AeZkvFQ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
9. Right click “Enable screen saver” and press “Edit”. Select “Enable” and press “Apply” and “Ok”. Do the same with the “Password protect the screensaver” setting. 
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
10. The last setting we will enable is “Screen saver timeout”. Enable that setting as well, and specify the value “60 seconds” before the screen saver is launched. Click “Apply” and “Ok”. 
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
11. It may take a few minutes for the new policy to take effect. Alternatively, you can manually force the policy by logging into the “Client” VM we created and open the command prompt as an administrator. Then type the command “gpupdate /force”.
<br />
<br />

<h3>Duo 2-Factor Authentication For WIndow Server/VM</h3>
1. In this section, we will use Duo to secure our virtual machines. We’re going to leverage Duo’s free tier of MFA products, Duo MFA, to add another layer of protection to our environment. Browse to https://duo.com/ and click on the  “Free Trail” button. Run through the requested information to start your free trial.
<br />
<br />
2. After setting up your free Duo trial, We need to add users to our Duo console. To do this with Duo free tier, find the “Groups” tab on the left side and click “Add Group”.
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
3. Enter a name and description for the Group. Once you’ve entered both a name and a description, hit the “Add Group” button.
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
4. Next, we will add users manually by going to the “Users” section. Click “Add Users”.
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
5. Enter a username that matches the logon name of the user in Active directory. In my homelab, the user is adm_jzhang. Scroll down and enter your email address and add the user to the group we created in the previous step(VM Admin Group).
<br />
<br />
6. Once changes have been saved, scroll back to the top and click the “Send Enrollment Email”. This email will provide the user to download Duo and activate it. 
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
7. Download the Duo Mobile app on your smartphone. Click on the enrollment link and scan the QR code from your smartphone to activate. You can verify if your device is enrolled by going under the user account you created earlier(adm_jzhang).
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
8. Go back to the Duo admin site. Locate the “Applications” section on the left column and  click “Protect an Application”.
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
9. Search for “Microsoft RDP” since we will be adding 2FA to our window system. 
Click Protect. 
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
10. Once you click “Protect”, Duo will generate an integration key, a secret key and an API hostname. Make sure to save this information securely!
<br />
<br />
11. Scroll down and find Global Policy.Click “Edit Policy”. 
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
12. By default, “Require Enrollment” is selected. Let’s change it to “Deny Access” – Any user that is not enrolled in Duo will be denied access to the system you are securing. Click on Save Policy.
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
13. Next we need to install the Duo Windows Client on our Virtual Machine. We will login to our Domain Controller VM.
<br />
<br />
14. Open a browser and paste this website: https://dl.duosecurity.com/duo-win-login-latest.exe
<br />
<br />
15. The executable will download and we will run through the installation. Click “Next” from the welcome page.
<br />
<br />
16. Enter the API Hostname from the previous step(Step 9). Click the “Next” button.
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
17. Enter both the integration key and secret keys that were generated from Duo from the previous step(Step 9). Click “Next”. 
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
18. Check the “Use auto push to authenticate if available”. This will automatically trigger the app on your smartphone to prompt. Click “Next”.
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
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
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
23. Let’s try logging into the same VM with a user that isn’t enrolled in Duo. Once you enter those credentials, you’re automatically denied.
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
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
