# Prepare the contract 

## Ensure necessary environment variables are set
    
1. Go to a command prompt on your prep system

2. You should have each of these environment variables set on your prep system:

    ``` bash
    echo LAB_WORKDIR is ${LAB_WORKDIR}
    echo LAB_TAR is ${LAB_TAR}
    echo LOG_INGESTION_KEY is ${LOG_INGESTION_KEY}
    echo LOG_HOSTNAME is ${LOG_HOSTNAME}
    ```  
         
    If any of the above commands do not display a value after the _is_ then revisit [the section on setting environment variables](../prereqs/setup.md){target="_blank" rel="noopener"}.
             
## Make a new directory hierarchy for Lab 2

1. Make a fresh directory structure and change in to it:

    ``` bash
    mkdir -p ${LAB_WORKDIR}/lab2/workload/play && cd ${LAB_WORKDIR}/lab2
    ```

## Create a Pod descriptor to specify the application workload

1. Switch to the directory that will hold a Pod descriptor:

    ``` bash
    cd workload/play
    ```

2. Create the Pod descriptor::

    ``` bash
    cat << EOF > play.yml    
      play:
        resources:
          - apiVersion: v1
            kind: Pod
            metadata:
              name: busybox
            spec:
              containers:
              - name: main
                image: docker.io/library/busybox@sha256:3614ca5eacf0a3a1bcc361c939202a974b4902b9334ff36eb29ffe9011aaad83
                command: ["/bin/sh", "-c"]
                args:
                - mkdir -p /data/cloudlabs ;
                  env | tee -a /data/cloudlabs/env.out ;
                  cat /data/cloudlabs/env.out ;
                  head -20 /data/cloudlabs/env.out ;
                  head -20 /data/cloudlabs/greetings.out ;
                  tail -20 /data/cloudlabs/greetings.out ;
                  while true ;
                  do echo Hi \${name:-World} the time is \$(date) | tee -a /data/cloudlabs/greetings.out ;
                  sleep \${interval:-60} ; 
                  done
                envFrom:
                - configMapRef:
                    name: contract.config.map
                    optional: false
                volumeMounts: 
                  - mountPath: /data
                    name: data-vol
                    readOnly: false
              restartPolicy: Never
              volumes:
                - hostPath:
                    path: /mnt/data
                    type: Directory
                  name: data-vol
    EOF
    ```

3. Display the file's content.

    ``` bash
    cat play.yml
    ```

1. **Optional**.  This command will show that you are indeed using the same pod descriptor in this lab (lab2)  as you did in the previous lab (lab1).  We could have had you just copy that file over instead of using the *cat* command in the prior step.


    ``` bash
    diff --report-identical-files \
      ${LAB_WORKDIR}/lab1/play/play.yml \
      ${LAB_WORKDIR}/lab2/workload/play/play.yml
    ```

1. Change to the directory one level higher than your current location (and display it):

    ``` bash
    cd .. && pwd
    ```
    
## Create encrypted workload section of the contract

1. Create this convenience script to encrypt the workload portion of the contract:
 
    ``` bash
    cat << EOF > flow.workload
    # Create the workload section of the contract and add the contents in the workload.yaml file.
    
    WORKLOAD_PLAIN=./workload.yaml.plaintext
    WORKLOAD=workload.yaml
    
    echo "  type: workload
      volumes:
        data:
          filesystem: ext4
          mount: /mnt/data
          seed: seed-supplied-by-workload-persona
    \$(cat play/play.yml)" > \${WORKLOAD_PLAIN}
    
    # Download certificate to encrypt contract for Hyper Protect Container Runtime:
    HPCR_rev=13
    CONTRACT_KEY=./ibm-hyper-protect-container-runtime-1-0-s390x-\${HPCR_rev}-encrypt.crt
    curl https://cloud.ibm.com/media/docs/downloads/hyper-protect-container-runtime/ibm-hyper-protect-container-runtime-1-0-s390x-\${HPCR_rev}-encrypt.crt > \${CONTRACT_KEY}
    
    
    # Use the following command to create a random password:
    PASSWORD_WORKLOAD="\$(openssl rand 32 | base64 \${LAB_WRAP})"
    
    # Use the following command to encrypt password with the Hyper Protect Container Runtime Contract Encryption Key:
    ENCRYPTED_WORKLOAD_PASSWORD="\$(echo -n "\$PASSWORD_WORKLOAD" | base64 -d | openssl rsautl -encrypt -inkey \$CONTRACT_KEY -certin | base64 \${LAB_WRAP})"
    
    # Use the following command to encrypt the workload.yaml file with a random password:
    ENCRYPTED_WORKLOAD="\$(echo -n "\$PASSWORD_WORKLOAD" | base64 -d | openssl enc -aes-256-cbc -pbkdf2 -pass stdin -in "\$WORKLOAD_PLAIN" | base64 \${LAB_WRAP})"
    
    # Use the following command to get the encrypted section of the contract:
    WORKLOAD_ENCRYPTED="hyper-protect-basic.\${ENCRYPTED_WORKLOAD_PASSWORD}.\${ENCRYPTED_WORKLOAD}"
    
    echo ""
    echo "See `pwd`/workload.yaml.plaintext to see what was encrypted for the workload section of your contract"
    echo ""
    
    echo "\$WORKLOAD_ENCRYPTED" > ../\$WORKLOAD
    EOF
    ```

1. Run the script you just created:

    ``` bash
    . ./flow.workload
    ```

1. Back up one directory:

    ``` bash
    cd ..
    ```

1. Display the encrypted workload section you just created:  

    ``` bash
    cat workload.yaml
    ```

## Add a plaintext environment section to the encrypted workload section

1. This command combines the workload section (which is encrypted) with a plain text environment section.  The contract is the output file, _user_data.yaml_.

    ``` bash
    cat << EOF > user_data.yaml
    workload: $(cat workload.yaml)
    env: |
      type: env
      logging:
        logDNA:
          hostname: ${LOG_HOSTNAME}
          ingestionKey: ${LOG_INGESTION_KEY}
          port: 6514
      volumes:
        data:
          seed: seed-supplied-by-env-persona
      env:
        name: Lab 2 Student
        interval: "30"
    EOF
    ```
   
1. Display your contract data:

    ``` bash
    cat user_data.yaml
    ```

Proceed to the next section to create your Hyper Protect Virtual Server for VPC instance.

