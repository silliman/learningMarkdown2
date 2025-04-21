# Run GREP11 Client code

## Overview of this section

You have done a lot of work in this lab, have learned so much, and yet have had so much fun! 
It doesn't get any better than this!

Let's recap the highlights:

You configured an _rsyslog_ service on your Ubuntu KVM guest.

You did the X509 work to enable communication between the GREP11 server and the _rsyslog_ service.

You did the X509 work to enable the GREP11 Server to communicate with the CENA4SEE server.

You did the server side of the X509 work to enable the GREP11 Server to communicate with GREP11 clients.

You created the _contract_ expected by HPVS 2.1.x.

You successfully launched the GREP11 server as a Secure Execution-protected HPVS 2.1.x KVM Guest!

Your last task is to run some GREP11 client code to verify that everything is working from end-to-end!

In this section you will:

1. Install Go on your Ubuntu KVM guest

2. Download the GREP11 client code from GitHub

3. Do the client side of the X509 work to enable communication between your GREP11 Server and this GREP11 client code

4. Modify the GREP11 client code to point to your GREP11 Server

5. Run the GREP11 client code

## Start this section in your Ubuntu KVM guest session

This section starts out in your Ubuntu KVM guest, which is where you should be if you have been doing the lab in order in one sitting. 

<img src="../../../images/KVMGuest.png" width="351" height="217" />

If you are not logged in, the command to log in is `ssh -p ${Student_SSH_Port} -l student 192.168.22.64`

## Install Go 

The _Go_ language compiler is not installed on your system.  Prove that with this command:

   ``` bash
   go version
   ```

You'll be given some ideas on how to install it:

???- example "Output when go is not installed"

      ```
      sudo snap install go         # version 1.21.4, or
      sudo apt  install golang-go  # version 2:1.18~0ubuntu2
      sudo apt  install gccgo-go   # version 2:1.18~0ubuntu2
      See 'snap info go' for additional versions.
      ```

Please pick the second option to avoid breaking this lab's warranty :smile: as that is the option the instructors used when creating the lab.

   ``` bash
   sudo apt install golang-go
   ```

Type _Y_ when prompted to continue.  The output from the installation will look like this:

