# Files created in the on-premises labs

## Directories created on your Ubuntu KVM guest

| Directory | Purpose |
|-|-|
| x509Work | High-level directory for doing x509 work |
| x509Work/GREP11Client | Directory for creating certificate for your GREP11 Client code |
| x509Work/rsyslog | High-level directory for x509 work related to rsyslog service on your Ubuntu KVM guest |
| x509Work/rsyslog/CA | Directory that holds a Certificate Authority certificate for your rsyslog service |
| x509Work/rsyslog/clients | For each lab you send a CSR to this directory from your host RHEL userid |
| x509Work/rsyslog/server | You use this as a working directory to create the rsyslog service's certificate |
| hpcs-grep11-go | From the GREP11 lab, this is where the GitHub repo is cloned so that you can modify and run the sample GREP11 code |
| paynow-website | From the PayNow lab, this is where the GitHub repo is cloned so that you can build and run the OCI image |

## Directories created in your host RHEL studentxx userid's home directory 

| Directory | Purpose |
|-|-|
| grep11Lab | High-level directory for Grep11 Lab |
| grep11Lab/contract | High-level directory for creating the contract used by the HPVS KVM guest for the GREP11 Server |
| grep11Lab/contract/environment | Directory used by the "workload deployer" persona to create the environment section of the contract |
| grep11Lab/contract/environment/rsyslog | Directory to contain the x509 material needed to establish a mutual TLS connection with the rsyslog service that you set up on your Ubuntu KVM guest |
| grep11Lab/contract/workload | Directory used by the "workload provider" persona to create the workload section of the contract |
| grep11Lab/contract/workload/compose | Directory containing the Docker Compose file and files referenced by the Docker Compose file |
| grep11Lab/x509Work | High-level directory for x509 work |
| grep11Lab/x509Work/CENA4SEEClient | Directory used for creating client certificate for access to the CENA4SEE server.  Your HPVS GREP11 Server is a client to the CENA4SEE server. |
| grep11Lab/x509Work/GREP11Server | High-level directory for x509 work related to the GREP11 Server and mutual authentication with its clients (the sample GREP11 client code in this lab) |
| grep11Lab/x509Work/GREP11Server/CA | Directory that holds a Certificate Authority certificate for the GREP11 Server |
| grep11Lab/x509Work/GREP11Server/clients | You send a CSR to this directory from your Ubuntu guest on behalf of your GREP11 Client code which runs in your Ubuntu guest |
| grep11Lab/x509Work/GREP11Server/server | You use this as a working directory to create the GREP11 server's certificate |
| grep11Lab/x509Work/rsyslogClient | work directory used for creating the client certificate that will allow your HPVS GREP11 Server to send log messages to your rsyslog service running on your Ubuntu guest |
| paynowLab | High-level directory for the PayNow Lab |
| paynowLab/contract | High-level directory for creating the contract used by the HPVS KVM guest for the PayNow app |
| paynowLab/contract/environment/rsyslog | Directory to contain the x509 material needed to establish a mutual TLS connection with the rsyslog service that you set up on your Ubuntu KVM guest |
| paynowLab/contract/workload | Directory used by the "workload provider" persona to create the workload section of the contract |
| paynowLab/contract/workload/play | Directory containing the pod descriptor file which specifies the OCI image to run |
| paynowLab/x509Work | High-level directory for x509 work |
| paynowLab/x509Work/rsyslogClient | work directory used for creating the client certificate that will allow your HPVS PayNow app to send log messages to your rsyslog service running on your Ubuntu guest |

## Files in your Ubuntu guest's x509work/GREP11Client/ directory

| File | Comments |
|-|-|
| client.cnf | Configuration file used so that openssl command does not ask interactive questions |
| client.csr | Certificate signing request you create and then send to the "GREP11 CA" registrar on your host RHEL account |
| client.key | Private key associated with your GREP11 client certificate |
| client.pem | GREP11 client certificate that your "GREP11 CA" registrar on your host RHEL account sends to you |

