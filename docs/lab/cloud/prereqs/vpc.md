# Create a Virtual Private Cloud

!!! Tip "We advise you to create a new Virtual Private Cloud for these labs"

    These labs assume you are starting with an account that has no Virtual Private Cloud (VPC) resources defined when you start the lab.  If your account has existing VPC resources then it is up to you to decide whether to use those existing resources when available or whether to create new resources for all parts of the labs. Using existing resources may help to avoid incurring costs, but at the risk of inadvertently impacting existing users of these resources- an essential consideraton if these resources are being used in production!  You'd also have to take extra care when deleting resources at the end of the labs.
Navigate to [IBM Cloud](https://cloud.ibm.com){target="_blank" rel="noopener"} and log in to the IBM Cloud Web UI.

Click on the _Navigation Menu_ icon (often informally referred to as the "hamburger" icon) in the upper left of the IBM Cloud Web UI.  From there, navigate to _VPC Infrastructure_ and _VPC Layout_ as shown here:

<img src="../vpc010.png" width="534" height="622"/>


The labs are written with the assumption that all resources used for the labs are created in the lab and then deleted at the end of the lab.  You'll have to tailor your implementation of the lab directions appropriately if you do use existing resources.

Here's a screen snippet showing the _VPC Layout_ screen when the account has no existing VPC infrastructure at the start of the labs:

<img src="../vpc020.png" width="801" height="933"/>

Click the **Create a VPC** link.

Choose one of the following six locations which support Hyper Protect Virtual Server for IBM Cloud VPC:  London, Madrid, Sao Paulo, Tokyo, Toronto or Washington, D.C.  These labs' instructions and screen snippets will show the Washington, D.C. region (_us-east_) in use, but you may use one of the other aforementioned regions as well- tailor your implementation of the directions appropriately if you do. 

The screen snippet below shows after we've chosen the _Washington DC_ region in the _North America_ geography and given the new VPC the name _lab-was-vpc_.  Throughout the labs we will often be using the naming convention _lab-region-resource type-optional description_. If you perform the lab in the Washington, D.C., region you will be able to use the instructions almost verbatim.  If you choose to use another region you can choose to use our naming convention and tailor the _<region>_ portion of the name appropriately, or you may choose any other naming convention that suits you.

<img src="../vpc030.png" width="536" height="951" />

Scroll down to see the list of subnets offered to you-  by default you will be offered three subnets- one for each of the three _availability zones_ within a region.  For our labs, in an effort to minimize costs, we've deleted two of the three subnets as we will limit our lab activities to a single availability zone.  If you wish to do likewise then click the rightmost icon of the two subnets you want to get rid of, as indicated in the below screen snippet:

<img src="../vpc040.png" width="1296" height="549" />

Your subnet section will look similar to this if you choose to work with only one subnet:

<img src="../vpc050.png" width="1288" height="268" />

You are now ready to click the blue **Create virtual private cloud** button on the lower right of your page. If this button is not enabled then you probably forgot to enter some required information such as a name for your VPC:


<img src="../vpc060.png" width="307" height="213" />

After clicking the button, your new VPC will be created and you'll be taken to a screen like this.  Notice that a *Default Access Control List (ACL)* and a *Default Security Group* will be created for you, both of which will have meaningless randomly-generated names.  You can accept them as-is for the labs.

<img src="../vpc070.png" width="1229" height="502" />

Click the **Next** link at the lower right of this page so you can move to the next section where you will create a public gateway within your new VPC.
