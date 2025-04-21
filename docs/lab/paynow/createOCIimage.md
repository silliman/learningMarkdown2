# Create OCI Image
                
You will be performing this section from your Ubuntu KVM guest. 

Your window or tab should like like this (unless you customized the profile we provided you):
        
<img src="../../../images/KVMGuest.png" width="351" height="217" />

## Install Docker

You will need to install Docker on your guest.  You can see that it is not currently installed by running the following command:

   ``` bash
   which docker || echo 'Docker is not found'
   ```

???- Example "Output showing Docker is not found"

      ```
      Docker is not found
      ```

See what version of Docker is available to install with this command:

   ``` bash
   sudo apt-cache policy docker.io
   ```

You can see from the output that _docker.io_ is not currently installed and that version _24.0.5_ is the candidate, or suggested, version to install:

???- Example "Output showing Docker version to install"

      ```
      docker.io:
        Installed: (none)
        Candidate: 24.0.5-0ubuntu1~22.04.1
        Version table:
           24.0.5-0ubuntu1~22.04.1 500
              500 http://ports.ubuntu.com/ubuntu-ports jammy-updates/universe s390x Packages
           20.10.21-0ubuntu1~22.04.3 500
              500 http://ports.ubuntu.com/ubuntu-ports jammy-security/universe s390x Packages
           20.10.12-0ubuntu4 500
              500 http://ports.ubuntu.com/ubuntu-ports jammy/universe s390x Packages
      ```

Install Docker with this command:

   ``` bash
   sudo apt install docker.io
   ```

Type **Y** and press *Return* when asked if you want to continue with the installation.

