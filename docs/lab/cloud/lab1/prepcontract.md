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

## Make the directory hierarchy for Lab 1

1. Make a fresh directory structure and change in to it:

    ``` bash
    mkdir -p ${LAB_WORKDIR}/lab1/play && cd ${LAB_WORKDIR}/lab1
    ```

## Create a Pod descriptor to specify the application workload

1. Switch to the directory that will hold a Pod descriptor:

    ``` bash
    cd play
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

## Create the contract

1. Change to the directory one level higher than your current location (and display it):

    ``` bash
    cd .. && pwd
    ```
    
1. Create the contract.

       ``` bash
       cat << EOF > user_data.yaml
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
           name: Lab 1 Student
           interval: "30"
       workload: |
         type: workload
         volumes:
           data:
             filesystem: ext4
             mount: /mnt/data
             seed: seed-supplied-by-workload-persona
       $(cat play/play.yml)
       EOF
       ```

1. Display your contract data. Keep your terminal session handy- later on in the next section you will be directed to copy these contents into the IBM Cloud Web UI. 

    ``` bash
    cat user_data.yaml
    ```

Proceed to the next section to create your Hyper Protect Virtual Servers for IBM Cloud VPC instance.

  
