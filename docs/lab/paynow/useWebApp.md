# Use the PayNow demo app

In your *KVM Standard Guest* session, enter this command:

   ``` bash
   echo https://192.168.22.64:${Student_PayNow_Port} 
   ```

This will fill out the URL with your student specific port for your PayNow demo that you just started up in the previous section.
Your port has been specified in an environment variable specified in your guest's login profile.
It should be a number from 28444 to 28463.
For those who like arithmetic, the formula is `28443 plus the two digit suffix of your RHEL host login id`. 

Right-click on the completed URL and choose *Open Link* and it will open up the PayNow demo in another tab in your Firefox browser on your jumpbox.

The demo uses a self-signed X509 certificate which your browser will not recognize, so you will have to "click through" any warnings that appear. For a "real world", production application, this would not be an acceptable setup, but it is okay for the purposes of this demo app-  the certificate will provide encryption of data that travels through the network, it just doesn't have the pre-established trust from your browser that it would have if it had been signed by a certificate authority that your browser or operating system was configured to trust.

Click either the *_PayNow_* link at the top right of the page or the *_PayNow_* button in the middle of the page.

You will be shown a "Payment Form" that has fields to enter a name, email address, credit card number, CVV, and amount, respectively. You can, and should,  enter fake values for everything.  Just enter any 16 digits for credit card number and any 3 digits for CVV.  Click the "Pay Now" button just underneath the payment amount field.

You will see a new icon underneath that contains the name and payment amount you entered, and a randomly chosen picture displayed underneath-  you may find that the picture shown under your name is radically different from what you look like in real life.  (I told you to use a fake name!).  Note that these pictures are randomly chosen each time the page refreshes.  (Did I mention that this is just a demo?)

Feel free to add another payment or two-  one is really enough for demo purposes, but it won't cause harm to enter more than one payment.

Click the *Next* link at the bottom right of this page to continue with the lab.
