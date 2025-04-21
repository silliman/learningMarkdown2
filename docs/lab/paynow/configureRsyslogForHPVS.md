# Create rsyslog client certificate for your HPVS KVM guest for the PayNow demo

## Overview of this section

In the last section you created the following:

1. self-signed CA for the rsyslog service
2. server certificate for the rsyslog service

Your HPVS KVM guest will be a client to the rsyslog service, so in this section you will use your self-signed CA (1 above) to create a client certificate for your soon-to-be-created HPVS 2.1.x KVM guest for the PayNow Demo.


!!! Warning "Please read the instructions carefully"
	
	You'll be switching between both of your userids in this section:

	1. your _studentnn_ userid on the RHEL host where _nn_ is unique to you and between 01 and 20
	2. your _student_ userid on your Ubuntu KVM guest 

	We'll do our part by telling you when to switch.  Please do your part by reading the instructions carefully!


## If necessary, log in to the RHEL host

Switch to the correct terminal tab or window for your session on the RHEL host, the one that looks like this:

<img src="../../../images/RHELHost.png" width="351" height="216" />

You should already be logged in, but, if you need to log in for any reason, the command is `ssh -l ${StudentID} 192.168.22.64`

## Create certificate for client access to rsyslog

Steps 1 through 5 will be performed on the RHEL host.

1. Create a working directory and switch to it:

	``` bash
	mkdir -p ~/paynowLab/x509Work/rsyslogClient \
    && cd ~/paynowLab/x509Work/rsyslogClient
	```

2. Create a new private key:

	``` bash
	openssl genrsa -out client-key.pem 4096
	```

	???- example "Example output when creating RSA private key"

		```
		Generating RSA private key, 4096 bit long modulus (2 primes)
		..++++
		................................................................................++++
		e is 65537 (0x010001)
		```

	You should see output similar to what is shown above on the RHEL 8.5 host.  This same command was very quiet on your Ubuntu KVM guest.

3. Create a configuration file:

	``` bash
	cat << EOF > client.cnf
	[ req ]
	default_bits = 2048
	default_md = sha256
	prompt = no
	encrypt_key = no
	distinguished_name = dn

	[ dn ]
	C = US
	O = IBM WSC IBM Z and LinuxONE
	CN = SE-enabled HPVS KVM guest for PayNow demo

	EOF
	```

4. Create a certificate signing request (CSR):

	``` bash
	openssl req \
    -config client.cnf \
    -key client-key.pem \
    -new \
    -out paynowLab-client-req.csr
	```

5. Now you are going to use a pattern that is similar to a real-world pattern:
	
	You are going to send your CSR, which you just created on the RHEL host, to the Rsyslog CA which you created on your Ubuntu KVM guest:

	``` bash
	scp paynowLab-client-req.csr \
    student@${StudentGuestIP}:./x509Work/rsyslog/clients/.
	```

	???- example "Example prompt and output when sending file"

		```
		paynowLab-client-req.csr                                                          100% 1691     9.2MB/s   00:00 
		```

6. Switch to your terminal tab or window for your KVM Ubuntu guest.  Yes, this one:

    <img src="../../../images/KVMGuest.png" width="351" height="217" />


7. If you are doing the lab in one sitting, in order, then you are probably already logged in.  If you need to login for any reason the command is `ssh -p ${Student_SSH_Port} -l student 192.168.22.64`.  Steps 8 through 12 will be performed on your Ubuntu KVM guest.


8. You are now the CA registrar. Switch to your working directory and find the certificate signing request(CSR) that your customer (i.e., you) sent to you.  

	``` bash
	cd ${HOME}/x509Work/rsyslog/clients && ls -l paynowLab-client*.csr
	```

	???- example "Make sure your csr is listed"

		```
		-rw-r--r-- 1 student student 1691 Feb 14 01:47 paynowLab-client-req.csr
		```

