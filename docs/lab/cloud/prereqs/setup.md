# Set up environment variables used throughout the four IBM Cloud-based labs

!!! Note "A note about support for these labs"

    The official documentation [states often](https://cloud.ibm.com/docs/vpc?topic=vpc-about-contract_se&interface=ui#hpcr_contract_encrypt_workload){target="_blank" rel="noopener"} to use an Ubuntu system to enter commands for contract preparation.  We have written the lab to work on Linux and MacOS.   

    There are so many Linux distributions that it is impossible to test on all of them, but we think that this lab should work on many distributions besides Ubuntu.  We will attempt to help if you are having problems on a Linux distribution other than Ubuntu, but we reserve the right to ask you to use an Ubuntu system.

    Similarly, since we have Apple laptops we wrote the lab to work on MacOS as well.  Since it works for us on MacOS we suspect it could work for you as well, but, again, we reserve the right to ask you to use an Ubuntu system if you ask for our help in troubleshooting.

    These labs are for educational purposes at a beginning or intermediate level and do not demonstrate all product features, and may demonstrate practices not suitable for a production environment.  Usage of these labs for any purposes other than education is at your own discretion and risk. 

    Support for these labs is solely in an educational context, and solely from the lab authors on a best-effort basis. In other words, there is no guarantee of timeliness of response from the authors to inquiries, nor even a guarantee that the authors will ever resolve any problems you have with these labs.  This informal support is outside of any official IBM support channels.

On your prep system, there are some environment variables that will be used in the four labs.

| Variable name | Description |
|---|---|
| LAB_WORKDIR | working directory for contract preparation |
| LAB_TAR | The variant of tar used on the prep system |
| LAB_WRAP | Wrap arguments to base64 on Linux; typically unset on MacOS |
| LOG_INGESTION_KEY | The ingestion key for your IBM Log Analysis instance |
| LOG_HOSTNAME | The hostname on IBM Cloud that hosts your IBM Log Analysis instance |

!!! Note

    Our instructions set these variables only for the duration of your terminal session.  If you do not finish the labs in one terminal session, then you will need to revisit this section to set these variables again when you resume.  Advanced shell users may be interested in setting these variables permanently in their shell if they plan to do the lab in more than one session, but setting this up is not covered here.

## Set LAB_WORKDIR

In order to avoid interference with other work, we want you to create a brand new directory for these labs- each of the four labs will use a subdirectory underneath the new directory.

1. This next instruction sets the environment variable to ${HOME}/cloudlabs.

    ``` bash
    LAB_WORKDIR=${HOME}/cloudlabs
    ```


2. This next set of commands will either successfully create this new directory and then change to it, or it will warn you to try again.  (If you are warned, retry step 1 above with a different name and then try these commands again.)

    ``` bash
    if [[ -e ${LAB_WORKDIR} ]]; then
       echo ${LAB_WORKDIR} already exists
       echo "  This may be appropriate if you are"
       echo "  resuming these labs in a new terminal session"
       echo "  and have already created this directory structure"
       echo
       echo Otherwise, choose a new value for LAB_WORKDIR
       echo "  or rename ${LAB_WORKDIR}"
       echo "  and try again"
    else
        mkdir ${LAB_WORKDIR} && \
        echo Fresh lab working directory created \
        && cd ${LAB_WORKDIR} && \
        echo Changed to working directory ${LAB_WORKDIR}
    fi
    ```

## SET LOG_TAR

On most Linux distributions you will have *tar* and it will be fine.

On most MacOS systems the default *tar* command is less desirable than a version of tar provided by the GNU project, called *gtar*. *gtar* is preferred for the labs.

Follow these instructions on your prep system.  The environment variables set by these instructions are in effect only for the terminal session in which you enter the commands. If you finish the labs in one sitting within the same terminal session, you will only have to enter these instructions once.  Otherwise, you can repeat these instructions as necessary.

!!! Note

	The following set of commands will set an environment variable that will be used throughout the labs. It will point to either *tar* or *gtar* or it will print an error message if it cannot find either one.

	``` bash
	if [[ -n "${LAB_TAR}" ]]; then
	for i in {1..6} ; do echo '******************' ; done
	echo ''
	echo "CAUTION: Prior value of LAB_TAR was ${LAB_TAR}"
	echo "   Take note of this value in the unlikely event it was"
	echo "   already set by another application on your system"
	echo "   in which case you may need to restore this value later"
	echo ''
	for i in {1..6} ; do echo '******************' ; done
	fi
	unset LAB_TAR && which tar 1>/dev/null 2>&1 && LAB_TAR=tar
	which gtar 1>/dev/null 2>&1 && LAB_TAR=gtar
	echo ''
	if [[ -z "${LAB_TAR}" ]]; then 
	echo "ERROR:  neither tar nor gtar was found" 
	else
	echo You will use ${LAB_TAR} for the labs
	fi
	```

## Set LAB_WRAP if necessary

The following set of commands will set an environment variable that will be used throughout the labs if the *base64* command supports the *wrap* option. (Typically, the wrap option will be supported on Linux systems but not on MacOS.)

``` bash
if [[ -n "${LAB_WRAP}" ]]; then
  for i in {1..6} ; do echo '******************' ; done
  echo ''
  echo "CAUTION: Prior value of LAB_WRAP was ${LAB_WRAP}"
  echo "   Take note of this value in the unlikely event it was"
  echo "   already set by another application on your system"
  echo "   in which case you may need to restore this value later"
  echo ''
  for i in {1..6} ; do echo '******************' ; done
fi
LAB_WRAP="--wrap 0"
echo test | base64 ${LAB_WRAP} 1>/dev/null 2>&1 || unset LAB_WRAP 
if [[ -z "${LAB_WRAP}" ]]; then
  echo "No wrap argument to base64 will be used in the labs"
else  
  echo "wrap argument ${LAB_WRAP} to base64 will be used in the labs"
fi
```

## Set LOG_INGESTION_KEY

This command differs based on whether or not you are using bash or zsh.  If you are unsure of which shell you are using, enter the command `echo $0` from your shell.

1. Set an environment variable for your IBM Log Analysis ingestion key. You saved this somewhere safe earlier in the lab.  If you lost track of it, revisit the section [Create an IBM Log Analysis instance](ibmlog.md/#retrieve-your-ibm-log-analysis-instances-ingestion-key){target="_blank" rel="noopener"} for the steps to retrieve it. Enter the command appropriate to your shell and you will be prompted to enter your IBM Log Analysis Ingestion Key.

	=== "bash"

		``` bash
		read -sp "Log Ingestion Key: " LOG_INGESTION_KEY && echo
		```

	=== "zsh"

		``` zsh
		read -s "LOG_INGESTION_KEY?Log Ingestion Key: " && echo
		```
       
## Set LOG_HOSTNAME

This command differs based on whether or not you are using bash or zsh.  If you are unsure of which shell you are using, enter the command `echo $0` from your shell.

1. Set an environment variable for the hostname of your IBM Log Analysis instance. You saved this somewhere safe earlier in the lab. If you lost track of it, revisit the section [Create an IBM Log Analysis instance](ibmlog.md/#retrieve-your-ibm-log-analysis-instances-host-name){target="_blank" rel="noopener"} for the steps to retrieve it. Enter the command appropriate for your shell and you will be prompted to enter the hostname of your IBM Log Analysis instance.
    
	=== "bash"

		``` bash
		read -p "Log Hostname: " LOG_HOSTNAME
		```

	=== "zsh"

		``` zsh
		read "LOG_HOSTNAME?Log Hostname: "
		```
      
Click the **Next** link in the lower right to begin the first lab.

