# Start Ubuntu KVM Guest 

## Overview of this page
 
This page will help you verify that your jumpbox is configured properly and then guide you to logging in to the RHEL Host from which you will start your student-assigned KVM standard Ubuntu guest.

## Verify the student-specific environment variables on your jumpbox

You will first ensure that two crucial environment variables are set on your jumpbox.  Under most circumstances, the instructors will have already set these variables for you.  These variables will enable you to enter all of the commands in this lab without modification- where student-specific information is required in a command, the command will contain environment variables that will be resolved with the student-specific information. 

Environment variables are set in three places:

1. On your jumpbox.  In most cases, the instructors will have configured your jumpbox with your student-specific environment variables.

2. You will have a userid on the RHEL host, and this userid has been configured with student-specific environment variables.

3. You will have your own KVM standard guest running Ubuntu, and your guest is also configured with student-specific environment variables.

### Set or verify the environment variable on your jumpbox for your student ID

The instructors should have guided you through the process of obtaining a RHEL Jumpbox where you will perform the lab.

!!! Note
    The jumpbox is running the RHEL operating system, but the OS on the jumpbox is largely irrelevant, and in order to avoid confusion with the RHEL host (the Linux LPAR on the IBM z15 server in the Washington Systems Center data center in Herndon, Virgina, USA) that you will use during the lab, we will drop the 'RHEL' and refer to the _RHEL jumpbox_ as just _jumpbox_ from now on.

On your jumpbox, open a terminal window.  You can do this by clicking on _Activities_ in the upper left corner of your jumpbox and then clicking the icon that looks like a terminal window.  This will bring up a window using the _RHEL Host_ terminal profile, so your terminal window should have a dark background with a green prompt for the font, similar to the image shown in the previous section of the lab.  You will use this window to perform work on the RHEL host, but before logging in you will ensure that an environment variable specifying your unique student ID has been set properly.

Each student has a unique userid assigned to them. It is likely set for you already. In an instructor-led class, your instructors will let you know if this has been set for you already.

Check this by entering this echo command:

``` bash
echo ${StudentID}
```

???- example "Example output for *student02* [click to expand me]"

      ```
      silliman@nat-147 ~ % echo ${StudentID}
      student02

      ```

If a value starting with _student_ and ending with a two-digit number is returned to you, then your jumpbox has been configured properly and you may scroll down a bit to the section **_Log in to the RHEL 8.5 host_**. 

If no output is returned, set this variable to the userid assigned to you by the instructor.  E.g., if the instructor assigned you the userid `student00`, enter this command:
    ``` bash
    export StudentID=student00
    ```
If you had to use the previous `export` command, repeat the prior `echo` command to ensure this was set correctly. Now, you should see your userid displayed:
    ``` bash
    echo ${StudentID}
    ```

???- example "Example output [click to expand me]"

      ```
      silliman@nat-147 ~ % echo ${StudentID}         
      student02

      ```

!!! Question "Why did you make me do this?"

    This way we could provide instructions throughout this lab that are generic enough that every student can just copy and paste most commands "as-is" from the lab guide. (At least that was our goal).

### Optional but highly recommended- add your StudentID environment variable to your shell startup script

**Note:** If your *StudentID* variable was already present then your shell startup script was already updated appropriately and you can scroll down to the section with the title **_Log in to the RHEL 8.5 host_**

If we had our way in supplying a system from which you are running the lab, you are probably using _bash_.

If you are using your own workstation or laptop, if it is running Linux you are probably either using *bash* or you are savvy enough to figure out which shell you are running. 

If you are running it on Apple hardware then you are probably running *zsh* or *bash* or are savvy enough to figure out which shell you are running.

If you are running on a Windows machine then we hope that you are using a modern enough version of Windows so that you can use the Windows Subsystem for Linux and pretend that you are using a Linux machine.

If you are running on an older Windows machine then you should ask your manager for a new laptop. If that doesn't work out for you then ask the instructors for help (but not for a new laptop).

If you are not sure what shell you're using, you can use this command to find out what your shell is:

   ``` bash
   echo ${SHELL}
   ```