???- example "Output from installation of golang-go"

      ```
      Reading package lists... Done
      Building dependency tree... Done
      Reading state information... Done
      The following additional packages will be installed:
        bzip2 cpp cpp-11 fontconfig-config fonts-dejavu-core g++ g++-11 gcc gcc-11 gcc-11-base golang-1.18-go golang-1.18-src golang-src libasan6
        libatomic1 libc-dev-bin libc-devtools libc6-dev libcc1-0 libcrypt-dev libdeflate0 libdpkg-perl libfile-fcntllock-perl libfontconfig1
        libfreetype6 libgcc-11-dev libgd3 libgomp1 libisl23 libitm1 libjbig0 libjpeg-turbo8 libjpeg8 libmpc3 libnsl-dev libstdc++-11-dev libtiff5
        libtirpc-dev libubsan1 libwebp7 libxpm4 linux-libc-dev manpages-dev pkg-config rpcsvc-proto
      Suggested packages:
        bzip2-doc cpp-doc gcc-11-locales g++-multilib g++-11-multilib gcc-11-doc gcc-multilib make autoconf automake libtool flex bison gdb
        gcc-doc gcc-11-multilib bzr | brz mercurial subversion glibc-doc debian-keyring bzr libgd-tools libstdc++-11-doc dpkg-dev
      The following NEW packages will be installed:
        bzip2 cpp cpp-11 fontconfig-config fonts-dejavu-core g++ g++-11 gcc gcc-11 gcc-11-base golang-1.18-go golang-1.18-src golang-go
        golang-src libasan6 libatomic1 libc-dev-bin libc-devtools libc6-dev libcc1-0 libcrypt-dev libdeflate0 libdpkg-perl libfile-fcntllock-perl
        libfontconfig1 libfreetype6 libgcc-11-dev libgd3 libgomp1 libisl23 libitm1 libjbig0 libjpeg-turbo8 libjpeg8 libmpc3 libnsl-dev
        libstdc++-11-dev libtiff5 libtirpc-dev libubsan1 libwebp7 libxpm4 linux-libc-dev manpages-dev pkg-config rpcsvc-proto
      0 upgraded, 46 newly installed, 0 to remove and 12 not upgraded.
      Need to get 127 MB of archives.
      After this operation, 592 MB of additional disk space will be used.
      Do you want to continue? [Y/n] Y
      Get:1 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x bzip2 s390x 1.0.8-5build1 [34.4 kB]
      Get:2 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x gcc-11-base s390x 11.3.0-1ubuntu1~22.04 [20.8 kB]
      Get:3 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x libisl23 s390x 0.24-2build1 [701 kB]
      Get:4 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x libmpc3 s390x 1.2.1-2build1 [47.7 kB]
      Get:5 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x cpp-11 s390x 11.3.0-1ubuntu1~22.04 [7848 kB]
      Get:6 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x cpp s390x 4:11.2.0-1ubuntu1 [27.7 kB]
      Get:7 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x fonts-dejavu-core all 2.37-2build1 [1041 kB]
      Get:8 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x fontconfig-config all 2.13.1-4.2ubuntu5 [29.1 kB]
      Get:9 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libcc1-0 s390x 12.1.0-2ubuntu1~22.04 [46.3 kB]
      Get:10 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libgomp1 s390x 12.1.0-2ubuntu1~22.04 [123 kB]
      Get:11 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libitm1 s390x 12.1.0-2ubuntu1~22.04 [29.9 kB]
      Get:12 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libatomic1 s390x 12.1.0-2ubuntu1~22.04 [9008 B]
      Get:13 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libasan6 s390x 11.3.0-1ubuntu1~22.04 [2242 kB]
      Get:14 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libubsan1 s390x 12.1.0-2ubuntu1~22.04 [967 kB]
      Get:15 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libgcc-11-dev s390x 11.3.0-1ubuntu1~22.04 [825 kB]
      Get:16 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x gcc-11 s390x 11.3.0-1ubuntu1~22.04 [15.7 MB]
      Get:17 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x gcc s390x 4:11.2.0-1ubuntu1 [5118 B]
      Get:18 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libc-dev-bin s390x 2.35-0ubuntu3.1 [20.0 kB]
      Get:19 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x linux-libc-dev s390x 5.15.0-60.66 [1338 kB]
      Get:20 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x libcrypt-dev s390x 1:4.4.27-1 [114 kB]
      Get:21 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x rpcsvc-proto s390x 1.4.2-0ubuntu6 [64.7 kB]
      Get:22 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libtirpc-dev s390x 1.3.2-2ubuntu0.1 [189 kB]
      Get:23 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x libnsl-dev s390x 1.3.0-2build2 [70.9 kB]
      Get:24 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libc6-dev s390x 2.35-0ubuntu3.1 [1499 kB]
      Get:25 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libstdc++-11-dev s390x 11.3.0-1ubuntu1~22.04 [2089 kB]
      Get:26 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x g++-11 s390x 11.3.0-1ubuntu1~22.04 [9169 kB]
      Get:27 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x g++ s390x 4:11.2.0-1ubuntu1 [1408 B]
      Get:28 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x golang-1.18-src all 1.18.1-1ubuntu1 [16.2 MB]
      Get:29 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x golang-1.18-go s390x 1.18.1-1ubuntu1 [62.6 MB]
      Get:30 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x golang-src all 2:1.18~0ubuntu2 [4438 B]
      Get:31 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x golang-go s390x 2:1.18~0ubuntu2 [41.8 kB]
      Get:32 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libfreetype6 s390x 2.11.1+dfsg-1ubuntu0.1 [382 kB]
      Get:33 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x libfontconfig1 s390x 2.13.1-4.2ubuntu5 [133 kB]
      Get:34 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x libjpeg-turbo8 s390x 2.1.2-0ubuntu1 [119 kB]
      Get:35 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x libjpeg8 s390x 8c-2ubuntu10 [2264 B]                                           
      Get:36 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x libdeflate0 s390x 1.10-2 [72.1 kB]                                             
      Get:37 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libjbig0 s390x 2.1-3.1ubuntu0.22.04.1 [29.9 kB]                        
      Get:38 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x libwebp7 s390x 1.2.2-2 [167 kB]                                                
      Get:39 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libtiff5 s390x 4.3.0-6ubuntu0.3 [178 kB]                               
      Get:40 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libxpm4 s390x 1:3.5.12-1ubuntu0.22.04.1 [36.7 kB]                      
      Get:41 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x libgd3 s390x 2.3.0-2ubuntu2 [131 kB]                                           
      Get:42 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libc-devtools s390x 2.35-0ubuntu3.1 [29.2 kB]                          
      Get:43 http://ports.ubuntu.com/ubuntu-ports jammy-updates/main s390x libdpkg-perl all 1.21.1ubuntu2.1 [237 kB]                              
      Get:44 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x libfile-fcntllock-perl s390x 0.22-3build7 [33.6 kB]                            
      Get:45 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x manpages-dev all 5.10-1ubuntu1 [2309 kB]                                       
      Get:46 http://ports.ubuntu.com/ubuntu-ports jammy/main s390x pkg-config s390x 0.29.2-1ubuntu3 [47.3 kB]                                     
      Fetched 127 MB in 7s (17.1 MB/s)                                                                                                            
      Extracting templates from packages: 100%
      Selecting previously unselected package bzip2.
      (Reading database ... 56573 files and directories currently installed.)
      Preparing to unpack .../00-bzip2_1.0.8-5build1_s390x.deb ...
      Unpacking bzip2 (1.0.8-5build1) ...
      Selecting previously unselected package gcc-11-base:s390x.
      Preparing to unpack .../01-gcc-11-base_11.3.0-1ubuntu1~22.04_s390x.deb ...
      Unpacking gcc-11-base:s390x (11.3.0-1ubuntu1~22.04) ...
      Selecting previously unselected package libisl23:s390x.
      Preparing to unpack .../02-libisl23_0.24-2build1_s390x.deb ...
      Unpacking libisl23:s390x (0.24-2build1) ...
      Selecting previously unselected package libmpc3:s390x.
      Preparing to unpack .../03-libmpc3_1.2.1-2build1_s390x.deb ...
      Unpacking libmpc3:s390x (1.2.1-2build1) ...
      Selecting previously unselected package cpp-11.
      Preparing to unpack .../04-cpp-11_11.3.0-1ubuntu1~22.04_s390x.deb ...
      Unpacking cpp-11 (11.3.0-1ubuntu1~22.04) ...
      Selecting previously unselected package cpp.
      Preparing to unpack .../05-cpp_4%3a11.2.0-1ubuntu1_s390x.deb ...
      Unpacking cpp (4:11.2.0-1ubuntu1) ...
      Selecting previously unselected package fonts-dejavu-core.
      Preparing to unpack .../06-fonts-dejavu-core_2.37-2build1_all.deb ...
      Unpacking fonts-dejavu-core (2.37-2build1) ...
      Selecting previously unselected package fontconfig-config.
      Preparing to unpack .../07-fontconfig-config_2.13.1-4.2ubuntu5_all.deb ...
      Unpacking fontconfig-config (2.13.1-4.2ubuntu5) ...
      Selecting previously unselected package libcc1-0:s390x.
      Preparing to unpack .../08-libcc1-0_12.1.0-2ubuntu1~22.04_s390x.deb ...
      Unpacking libcc1-0:s390x (12.1.0-2ubuntu1~22.04) ...
      Selecting previously unselected package libgomp1:s390x.
      Preparing to unpack .../09-libgomp1_12.1.0-2ubuntu1~22.04_s390x.deb ...
      Unpacking libgomp1:s390x (12.1.0-2ubuntu1~22.04) ...
      Selecting previously unselected package libitm1:s390x.
      Preparing to unpack .../10-libitm1_12.1.0-2ubuntu1~22.04_s390x.deb ...
      Unpacking libitm1:s390x (12.1.0-2ubuntu1~22.04) ...
      Selecting previously unselected package libatomic1:s390x.
      Preparing to unpack .../11-libatomic1_12.1.0-2ubuntu1~22.04_s390x.deb ...
      Unpacking libatomic1:s390x (12.1.0-2ubuntu1~22.04) ...
      Selecting previously unselected package libasan6:s390x.
      Preparing to unpack .../12-libasan6_11.3.0-1ubuntu1~22.04_s390x.deb ...
      Unpacking libasan6:s390x (11.3.0-1ubuntu1~22.04) ...
      Selecting previously unselected package libubsan1:s390x.
      Preparing to unpack .../13-libubsan1_12.1.0-2ubuntu1~22.04_s390x.deb ...
      Unpacking libubsan1:s390x (12.1.0-2ubuntu1~22.04) ...
      Selecting previously unselected package libgcc-11-dev:s390x.
      Preparing to unpack .../14-libgcc-11-dev_11.3.0-1ubuntu1~22.04_s390x.deb ...
      Unpacking libgcc-11-dev:s390x (11.3.0-1ubuntu1~22.04) ...
      Selecting previously unselected package gcc-11.
      Preparing to unpack .../15-gcc-11_11.3.0-1ubuntu1~22.04_s390x.deb ...
      Unpacking gcc-11 (11.3.0-1ubuntu1~22.04) ...
      Selecting previously unselected package gcc.
      Preparing to unpack .../16-gcc_4%3a11.2.0-1ubuntu1_s390x.deb ...
      Unpacking gcc (4:11.2.0-1ubuntu1) ...
      Selecting previously unselected package libc-dev-bin.
      Preparing to unpack .../17-libc-dev-bin_2.35-0ubuntu3.1_s390x.deb ...
      Unpacking libc-dev-bin (2.35-0ubuntu3.1) ...
      Selecting previously unselected package linux-libc-dev:s390x.
      Preparing to unpack .../18-linux-libc-dev_5.15.0-60.66_s390x.deb ...
      Unpacking linux-libc-dev:s390x (5.15.0-60.66) ...
      Selecting previously unselected package libcrypt-dev:s390x.
      Preparing to unpack .../19-libcrypt-dev_1%3a4.4.27-1_s390x.deb ...
      Unpacking libcrypt-dev:s390x (1:4.4.27-1) ...
      Selecting previously unselected package rpcsvc-proto.
      Preparing to unpack .../20-rpcsvc-proto_1.4.2-0ubuntu6_s390x.deb ...
      Unpacking rpcsvc-proto (1.4.2-0ubuntu6) ...
      Selecting previously unselected package libtirpc-dev:s390x.
      Preparing to unpack .../21-libtirpc-dev_1.3.2-2ubuntu0.1_s390x.deb ...
      Unpacking libtirpc-dev:s390x (1.3.2-2ubuntu0.1) ...
      Selecting previously unselected package libnsl-dev:s390x.
      Preparing to unpack .../22-libnsl-dev_1.3.0-2build2_s390x.deb ...
      Unpacking libnsl-dev:s390x (1.3.0-2build2) ...
      Selecting previously unselected package libc6-dev:s390x.
      Preparing to unpack .../23-libc6-dev_2.35-0ubuntu3.1_s390x.deb ...
      Unpacking libc6-dev:s390x (2.35-0ubuntu3.1) ...
      Selecting previously unselected package libstdc++-11-dev:s390x.
      Preparing to unpack .../24-libstdc++-11-dev_11.3.0-1ubuntu1~22.04_s390x.deb ...
      Unpacking libstdc++-11-dev:s390x (11.3.0-1ubuntu1~22.04) ...
      Selecting previously unselected package g++-11.
      Preparing to unpack .../25-g++-11_11.3.0-1ubuntu1~22.04_s390x.deb ...
      Unpacking g++-11 (11.3.0-1ubuntu1~22.04) ...
      Selecting previously unselected package g++.
      Preparing to unpack .../26-g++_4%3a11.2.0-1ubuntu1_s390x.deb ...
      Unpacking g++ (4:11.2.0-1ubuntu1) ...
      Selecting previously unselected package golang-1.18-src.
      Preparing to unpack .../27-golang-1.18-src_1.18.1-1ubuntu1_all.deb ...
      Unpacking golang-1.18-src (1.18.1-1ubuntu1) ...
      Selecting previously unselected package golang-1.18-go.
      Preparing to unpack .../28-golang-1.18-go_1.18.1-1ubuntu1_s390x.deb ...
      Unpacking golang-1.18-go (1.18.1-1ubuntu1) ...
      Selecting previously unselected package golang-src.
      Preparing to unpack .../29-golang-src_2%3a1.18~0ubuntu2_all.deb ...
      Unpacking golang-src (2:1.18~0ubuntu2) ...
      Selecting previously unselected package golang-go:s390x.
      Preparing to unpack .../30-golang-go_2%3a1.18~0ubuntu2_s390x.deb ...
      Unpacking golang-go:s390x (2:1.18~0ubuntu2) ...
      Selecting previously unselected package libfreetype6:s390x.
      Preparing to unpack .../31-libfreetype6_2.11.1+dfsg-1ubuntu0.1_s390x.deb ...
      Unpacking libfreetype6:s390x (2.11.1+dfsg-1ubuntu0.1) ...
      Selecting previously unselected package libfontconfig1:s390x.
      Preparing to unpack .../32-libfontconfig1_2.13.1-4.2ubuntu5_s390x.deb ...
      Unpacking libfontconfig1:s390x (2.13.1-4.2ubuntu5) ...
      Selecting previously unselected package libjpeg-turbo8:s390x.
      Preparing to unpack .../33-libjpeg-turbo8_2.1.2-0ubuntu1_s390x.deb ...
      Unpacking libjpeg-turbo8:s390x (2.1.2-0ubuntu1) ...
      Selecting previously unselected package libjpeg8:s390x.
      Preparing to unpack .../34-libjpeg8_8c-2ubuntu10_s390x.deb ...
      Unpacking libjpeg8:s390x (8c-2ubuntu10) ...
      Selecting previously unselected package libdeflate0:s390x.
      Preparing to unpack .../35-libdeflate0_1.10-2_s390x.deb ...
      Unpacking libdeflate0:s390x (1.10-2) ...
      Selecting previously unselected package libjbig0:s390x.
      Preparing to unpack .../36-libjbig0_2.1-3.1ubuntu0.22.04.1_s390x.deb ...
      Unpacking libjbig0:s390x (2.1-3.1ubuntu0.22.04.1) ...
      Selecting previously unselected package libwebp7:s390x.
      Preparing to unpack .../37-libwebp7_1.2.2-2_s390x.deb ...
      Unpacking libwebp7:s390x (1.2.2-2) ...
      Selecting previously unselected package libtiff5:s390x.
      Preparing to unpack .../38-libtiff5_4.3.0-6ubuntu0.3_s390x.deb ...
      Unpacking libtiff5:s390x (4.3.0-6ubuntu0.3) ...
      Selecting previously unselected package libxpm4:s390x.
      Preparing to unpack .../39-libxpm4_1%3a3.5.12-1ubuntu0.22.04.1_s390x.deb ...
      Unpacking libxpm4:s390x (1:3.5.12-1ubuntu0.22.04.1) ...
      Selecting previously unselected package libgd3:s390x.
      Preparing to unpack .../40-libgd3_2.3.0-2ubuntu2_s390x.deb ...
      Unpacking libgd3:s390x (2.3.0-2ubuntu2) ...
      Selecting previously unselected package libc-devtools.
      Preparing to unpack .../41-libc-devtools_2.35-0ubuntu3.1_s390x.deb ...
      Unpacking libc-devtools (2.35-0ubuntu3.1) ...
      Selecting previously unselected package libdpkg-perl.
      Preparing to unpack .../42-libdpkg-perl_1.21.1ubuntu2.1_all.deb ...
      Unpacking libdpkg-perl (1.21.1ubuntu2.1) ...
      Selecting previously unselected package libfile-fcntllock-perl.
      Preparing to unpack .../43-libfile-fcntllock-perl_0.22-3build7_s390x.deb ...
      Unpacking libfile-fcntllock-perl (0.22-3build7) ...
      Selecting previously unselected package manpages-dev.
      Preparing to unpack .../44-manpages-dev_5.10-1ubuntu1_all.deb ...
      Unpacking manpages-dev (5.10-1ubuntu1) ...
      Selecting previously unselected package pkg-config.
      Preparing to unpack .../45-pkg-config_0.29.2-1ubuntu3_s390x.deb ...
      Unpacking pkg-config (0.29.2-1ubuntu3) ...
      Setting up gcc-11-base:s390x (11.3.0-1ubuntu1~22.04) ...
      Setting up manpages-dev (5.10-1ubuntu1) ...
      Setting up libxpm4:s390x (1:3.5.12-1ubuntu0.22.04.1) ...
      Setting up libfile-fcntllock-perl (0.22-3build7) ...
      Setting up libdeflate0:s390x (1.10-2) ...
      Setting up linux-libc-dev:s390x (5.15.0-60.66) ...
      Setting up libgomp1:s390x (12.1.0-2ubuntu1~22.04) ...
      Setting up bzip2 (1.0.8-5build1) ...
      Setting up libjbig0:s390x (2.1-3.1ubuntu0.22.04.1) ...
      Setting up libasan6:s390x (11.3.0-1ubuntu1~22.04) ...
      Setting up libtirpc-dev:s390x (1.3.2-2ubuntu0.1) ...
      Setting up rpcsvc-proto (1.4.2-0ubuntu6) ...
      Setting up libfreetype6:s390x (2.11.1+dfsg-1ubuntu0.1) ...
      Setting up libmpc3:s390x (1.2.1-2build1) ...
      Setting up libatomic1:s390x (12.1.0-2ubuntu1~22.04) ...
      Setting up fonts-dejavu-core (2.37-2build1) ...
      Setting up golang-1.18-src (1.18.1-1ubuntu1) ...
      Setting up libjpeg-turbo8:s390x (2.1.2-0ubuntu1) ...
      Setting up libdpkg-perl (1.21.1ubuntu2.1) ...
      Setting up libwebp7:s390x (1.2.2-2) ...
      Setting up libubsan1:s390x (12.1.0-2ubuntu1~22.04) ...
      Setting up libnsl-dev:s390x (1.3.0-2build2) ...
      Setting up libcrypt-dev:s390x (1:4.4.27-1) ...
      Setting up libisl23:s390x (0.24-2build1) ...
      Setting up libc-dev-bin (2.35-0ubuntu3.1) ...
      Setting up golang-src (2:1.18~0ubuntu2) ...
      Setting up libcc1-0:s390x (12.1.0-2ubuntu1~22.04) ...
      Setting up libitm1:s390x (12.1.0-2ubuntu1~22.04) ...
      Setting up libjpeg8:s390x (8c-2ubuntu10) ...
      Setting up cpp-11 (11.3.0-1ubuntu1~22.04) ...
      Setting up fontconfig-config (2.13.1-4.2ubuntu5) ...
      Setting up golang-1.18-go (1.18.1-1ubuntu1) ...
      Setting up pkg-config (0.29.2-1ubuntu3) ...
      Setting up libgcc-11-dev:s390x (11.3.0-1ubuntu1~22.04) ...
      Setting up gcc-11 (11.3.0-1ubuntu1~22.04) ...
      Setting up cpp (4:11.2.0-1ubuntu1) ...
      Setting up libc6-dev:s390x (2.35-0ubuntu3.1) ...
      Setting up libtiff5:s390x (4.3.0-6ubuntu0.3) ...
      Setting up libfontconfig1:s390x (2.13.1-4.2ubuntu5) ...
      Setting up golang-go:s390x (2:1.18~0ubuntu2) ...
      Setting up gcc (4:11.2.0-1ubuntu1) ...
      Setting up libgd3:s390x (2.3.0-2ubuntu2) ...
      Setting up libstdc++-11-dev:s390x (11.3.0-1ubuntu1~22.04) ...
      Setting up libc-devtools (2.35-0ubuntu3.1) ...
      Setting up g++-11 (11.3.0-1ubuntu1~22.04) ...
      Setting up g++ (4:11.2.0-1ubuntu1) ...
      update-alternatives: using /usr/bin/g++ to provide /usr/bin/c++ (c++) in auto mode
      Processing triggers for man-db (2.10.2-1) ...
      Processing triggers for libc-bin (2.35-0ubuntu3.1) ...
      Scanning processes...                                                                                                                        
      Scanning linux images...                                                                                                                     
      
      Running kernel seems to be up-to-date (ABI upgrades are not detected).

      No services need to be restarted.

      No containers need to be restarted.

      No user sessions are running outdated binaries.

      No VM guests are running outdated hypervisor (qemu) binaries on this host.
      ```