## Files in your Ubuntu guests's x509Work/rsyslog/CA/ directory

| File | Comments |
|-|-|
| ca-key.pem | Private key you create for your CA certificate for rsyslog |
| ca-req.csr | CSR request you create which you use to create the CA certificate for rsyslog |
| ca.cnf | Configuration file used so that openssl command does not ask interactive questions |
| ca.crt | Self-signed CA certificate for the CA used by the rsyslog service and its clients.  In our labs the client to rsyslog is the HPVS guest you create in each lab | 

## Files in your Ubuntu guests's x509Work/rsyslog/clients/ directory

| File | Comments |
|-|-|
| grep11Lab-client-req.csr | CSR sent to you from your host RHEL userid so that the HPVS GREP11 Server can authenticate with rsyslog |
| grep11Lab-client.crt | Certificate you create on your Ubuntu guest and then send to your host RHEL userid |
| paynowLab-client-req.csr | CSR sent to you from your host RHEL userid so that the HPVS PayNow app can authenticate with rsyslog |
| paynowLab-client.crt | Certificate you create on your Ubuntu guest and then send to your host RHEL userid |

## Files in your Ubuntu guest's x509Work/rsyslog/server/ directory

| File | Comments |
|-|-|
| server-key.pem | private key you create which is used with your rsyslog service's certificate |
| server-req.csr | CSR you create so that your "rsyslog CA" registrar can create your rsyslog service's certificate |
| server.cnf | Configuration file used so that openssl command does not ask interactive questions |
| server.crt | rsyslog service's certificate that your "rsyslog CA" registrar creates from your CSR |

## Files in your host RHEL studentxx's grep11Lab/contract/environment/rsyslog/ directory

| File | Comments | 
|-|-|
| ca.crt | The self-signed CA certificate used by the rsyslog service and its clients. You copy it from your Ubuntu KVM host, which is where the rsyslog service and its CA reside. |
| client-key-pkcs8.pem | This is your HPVS GREP11 Server's private key used for authentication with the rsyslog service, after it has been converted to PKCS #8 format |
| grep11Lab-client.crt | This is your HPVS GREP11 Server's client certificte used for authentication with the rsyslog service. It is created by your "rsyslog CA" registrar on your Ubuntu guest, sent to you in your working directory, and from there you copy it into this directory so that it can be included in the workload section of the contract |

## Files in your host RHEL studentxx's grep11Lab/contract/environment/ directory

| File | Comments |
|-|-|
| env.yaml.plaintext | This file is created by the *flow.env* convenience script and is in plaintext.  It is used within the *flow.env* script as input to produce an encrypted environment section of the contract. In this lab we've left the script intact for educational purposes. In a production environment you would either delete this plaintext file or restrict access to it as it contains sensitive information. |
| flow.env | This is a convenience script that encompasses many of the manual commands that are listed in the product documentation. Careful study of this script will be worthwhile for those who want a deep understanding of how an environment section of the contract is created and encrypted. |
| pubSigningKey.yaml | This is the public key corresponding to the private key that is used to sign the contract.  It is created by the *flow.prepare* convenience script and then added to the environment section of the contract.  The HPCR runtime uses this to verify the signature on the contract. |

## Files in your host RHEL studentxx's grep11Lab/contract/workload/compose/ directory

