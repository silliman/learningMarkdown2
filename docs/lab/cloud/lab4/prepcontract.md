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

## Make a new directory hierarchy for Lab 4

1. Make a fresh directory structure and change in to it:

    ``` bash
    mkdir -p ${LAB_WORKDIR}/lab4/{environment,workload/play} && cd ${LAB_WORKDIR}/lab4
    ```

## Create a Pod descriptor to specify the application workload

1. Switch to the directory with will hold a Pod descriptor:

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

## Create a convenience script to encrypt the workload section

1. Backup one directory level:

    ``` bash
    cd ..
    ```
    
2. Create this convenience script to encrypt the workload portion of the contract:
 
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

## Create a convenience script to encrypt the environment  section

1. Change to the directory used for the environment section display it:

    ``` bash
    cd ../environment && pwd
    ```
    
2. Create this convenience script to encrypt the environment portion of the contract:

    ``` bash
    cat << EOF > flow.env
    # Create the env section of the contract and add the contents in the env.yaml file.
    ENV_PLAIN="./env.yaml.plaintext"
    ENV="env.yaml"
    
    echo "  type: env
      logging:
        logDNA:
          hostname: \${LOG_HOSTNAME}
          ingestionKey: \${LOG_INGESTION_KEY}
          port: 6514
      env:
        name: Lab 4 Student
        interval: \"30\"
      volumes:
        data:
          seed: seed-supplied-by-env-persona" > \${ENV_PLAIN}
    
    cat ./pubSigningKey.yaml >> \${ENV_PLAIN}

    # Download certificate to encrypt contract for Hyper Protect Container Runtime:
    HPCR_rev=13
    CONTRACT_KEY=./ibm-hyper-protect-container-runtime-1-0-s390x-\${HPCR_rev}-encrypt.crt
    curl https://cloud.ibm.com/media/docs/downloads/hyper-protect-container-runtime/\$CONTRACT_KEY > \$CONTRACT_KEY
    
    
    # Use the following command to create a random password:
    PASSWORD_ENV="\$(openssl rand 32 | base64 \${LAB_WRAP})"
    
    #  Use the following command to encrypt password with the Hyper Protect Container Runtime Contract Encryption Key:
    ENCRYPTED_ENV_PASSWORD="\$(echo -n "\$PASSWORD_ENV" | base64 -d | openssl rsautl -encrypt -inkey \$CONTRACT_KEY -certin | base64 \${LAB_WRAP} )"
    
    # Use the following command to encrypt env.yaml with a random password:
    ENCRYPTED_ENV="\$(echo -n "\$PASSWORD_ENV" | base64 -d | openssl enc -aes-256-cbc -pbkdf2 -pass stdin -in "\$ENV_PLAIN" | base64 \${LAB_WRAP})"
    
    # Use the following command to get the encrypted section of the contract:
    ENV_ENCRYPTED="hyper-protect-basic.\${ENCRYPTED_ENV_PASSWORD}.\${ENCRYPTED_ENV}"
    
    echo ""
    echo "See `pwd`/env.yaml.plaintext to see what was encrypted for the env section of your contract"
    echo ""
    
    echo "\$ENV_ENCRYPTED" > ../\$ENV
    EOF
    ```

## Create convenience scripts to facilitate signing the contract

1. Move up one directory level:

    ``` bash
    cd .. && pwd
    ```

2. Create the following convenience script that will create an RSA key pair that you will use to sign the contract. 

    ``` bash
    cat << EOF > flow.prepare
    # Use the following command to generate key pair to sign the contract 
    openssl genrsa -aes128 -passout pass:test1234 -out private.pem 4096
    openssl rsa -in private.pem -passin pass:test1234 -pubout -out public.pem

    # The following command is an example of how you can get the signing key:
    key=\$(awk -vRS="\n" -vORS="\\\\\n" '1' public.pem)
    # echo "  signingKey: \"\${key%\\\\n}\"" > environment/pubSigningKey.yaml
    printf "%s" "  signingKey: \"\${key%\\\\n}\"" > environment/pubSigningKey.yaml
    EOF
    ```

3. Create the following convenience script that will sign the encrypted contract.

    ``` bash
    cat << EOF > flow.signature
    # combine workload and environment
    cat workload.yaml env.yaml | tr -d '\n' > contract.yaml

    # Sign the combination from workload and env being approved
    echo \$( cat contract.yaml | openssl dgst -sha256 -sign private.pem -passin pass:test1234 | openssl enc -base64) | tr -d ' ' > signature.yaml

    # Create user data and add signature:
    echo "workload: \$(cat workload.yaml)
    env: \$(cat env.yaml)
    envWorkloadSignature: \$(cat signature.yaml)" > user_data.yaml
    
    echo ""
    echo "import `pwd`/user_data.yaml into User Data or copy and paste from below:"
    echo ""
    
    cat user_data.yaml
    EOF
    ```

## Create and run helper script to produce the encrypted and signed contract

1. Create this helper script that you will use to create the signed and encrypted contract:

    ``` bash
    cat << EOF > makeContract
    . ./flow.prepare
    cd workload
    . ./flow.workload
    cd ../environment
    . ./flow.env
    cd ..
    . ./flow.signature
    EOF
    ```

2. Create the contract:

    ``` bash
    . ./makeContract
    ```
   
3. Display your contract data:

    ``` bash
    cat user_data.yaml
    ```

Proceed to the next section to create your Hyper Protect Virtual Servers for IBM Cloud VPC instance.