Now convince yourself that you have installed Go correctly:

   ``` bash
   go version
   ```

???- example "Go has been installed if you see this:"

      ```
      go version go1.18.1 linux/s390x
      ```

The version of go that you see may differ slightly if a newer version has become available.

## Download the GREP11 code repo

Go to your home directory:

   ``` bash
   cd ~
   ```

The instructor has created a _fork_ of a GitHub repo that is provided by an _IBM Cloud_ GitHub repo. 
The instructor could have just pointed you to the _IBM Cloud_ GitHub repo, but, had he done that, you can
be assured that a file that had not been changed in nineteen months would have changed an hour before 
you started the lab, breaking this section, frustrating you, humiliating the instructor, and by creating
his own fork he can ensure he has complete control of the code, and you probably think he's being paranoid 
and that this sort of thing would never have happened to him during a live demo (on a different product) in
front of an audience of hundreds of people.  Nope, that would never happen.  Fortunately it was only an IBM 
internal audience. The instructor has since recovered.

Having said that, clone the instructor's GitHub repo:

   ``` bash
   git clone https://github.com/silliman/hpcs-grep11-go 
   ```

???- example "Example output from git clone"

      ```
      Cloning into 'hpcs-grep11-go'...
      remote: Enumerating objects: 61, done.
      remote: Counting objects: 100% (61/61), done.
      remote: Compressing objects: 100% (32/32), done.
      remote: Total 61 (delta 22), reused 56 (delta 20), pack-reused 0
      Receiving objects: 100% (61/61), 121.04 KiB | 8.07 MiB/s, done.
      Resolving deltas: 100% (22/22), done.
      ```