9. You will do your due diligence and check the contents of the CSR:

	``` bash
	openssl req -noout -text -in paynowLab-client-req.csr
	```

	???- example "Example human-readable display of CSR"

		```
		Certificate Request:
			Data:
				Version: 1 (0x0)
				Subject: C = US, O = IBM WSC IBM Z and LinuxONE, CN = SE-enabled HPVS KVM guest for PayNow demo
				Subject Public Key Info:
					Public Key Algorithm: rsaEncryption
						Public-Key: (4096 bit)
						Modulus:
							00:b0:38:b1:27:ee:a2:9f:35:10:dd:74:b2:46:e6:
							b8:2a:e4:c9:7f:7d:b3:1d:45:96:7d:bc:9d:5a:90:
							06:64:da:b8:23:73:f3:99:46:54:a3:2a:a8:8e:db:
							10:96:7e:de:04:65:81:ee:68:f1:5e:4d:a1:3d:db:
							2e:44:3a:ff:e2:fe:60:86:ad:90:b9:91:f1:4b:94:
							c9:43:4a:85:56:32:2a:ab:c9:2a:71:de:b7:fc:40:
							e2:1b:aa:17:08:3a:65:4a:b8:70:d8:5c:b4:b6:ca:
							4f:8d:a1:d0:03:04:20:4e:7e:23:26:20:85:45:e4:
							21:ec:bb:f8:38:64:36:6d:7c:a1:8a:d8:af:14:1b:
							72:bf:e8:cd:2f:2d:2c:0b:5a:39:4e:53:41:f8:a0:
							33:91:be:90:64:18:1c:cf:c2:d9:a0:bf:78:db:88:
							19:6b:be:0c:10:76:fc:96:fb:01:14:f5:90:8a:4d:
							a8:0c:0b:10:29:1d:fb:45:e1:f2:59:b5:33:e5:20:
							f8:76:22:c8:4d:d1:55:dc:de:10:79:66:b8:ff:fa:
							ee:e4:03:a5:77:9d:50:a1:f2:60:35:84:e1:44:ef:
							f4:be:be:a9:1b:17:5e:26:4a:ea:24:7d:ff:80:d2:
							d6:95:4f:1b:b6:5e:22:c6:f2:81:17:bb:fe:ce:f6:
							44:29:79:4e:ad:76:04:db:a7:8d:a4:db:8c:e3:cd:
							bf:48:37:99:4c:1c:e0:26:0f:9f:8b:a4:1f:48:71:
							44:d0:5f:ae:c6:93:83:ab:b8:7b:7b:b8:f3:1d:f1:
							7d:34:3b:d5:32:f0:74:d9:ee:0b:cd:e7:a9:54:49:
							2b:23:dc:1a:57:ae:a3:03:d8:9c:47:14:75:0c:47:
							c6:be:e3:84:61:e7:15:b8:fe:0b:5f:53:a0:f6:a8:
							92:e4:2c:c9:51:43:de:3f:be:0f:a6:c7:44:1f:81:
							c9:c0:9d:d3:3a:42:2f:b0:52:59:47:c6:da:96:93:
							ba:e7:11:f4:dd:ba:75:46:86:b5:ef:ee:49:34:92:
							36:03:32:00:99:71:ed:83:1a:cd:3f:e3:79:7b:ee:
							04:49:59:aa:01:ce:4d:67:0e:0f:88:e6:62:82:1e:
							0b:07:01:cf:74:38:20:7b:0d:69:f5:2e:09:e5:84:
							20:f3:82:15:7f:a4:0d:ae:35:da:de:f2:a9:30:6e:
							3e:e3:72:26:b3:18:10:6c:d7:df:4c:fc:bf:e3:33:
							8c:c6:e3:83:04:db:c9:a9:a8:41:d2:97:be:a0:ec:
							bd:f1:89:18:eb:c5:e7:0b:fc:47:30:c8:e1:cd:e6:
							54:cd:f1:e7:c3:23:51:48:4f:fd:89:49:43:6d:96:
							e0:cc:69
						Exponent: 65537 (0x10001)
				Attributes:
					(none)
					Requested Extensions:
			Signature Algorithm: sha256WithRSAEncryption
			Signature Value:
				8d:0b:7b:fd:eb:6b:04:85:4f:b6:a8:81:8f:03:77:aa:26:7d:
				58:44:3a:af:1b:de:fe:73:52:38:7c:8b:e9:2d:47:34:93:31:
				9d:04:0b:08:3a:3c:92:72:cf:60:c6:3b:83:6c:9a:8d:7b:08:
				4b:13:44:8b:3c:14:58:f7:b6:26:8c:c8:d5:29:f7:f8:fb:98:
				a6:9f:78:6a:9a:f4:10:88:16:55:b8:83:ee:7d:1b:95:4c:02:
				77:10:9c:ca:61:01:c7:33:7f:65:81:6e:5e:18:25:a7:68:26:
				e0:5e:b5:6d:89:00:31:ed:21:bf:32:c8:13:4b:00:c6:a3:b5:
				5f:4d:13:4c:86:51:31:59:02:92:fd:88:30:3a:1f:ac:da:8b:
				82:25:b2:3d:7e:1d:1f:e3:55:aa:7a:26:1f:85:b6:86:87:34:
				9a:36:5e:55:0b:a9:6b:dd:77:56:4f:54:3e:27:ec:ac:a7:aa:
				ea:bb:86:40:a2:e8:af:88:77:5b:41:ec:42:0f:06:1e:7a:36:
				85:5f:36:14:d4:02:30:3c:27:8d:85:61:0c:93:83:a0:0d:cd:
				e7:c3:ac:02:d9:49:2e:58:a5:a1:24:33:56:a6:6c:e1:dc:dc:
				5b:11:32:65:84:08:70:7e:b2:52:2f:34:5e:83:46:45:8e:91:
				dc:4a:2d:31:2d:3e:3a:4a:03:a2:c4:02:d9:7f:6a:89:42:10:
				da:a4:7a:24:c2:2a:b5:fb:25:c8:1b:45:5f:f1:85:91:ca:0a:
				44:74:8f:60:44:86:e5:49:ab:d9:d1:d8:fa:0c:6d:1f:a8:7c:
				7c:6f:3f:66:0b:d9:46:5a:5c:4d:6e:79:7a:c2:eb:d2:02:a9:
				80:1e:66:53:b9:fd:5d:cf:6e:86:e7:58:7f:a4:74:31:cd:9f:
				b6:c2:b0:24:69:70:2f:9e:6e:4f:2d:74:53:8b:15:74:6c:08:
				bd:f0:b9:d2:e4:e0:a4:14:cf:b1:77:4d:6d:88:8a:ee:c7:6c:
				4b:15:c9:91:85:7d:a2:fa:cd:10:27:b3:27:fc:3b:f2:d1:86:
				57:33:0d:27:02:f2:c6:ab:46:8e:00:de:88:1f:59:d0:fd:6f:
				30:39:94:ba:af:17:89:37:df:0d:9e:1a:a7:d6:49:de:f5:40:
				61:e3:fa:52:70:3d:57:76:9f:fa:15:30:be:64:85:27:61:b0:
				02:9f:f6:20:c3:2d:1a:84:44:48:f6:08:db:f8:80:b9:ea:38:
				16:52:fe:2a:c0:f1:d9:8f:80:37:9f:fd:e2:ec:1e:99:c3:01:
				2d:b6:11:dd:5a:29:c8:02:2c:aa:d7:3f:78:c5:f2:fe:29:d7:
				98:f4:d1:1d:7e:9e:5d:8d
		```

