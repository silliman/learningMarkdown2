# Overview and Pre-reqs

## Overview

These labs provide a gentle introduction to the art of creating a [*contract*](https://cloud.ibm.com/docs/vpc?topic=vpc-about-contract_se&interface=ui){target="_blank" rel="noopener"} for a Hyper Protect Virtual Servers instance. The contract you specify to a Hyper Protect Virtual Servers instance specifies the application workload you wish to run and the environmental characteristics such as where to send log messages from your instance.

A best practice is to specify a contract that is encrypted and signed.  These labs start simple-  with an unsigned, plain text contract.  Such a contract would be hard to justify in a production environment, but is a great starting point for learning what is involved with creating a contract.

Then each subsequent lab increases the complexity of the contract such that our final lab guides you to the best practice of creating an encrypted and signed contract.

!!! Warning "Caution- you may incur charges from IBM Cloud for activities performed in these labs "

    You may incur charges for the artifacts created during the lab.  If you do the labs in one sitting and delete your lab-related resources after the lab then any charges should be negligible. These charges are your responsibility.  Each lab ends with instructions to delete these lab artifacts in order to minimize any costs you might incur.

The flow of the labs can be summarized as:

1. Create prerequisite resources on IBM Cloud such as a Virtual Private Cloud and an IBM Log Analysis instance.

2. Create a Hyper Protect Virtual Server instance using a plain text contract.

3. Create a Hyper Protect Virtual Server instance where the workload provider encrypts their portion of the contract but the environment provider provides their portion in plain text.

4. Create a Hyper Protect Virtual Server instance where both the workload provider encrypts their portion of the contract and the environment provider encrypts their portion of the contract.

5. Create a Hyper Protect Virtual Server instance where both portions of the contract are encrypted and then signed by the environment provider.

6. Delete the resources created during the labs.


## Prerequisites

### IBM Cloud account 

These labs assume that you have an IBM Cloud account and have the authorization needed to create the artifacts required by the labs.

All IBM Cloud resources are created with the IBM Cloud web UI from your web browser.

You must be able to create the following IBM Cloud artifacts in order to complete this lab.


| Resource | Comments |
|---|---|
| IBM Log Analysis | required as a target for log messages from your Hyper Protect Virtual Server instances |
| Virtual Private Cloud | Must reside in London, Madrid, Sao Paulo, Tokyo, Toronto or Washington, D.C |
| Subnets for VPC | created when you create your Virtual Private Cloud |
| Public gateways for VPC | needed so that your instances can communicate with your IBM Log Analysis instance |
| Access control lists for VPC | created automatically when you create your Virtual Private Cloud |
| Security groups for VPC | created automatically when you create your Virtual Private Cloud |
| Virtual Server for VPC | The resource type of your Hyper Protect Virtual Server instances |
| Block storage volumes for VPC | Used to provide persistent disk storage |
| Hyper Protect Virtual Server for Classic | *Optional*- can be used as a _prep system_ if you can provide an SSH key pair |
| Virtual Server for Classic | *Optional*- can be used as a _prep system_ if you cannot provide an SSH key pair |

### Prep system to prepare contracts for Hyper Protect Virtual Server instances

You must have access to a Linux or MacOS system in order to create the contracts required by the lab.

If your laptop or workstation is not running Linux or MacOS there are some alternatives available to you:

* If you have a Windows system you can try to use the [Windows Subsystem for Linux](https://learn.microsoft.com/en-us/windows/wsl/){target="_blank" rel="noopener"} or you can create a Linux server elsewhere. 

* We provide instructions on how to create a Linux server using *Hyper Protect Virtual Server for Classic* in a [subsequent section](./prepsystem.md){target="_blank" rel="noopener"} of the lab. This is a server running on LinuxONE servers in IBM Cloud and uses IBM's earlier, Secure Service Container-based implementation of Confidential Computing.  You may be able to provision this for free- each account can have two free instances at any point in time. You must provide the public key portion of your SSH key pair when provisioning this. 

    !!! Note "An attempt at clarification"

        This lab focuses on the newer, Secure Execution-based implementation of *Hyper Protect Virtual Server for IBM Cloud VPC*- not the earlier, Secure Service Container-based implementation of *Hyper Protect Virtual Server for Classic* that we are suggesting may be used as your *prep system*.  We'd prefer that you use your own system for the *prep system* if possible.  Why? Because it will be easier for you to save the lab artifacts that way.  At the risk of confusing you, we suggest *Hyper Protect Virtual Server for Classic* as a *prep system* for a key reason-  you can (probably) provision one for free! 

* If you cannot provide your SSH key pair's public key portion, you can provision a Linux server using *Virtual Server for Classic* that will enable access via a password instead of an SSH key.  This is a server running on x86 architecture and you may incur charges for this service.



