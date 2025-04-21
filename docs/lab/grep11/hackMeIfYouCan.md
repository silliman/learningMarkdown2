# Demonstrate the protection of the Secure Execution-enabled HPVS 2.1.x guest

## Overview of this section

In this section you will demonstrate the protection offered by the Secure Execution-enabled HPVS 2.1.x guest in contrast to the ease in which a malicious insider can eavesdrop on a standard KVM guest.

## Log out of your Ubuntu KVM guest 

All of the work in this section is performed on the RHEL 8.5 host, so log out of your Ubuntu KVM guest. You have finished your work in the Ubuntu KVM guest for this lab:

   ``` bash
   exit
   ```

## Switch to your RHEL host terminal session 

Switch to your terminal tab or window for your RHEL host session:

<img src="../../../images/RHELHost.png" width="351" height="217" />

You should be logged in still if you have been following the lab in order in one sitting, but if you need to log in again the command is `ssh -l ${StudentID} 192.168.22.64`

## Snoop into your standard KVM guest with ease

A systems administrator at the host level does not have a difficult time getting into a standard KVM guest's business.  Try this command to dump the entire address space of your Ubuntu KVM guest in the home directory of your lab userid:

   ``` bash
   cd ${HOME} && sudo virsh dump $(whoami) $(whoami).dump
   ```

This will take a little while but you have just dumped the entire memory of your KVM guest.

Look at the file size:

   ``` bash
   ls -lh $(whoami).dump
   ```

We suspect that a malicious actor might have a few more tools in their toolbag than what we will show you here, but try this command:

   ``` bash
   sudo strings $(whoami).dump
   ```

The above command will print out all of the strings it recognizes in the memory dump.  You are probably getting tired of seeing them pass by on your terminal screen, so type `Ctrl-C` when you want your command prompt back.

Try this command to see how many strings were found in the file:

   ``` bash
   sudo strings $(whoami).dump | wc --lines
   ```

Your output may differ, but when we tried this command while writing up the lab, we had 2,397,409 strings found in our dump.  Now we didn't dig much deeper than this, but it's possible that a motivated hacker might find something among those millions of strings with which to make mischief.

## Go ahead and try to hack me says the HPVS 2.1.x guest

Try to snoop on your Secure Execution-enabled Hyper Protect Virtual Servers 2.1.x guest that is running your GREP11 Server. See what happens:

   ``` bash
   suffix=$(temp=$(whoami) && echo ${temp: -2}) ; \
   sudo virsh dump grep11se${suffix} grep11se${suffix}.dump
   ```

??? example "Shot down in flames, ain't it a shame"

    ```
    error: Failed to core dump domain 'grep11se01' to grep11se01.dump
    error: internal error: unable to execute QEMU command 'migrate': protected VMs are currently not migrateable.
    ```

If your name or address or credit card number or social security number or bank card PIN code or cryptocurrency wallet was in memory, would you have preferred it to be in memory in a standard KVM guest or in a Secure Execution-enabled Hyper Protect Virtual Servers guest?  If you answered  _standard KVM guest_, then please return to the beginning of the lab and start over.  If you chose _SE-enabled HPVS guest_, then congratulations, you have successfully completed the lab!! (Except for cleanup).

Please proceed to the next section of the lab for lab cleanup.