10. Time to mint the certificate

	!!! Info "Due diligence check"
		For the purposes of this lab assume you've done a background check on the customer, checked their reviews on Yelp and NextDoor, looked at their Facebook page and LinkedIn profiles.  You're a little concerned with some of those college fraternity party pictures on Facebook, but, what the heck, their check has cleared the bank, so you decide to go ahead and _mint_ the certificate.

	``` bash
	openssl x509 -req -in paynowLab-client-req.csr \
          -days 365 -CA ../CA/ca.crt -CAkey ../CA/ca-key.pem \
          -CAcreateserial -out paynowLab-client.crt
	```

	???- example "Output from creating the certificate"

		```
		Certificate request self-signature ok
		subject=C = US, O = IBM WSC IBM Z and LinuxONE, CN = SE-enabled HPVS KVM guest for PayNow demo
		```

11. Your quality control department asks you to display the certificate before sending it to the customer:

	``` bash
	openssl x509 -noout -text -in paynowLab-client.crt 
	```

	???- example "It should look similar to this [click to expand]"

		```
		Certificate:
			Data:
				Version: 1 (0x0)
				Serial Number:
					29:4a:dd:c7:66:81:ab:5a:1d:bb:20:76:a0:25:34:90:21:93:40:6b
				Signature Algorithm: sha256WithRSAEncryption
				Issuer: C = US, O = IBM WSC IBM Z and LinuxONE, CN = CA for rsyslog for SE-enabled KVM guests
				Validity
					Not Before: Feb 14 01:58:14 2023 GMT
					Not After : Feb 14 01:58:14 2024 GMT
				Subject: C = US, O = IBM WSC IBM Z and LinuxONE, CN = SE-enabled HPVS KVM guest for PayNow demo
				Subject Public Key Info:
					Public Key Algorithm: rsaEncryption
						Public-Key: (4096 bit)
						Modulus:
							00:b0:38:b1:27:ee:a2:9f:35:10:dd:74:b2:46:e6:
							b8:2a:e4:c9:7f:7d:b3:1d:45:96:7d:bc:9d:5a:90:
							06:64:da:b8:23:73:f3:99:46:54:a3:2a:a8:8e:db:
							10:96:7e:de:04:65:81:ee:68:f1:5e:4d:a1:3d:db:
							2e:44:3a:ff:e2:fe:60:86:ad:90:b9:91:f1:4b:94:
							c9:43:4a:85:56:32:2a:ab:c9:2a:71:de:b7:fc:40:
							e2:1b:aa:17:08:3a:65:4a:b8:70:d8:5c:b4:b6:ca:
							4f:8d:a1:d0:03:04:20:4e:7e:23:26:20:85:45:e4:
							21:ec:bb:f8:38:64:36:6d:7c:a1:8a:d8:af:14:1b:
							72:bf:e8:cd:2f:2d:2c:0b:5a:39:4e:53:41:f8:a0:
							33:91:be:90:64:18:1c:cf:c2:d9:a0:bf:78:db:88:
							19:6b:be:0c:10:76:fc:96:fb:01:14:f5:90:8a:4d:
							a8:0c:0b:10:29:1d:fb:45:e1:f2:59:b5:33:e5:20:
							f8:76:22:c8:4d:d1:55:dc:de:10:79:66:b8:ff:fa:
							ee:e4:03:a5:77:9d:50:a1:f2:60:35:84:e1:44:ef:
							f4:be:be:a9:1b:17:5e:26:4a:ea:24:7d:ff:80:d2:
							d6:95:4f:1b:b6:5e:22:c6:f2:81:17:bb:fe:ce:f6:
							44:29:79:4e:ad:76:04:db:a7:8d:a4:db:8c:e3:cd:
							bf:48:37:99:4c:1c:e0:26:0f:9f:8b:a4:1f:48:71:
							44:d0:5f:ae:c6:93:83:ab:b8:7b:7b:b8:f3:1d:f1:
							7d:34:3b:d5:32:f0:74:d9:ee:0b:cd:e7:a9:54:49:
							2b:23:dc:1a:57:ae:a3:03:d8:9c:47:14:75:0c:47:
							c6:be:e3:84:61:e7:15:b8:fe:0b:5f:53:a0:f6:a8:
							92:e4:2c:c9:51:43:de:3f:be:0f:a6:c7:44:1f:81:
							c9:c0:9d:d3:3a:42:2f:b0:52:59:47:c6:da:96:93:
							ba:e7:11:f4:dd:ba:75:46:86:b5:ef:ee:49:34:92:
							36:03:32:00:99:71:ed:83:1a:cd:3f:e3:79:7b:ee:
							04:49:59:aa:01:ce:4d:67:0e:0f:88:e6:62:82:1e:
							0b:07:01:cf:74:38:20:7b:0d:69:f5:2e:09:e5:84:
							20:f3:82:15:7f:a4:0d:ae:35:da:de:f2:a9:30:6e:
							3e:e3:72:26:b3:18:10:6c:d7:df:4c:fc:bf:e3:33:
							8c:c6:e3:83:04:db:c9:a9:a8:41:d2:97:be:a0:ec:
							bd:f1:89:18:eb:c5:e7:0b:fc:47:30:c8:e1:cd:e6:
							54:cd:f1:e7:c3:23:51:48:4f:fd:89:49:43:6d:96:
							e0:cc:69
						Exponent: 65537 (0x10001)
			Signature Algorithm: sha256WithRSAEncryption
			Signature Value:
				9f:41:62:18:0f:db:0a:84:f6:59:bc:cd:22:e4:73:d6:18:b0:
				d0:4e:2a:da:8f:5c:46:06:f1:80:f3:4b:5d:cf:fe:a2:a3:97:
				cc:bd:96:8e:d2:d4:58:ab:ac:56:dd:6f:12:3b:52:a8:df:e5:
				4b:26:8e:92:b3:ed:28:9a:c3:28:6d:8b:f9:13:b0:01:fa:ed:
				8f:48:08:08:07:ac:8f:61:00:fc:53:41:9e:d2:53:c5:b8:d7:
				f4:f2:c9:cc:87:58:2d:48:f3:34:be:fe:0d:dc:9e:b6:11:74:
				18:da:92:db:db:b3:c6:4f:10:63:6c:4c:fb:5f:86:36:9a:a8:
				58:a9:d3:d9:7c:e0:8d:2f:96:f3:64:85:bf:8d:39:28:d2:06:
				8b:63:93:d6:42:e3:ad:6d:5b:2e:d3:5a:3d:3c:af:1e:a2:61:
				a0:d7:c7:a0:4f:b7:16:f1:3b:94:44:23:d8:16:6f:d7:38:36:
				84:10:31:ac:e7:17:43:2a:24:04:26:5b:46:50:03:05:7c:8d:
				cc:77:f5:c1:c1:e3:a2:04:4a:6d:7c:b2:c7:1e:e3:68:b0:4e:
				24:92:63:dd:bd:87:3c:af:8c:63:a5:ea:2f:41:90:67:79:e3:
				31:89:41:54:be:aa:44:89:45:65:85:2e:5e:b9:8c:af:7c:7e:
				0f:08:9a:9b:97:7c:6f:fc:9f:30:e8:0c:30:c4:be:7a:0c:7d:
				d0:45:71:f2:a7:35:c3:f9:f1:b7:2c:9e:1d:a1:da:3b:70:59:
				5b:05:93:a3:fc:59:41:c5:db:bf:0f:20:ec:15:ef:64:61:7e:
				52:3b:6a:a1:69:0b:73:93:52:a4:a3:79:ca:b3:0c:b8:cd:2b:
				59:b5:19:03:2e:21:b8:b5:d3:8d:05:2e:d6:0d:b0:9a:7d:e9:
				f9:e7:2b:96:3a:a5:e3:05:b6:d8:0a:e2:ea:2f:b0:02:42:ba:
				a5:9c:1d:d8:29:7f:3b:bd:7c:73:1a:4a:ae:ca:3a:1d:50:16:
				3a:42:3c:0c:23:6a:15:ed:57:01:88:f3:dc:b7:e3:3e:55:48:
				31:07:4f:38:9c:dc:10:71:e8:8c:82:d3:9e:a6:97:ca:70:20:
				e9:70:31:b2:46:09:79:03:20:93:b0:16:af:07:67:eb:0c:4f:
				b0:c0:a9:e8:eb:bc:ab:74:37:93:76:89:92:82:f3:48:a5:a1:
				16:62:39:2d:d5:79:67:e2:ea:6e:a9:6e:40:e1:7f:da:01:df:
				f0:4f:6f:a0:36:80:ae:ab:a2:4d:07:6e:ba:14:bf:85:82:50:
				e1:3d:df:64:bc:91:3d:60:c4:90:8c:3b:6f:0f:11:31:a6:5f:
				4f:36:5a:69:04:05:88:b5
		```

