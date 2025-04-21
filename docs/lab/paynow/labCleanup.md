# Clean up the resources you created during the lab

All of the work in this section is performed on the RHEL 8.5 host:

<img src="../../../images/RHELHost.png" width="351" height="216" />

 You should already be logged in to it if you have been following this lab in order.

## Shut down your HPVS 2.1.x guest that was running PayNow demo

   ``` bash
   suffix=$(temp=$(whoami) && echo ${temp: -2}) ;\
   sudo virsh shutdown paynowse${suffix} 
   ```

## Shut down your standard Ubuntu KVM guest

Enter this command to shut down your standard Ubuntu KVM guest:

   ``` bash
   sudo virsh shutdown $(whoami)
   ```

## Delete the core dumps in the home directory of your userid on the RHEL 8.5 host:

   ``` bash 
   cd ${HOME} && rm -vf core.*
   ```

## Log out of the RHEL host:

   ``` bash
   exit
   ```

Thank you for cleaning up and congratulations on finishing the *PayNow Lab*!  We hope you enjoyed it and learned from it and we welcome your feedback on how to make it better.

If you haven't yet taken the *GREP11 with CENA4SEE Lab*, consider doing that lab now. 

If you have an IBM Cloud account, you are free at any time to do the *IBM Cloud-based labs* which are available from the *Next* link at the lower right of this page.  Please read the instructions carefully and be aware that your IBM Cloud account may incur modest charges as a result of doing those labs.  