| File | Comments |
|-|-|
| c16client.yaml | Configuration file for the connection between your HPVS GREP11 Server and the Crypto Express Network API for Secure Execution Enclaves (CENA4SEE) appliance |
| c16server-ca.pem | The self-signed CA certificate used by the CENA4SEE appliance and its clients.  It is created by the instructors and copied into this directory by you during the lab. All students share a single CENA4SEE appliance, so control of this appliance's CA is in the hands of the instructors. |
| c16server-client.key | Private key you create used for authentication between your HPVS GREP11 Server and the CENA4SEE appliance. |
| c16server-client.pem | Certificate created for you by the instructors and used for authentication between your HPVS GREP11 Server and the CENA4SEE appliance. All students share a single CENA4SEE appliance, so control of this appliance's CA is in the hands of the instructors, which is why the instructors have to create this certificate for you. |
| c16server-restricted-server.pem | This is the CENA4SEE appliance's server certificate. It is specified in the *c16client.yaml* file which prevents the GREP11 Server from communicating with the CENA4SEE appliance if it does not present this certficiate. |
| docker-compose.yml | This file specifies the OCI image for the application worklaod (the GREP11 Server in this case), as well as references to several files that the GREP11 Server needs. |
| grep11-ca.pem | The self-signed CA certificate for the "GREP11 Server" CA. It is required for mutual TLS authentication with clients to the GREP11 Server. |
| grep11-server.key | The private key used by the GREP11 Server's server certificate. It is required for mutual TLS authentication with clients to the GREP11 Server. |
| grep11-server.pem | The GREP11 Server's server certificate. It is required for mutual TLS authentication with clients to the GREP11 Server. |
| grep11server.yaml | Configuration file that governs communication with GREP11 clients and also specifies with crypto card domain on the CENA4SEE appliance LPAR this GREP11 Server is associated with. |

## Files in your host RHEL studentxx's grep11Lab/contract/workload/ directory

| File | Comments |
|-|-|
| flow.workload | This is a convenience script that encompasses many of the manual commands that are listed in the product documentation. Careful study of this script will be worthwhile for those who want a deep understanding of how a workload section of the contract is created and encrypted. |
| workload.yaml.plaintext | This file is created by the *flow.workload* convenience script and is in plaintext.  It is used within the *flow.workload* script as input to produce an encrypted workload section of the contract. In this lab we've left the script intact for educational purposes. In a production environment you would either delete this plaintext file or restrict access to it as it contains sensitive information. In some use cases the *workload deployer* persona would never even have access to the plaintext workload section because the *workload provider* persona would provide the workload section that is already encrypted by the Hyper Protect Container Runtime image's public key. |

## Files in your host RHEL studentxx's grep11Lab/contract/ directory

| File | Comments |
|-|-|
| contract.yaml | This is an intermediate file created by the flow.signature script.  It is the concatenation of the encrypted workload and the encrypted environment sections of the contract.  It is then signed in the flow.signature script and the signature is then appended to ths file to product the final contract that is passed to the HPVS instance |
| env.yaml | Intermediate file created by the flow.env script.  It is the encrypted environment section of the contract. |
| flow.clear | Convenience script that deletes some of the intermediate files created by the convenience scripts. |
| flow.prepare | Convenience script that creates an RSA key pair which will be used to sign the encrypted environment and workload sections of the contract. |
| flow.signature | Convenience script that signs the encrypted environment and workload sections of the contract using the RSA key pair created by flow.prepare. flow.signature then appends the signature to the encrypted sections that it just signed. |
| makeContract | High-level, or "wrapper", script that runs all of the "flow.*" scripts in the proper order in order to create the contract |
| meta-data | Input file used in created the ISO image. It contains the hostname that will be assigned to the HPVS instance. |
| private.pem | Private key created in flow.prepare that is used to create the signature over the contract. |
| public.pem | Public key created in flow.prepare.  It is passed to the contract so that the HPCR can verify the signature on the contract. |
| signature.yaml | The signature over the contract, created in flow.signature and then added to the contract as the 'envWorkloadSignature' key. |
| user-data | Output of the makeContract script, it is the signed and encrypted contract. |
| user_data.yaml | A copy of user-data that is then used as input to the genisoimage command that generates the ISO image that contains the contract |
| vendor-data | Input to the command that generates the ISO image. Not much of interest in it for these labs. |
| workoad.yaml | Intermediate file created by the flow.workload script. It is the encrypted workload section of the contract. |