The git clone created a directory named `hpcs-grep11-go`, change into it:

   ``` bash
   cd hpcs-grep11-go
   ```

List the contents of the `cert` directory, see that there is a single file named `README` 

   ``` bash
   ls -l certs/
   ```

READ IT (weeping is optional):

   ``` bash
   cat certs/README
   ```

You read that right- more X509 work!! 

## Create TLS certificate and key for your GREP11 Client

1.  Create a working directory to use for the creation of a certificate to allow you to be a client of the GREP11 Server, and then change into it:

    ``` bash
    mkdir -p ~/x509Work/GREP11Client \
    && cd ~/x509Work/GREP11Client
    ```

2. Create an RSA private key

    ``` bash
    openssl genrsa -out client.key 2048
    ```

3. Create a configuration file to avoid interrogation by _openssl_:

    ``` bash

    cat << EOF > client.cnf
    RANDFILE               = \$ENV::HOME/.rnd

    [ req ]
    default_bits           = 2048
    default_keyfile        = keyfile.pem
    distinguished_name     = req_distinguished_name
    attributes             = req_attributes
    prompt                 = no
    output_password        = mypass

    [ req_distinguished_name ]
    C                      = US
    ST                     = Virginia
    L                      = Herndon
    O                      = IBM
    OU                     = Washington Systems Center - IBM IBM Z and LinuxONE
    CN                     = Lab Student
    emailAddress           = student@not.real.email.com

    [ req_attributes ]
    challengePassword              = A challenge password

    [ x509_extensions ]
    subjectKeyIdentifier   = hash
    authorityKeyIdentifier = keyid,issuer
    basicConstraints       = critical,CA:TRUE
    EOF
    ```