???- Example "Output from Docker installation"

      ```
      Reading package lists... Done
      Building dependency tree... Done
      Reading state information... Done
      The following additional packages will be installed:
        bridge-utils containerd dns-root-data dnsmasq-base pigz runc ubuntu-fan
      Suggested packages:
        ifupdown aufs-tools cgroupfs-mount | cgroup-lite debootstrap docker-doc rinse zfs-fuse | zfsutils
      The following NEW packages will be installed:
        bridge-utils containerd dns-root-data dnsmasq-base docker.io pigz runc ubuntu-fan
      0 upgraded, 8 newly installed, 0 to remove and 112 not upgraded.
      Need to get 54.8 MB of archives.
      After this operation, 232 MB of additional disk space will be used.
      Do you want to continue? [Y/n] y
      Get:1 http://ports.ubuntu.com/ubuntu-ports jammy/universe s390x pigz s390x 2.6-1 [67.2 kB]
      Get:2 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x bridge-utils s390x 1.7-1ubuntu3 [34.3 kB]
      Get:3 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x runc s390x 1.1.7-0ubuntu1~22.04.1 [4118 kB]
      Get:4 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x containerd s390x 1.7.2-0ubuntu1~22.04.1 [28.0 MB]
      Get:5 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x dns-root-data all 2021011101 [5256 B]
      Get:6 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x dnsmasq-base s390x 2.86-1.1ubuntu0.3 [348 kB]
      Get:7 http://ports.ubuntu.com/ubuntu-ports jammy-updates/universe s390x docker.io s390x 24.0.5-0ubuntu1~22.04.1 [22.2 MB]
      Get:8 http://ports.ubuntu.com/ubuntu-ports jammy/universe s390x ubuntu-fan all 0.12.16 [35.2 kB]
      Fetched 54.8 MB in 2s (24.1 MB/s)      
      Preconfiguring packages ...
      Selecting previously unselected package pigz.
      (Reading database ... 78375 files and directories currently installed.)
      Preparing to unpack .../0-pigz_2.6-1_s390x.deb ...
      Unpacking pigz (2.6-1) ...
      Selecting previously unselected package bridge-utils.
      Preparing to unpack .../1-bridge-utils_1.7-1ubuntu3_s390x.deb ...
      Unpacking bridge-utils (1.7-1ubuntu3) ...
      Selecting previously unselected package runc.
      Preparing to unpack .../2-runc_1.1.7-0ubuntu1~22.04.1_s390x.deb ...
      Unpacking runc (1.1.7-0ubuntu1~22.04.1) ...
      Selecting previously unselected package containerd.
      Preparing to unpack .../3-containerd_1.7.2-0ubuntu1~22.04.1_s390x.deb ...
      Unpacking containerd (1.7.2-0ubuntu1~22.04.1) ...
      Selecting previously unselected package dns-root-data.
      Preparing to unpack .../4-dns-root-data_2021011101_all.deb ...
      Unpacking dns-root-data (2021011101) ...
      Selecting previously unselected package dnsmasq-base.
      Preparing to unpack .../5-dnsmasq-base_2.86-1.1ubuntu0.3_s390x.deb ...
      Unpacking dnsmasq-base (2.86-1.1ubuntu0.3) ...
      Selecting previously unselected package docker.io.
      Preparing to unpack .../6-docker.io_24.0.5-0ubuntu1~22.04.1_s390x.deb ...
      Unpacking docker.io (24.0.5-0ubuntu1~22.04.1) ...
      Selecting previously unselected package ubuntu-fan.
      Preparing to unpack .../7-ubuntu-fan_0.12.16_all.deb ...
      Unpacking ubuntu-fan (0.12.16) ...
      Setting up dnsmasq-base (2.86-1.1ubuntu0.3) ...
      Setting up runc (1.1.7-0ubuntu1~22.04.1) ...
      Setting up dns-root-data (2021011101) ...
      Setting up bridge-utils (1.7-1ubuntu3) ...
      Setting up pigz (2.6-1) ...
      Setting up containerd (1.7.2-0ubuntu1~22.04.1) ...
      Created symlink /etc/systemd/system/multi-user.target.wants/containerd.service → /lib/systemd/system/containerd.service.
      Setting up ubuntu-fan (0.12.16) ...
      Created symlink /etc/systemd/system/multi-user.target.wants/ubuntu-fan.service → /lib/systemd/system/ubuntu-fan.service.
      Setting up docker.io (24.0.5-0ubuntu1~22.04.1) ...
      Adding group `docker' (GID 121) ...
      Done.
      Created symlink /etc/systemd/system/multi-user.target.wants/docker.service → /lib/systemd/system/docker.service.
      Created symlink /etc/systemd/system/sockets.target.wants/docker.socket → /lib/systemd/system/docker.socket.
      Processing triggers for dbus (1.12.20-2ubuntu4.1) ...
      Processing triggers for man-db (2.10.2-1) ...
      Scanning processes...                                                                                                              
      Scanning linux images...                                                                                                           
      
      Running kernel seems to be up-to-date (ABI upgrades are not detected).
      
      No services need to be restarted.
      
      No containers need to be restarted.
      
      No user sessions are running outdated binaries.
      
      No VM guests are running outdated hypervisor (qemu) binaries on this host.
      ```

Repeat this command from earlier and you'll now see that Docker is installed:

   ``` bash
   sudo apt-cache policy docker.io
   ```

???- Example "Output showing docker.io is installed"

      ```
      docker.io:
        Installed: 24.0.5-0ubuntu1~22.04.1
        Candidate: 24.0.5-0ubuntu1~22.04.1
        Version table:
       *** 24.0.5-0ubuntu1~22.04.1 500
              500 http://ports.ubuntu.com/ubuntu-ports jammy-updates/universe s390x Packages
              100 /var/lib/dpkg/status
           20.10.21-0ubuntu1~22.04.3 500
              500 http://ports.ubuntu.com/ubuntu-ports jammy-security/universe s390x Packages
           20.10.12-0ubuntu4 500
              500 http://ports.ubuntu.com/ubuntu-ports jammy/universe s390x Packages
      ```

Run this command to see where the _docker_ binary lives:

   ``` bash
   which docker && echo Docker is found!
   ```

???- Example "Output showing where the docker binary resides"

      ```
      /usr/bin/docker
      Docker is found!
      ```

Run this command to display the Docker version:

   ``` bash
   docker version
   ```

Besides noting the version, note the permission error at the bottom of the output:

???- Example "Docker version info, plus a permission error"

      ```
      Client:
       Version:           24.0.5
       API version:       1.43
       Go version:        go1.20.3
       Git commit:        24.0.5-0ubuntu1~22.04.1
       Built:             Mon Aug 21 19:50:14 2023
       OS/Arch:           linux/s390x
       Context:           default
      permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/version": dial unix /var/run/docker.sock: connect: permission denied
      ```


You need to add your userid to the _docker_ group in order to have permission to use Docker:

   ``` bash
   sudo usermod -aG docker student
   ```

(There is no output from the above command when it works).

You will need to log out and then log back in in order for your updated permissions to take effect.

Log out:

   ``` bash
   exit
   ```

Log back in:

   ``` bash
   ssh -p ${Student_SSH_Port} -l student 192.168.22.64
   ```

Now repeat _docker version_ and you should not see any errors and you should see more information as well:

   ``` bash
   docker version
   ```

???- Example "Output when your permissions are correct"

      ```
      Client:
       Version:           24.0.5
       API version:       1.43
       Go version:        go1.20.3
       Git commit:        24.0.5-0ubuntu1~22.04.1
       Built:             Mon Aug 21 19:50:14 2023
       OS/Arch:           linux/s390x
       Context:           default
      
      Server:
       Engine:
        Version:          24.0.5
        API version:      1.43 (minimum version 1.12)
        Go version:       go1.20.3
        Git commit:       24.0.5-0ubuntu1~22.04.1
        Built:            Mon Aug 21 19:50:14 2023
        OS/Arch:          linux/s390x
        Experimental:     false
       containerd:
        Version:          1.7.2
        GitCommit:        
       runc:
        Version:          1.1.7-0ubuntu1~22.04.1
        GitCommit:        
       docker-init:
        Version:          0.19.0
        GitCommit: 
      ```

## Build OCI Image for PayNow demo

Switch to the proper directory:

   ``` bash
   cd ~/paynow-website && pwd
   ```


Before you get started, run this command to see that you currently have no OCI images on your system:

   ``` bash
   docker images
   ```

???- example "Expected output"

      ```
      REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
      ```

Now, build your OCI image containing the PayNow Demo.

   ``` bash
   docker build -t paynow .
   ```

You can ignore any warning messages. Example output is shown below.

???- example "Example output"

      ```
      DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
                  Install the buildx component to build images with BuildKit:
                  https://docs.docker.com/go/buildx/
      
      Sending build context to Docker daemon  2.846MB
      Step 1/7 : FROM node:19
      19: Pulling from library/node
      44d1d02f9172: Pull complete 
      e4000487deec: Pull complete 
      71c736ce76be: Pull complete 
      c037e6c2d715: Pull complete 
      294c8876dcdb: Pull complete 
      33a351284190: Pull complete 
      5ac921848b31: Pull complete 
      86b7a0ecd4be: Pull complete 
      Digest: sha256:92f06fc13bcc09f1ddc51f6ebf1aa3d21a6532b74f076f224f188bc6b9317570
      Status: Downloaded newer image for node:19
       ---> f2e8386523b1
      Step 2/7 : WORKDIR /app
       ---> Running in de71bb50b43a
      Removing intermediate container de71bb50b43a
       ---> 25f7fababad3
      Step 3/7 : COPY app/package*.json ./
       ---> 6c01e8cc8944
      Step 4/7 : RUN npm install
       ---> Running in f4a6ec88dc55
      npm WARN old lockfile 
      npm WARN old lockfile The package-lock.json file was created with an old version of npm,
      npm WARN old lockfile so supplemental metadata must be fetched from the registry.
      npm WARN old lockfile 
      npm WARN old lockfile This is a one-time fix-up, please be patient...
      npm WARN old lockfile 
      
      added 65 packages, and audited 66 packages in 2s
      
      7 packages are looking for funding
        run `npm fund` for details
      
      found 0 vulnerabilities
      npm notice 
      npm notice New major version of npm available! 9.6.3 -> 10.0.0
      npm notice Changelog: <https://github.com/npm/cli/releases/tag/v10.0.0>
      npm notice Run `npm install -g npm@10.0.0` to update!
      npm notice 
      Removing intermediate container f4a6ec88dc55
       ---> a5b9897de6c8
      Step 5/7 : COPY app/ .
       ---> 50c2cc75996f
      Step 6/7 : EXPOSE 8443
       ---> Running in 6a9c4f0be331
      Removing intermediate container 6a9c4f0be331
       ---> 306e777a7247
      Step 7/7 : CMD npm start
       ---> Running in c8d6a6817780
      Removing intermediate container c8d6a6817780
       ---> fd801119534e
      Successfully built fd801119534e
      Successfully tagged paynow:latest
      ```

Please click the *Next* link at the lower right to continue in the lab.
