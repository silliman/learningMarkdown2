# Delete the resources you created in the IBM Cloud-based labs

!!! danger

    We get a little nervous when telling students to delete things on their IBM Cloud account, but we promised to help you minimize any costs associated with this lab. 

    Be aware that the these cleanup instructions make two important assumptions.  

    The first is that you created a new Virtual Private Cloud (VPC) just for these labs so that you can delete the entire VPC without impacting any other users or resources.  

    The second assumption is that you created a new _IBM Log Analysis_ instance just for these labs so that you can delete the IBM Log Analysis instance without impacting any other users or resources.

    ==If you used a pre-existing virtual private cloud or a pre-existing IBM Log Analysis instance, do not follow these instructions verbatim but take extreme care instead in deleting only the resources you created for theses labs.  Our assumption is that if you were confident enough to use pre-existing resources, then you are confident enough to know what to delete afterwards and what not to delete. Please, err on the side of caution-  ask an instructor or the appropriate person(s) in your company for help if you are unsure!==

## Delete your virtual private cloud

!!! Warning

    Skip this step if you used a pre-existing Virtual Private Cloud that you or others in your company are using for any purposes other than these labs.  

Navigate to your Virtual Private Cloud and from the "vertical dots" icon on the right, select the **Delete** choice.

<img src="../cleanup010.png" height="2039" width="800" />

You may be presented with a list of attached resources that will also be deleted when you delete the VPC, similar to what is shown here:

<img src="../cleanup020.png" height="1799" width="1368" />

1. **Select** the checkbox to confirm that you agree to these attached resources.

2. Click the blue **Delete all** button in the lower right to start the deletion.

Wait until you see the message that your VPC and all attached resources have been deleted:

<img src="../cleanup030.png" height="1764" width="746" />

## Delete your storage volume

After deleting your VPC your storage volume remains assigned to you.  To delete it, select **Block storage volumes** to see your data volume in a list:

<img src="../cleanup040.png" height="2477" width="1193" />

From the "vertical dots" menu on the right select the **Delete** action:

<img src="../cleanup050.png" height="2036" width="1155" />

Confirm the deletion of your storage volume:

<img src="../cleanup060.png" height="1612" width="444" />

## Delete your IBM Log Analysis instance

!!! Warning

    Skip this step if you used a pre-existing IBM Log Analysis instance that you or others in your company are using for any purposes other than these labs

1.  Go to your resources list

2.  Expand  the **Logging and Monitoring** category

3.  Find your IBM Log Analysis instance and find the **Delete** option from the 'vertical dots' menu
 
The below screenshot shows what it looks like after completing the above three steps:

<img src="../cleanup070.png" height="2373" width="987" />

4.  Click **Delete** and confim the deletion if prompted.

## Congratulations!!

Congratulations on finishing the labs and cleaning up afterwards.  We hope you learned something useful in the labs and we know there is always room for improvement, so constructive criticism on the current labs or ideas for new labs are always welcome, either by a GitHub issue if you are comfortable with that (click the GitHub link on the right side of the banner at the top of the page) or via [email to the authors](../../../index.md#workshop-authors){target="_blank" rel="_noopener"}.


