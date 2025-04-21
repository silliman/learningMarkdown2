# Clean up the resources you created during the lab

All of the work in this section is performed on the RHEL 8.5 host:

<img src="../../../images/RHELHost.png" width="351" height="216" />

 You should already be logged in to it if you have been following this lab in order.

## Shut down your standard Ubuntu KVM guest

Enter this command to shut down your standard Ubuntu KVM guest:

``` bash
sudo virsh shutdown $(whoami)
```

## Shut down your HPVS 2.1.x guest (your GREP11 server):

``` bash
suffix=$(temp=$(whoami) && echo ${temp: -2}) ; \
sudo virsh shutdown grep11se${suffix} 
```

## Delete the dump in the home directory of your userid on the RHEL 8.5 host:

``` bash 
cd ${HOME} && rm -vf $(whoami).dump
```

## Log out of the RHEL host:

``` bash
exit
```

Thank you for cleaning up and congratulations on finishing this lab!  We hope you enjoyed it and learned from it and we welcome your feedback on how to make it better.

If you wish to do the *PayNow Lab* then click the link on the lower right of the page. Otherwise, no further action is necessary on your part.

There is no need to click the `Next` link at the bottom as that will take you to a page that is for instructor usage.  Feel free to check it out though, as it will give you insight into the tools that we use to create and update the lab documentation. 