12. Now you send the certificate to the customer:

	``` bash
	scp paynowLab-client.crt \
    ${StudentID}@192.168.22.64:./paynowLab/x509Work/rsyslogClient/.
	```

	???- example "Example prompt and output from sending file"

		```
		client.crt                                                              100% 1907     9.7MB/s   00:00 
		```

13. Now switch back to your terminal tab or window for your session on the RHEL host.  A gentle reminder of what that tab or window looks like:

    <img src="../../../images/RHELHost.png" width="351" height="216" />

14. If you are doing the lab in one sitting, in order, then you are still logged in on the RHEL host.  If you need to login for any reason the command is `ssh -l ${StudentID} 192.168.22.64`.  Steps 15 and 16 will be performed on the RHEL 8.5 host.

15. Switch to the directory where the CA "sent" your new certificate and list the files:

	``` bash
	cd ${HOME}/paynowLab/x509Work/rsyslogClient/ && ls -ltr
	```

	???- example "File listing shows your client certificate (client.crt)"

		``` bash
		total 16
		-rw------- 1 student02 hpvs_students 3247 Feb 13 20:42 client-key.pem
		-rw-r--r-- 1 student02 hpvs_students  192 Feb 13 20:44 client.cnf
		-rw-r--r-- 1 student02 hpvs_students 1691 Feb 13 20:45 paynowLab-client-req.csr
		-rw-r--r-- 1 student02 hpvs_students 1907 Feb 13 21:06 paynowLab-client.crt
		```

