# Create a public gateway

You need a public gateway so that the Hyper Protect Virtual Servers for IBM Cloud VPC instances that you will create in the labs can send log messages to your IBM Log Analysis instance which is running outside of your VPC.

1. Click the **Public gateways** link on the left and you should see that you currently have no public gateways defined.
 
2. Click the blue **Create** button in the upper right:

<img src="../gw010.png" width="2457" height="992" />

Choose the correct geography (not shown in the screen snippet below), region, and zone.  Give your public gateway a name, we've chosen *lab-was-pubgw*. Ensure that your virtual private cloud is selected, and then click the blue **Create** button.

<img src="../gw020.png" width="452" height="885" />

As shown below, you'll be shown your new public gateway.  Note that in the _Attached subnet_ column there is no entry. You need to attach a subnet to this public gateway.  To do so, click the _vertical dots_ icon on the right and choose the _Attach_ popup menu item, as indicated here:

<img src="../gw030.png" width="1337" height="383" />

Ensure that your subnet is selected and click the blue **Attach** button:

<img src="../gw040.png" width="799" height="304" />

You should see that your subnet is now attached to your public gateway:

<img src="../gw050.png" width="1351" height="275" />

Click the **Next** link at the lower right to go to your next task, which is to create an *IBM Log Analysis* instance.