4. Create a certificate signing request:

    ``` bash
    openssl req -new -key client.key -out client.csr -config client.cnf
    ```

5. Display the CSR that you created:

    ``` bash
    openssl req -noout -text -in client.csr
    ```

    It will look like this:

    ???- example "Example CSR display"

        ```
        Certificate Request:
            Data:
                Version: 1 (0x0)
                Subject: C = US, ST = Virginia, L = Herndon, O = IBM, OU = Washington Systems Center - IBM IBM Z and LinuxONE, CN = Lab Student, emailAddress = student@not.real.email.com
                Subject Public Key Info:
                    Public Key Algorithm: rsaEncryption
                        Public-Key: (2048 bit)
                        Modulus:
                            00:e4:07:f9:43:c6:6b:fd:2c:39:d4:ef:b4:cb:db:
                            f5:06:b1:82:bc:48:1a:a1:57:3c:1d:05:a9:fe:65:
                            c2:bb:a0:ed:12:58:51:e0:95:2f:44:95:5c:be:88:
                            82:a0:c0:4d:28:62:c4:32:90:66:2e:aa:bc:77:cc:
                            a0:bc:04:3f:22:01:37:58:0a:44:ab:29:9f:4c:01:
                            8b:24:33:21:a5:bf:27:5d:4e:1e:a3:14:15:79:f1:
                            8d:02:61:7b:4d:9f:18:d9:4a:5e:b9:62:d9:c3:96:
                            79:cd:2c:82:f2:f1:3e:8f:ca:29:89:6a:45:b7:48:
                            b5:54:4a:bc:0d:0e:1c:22:8b:f7:8d:e4:72:54:9e:
                            8e:ef:b7:2e:d3:3b:e3:10:9e:1c:35:79:eb:57:1b:
                            aa:61:15:d7:19:6a:89:76:2e:63:19:07:c1:db:92:
                            98:bb:48:2b:e7:55:8b:cb:74:e1:00:76:f5:0a:8e:
                            e8:1a:69:a6:14:bf:7c:7f:eb:a8:ee:ad:b1:f0:df:
                            92:cc:0c:10:3d:42:b3:02:0d:7c:0d:55:38:70:49:
                            ab:84:da:d0:1e:52:1b:19:47:6e:26:b9:8c:cf:0e:
                            12:17:b1:cd:d8:bd:55:2f:fd:6b:6e:12:f1:8b:c4:
                            60:96:67:c4:55:a3:03:43:1b:70:a2:d7:0e:73:c6:
                            79:23
                        Exponent: 65537 (0x10001)
                Attributes:
                    challengePassword        :A challenge password
                    Requested Extensions:
            Signature Algorithm: sha256WithRSAEncryption
            Signature Value:
                3d:ef:35:37:b6:aa:e4:33:cb:fe:17:aa:18:a5:77:d2:23:3b:
                b7:01:ac:6e:65:0c:0c:68:a6:80:37:04:8d:d2:7e:e8:9b:57:
                9d:63:8f:82:06:22:07:7f:bc:6b:b3:60:1c:3f:a1:3d:75:c5:
                3a:d5:f6:74:5c:93:9a:60:9e:40:4a:95:09:bf:38:6b:fb:fb:
                1a:6a:91:be:6d:4d:15:46:79:b4:e1:19:cb:9d:00:97:95:75:
                c1:3a:1d:4b:10:0f:c0:90:5b:f0:b9:5a:e6:b8:6d:11:84:d3:
                0b:aa:7c:eb:07:51:4c:0c:c3:7d:e5:7e:d0:5a:81:f8:0e:3f:
                08:db:3b:78:3d:aa:38:a0:65:60:60:f5:19:4f:47:fe:08:2d:
                4a:af:f9:40:1d:ff:2b:92:67:91:99:eb:b1:8c:cf:d1:2a:c1:
                c0:0d:6c:38:2b:ca:69:d2:40:7e:9d:84:c6:7a:2c:33:d0:28:
                b0:09:97:19:e7:3c:e9:fe:dc:bf:71:a3:00:2f:46:19:f4:1a:
                7c:fc:0f:ff:18:1f:38:77:78:17:49:0e:3e:e7:8a:f0:29:bb:
                a2:37:f4:4a:12:48:e2:ea:1f:14:cc:31:ac:19:6d:b5:bf:98:
                2b:91:aa:a8:71:07:62:54:48:79:55:72:11:9e:87:86:36:3d:
                5d:24:b0:9b
        ```