???- example "Example output when using zsh [click to expand me]"

      ```
      silliman@nat-147 ~ % echo ${SHELL}
      /bin/zsh

      ```

Garrett uses `bash` 5.x on his Mac.  Barry uses `zsh` - `zsh` being the default shell on newer versions of MacOS.
Thus, we will show two commands to add the environment variable to your shell startup script, one for `bash` and one for `zsh`.  If you are using a different shell, we trust you'll be able to figure out the equivalent command.  

!!! Warning "The Copy Button is Your Friend!"

	Please enter the appropriate command exactly as shown using the copy button whenever possible.  Approximately 0.47% of students think they have to make the variable substitution before entering the command.  That doesn't end well.  This advice applies generally to every command in this lab unless we explicitly state otherwise.

For users of `bash`:
   ``` bash
   echo "export StudentID='${StudentID}'" >> "${HOME}/.bashrc" 
   ``` 

For users of `zsh`:
   ``` bash
   echo "export StudentID='${StudentID}'" >> "${HOME}/.zshrc"
   ```

!!! Question "Why did I just do that?"

	If you use more than one terminal window to do this lab, then this would allow new terminal windows to be set with this _StudentID_ variable so that you do not have to re-enter it.  This will be handy if you either want to use multiple terminal windows for the lab or if you need to open a new window due to an old one closing for whatever reason.  We are here to make your flight as comfortable as possible.



## Log in to the RHEL 8.5 host

You will now sign into our z15 LPAR running Red Hat Enterprise Linux 8.5.  This is a system that has been enabled for Secure Execution and so can run workloads provisioned with IBM Hyper Protect Virtual Servers 2.1.x.  

Use your terminal tab or window set aside for doing work on the RHEL host- the one that (by default) looks like this:

<img src="../../../images/RHELHost.png"/ width="351 height="216" />

Run this command:

   ``` bash
   ssh -l ${StudentID} 192.168.22.64
   ```

One of two things should happen:  

a. If you are on an instructor-provided system and the instructors have had the time to load it with an appropriate RSA private key that matches an RSA public key that has been loaded into your assigned userid's account on the RHEL host:

- you will be able to sign in without a password!

**OR**

b. If you are not on an instructor-provided system or we did not have a chance to load the parts of the RSA key pair in the appropriate locations

- you will be prompted to enter a password.  Your instructor will provide you a password by some clandestine means, surely we're not going to put it on a page on the Internet :shushing_face:!

???- example "Example messages upon login [Click me]"

      ```
      *
      *  IBM Washington Systems Center (WSC)   .....
      *    IBM Z and LinuxONE                 C C  /
      *                                      /<   /
      *                       ___ __________/_#__=o
      *                      /(- /(\_\________   \
      *                      \ ) \ )_      \o     \
      *                      /|\ /|\       |'     |
      *                                    |     _|
      *  Red Hat Enterprise Linux 8.5      /o   __\
      *                                   / '     |
      *                                  / /      |
      *                                 /_/\______|
      *                                (   _(    <
      *  KVM Hypervisor for Blockchain  \    \    \
      *       and Hyper Protect          \    \    \
      *       and Digital Assets          \____\____\
      *    on IBM Z and LinuxONE        ____\_\__\_\
      *                                /`   /`     o\
      *           "It's alive!"        |___ |_______|..o-o-o-(#)
      *
      Activate the web console with: systemctl enable --now cockpit.socket

      Register this system with Red Hat Insights: insights-client --register
      Create an account or view all your systems at https://red.ht/insights-dashboard
      Last login: Mon Feb 13 16:50:14 2023 from 192.168.215.147
      [student02@bczkvm(192.168.22.64) ~ [19:11:51] (0)]$ 
      ```

## Start your Ubuntu KVM guest

A KVM Guest has been defined for each student by the instructors.  This guest has the Ubuntu 22.04.2 operating system installed on it.  A very straightforward installation path was taken with no additional software packages selected during the installation. You will add additional software packages as necessary during the lab. This guest does not take advantage of the additional protection offered by Secure Execution and HPVS.  It could have, but you will already be creating another KVM Guest that is protected by Secure Execution and HPVS.  This also helps to make the point that you can run "standard", i.e., non-Secure Execution-protected guests, and Secure Execution-protected guests on the same LPAR.

