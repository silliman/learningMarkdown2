# Use the HPVS-based PayNow demo app

In your *RHEL host* terminal session, enter this command:

   ``` bash
   echo https://192.168.22.64:${Student_HPVS_PayNow_Port} 
   ```

This will fill out the URL with your student specific port for your HPVS Guest that runs the PayNow demo, which you just started up in the previous section.
Your port has been specified in an environment variable specified in your RHEL host login id's profile.
It should be a number from 29444 to 29463.
The formula is `29443 plus the two digit suffix of your RHEL host login id`. 

Right-click on the completed URL and choose *Open Link* and it will open up the PayNow demo in another tab in your Firefox browser on your jumpbox.

The demo uses a self-signed X509 certificate which your browser will not recognize, so you will have to "click through" any warnings that appear. For a "real world", production application, this would not be an acceptable setup, but it is okay for the purposes of this demo app-  the certificate will provide encryption of data that travels through the network, it just doesn't have the pre-established trust from your browser that it would have if it had been signed by a certificate authority that your browser or operating system was configured to trust.

This is the exact same application that you already worked with in a regular KVM guest, so the user interface is the same.  Add one or more fake payments-  if you need a refresher on using the UI, continue to read the below paragraphs which are repeats from a previous section when you used the app in your standard KVM guest.

Click either the *_PayNow_* link at the top right of the page or the *_PayNow_* button in the middle of the page.

You will be shown a "Payment Form" that has fields to enter a name, email address, credit card number, CVV, and amount, respectively. You can, and should,  enter fake values for everything.  Just enter any 16 digits for credit card number and any 3 digits for CVV.  Click the "Pay Now" button just underneath the payment amount field.

You will see a new icon underneath that contains the name and payment amount you entered, and a randomly chosen picture displayed underneath-  you may find that the picture shown under your name is radically different from what you look like in real life.  Note that these pictures are randomly chosen each time the page refreshes.  

Feel free to add another payment or two-  one is really enough for demo purposes, but it won't cause harm to enter more than one payment.

Click the *Next* link at the bottom right of this page to continue with the lab.