6. Your GREP11 CA is on your account on the RHEL host. Send your CSR to it:

    ``` bash
    scp client.csr \
    ${StudentID}@192.168.22.64:grep11Lab/x509Work/GREP11Server/clients/. 
    ```

7. **Switch to your terminal tab or window for your RHEL host session.:**

    <img src="../../../images/RHELHost.png" width="351" height="216" />

8. Change to your GREP11 CA working directory:

    ``` bash
    cd ${HOME}/grep11Lab/x509Work/GREP11Server/CA
    ```

9. Display the CSR that the client sent you:

    ``` bash
    openssl req -noout -text -in ../clients/client.csr
    ```

    *Note:* The output is not shown here as it should look the same as when you displayed this same certificate moments ago when you were in "client" mode.

10. Create the certificate for the client:

    ``` bash
    openssl x509 -req -days 300 \
    -in ../clients/client.csr \
    -CA grep11-ca.pem \
    -CAcreateserial \
    -CAkey grep11-ca-key.pem \
    -out ../clients/client.pem
    ```

    ???- example "Output from certificate creation"

        ```
        Signature ok
        subject=C = US, ST = Virginia, L = Herndon, O = IBM, OU = Washington Systems Center - IBM IBM Z and LinuxONE, CN = Lab Student, emailAddress = student@not.real.email.com
        Getting CA Private Key
        ```

