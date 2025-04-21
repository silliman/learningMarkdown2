# Create an IBM Log Analysis instance and save connection information

## Create an IBM Log Analysis instance

**Note:** You may use an existing IBM Log Analysis instance if you already have one.  It can be in any IBM Cloud region and availability zone.  If you are creating a new IBM Log Analysis instance for the lab, we suggest you create it in the same region and availability zone in which you created your virtual private cloud in the previous sections.

1. Click the **Catalog** link at the top of your IBM Cloud web UI
2. In the search box, start typing **IBM Log Analysis** until it appears in the list of search results
3. Click on it in that list

<img src="../log010.png" width="1156" height="374" />

Choose your location for the logging instance, the plan you prefer- the free *Lite* plan is sufficient for the labs- and accept the terms and conditions and then click the blue **Create** button:

<img src="../log020.png" width="1420" height="848" />

You'll see a screen like below.  (The name of this IBM Log Analysis instance is the default name provided when we created it-  unlike the resources we're creating within our virtual private cloud, we're not concerned about following a naming convention for this resource).

<img src="../log030.png" width="1627" height="556" />

Click the blue **Open Dashboard** button in the upper right.

## Retrieve your IBM Log Analysis instance's ingestion key

Click the *Install instructions* icon (that looks like a question mark) in the lower left of your IBM Log Analysis dashboard:

<img src="../log040.png" width="470" height="952" />

In the window that opens up, click the icon highlighted below in order to copy your IBM Log Analysis instance's ingestion key into your clipboard.  Paste it into a safe place for use later in the lab.  **Treat this ingestion key with care like you would a password, _especially if you are using an already existing IBM Log Analysis instance_**. (You do have the ability to return to this screen to retrieve it later if you lose track of where you paste it).  See the below screen snippet:

<img src="../log050.png" width="936" height="186" />

## Retrieve your IBM Log Analysis instance's host name

1. Scroll down on this same window and choose **rsyslog**
2. Use copy and paste to save the information that corresponds to the information highlighted in the screen snippet below.  (This information will not be highlighted on your screen until you select it as we did prior to taking this screenshot). You will be using this information later in the labs.  This information does not have to be kept secret.

<img src="../log060.png" width="802" height="671" />

You do not have to follow the instructions that the popup window is giving you, you only needed to copy the information as directed here in the lab. You can click the **X** in the upper right corner of the popup window or just click outside of the popup window so that it goes away.

Click the **Next** link in the lower right to continue.