Display your KVM guest's definition with this command:

   ``` bash
   sudo virsh dumpxml $(whoami)
   ```

We named your Ubuntu KVM guest the same as your userid on the RHEL host, which is why you can use the `whoami` command.

???- example "Example virsh dumpxml output [Click me]"

      ``` hl_lines="20"
      <domain type='kvm'>
        <name>student02</name>
        <uuid>531199d9-3671-424e-a9c9-74ff5ca3980b</uuid>
        <memory unit='KiB'>2097152</memory>
        <currentMemory unit='KiB'>2097152</currentMemory>
        <vcpu placement='static'>2</vcpu>
        <os>
          <type arch='s390x' machine='s390-ccw-virtio-rhel8.6.0'>hvm</type>
          <boot dev='hd'/>
        </os>
        <cpu mode='host-model' check='partial'/>
        <clock offset='utc'/>
        <on_poweroff>destroy</on_poweroff>
        <on_reboot>restart</on_reboot>
        <on_crash>destroy</on_crash>
        <devices>
          <emulator>/usr/libexec/qemu-kvm</emulator>
          <disk type='file' device='disk'>
            <driver name='qemu' type='qcow2'/>
            <source file='/var/lib/libvirt/images/hpvslab/student02/student02-ubuntu22.04.qcow2'/>
            <target dev='vda' bus='virtio'/>
            <address type='ccw' cssid='0xfe' ssid='0x0' devno='0x0000'/>
          </disk>
          <disk type='file' device='cdrom'>
            <driver name='qemu' type='raw'/>
            <target dev='sda' bus='scsi'/>
            <readonly/>
            <address type='drive' controller='0' bus='0' target='0' unit='0'/>
          </disk>
          <controller type='scsi' index='0' model='virtio-scsi'>
            <address type='ccw' cssid='0xfe' ssid='0x0' devno='0x0002'/>
          </controller>
          <controller type='pci' index='0' model='pci-root'/>
          <controller type='virtio-serial' index='0'>
            <address type='ccw' cssid='0xfe' ssid='0x0' devno='0x0003'/>
          </controller>
          <interface type='network'>
            <mac address='52:54:00:67:e5:c1'/>
            <source network='default'/>
            <model type='virtio'/>
            <address type='ccw' cssid='0xfe' ssid='0x0' devno='0x0001'/>
          </interface>
          <console type='pty'>
            <target type='sclp' port='0'/>
          </console>
          <channel type='unix'>
            <target type='virtio' name='org.qemu.guest_agent.0'/>
            <address type='virtio-serial' controller='0' bus='0' port='1'/>
          </channel>
          <audio id='1' type='none'/>
          <memballoon model='virtio'>
            <address type='ccw' cssid='0xfe' ssid='0x0' devno='0x0004'/>
          </memballoon>
          <panic model='s390'/>
        </devices>
      </domain>
      ```

Look for the your userid in the output of the *virsh dumpxml* command.  You'll see it in two places- at the top where it names your guest, and then within the filepath and filename of the *qcow2* image that provides your KVM guest.  


Run this command to start your Ubuntu KVM guest:

   ``` bash
   sudo virsh start $(whoami)
   ```

???- example "Expected output (for *student02*) "

      ```
      Domain 'student02' started

      ```

=== "Your domain (i.e., your KVM guest) has started"

	!!! Success "You are off to a smashing start! :rocket:"
		
		You should now open a new window or tab using the terminal profile named _KVM Standard Guest_. From your current terminal window, click _File_ on the menu bar, and then choose either _New Tab_ or _New Window_ based on your personal preference, but, in either case, choose the profile named _KVM Standard Guest_.  This will create a terminal tab or window with a light beige background and a gray font.  You'll be directed to use this new tab or window when doing work on your KVM Ubuntu guest and you'll be directed to use your original terminal tab or window when doing work on the RHEL host. 

=== "Your domain didn't start for whatever reason"

	!!! Failure "You have departed from the happy path... :disappointed:"

		Please ask your instructor for help.