11. Display the certificate before sending it to the client:

    ``` bash
    openssl x509 -noout -text -in ../clients/client.pem
    ```

    ???- example "Display of GREP11 client certificate"

        ```
        Certificate:
            Data:
                Version: 1 (0x0)
                Serial Number:
                    79:dd:5b:25:cc:24:f9:71:e5:e0:71:23:db:f8:9e:b8:92:b9:2d:1a
                Signature Algorithm: sha256WithRSAEncryption
                Issuer: C = US, ST = Virginia, L = Herndon, O = IBM, OU = Washington Systems Center - IBM IBM Z and LinuxONE, CN = WSC student02 HPVS CA, emailAddress = student@notreal.email.com.com
                Validity
                    Not Before: Feb 15 16:07:56 2023 GMT
                    Not After : Dec 12 16:07:56 2023 GMT
                Subject: C = US, ST = Virginia, L = Herndon, O = IBM, OU = Washington Systems Center - IBM IBM Z and LinuxONE, CN = Lab Student, emailAddress = student@not.real.email.com
                Subject Public Key Info:
                    Public Key Algorithm: rsaEncryption
                        RSA Public-Key: (2048 bit)
                        Modulus:
                            00:e4:07:f9:43:c6:6b:fd:2c:39:d4:ef:b4:cb:db:
                            f5:06:b1:82:bc:48:1a:a1:57:3c:1d:05:a9:fe:65:
                            c2:bb:a0:ed:12:58:51:e0:95:2f:44:95:5c:be:88:
                            82:a0:c0:4d:28:62:c4:32:90:66:2e:aa:bc:77:cc:
                            a0:bc:04:3f:22:01:37:58:0a:44:ab:29:9f:4c:01:
                            8b:24:33:21:a5:bf:27:5d:4e:1e:a3:14:15:79:f1:
                            8d:02:61:7b:4d:9f:18:d9:4a:5e:b9:62:d9:c3:96:
                            79:cd:2c:82:f2:f1:3e:8f:ca:29:89:6a:45:b7:48:
                            b5:54:4a:bc:0d:0e:1c:22:8b:f7:8d:e4:72:54:9e:
                            8e:ef:b7:2e:d3:3b:e3:10:9e:1c:35:79:eb:57:1b:
                            aa:61:15:d7:19:6a:89:76:2e:63:19:07:c1:db:92:
                            98:bb:48:2b:e7:55:8b:cb:74:e1:00:76:f5:0a:8e:
                            e8:1a:69:a6:14:bf:7c:7f:eb:a8:ee:ad:b1:f0:df:
                            92:cc:0c:10:3d:42:b3:02:0d:7c:0d:55:38:70:49:
                            ab:84:da:d0:1e:52:1b:19:47:6e:26:b9:8c:cf:0e:
                            12:17:b1:cd:d8:bd:55:2f:fd:6b:6e:12:f1:8b:c4:
                            60:96:67:c4:55:a3:03:43:1b:70:a2:d7:0e:73:c6:
                            79:23
                        Exponent: 65537 (0x10001)
            Signature Algorithm: sha256WithRSAEncryption
                66:9c:1a:d8:75:5b:52:1c:8c:f7:d7:63:f6:ea:1e:4e:b1:fa:
                92:78:1a:ab:83:11:7d:73:50:b9:ce:34:2b:33:d1:97:1f:90:
                f6:c7:85:45:20:9f:95:6d:f7:16:f9:64:fd:7b:3d:48:44:33:
                af:9e:dc:5a:f4:56:dd:50:27:6f:3e:9e:75:f2:52:d1:cf:fe:
                76:52:98:8a:0b:cd:62:a6:68:49:34:43:f9:d2:e3:ab:f6:b3:
                3f:fd:ff:3a:92:06:32:2b:c0:64:29:b5:00:c4:b8:66:57:07:
                de:64:8a:7a:88:0b:27:79:5a:6d:8f:4d:52:bf:cc:5e:03:53:
                4a:40:4d:22:e5:e7:0f:c3:1e:6c:2a:cf:79:f2:d5:4b:b3:13:
                be:dd:51:c7:2f:2d:8b:f5:97:1e:3f:86:2e:6c:13:c5:43:0f:
                a6:49:ed:a4:a2:7e:ec:3f:f9:9b:f4:65:f1:ff:d5:9c:60:0f:
                90:a8:18:a7:e0:2a:e4:b9:f2:4c:36:d9:f7:94:c9:a5:71:10:
                bf:56:0d:df:d7:3e:71:a7:f7:d0:cc:dc:52:49:bf:c1:71:72:
                e3:46:89:d6:5d:d4:60:04:a3:5b:46:84:ef:9f:de:02:8c:c8:
                69:89:5a:ef:49:5a:48:fc:72:af:09:21:dd:22:f7:91:b5:57:
                3b:50:e3:58
        ```

12. Send the certificate back to the client that requested it:

    ``` bash
    scp ../clients/client.pem \
    student@${StudentGuestIP}:./x509Work/GREP11Client/.   
    ```

13. Your work as a CA registrar is done for the remainder of the lab!  

    **Switch to your terminal tab or window for your Ubuntu KVM guest session:**

    <img src="../../../images/KVMGuest.png" width="351" height="217" />

14. Ensure you're in the directory that has your certificates and key:

    ``` bash
    cd ${HOME}/x509Work/GREP11Client
    ```

15. Display the client certificate that your CA registrar sent you:

    ``` bash
    openssl x509 -noout -text -in client.pem
    ```

    You know that you know what it should look like so we won't repeat the output here.