## Files in your host RHEL studentxx's grep11Lab/x509Work/CENA4SEEClient/ directory

| File | Comments |
|-|-|
| c16server-client.csr | CSR for your GREP11 Server, which will be used by the instructors to create your GREP11 Server's certificate that will allow it to authenticate with the CENA4SEE Server. |
| c16server-client.key | Private key associated with your GREP11 Server's certificate that will allow it to authenticate with the CENA4SEE Server. |
| c16server-client.pem | Certificate created by the instructors that will allow your HPVS GREP11 Server to communicate with the CENA4SEE Serer. |
| csr.cfg | Configuration file used in created the CSR that will prevent interactive questions during the CSR creation. |

## Files in your host RHEL studentxx's grep11Lab/x509Work/GREP11Server/CA/ directory
| File | Comments |
|-|-|
| ca.cnf | Configuration file used to avoid interactive questions while creating the CSR |
| grep11-ca-key.pem | Private key associated with the self-signed CA root certificate used for authentication between the GREP11 Server and its clients. |
| grep11-ca.pem | Self-signed CA root certificate used for authentication between the GREP11 Server and its clients. |
| grep11-ca.srl | File created during the creation of the self-signed CA root certificate |
| grep11-server.csr | Copy of the GREP11 Server CSR request that you create in the '../server' directory and then copy into this directory.  Think of the '../server' directory as belonging to a "GREP11 Server administrator" persona and the directory in this table as belong to a "GREP11 CA Registrar" persona. |
| grep11-server.pem | The GREP11 Server certificate that the "GREP11 CA Registrar" persona creates in this directory and then copies to the "../server" directory. |
| server.cnf | Configuration file to avoid interactive questions |

## Files in your host RHEL studentxx's grep11Lab/x509Work/GREP11Server/clients/ directory

| File | Comments |
|-|-|
| client.csr | CSR sent from your Ubuntu KVM guest |
| client.pem | Certificate that you create on behalf of your GREP11 Client code. You send this certificate back to your Ubuntu KVM guest so that your GREP11 Client code that runs there can authenticate with your HPVS GREP11 Server. |

## Files in your host RHEL studentxx's grep11Lab/x509Work/GREP11Server/server/ directory

| File | Comments |
|-|-|
| grep11-ca.pem | A copy of the "GREP11 CA" Registrar's self-signed public CA certificate. Copying it from the "../CA" directory to this directory is simply a simulation of a real-world scenario where the "GREP11 Server administrator" persona would obtain this certificate by some means. |
| grep11-server.csr | The CSR that the "GREP11 Server administraro" creates and then copies to the "../CA" directory as s simulation of a real-world scenario where the "GREP11 Server administrator" would send a CSR to the "GREP11 CA Registrar". |
| grep11-server.pem | The "GREP11 CA Registrar" creates this in the "../CA" directory and then copies it here in a simulation of a real-world scenario where the "GREP11 CA Registrar" creates the certificate and then delivers it to the "GREP11 Server administrator" by some means. |
| serverCSR.cnf | Configuration file used to avoid interactive questions. |

## Files in your host RHEL studentxx's grep11Lab/x509Work/rsyslogClient/ directory

| File | Comments |
|-|-|
| client.cnf | Configuration file used to avoid interactive questions. |
| client-key.pem | Private key associated with the CSR you create (and the certificate created from the CSR) that is used in authenticating your HPVS GREP11 Server to the rsyslog service that you set up on your Ubuntu KVM guest. |
| grep11Lab-client.crt | Certificate created on your Ubuntu KVM guest by your "Rsyslog CA Registrar" persona and then sent to you here by that persona. |
| grep11Lab-client-req.csr | CSR that you create here and then send to your Ubuntu KVM guest, where the "Rsyslog CA Registrar" persona creates the certificate. |