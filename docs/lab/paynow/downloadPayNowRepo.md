# Download PayNow GitHub Repo

### Open a new terminal window or tab with the _KVM Standard Guest_ profile
                
From your terminal window with the _RHEL Host_ profile, click on _File_ in the menu bar and then, according to your preferences, select either _New Tab_ or _New Window_, and, from either choice, select _1. KVM Standard Guest_

Choosing a new tab offers compactness but you won't be able to see both the _RHEL Host_ tab and the _KVM Standard Guest_ tab at the same time- you have to switch back and forth by clicking the appropriate tab header at the top.  Choosing a new window allows you to drag your windows or otherwise rearrange them so that you can see both windows on your screen.  The choice is yours.  Advanced students may wish to open more windows and tabs but the lab is written with the assumption that you have just one window or tab with the _RHEL Host_ profile and just one window or tab with the _KVM Standard Guest_ profile.

Your window or tab should like like this (unless you customized the profile we provided you):
        
<img src="../../../images/KVMGuest.png" width="351" height="217" />
                
You're now ready to log in to your Ubuntu KVM guest:

   ``` bash
   ssh -p ${Student_SSH_Port} -l student 192.168.22.64
   ```  
 
???- example "Example messages logging into Ubuntu KVM guest"

      ```
      silliman@nat-147 ~ % ssh -p ${Student_SSH_Port} -l student 192.168.22.64

      Last login: Thu Feb  9 19:32:09 2023 from 192.168.215.147
      student@ubuntu2204:~$
      ```

Download the _PayNow_ demo from GitHub:

   ``` bash
   git clone https://github.com/ibm-hyper-protect/paynow-website 
   ```

???- example "Example output from _git clone_"

      ```
      Cloning into 'paynow-website'...
      remote: Enumerating objects: 126, done.
      remote: Counting objects: 100% (30/30), done.
      remote: Compressing objects: 100% (26/26), done.
      remote: Total 126 (delta 10), reused 14 (delta 4), pack-reused 96
      Receiving objects: 100% (126/126), 1.53 MiB | 7.89 MiB/s, done.
      Resolving deltas: 100% (43/43), done.
      ```

Switch to the _paynow-website_ directory which was just created by the _git clone_ comamand:

   ``` bash
   cd paynow-website && pwd
   ```
   
???- example "Example output after switching directories"

      ```
      /home/student/paynow-website
      ```

You may proceed to the next section of the lab by clicking the *Next* link at the bottom right of this page.



