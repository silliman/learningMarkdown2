# Create an instance

**Note:** There are often multiple ways to perform a task. The lab instructions may describe a particular way to go about things, but if you have prior experience with the IBM Cloud Web user interface and can perform the same task through different methods, feel free to do so.  The lab instructions themselves may provide alternative ways to accomplish the tasks in different sections of the labs.

These instructions assume you are logged in to the IBM Cloud Web UI.  If not, please log in before proceeding.

1. Go to your VPC

    One way to do this is to start by clicking the "hamburger" menu in the top left (the icon will then turn into an "x" as shown in the screen snippet below), then click **VPC Infrastucture** and then **VPCs**:

    <img src="../create010.png" width="624" height="875" />

    Then, select the link for your VPC from the list that is shown:

    <img src="../create020.png" width="531" height="287" />

2. Click the link to create a virtual server instance

    You may have to scroll down on the page- find and click on the "Create a virtual server instance" link:

    <img src="../create030.png" width="801" height="504" />

3. In the *Location* section, ensure that you select the *Zone* that contains your subnet. Give your instance a name in the *Name* field in the *Details* section.  We chose *lab-was-hpvs-lab4* in the screen shot below:  

    <img src="../create050.png" width="1692" height="1326" />

4. Scroll down and click the **Change image** link to select it:

    <img src="../create052.png" width="2170" height="636" />

5. On the *Select an image* screen, perform the following actions:

    1. Click the *IBM Z, Linux ONE* box.
    2. Toggle the slider on for "Run your workload with an OS and a profile for Secure Execution".
    3. Select the most recent image, *ibm-hyper-protect-container-runtime-1-0-s390x-13*.
    4. Click the blue **Save** button.

    <img src="../create053.png" width="2504" height="1746" />


6. Scroll down and in the *Advanced options* section, within the *Instance configuration* subsection, click the arrow at the right of the *User data* item.  Drag the lower right corner of the *User data* box that appears in order to enlarge it a bit, like we've done in the screen shot below:

    <img src="../create060.png" width="1075" height="685" />

7. At the end of the previous section of the lab, *Prepare the contract*, in the very last instruction, you displayed the contents of your `user_data.yaml`, on your prep system.  Go back to your prep system and copy the file contents that you displayed to your clipboard.  Then paste them into the *user data* box.  It should look similar to what is shown below. The screen shot below does not show the entire contract- the entire encrypted workload section and the top of the encrypted environment section is not visible in the screen shot, but we are showing the entire base64-encoded *envWorkloadSignature* section is shown:

    <img src="../create070.png" width="535" height="878" />

8. Go to your IBM Log Analysis Dashboard so you can verify that you receive log messages from the instance that you're about to create.

    Open another tab in your browser and go to cloud.ibm.com.  Log in if necessary.  Assuming you're logged in, the below screenshot provides guidance on one way to get to your list of IBM Log Analysis instances:

    <img src="../create080.png" width="550" height="895" />

    From the list, click the **Open dashboard** link:

    <img src="../create090.png" width="1204" height="261" />

9. Now, go back to the tab where you were setting up your virtual server instance-  click the blue **Create virtual server** button in the lower right.  **Reminder: you may incur costs for this action, and these costs are your responsibility. We will provide instructions to delete resources that are no longer needed to help you minimize any costs you might incur.**

    <img src="../create100.png" width="323" height="228" />

10. Verify that your instance has started to come up.

    Within a couple of minutes of starting your instance, you should see many messages appear in your IBM Log Analysis Dashboard. However, unlike the previous lab, the instance will not come up completely until you take further action- specifically, you need to attach the data volume that you created in the previous lab.  Continue with these instructions. 

11. Attach the disk volume that you created in lab:

    **Note:** The screen shots in this instruction are taken from the section for *lab 2* but the process is exactly the same for your *lab 4* instance, so don't be confused if you see '*lab 2*' in some of these screen snippets.

    Click on the link for your instance in the list under *Virtual server instances for VPC*:

    <img src="../create102.png" width="840" height="281" />

    Scroll down to the *Storage volumes* section and click the blue **Attach** button:

    <img src="../create104.png" width="1355" height="239" />

    On the *Attach storage volume* screen, select the *Block volume* that you created in the first lab (*lab-was-datavol* if you used the same name we used in these instructions) and then click the blue **Save** button at the bottom:

    <img src="../create106.png" width="446" height="912" />

    Now back on the screen displaying your instance information, you should see this data volume in the list of *Storage volumes*, similar to the screen shot below:

    <img src="../create108.png" width="1343" height="279" />

12. Now that you have attached the disk volume, startup continues and completes. Within your IBM Log Analysis Dashboard you should be able to see some messages greeting *Lab 1 Student* and some messages greeting *Lab 3 Student* (and maybe with some messages to *Lab 2 Student*- extra credit if you understand why you may or may not see greetings to her)- this is data written from your earlier instances from the first three labs.  (You may need to scroll up to find them.) Then, this instance will provide similar greetings to *Lab 4 Student*- this provides a demonstration of successfully reusing a data volume to provide data persistence across different virtual server instances. 

    If something went wrong in your setup of the contract that the hyper protect container runtime detects, your instance will automatically be stopped in five minutes.  So if you receive no messages within five minutes of starting your instance, it is time to contact your instructor.

13. Delete your instance.

    Your instance either started successfully- as evidenced by the display of earlier greetings to *Lab 1 Student* and *Lab 3 Student* and then periodic greetings to *Lab 4 Student*, or it failed to start successfully.  In either case you will want to delete your instance at this point.  

    The screenshot below shows how you can delete an instance if you are currently displaying it-  by clicking the blue **Actions** button in the upper right, then choosing **Delete**.  From there, follow the instructions to confirm your intention to delete the instance.  (This screen snippet is from the instance created in lab 1, but the process to delete the instance is the same.) 

    <img src="../create110.png" width="1389" height="482" />

14. Proceed to the next section to clean up all of the resources you created in these four labs.