16. Now that you have your GREP11 client certificate, copy it, along with its corresponding private key, into the directory where your sample code will be looking for it:

    ``` bash
    cp -ipv client.pem client.key ${HOME}/hpcs-grep11-go/certs/.
    ```

17. Get your CA's public certificate so that you can authenticate your connection with the GREP11 Server:

    ``` bash
    scp ${StudentID}@192.168.22.64:grep11Lab/x509Work/GREP11Server/CA/grep11-ca.pem \
    ${HOME}/hpcs-grep11-go/certs/.
    ```

## Modify GREP11 code for authentication to your GREP11 Server

1. Switch to this directory:

    ``` bash
    cd ${HOME}/hpcs-grep11-go/examples
    ```

2. Run this command to see the one line you need to change in the source code you downloaded:

    ``` bash
    grep --after-context 3 STUDENT server_test.go
    ```

    Your output will look like this:

    ???- example "The line you need to change, plus the files you just made"

        ```
        const Address = "STUDENT_GREP11SERVER_IP:9876"
        const cert = "../certs/client.pem"
        const key = "../certs/client.key"
        const ca = "../certs/grep11-ca.pem"
        ```

3. This command changes the generic placeholder string *STUDENT_GREPSERVER_IP* with the IP of your GREP11 Server, which the instructors stored in an environment variable in your bash login profile at *~/.bashrc*:

    ```
    sed -i -e "s/STUDENT_GREP11SERVER_IP/${GREP11ServerIP}/g" server_test.go
    ```

4. Run this command to ensure that you made the change correctly:

    ```
    grep --after-context 3 ${GREP11ServerIP} server_test.go
    ```

    ???- example "Expected output (your IP will differ, example shown for student02)"

        ```
        const Address = "172.16.0.62:9876"
        const cert = "../certs/client.pem"
        const key = "../certs/client.key"
        const ca = "../certs/grep11-ca.pem"
        ```

## Run the GREP11 client code

Drum roll please...

1. Enter this command to test your GREP11 client code:

    ``` bash
    go test -v
    ```

    Your output should look like this. If it does, you have achieved great success in the lab!!!

    ???- example "Expected output from testing the GREP11 client code"

        ```
        go: downloading google.golang.org/grpc v1.34.0
        go: downloading github.com/gogo/protobuf v1.3.2
        go: downloading github.com/golang/protobuf v1.4.3
        go: downloading golang.org/x/net v0.0.0-20201021035429-f5854403a974
        go: downloading google.golang.org/genproto v0.0.0-20201001141541-efaab9d3c4f7
        go: downloading google.golang.org/protobuf v1.25.0
        go: downloading golang.org/x/sys v0.0.0-20200930185726-fdedc70b468f
        go: downloading golang.org/x/text v0.3.3
        === RUN   Test_signAndVerifyUsingDilithiumKeyPair
        Generated Dilithium key pair
        Data signed
        Verified
        --- PASS: Test_signAndVerifyUsingDilithiumKeyPair (0.05s)
        === RUN   Test_rewrapKeyBlob
            server_test.go:1375: 
            
                Skipping the rewrapKeyBlob test. To enable, comment out the t.Skipf and message lines within the Test_rewrapKeyBlob test
                
                NOTE: This test contains two pauses that require the user to type CTRL-c after ensuring
                    that the stated pre-requisite activity has been completed.  There needs to be 
                    coordination with your HPCS cloud service contact in order to place your HSM
                    into the required states.
            
        --- SKIP: Test_rewrapKeyBlob (0.00s)
        === RUN   Example_getMechanismInfo
        --- PASS: Example_getMechanismInfo (0.01s)
        === RUN   Example_generateGenericKey
        --- PASS: Example_generateGenericKey (0.01s)
        === RUN   Example_encryptAndDecryptUsingAES
        --- PASS: Example_encryptAndDecryptUsingAES (0.02s)
        === RUN   Example_digest
        --- PASS: Example_digest (0.01s)
        === RUN   Example_signAndVerifyUsingRSAKeyPair
        --- PASS: Example_signAndVerifyUsingRSAKeyPair (0.04s)
        === RUN   Example_signAndVerifyUsingDSAKeyPair
        --- PASS: Example_signAndVerifyUsingDSAKeyPair (0.54s)
        === RUN   Example_deriveKeyUsingDHKeyPair
        --- PASS: Example_deriveKeyUsingDHKeyPair (1.51s)
        === RUN   Example_signAndVerifyUsingECDSAKeyPair
        --- PASS: Example_signAndVerifyUsingECDSAKeyPair (0.02s)
        === RUN   Example_signAndVerifyToTestErrorHandling
        --- PASS: Example_signAndVerifyToTestErrorHandling (0.02s)
        === RUN   Example_wrapAndUnwrapKey
        --- PASS: Example_wrapAndUnwrapKey (0.04s)
        === RUN   Example_deriveKey
        --- PASS: Example_deriveKey (0.04s)
        === RUN   Example_wrapAndUnwrapAttributeBoundKey
        --- PASS: Example_wrapAndUnwrapAttributeBoundKey (0.03s)
        === RUN   Example_tls
        --- PASS: Example_tls (0.03s)
        PASS
        ok  	github.com/IBM-Cloud/hpcs-grep11-go/examples	2.373s

        ```

2. Before you go, check out the your rsyslog log messages to see evidence of the test you just ran:

    ``` bash
    journalctl --since "-5 minutes " --output short-full --no-pager
    ```

    The above command assumes you ran this within five minutes of running the test.  If you missed the boat, then just rerun the test again or change the argument to _--since_ to show a longer timeframe of messages.

