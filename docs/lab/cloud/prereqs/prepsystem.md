# Establish a *prep system* for creating contracts

Here are our suggestions for what to use for your [*prep system*](../prereqs/index.md#prep-system-to-prepare-contracts-for-hyper-protect-virtual-server-instances){target="_blank" rel="noopener"}.  When you find a solution that works for you, you can move on the next section by clicking the *Next* link at the lower right of the page.

The lab authors have tested the lab instructions using the following prep systems:

| Operating System | Architecture | Comments |
|---|---|---|
| MacOS | Apple M1 Silicon | |
| Linux | Apple M1 Silicon | running as Ubuntu guest under MacOS |
| Linux | s390x | Hyper Protect Virtual Server for Classic free instance on IBM Cloud |
| Linux | x86-64 | Virtual Server for Classic paid instance on IBM Cloud |
| Windows 11 | x86-64 | using Windows Subsystem for Linux 2 with Ubuntu 22.04 installed |

## Use your workstation if it is running Linux or MacOS

If you already have access to a Linux system or to a MacOS system we encourage you to use it as your *prep system*.  *prep system* is a term we've coined in these labs to refer to the system on which you will create the contracts for your Hyper Protect Virtual Server instance. This could certainly be your laptop or workstation if you are running a Linux distribution or MacOS.

## If you are running Windows 

If you have a Windows system you could try one of the following options.

!!! Caution

    Although it should be a viable option, Option 1 has not been tested by the lab instructors.

1. You could run a Linux virtual machine under a virtualization hypervisor such as VMware or VirtualBox

2. You could run the Windows Subsystem for Linux under more recent versions of Windows such as Windows 10 or Windows 11

If your own laptop or workstation is not running Linux or MacOS or if you have Windows but are unable or unwilling to try one of the two suggestions from above, keep reading..

## Create a Hyper Protect Virtual Server for Classic instance on IBM Cloud 

If you do not have a suitable system already or would like to, you can create a Hyper Protect Virtual Server for Classic instance for free that will provide an Ubuntu system for you.  You will need to provide the public key portion of an SSH key pair when you provision this instance. 

**NOTE: Whether you already have a suitable system or choose to create one with the directions below, we will refer to this system as *your prep system* in the lab instructions, since you will use this system to *prepare* your contracts.**

To create this instance: 

!!! Note "We beg your pardon"

    If you are reading this, it means that we have yet to add some screenshots to these instructions.  Hopefully the text is sufficient to guide you to successful completion of this task.  If not, [let us know](../../../index.md#workshop-authors){target="_blank",rel="noopener"}.

1. Click on the **Catalog** link at the top of the page.

2. Start typing _Hyper Protect Virtual Server_ in the search box until **Hyper Protect Virtual Server for Classic** appears in the search results and then select it.

3. Choose the **Free** plan- select the *region* and *zone* you prefer. We prefer to use the same *zone* that we used for our virtual private cloud, if possible.  (This instance will not be running inside a VPC.)

4. Provide your SSH public key.

Once your Hyper Protect Virtual Server for Classic instance is available, you need to install two packages:

1. Log in to your instance via ssh.

2. Enter the following command:

    ``` bash
    apt install -y curl vim
    ```

## Create a Virtual Server for Classic instance on IBM Cloud

If for some reason you cannot create an SSH key pair, a *Virtual Server for Classic* instance allows you to log in with a password that it creates for you at provisioning time.  Using a password for logon is less secure than using an SSH key, and this service does not appear to offer a free tier, so this is our suggestion of last resort.  

The steps to create an instance are:

1. From the IBM Cloud Catalog, search for *Virtual Server for Classic* and then select it.

2. It doesn't matter where you provision it.  This instance does not run in your VPC.

3. Choose the Ubuntu 22.04 operating system.

4. On the provisioning screen, leave the *SSH key* field blank.

5. Click the *Create* button to create the instance.

After the instance is provisioned:

1. From the Resource List, expand the *Compute* category.

2. Select your new instance

3. Note your instance's public IP address

4. Click the tab for passwords and click the icon to show your root userid's password

5. Log in with this command:  `ssh -l root <your instance's public IP>` 

6. Enter root's password when prompted 
