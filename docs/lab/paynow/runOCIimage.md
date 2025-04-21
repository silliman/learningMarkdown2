# Run OCI image

You will be performing this section from your Ubuntu KVM guest. 

Your window or tab should like like this (unless you customized the profile we provided you):

<img src="../../../images/KVMGuest.png" width="351" height="217" />

## List your OCI image

Run this command to see the OCI image that you created, as well as an image (_node:19_) that was pulled down and used as the _base_ image of your _paynow:latest_ image:

   ``` bash
   docker images
   ```

Your output will look like this: 

???+ example "Example output"

      ```
      REPOSITORY   TAG       IMAGE ID       CREATED              SIZE
      paynow       latest    fd801119534e   About a minute ago   934MB
      node         19        f2e8386523b1   3 months ago         926MB
      ```

## Run the OCI image

Run this command to start the PayNow app as a Docker container:

   ``` bash
   CONTAINER_ID=$(docker run -itd --rm -p 8443:8443 paynow)
   ```

The above command should have produced no visible output.  That is because we assigned the output of the *docker run...* command to the environment variable *CONTAINER_ID* instead of letting the output go to your terminal.  The output is simply the unique identifier Docker assigned to the container.  

This next command will show you the container ID:

   ``` bash
   echo ${CONTAINER_ID}
   ```

You will see output similar to this- 64 hex characters- but it will be different than the example shown here:

???+ example "Example Container ID"
      
      ```

      8ebe1f2020951b01986a3974944a8e7689982d6ac44e45a6f360e79da9d0fd50

      ```

*Note:* If your output from above is different in format, then it is probably an error message and you should seek help from an instructor if necessary.

Now you can display the log messages from your container with this command:

   ``` bash
   docker logs ${CONTAINER_ID}
   ```

You should see the following messages which indicate that the PayNow application successfully started:

???+ example "Log messages from starting the PayNow app"

      ```
      
      > hyper-protect-pay-now@1.0.0 start
      > node app.js
      
      ```

Please click *Next* on the bottom right of the page to continue.