16. Display your certificate in human-readable form to make sure your CA did their job correctly:

	``` bash
	openssl x509 -noout -text -issuer -subject -in paynowLab-client.crt
	```

	???- example "Example display of certificate"

		```
		Certificate:
			Data:
				Version: 1 (0x0)
				Serial Number:
					29:4a:dd:c7:66:81:ab:5a:1d:bb:20:76:a0:25:34:90:21:93:40:6b
				Signature Algorithm: sha256WithRSAEncryption
				Issuer: C = US, O = IBM WSC IBM Z and LinuxONE, CN = CA for rsyslog for SE-enabled KVM guests
				Validity
					Not Before: Feb 14 01:58:14 2023 GMT
					Not After : Feb 14 01:58:14 2024 GMT
				Subject: C = US, O = IBM WSC IBM Z and LinuxONE, CN = SE-enabled HPVS KVM guest for PayNow demo
				Subject Public Key Info:
					Public Key Algorithm: rsaEncryption
						RSA Public-Key: (4096 bit)
						Modulus:
							00:b0:38:b1:27:ee:a2:9f:35:10:dd:74:b2:46:e6:
							b8:2a:e4:c9:7f:7d:b3:1d:45:96:7d:bc:9d:5a:90:
							06:64:da:b8:23:73:f3:99:46:54:a3:2a:a8:8e:db:
							10:96:7e:de:04:65:81:ee:68:f1:5e:4d:a1:3d:db:
							2e:44:3a:ff:e2:fe:60:86:ad:90:b9:91:f1:4b:94:
							c9:43:4a:85:56:32:2a:ab:c9:2a:71:de:b7:fc:40:
							e2:1b:aa:17:08:3a:65:4a:b8:70:d8:5c:b4:b6:ca:
							4f:8d:a1:d0:03:04:20:4e:7e:23:26:20:85:45:e4:
							21:ec:bb:f8:38:64:36:6d:7c:a1:8a:d8:af:14:1b:
							72:bf:e8:cd:2f:2d:2c:0b:5a:39:4e:53:41:f8:a0:
							33:91:be:90:64:18:1c:cf:c2:d9:a0:bf:78:db:88:
							19:6b:be:0c:10:76:fc:96:fb:01:14:f5:90:8a:4d:
							a8:0c:0b:10:29:1d:fb:45:e1:f2:59:b5:33:e5:20:
							f8:76:22:c8:4d:d1:55:dc:de:10:79:66:b8:ff:fa:
							ee:e4:03:a5:77:9d:50:a1:f2:60:35:84:e1:44:ef:
							f4:be:be:a9:1b:17:5e:26:4a:ea:24:7d:ff:80:d2:
							d6:95:4f:1b:b6:5e:22:c6:f2:81:17:bb:fe:ce:f6:
							44:29:79:4e:ad:76:04:db:a7:8d:a4:db:8c:e3:cd:
							bf:48:37:99:4c:1c:e0:26:0f:9f:8b:a4:1f:48:71:
							44:d0:5f:ae:c6:93:83:ab:b8:7b:7b:b8:f3:1d:f1:
							7d:34:3b:d5:32:f0:74:d9:ee:0b:cd:e7:a9:54:49:
							2b:23:dc:1a:57:ae:a3:03:d8:9c:47:14:75:0c:47:
							c6:be:e3:84:61:e7:15:b8:fe:0b:5f:53:a0:f6:a8:
							92:e4:2c:c9:51:43:de:3f:be:0f:a6:c7:44:1f:81:
							c9:c0:9d:d3:3a:42:2f:b0:52:59:47:c6:da:96:93:
							ba:e7:11:f4:dd:ba:75:46:86:b5:ef:ee:49:34:92:
							36:03:32:00:99:71:ed:83:1a:cd:3f:e3:79:7b:ee:
							04:49:59:aa:01:ce:4d:67:0e:0f:88:e6:62:82:1e:
							0b:07:01:cf:74:38:20:7b:0d:69:f5:2e:09:e5:84:
							20:f3:82:15:7f:a4:0d:ae:35:da:de:f2:a9:30:6e:
							3e:e3:72:26:b3:18:10:6c:d7:df:4c:fc:bf:e3:33:
							8c:c6:e3:83:04:db:c9:a9:a8:41:d2:97:be:a0:ec:
							bd:f1:89:18:eb:c5:e7:0b:fc:47:30:c8:e1:cd:e6:
							54:cd:f1:e7:c3:23:51:48:4f:fd:89:49:43:6d:96:
							e0:cc:69
						Exponent: 65537 (0x10001)
			Signature Algorithm: sha256WithRSAEncryption
				9f:41:62:18:0f:db:0a:84:f6:59:bc:cd:22:e4:73:d6:18:b0:
				d0:4e:2a:da:8f:5c:46:06:f1:80:f3:4b:5d:cf:fe:a2:a3:97:
				cc:bd:96:8e:d2:d4:58:ab:ac:56:dd:6f:12:3b:52:a8:df:e5:
				4b:26:8e:92:b3:ed:28:9a:c3:28:6d:8b:f9:13:b0:01:fa:ed:
				8f:48:08:08:07:ac:8f:61:00:fc:53:41:9e:d2:53:c5:b8:d7:
				f4:f2:c9:cc:87:58:2d:48:f3:34:be:fe:0d:dc:9e:b6:11:74:
				18:da:92:db:db:b3:c6:4f:10:63:6c:4c:fb:5f:86:36:9a:a8:
				58:a9:d3:d9:7c:e0:8d:2f:96:f3:64:85:bf:8d:39:28:d2:06:
				8b:63:93:d6:42:e3:ad:6d:5b:2e:d3:5a:3d:3c:af:1e:a2:61:
				a0:d7:c7:a0:4f:b7:16:f1:3b:94:44:23:d8:16:6f:d7:38:36:
				84:10:31:ac:e7:17:43:2a:24:04:26:5b:46:50:03:05:7c:8d:
				cc:77:f5:c1:c1:e3:a2:04:4a:6d:7c:b2:c7:1e:e3:68:b0:4e:
				24:92:63:dd:bd:87:3c:af:8c:63:a5:ea:2f:41:90:67:79:e3:
				31:89:41:54:be:aa:44:89:45:65:85:2e:5e:b9:8c:af:7c:7e:
				0f:08:9a:9b:97:7c:6f:fc:9f:30:e8:0c:30:c4:be:7a:0c:7d:
				d0:45:71:f2:a7:35:c3:f9:f1:b7:2c:9e:1d:a1:da:3b:70:59:
				5b:05:93:a3:fc:59:41:c5:db:bf:0f:20:ec:15:ef:64:61:7e:
				52:3b:6a:a1:69:0b:73:93:52:a4:a3:79:ca:b3:0c:b8:cd:2b:
				59:b5:19:03:2e:21:b8:b5:d3:8d:05:2e:d6:0d:b0:9a:7d:e9:
				f9:e7:2b:96:3a:a5:e3:05:b6:d8:0a:e2:ea:2f:b0:02:42:ba:
				a5:9c:1d:d8:29:7f:3b:bd:7c:73:1a:4a:ae:ca:3a:1d:50:16:
				3a:42:3c:0c:23:6a:15:ed:57:01:88:f3:dc:b7:e3:3e:55:48:
				31:07:4f:38:9c:dc:10:71:e8:8c:82:d3:9e:a6:97:ca:70:20:
				e9:70:31:b2:46:09:79:03:20:93:b0:16:af:07:67:eb:0c:4f:
				b0:c0:a9:e8:eb:bc:ab:74:37:93:76:89:92:82:f3:48:a5:a1:
				16:62:39:2d:d5:79:67:e2:ea:6e:a9:6e:40:e1:7f:da:01:df:
				f0:4f:6f:a0:36:80:ae:ab:a2:4d:07:6e:ba:14:bf:85:82:50:
				e1:3d:df:64:bc:91:3d:60:c4:90:8c:3b:6f:0f:11:31:a6:5f:
				4f:36:5a:69:04:05:88:b5
		issuer=C = US, O = IBM WSC IBM Z and LinuxONE, CN = CA for rsyslog for SE-enabled KVM guests
		subject=C = US, O = IBM WSC IBM Z and LinuxONE, CN = SE-enabled HPVS KVM guest for PayNow demo
		```

Click the _Next_ link at the bottom of the page to continue to the next part of the lab, where you will create the _contract_ that HPVS 2.1.x expects, so that you can run the PayNow demo in your HPVS KVM guest.

