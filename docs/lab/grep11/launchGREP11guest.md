# Start the GREP11 Server as a Secure Execution-enabled, HPVS 2.1.x guest

## launch the HPVS 2.1.x GREP11 server

You will start this section from your login session on the RHEL host, and will soon be instructed to switch to your Ubuntu KVM guest session.
But until then, start from this familiar window or tab:

<img src="../../../images/RHELHost.png" width="351" height="216" >

This fancy command figures out the last two characters of your assigned userid and is used in other commands in this section, so that the lab instructions will work for everybody:

   ``` bash
   suffix=$(temp=$(whoami) && echo ${temp: -2})
   ```

You aren't going to change anything here since it's already been defined for you by the instructors, but you can display the KVM guest definition of your HPVS 2.1.x GREP11 Server:

   ``` bash
   sudo virsh dumpxml grep11se${suffix}
   ```

???- example "Definition of KVM guest for GREP11 Server" 

      ``` hl_lines="25 32" 
      <domain type='kvm'>
        <name>grep11se02</name>
        <uuid>2315f8ea-a340-4506-abbf-ae04cf7ea868</uuid>
        <metadata>
          <libosinfo:libosinfo xmlns:libosinfo="http://libosinfo.org/xmlns/libvirt/domain/1.0">
            <libosinfo:os id="http://ubuntu.com/ubuntu/20.04"/>
          </libosinfo:libosinfo>
        </metadata>
        <memory unit='KiB'>3903488</memory>
        <currentMemory unit='KiB'>3903488</currentMemory>
        <vcpu placement='static'>2</vcpu>
        <os>
          <type arch='s390x' machine='s390-ccw-virtio-rhel8.2.0'>hvm</type>
          <boot dev='hd'/>
        </os>
        <cpu mode='host-model' check='partial'/>
        <clock offset='utc'/>
        <on_poweroff>destroy</on_poweroff>
        <on_reboot>restart</on_reboot>
        <on_crash>destroy</on_crash>
        <devices>
          <emulator>/usr/libexec/qemu-kvm</emulator>
          <disk type='file' device='disk'>
            <driver name='qemu' type='qcow2' iommu='on'/>
            <source file='/var/lib/libvirt/images/hpcr/student02/ibm-hyper-protect-container-runtime-23.11.1.qcow2'/>
            <backingStore/>
            <target dev='vda' bus='virtio'/>
            <address type='ccw' cssid='0xfe' ssid='0x0' devno='0x0000'/>
          </disk>
          <disk type='file' device='disk'>
            <driver name='qemu' type='raw' cache='none' io='native' iommu='on'/>
            <source file='/var/lib/libvirt/images/hpcr/student02/ciiso.iso'/>
            <target dev='vdc' bus='virtio'/>
            <readonly/>
            <address type='ccw' cssid='0xfe' ssid='0x0' devno='0x0002'/>
          </disk>
          <controller type='pci' index='0' model='pci-root'/>
          <interface type='network'>
            <mac address='52:54:00:b1:e0:11'/>
            <source network='default'/>
            <model type='virtio'/>
            <driver name='vhost' iommu='on'/>
            <address type='ccw' cssid='0xfe' ssid='0x0' devno='0x0001'/>
          </interface>
          <console type='pty'>
            <target type='sclp' port='0'/>
          </console>
          <audio id='1' type='none'/>
          <memballoon model='none'/>
          <panic model='s390'/>
        </devices>
      </domain>
      ```

In the example output above, observe the two highlighted lines. The first line shows the _Hyper Protect Container Runtime_ image that is provided by Hyper Protect Virtual Servers 2.1.x.  This is the Secure Execution-enabled KVM guest that will read your contract, validate its correctness and integrity, and then start your application workload. The second line shows the _ciiso.iso_ file that you created in the previous section with the `genisoimage` command.  The _ciiso.iso_ file contains your contract.

Start your GREP11 Server and attach to its console.  Watch the messages carefully.  You should not see any failures:

   ``` bash
   sudo virsh start grep11se${suffix} --console
   
   ```

???- example "This is what success looks like"

      ```
      Domain 'grep11se02' started
      Connected to domain 'grep11se02'
      Escape character is ^] (Ctrl + ])
      # HPL11 build:23.11.0 enabler:23.6.0
      # Tue Sep  5 21:07:01 UTC 2023
      # Machine Type/Plant/Serial: 8561/02/31A38
      # create new root partition...
      # encrypt root partition...
      # create root filesystem...
      # write OS to root disk...
      # decrypt user-data...
      2 token decrypted, 0 encrypted token ignored
      # run attestation...
      # set hostname...
      # finish root disk setup...
      # Tue Sep  5 21:07:29 UTC 2023
      # HPL11 build:23.11.0 enabler:23.6.0
      # HPL11099I: bootloader end
      hpcr-dnslookup[729]: HPL14000I: Network connectivity check completed successfully.
      hpcr-logging[871]: Configuring logging ...
      hpcr-logging[872]: Version [1.1.147]
      hpcr-logging[872]: Configuring logging, input [/var/hyperprotect/user-data.decrypted] ...
      hpcr-logging[872]: HPL01010I: Logging has been setup successfully.
      hpcr-logging[871]: Logging has been configured
      hpcr-catch-success[1337]: VSI has started successfully.
      hpcr-catch-success[1337]: HPL10001I: Services succeeded -> systemd triggered hpl-catch-success service
      ```

You will have to enter the ++control+bracket-right++ key-combination to break out of the console.

## verify that GREP11 server log messages are received by rsyslog

**The logging of the GREP11 server is going to the _rsyslog_ service that you configured on your Ubuntu guest, so switch to the terminal tab or window for your KVM standard guest.**

You should still be comfortably logged in on this tab or window:

<img src="../../../images/KVMGuest.png" width="351" height="217" />

The arguments to the _journalctl_ command here aren't the most elegant in the world, but, unless midnight passed since you started your GREP11 Server, you will be able to see messages in rsyslog from when you just started up your GREP11 Server:

   ``` bash
   journalctl --since today --no-pager
   ```

There are a lot of messages logged, a veritable trove of treasure for the curious.  Here is an example of what you should be able to see:

???- example "Log messages in rsyslog from starting the GREP11 Server"

      ```
      Nov 03 21:17:09 ubuntu2204 vpcnode[1623]: authentication probe
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Linux version 5.15.0-79-generic (buildd@bos02-s390x-016) (gcc (Ubuntu 11.3.0-1ubuntu1~22.04.1) 11.3.0, GNU ld (GNU Binutils for Ubuntu) 2.38) #86-Ubuntu SMP Mon Jul 10 16:19:54 UTC 2023 (Ubuntu 5.15.0-79.86-generic 5.15.111)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  setup: Linux is running under KVM in 64-bit mode
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  setup: Relocating AMODE31 section of size 0x00003000
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  setup: The maximum memory size is 3812MB
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  cpu: 2 configured CPUs, 0 standby CPUs
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Write protected kernel read-only data: 18692k
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Zone ranges:
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:    DMA      [mem 0x0000000000000000-0x000000007fffffff]
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:    Normal   [mem 0x0000000080000000-0x00000000ee3fffff]
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Movable zone start for each node
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Early memory node ranges
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:    node   0: [mem 0x0000000000000000-0x00000000ee3fffff]
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Initmem setup node 0 [mem 0x0000000000000000-0x00000000ee3fffff]
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  On node 0, zone Normal: 7168 pages in unavailable ranges
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  percpu: Embedded 32 pages/cpu s91904 r8192 d30976 u131072
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  pcpu-alloc: s91904 r8192 d30976 u131072 alloc=32*4096
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  pcpu-alloc: [0] 0 [0] 1 
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Built 1 zonelists, mobility grouping on.  Total pages: 960624
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Policy zone: Normal
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Kernel command line: panic=0 blacklist=virtio_rng swiotlb=262144 cloud-init=disabled console=ttyS0 printk.time=0 systemd.getty_auto=0 systemd.firstboot=0 module.sig_enforce=1 quiet loglevel=0 systemd.show_status=0
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Unknown kernel command line parameters "blacklist=virtio_rng cloud-init=disabled", will be passed to user space.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Dentry cache hash table entries: 524288 (order: 10, 4194304 bytes, linear)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Inode-cache hash table entries: 262144 (order: 9, 2097152 bytes, linear)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  mem auto-init: stack:off, heap alloc:on, heap free:off
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  software IO TLB: mapped [mem 0x000000005fffc000-0x000000007fffc000] (512MB)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Memory: 3266280K/3903488K available (11988K kernel code, 3212K rwdata, 6704K rodata, 5200K init, 1252K bss, 637208K reserved, 0K cma-reserved)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  SLUB: HWalign=256, Order=0-3, MinObjects=0, CPUs=2, Nodes=1
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  ftrace: allocating 34120 entries in 134 pages
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  ftrace: allocated 134 pages with 3 groups
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  rcu: Hierarchical RCU implementation.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  rcu: #011RCU restricting CPUs from NR_CPUS=512 to nr_cpu_ids=2.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  #011Rude variant of Tasks RCU enabled.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  #011Tracing variant of Tasks RCU enabled.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  rcu: RCU calculated value of scheduler-enlistment delay is 10 jiffies.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  rcu: Adjusting geometry for rcu_fanout_leaf=16, nr_cpu_ids=2
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  NR_IRQS: 3, nr_irqs: 3, preallocated irqs: 3
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  clocksource: tod: mask: 0xffffffffffffffff max_cycles: 0x3b0a9be803b0a9, max_idle_ns: 1805497147909793 ns
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  random: crng init done
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Console: colour dummy device 80x25
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  printk: console [ttyS0] enabled
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  printk: console [ttysclp0] enabled
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Calibrating delay loop (skipped)... 24038.00 BogoMIPS preset
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  pid_max: default: 32768 minimum: 301
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  LSM: Security Framework initializing
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  landlock: Up and running.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Yama: becoming mindful.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  AppArmor: AppArmor initialized
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Mount-cache hash table entries: 8192 (order: 4, 65536 bytes, linear)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Mountpoint-cache hash table entries: 8192 (order: 4, 65536 bytes, linear)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  rcu: Hierarchical SRCU implementation.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  smp: Bringing up secondary CPUs ...
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  smp: Brought up 1 node, 2 CPUs
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  devtmpfs: initialized
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  clocksource: jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 19112604462750000 ns
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  futex hash table entries: 512 (order: 5, 131072 bytes, linear)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  NET: Registered PF_NETLINK/PF_ROUTE protocol family
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  audit: initializing netlink subsys (disabled)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  audit: type=2000 audit(1699046202.087:1): state=initialized audit_enabled=0 res=1
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Spectre V2 mitigation: etokens
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  HugeTLB registered 1.00 MiB page size, pre-allocated 0 pages
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  iommu: Default domain type: Translated 
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  iommu: DMA domain TLB invalidation policy: strict mode 
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  SCSI subsystem initialized
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  NetLabel: Initializing
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  NetLabel:  domain hash size = 128
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  NetLabel:  protocols = UNLABELED CIPSOv4 CALIPSO
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  NetLabel:  unlabeled traffic allowed by default
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  zpci: PCI is not supported because CPU facilities 69 or 71 are not available
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  VFS: Disk quotas dquot_6.6.0
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  VFS: Dquot-cache hash table entries: 512 (order 0, 4096 bytes)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  AppArmor: AppArmor Filesystem Enabled
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  NET: Registered PF_INET protocol family
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  IP idents hash table entries: 65536 (order: 7, 524288 bytes, linear)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  tcp_listen_portaddr_hash hash table entries: 2048 (order: 3, 32768 bytes, linear)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Table-perturb hash table entries: 65536 (order: 6, 262144 bytes, linear)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  TCP established hash table entries: 32768 (order: 6, 262144 bytes, linear)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  TCP bind hash table entries: 32768 (order: 7, 524288 bytes, linear)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  TCP: Hash tables configured (established 32768 bind 32768)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  MPTCP token hash table entries: 4096 (order: 4, 98304 bytes, linear)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  UDP hash table entries: 2048 (order: 4, 65536 bytes, linear)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  UDP-Lite hash table entries: 2048 (order: 4, 65536 bytes, linear)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  NET: Registered PF_UNIX/PF_LOCAL protocol family
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  NET: Registered PF_XDP protocol family
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Trying to unpack rootfs image as initramfs...
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  kvm-s390: SIE is not available
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  hypfs: The hardware system does not support hypfs
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Initialise system trusted keyrings
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Key type blacklist registered
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  workingset: timestamp_bits=45 max_order=20 bucket_order=0
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  zbud: loaded
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  squashfs: version 4.0 (2009/01/31) Phillip Lougher
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  fuse: init (API version 7.34)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  integrity: Platform Keyring initialized
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Key type asymmetric registered
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Asymmetric key parser 'x509' registered
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Block layer SCSI generic (bsg) driver version 0.4 loaded (major 249)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  io scheduler mq-deadline registered
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  hvc_iucv: The z/VM IUCV HVC device driver cannot be used without z/VM
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  loop: module loaded
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  tun: Universal TUN/TAP device driver, 1.6
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  device-mapper: core: CONFIG_IMA_DISABLE_HTABLE is disabled. Duplicate IMA measurements will not be recorded in the IMA log.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  device-mapper: uevent: version 1.0.3
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  device-mapper: ioctl: 4.45.0-ioctl (2021-03-22) initialised: dm-devel@redhat.com
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  drop_monitor: Initializing network drop monitor service
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  NET: Registered PF_INET6 protocol family
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Freeing initrd memory: 9828K
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Segment Routing with IPv6
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  In-situ OAM (IOAM) with IPv6
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  NET: Registered PF_PACKET protocol family
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Key type dns_resolver registered
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  cio: Channel measurement facility initialized using format extended (mode autodetected)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  sclp_sd: Store Data request failed (eq=2, di=3, response=0x40f0, flags=0x00, status=0, rc=-5)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  ap: The hardware system does not support AP instructions
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  registered taskstats version 1
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Loading compiled-in X.509 certificates
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Loaded X.509 cert 'Build time autogenerated kernel key: 033cfe156234b615233dffd1cb0a66d4b6280b04'
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Loaded X.509 cert 'Canonical Ltd. Live Patch Signing: 14df34d1a87cf37625abec039ef2bf521249b969'
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Loaded X.509 cert 'Canonical Ltd. Kernel Module Signing: 88f752e560a1e0737e31163a466ad7b70a850c19'
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  blacklist: Loading compiled-in revocation X.509 certificates
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing: 61482aa2830d0ab2ad5af10b7250da9033ddcef0'
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (2017): 242ade75ac4a15e50d50c84b0d45ff3eae707a03'
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (ESM 2018): 365188c1d374d6b07c3c8f240f8ef722433d6a8b'
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (2019): c0746fd6c5da3ae827864651ad66ae47fe24b3e8'
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (2021 v1): a8d54bbb3825cfb94fa13c9f8a594a195c107b8d'
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (2021 v2): 4cf046892d6fd3c9a5b03f98d845f90851dc6a8c'
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (2021 v3): 100437bb6de6e469b581e61cd66bce3ef4ed53af'
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (Ubuntu Core 2019): c1d57b8f6b743f23ee41f4f7ee292f06eecadfb9'
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  zswap: loaded using pool lzo/zbud
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Key type .fscrypt registered
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Key type fscrypt-provisioning registered
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Key type encrypted registered
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  AppArmor: AppArmor sha1 policy hashing enabled
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  ima: No TPM chip found, activating TPM-bypass!
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Loading compiled-in module X.509 certificates
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Loaded X.509 cert 'Build time autogenerated kernel key: 033cfe156234b615233dffd1cb0a66d4b6280b04'
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  ima: Allocated hash algorithm: sha1
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  ima: No architecture policies found
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  evm: Initialising EVM extended attributes:
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  evm: security.selinux
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  evm: security.SMACK64
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  evm: security.SMACK64EXEC
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  evm: security.SMACK64TRANSMUTE
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  evm: security.SMACK64MMAP
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  evm: security.apparmor
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  evm: security.ima
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  evm: security.capability
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  evm: HMAC attrs: 0x1
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Freeing unused kernel image (initmem) memory: 5200K
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Write protected read-only-after-init data: 136k
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Checked W+X mappings: passed, no unexpected W+X pages found
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Run /init as init process
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:    with arguments:
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:      /init
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:    with environment:
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:      HOME=/
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:      TERM=linux
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:      blacklist=virtio_rng
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:      cloud-init=disabled
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  virtio_blk virtio0: [vda] 209715200 512-byte logical blocks (107 GB/100 GiB)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  GPT:Primary header thinks Alt. header is not at the end of the disk.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  GPT:8388607 != 209715199
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  GPT:Alternate GPT header not at the end of the disk.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  GPT:8388607 != 209715199
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  GPT: Use GNU Parted to correct GPT errors.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:   vda: vda1
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  virtio_blk virtio1: [vdb] 816 512-byte logical blocks (418 kB/408 KiB)
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  EXT4-fs (dm-0): mounted filesystem with ordered data mode. Opts: (null). Quota mode: none.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  EXT4-fs (vda1): mounted filesystem with ordered data mode. Opts: (null). Quota mode: none.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  ISO 9660 Extensions: Microsoft Joliet Level 3
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  ISO 9660 Extensions: RRIP_1991A
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  EXT4-fs (dm-0): re-mounted. Opts: (null). Quota mode: none.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  systemd 249.11-0ubuntu3.9 running in system mode (+PAM +AUDIT +SELINUX +APPARMOR +IMA +SMACK +SECCOMP +GCRYPT +GNUTLS +OPENSSL +ACL +BLKID +CURL +ELFUTILS +FIDO2 +IDN2 -IDN +IPTC +KMOD +LIBCRYPTSETUP +LIBFDISK +PCRE2 -PWQUALITY -P11KIT -QRENCODE +BZIP2 +LZ4 +XZ +ZLIB +ZSTD -XKBCOMMON +UTMP +SYSVINIT default-hierarchy=unified)
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Detected virtualization kvm.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Detected architecture s390x.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Hostname set to <student03-grep11server>.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Initializing machine ID from random generator.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Installed transient /etc/machine-id file.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  /lib/systemd/system/verify-disk-encryption-invoker.service:6: Special user nobody configured, this is not safe!
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  /lib/systemd/system/se-dnslookup.service:10: Special user nobody configured, this is not safe!
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  /lib/systemd/system/hpl-catch-success.service:13: Special user nobody configured, this is not safe!
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  /lib/systemd/system/hpl-catch-failed.service:10: Special user nobody configured, this is not safe!
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  se-logging.service: Found ordering cycle on basic.target/start
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  se-logging.service: Found dependency on sockets.target/start
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  se-logging.service: Found dependency on docker.socket/start
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  se-logging.service: Found dependency on se-registry-auth.service/start
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  se-logging.service: Found dependency on hpl-logging.target/verify-active
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  se-logging.service: Found dependency on se-logging-sync.target/start
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  se-logging.service: Found dependency on se-logging.service/start
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  se-logging.service: Job sockets.target/start deleted to break ordering cycle starting with se-logging.service/start
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Queued start job for default target Multi-User System.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Created slice Slice /system/modprobe.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Created slice Slice /system/systemd-fsck.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Created slice User and Session Slice.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Dispatch Password Requests to Console Directory Watch.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Forward Password Requests to Wall Directory Watch.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Set up automount Arbitrary Executable File Formats File System Automount Point.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Local Encrypted Volumes.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Path Units.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Remote File Systems.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Slice Units.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Swaps.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Local Verity Protected Volumes.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Listening on Syslog Socket.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Listening on fsck to fsckd communication Socket.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Listening on initctl Compatibility Named Pipe.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Listening on Journal Audit Socket.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Listening on Journal Socket (/dev/log).
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Listening on Journal Socket.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Listening on Network Service Netlink Socket.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Listening on udev Control Socket.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Listening on udev Kernel Socket.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Mounting Huge Pages File System...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Mounting POSIX Message Queue File System...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Mounting Kernel Debug File System...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Mounting Kernel Trace File System...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Journal Service...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Set the console keyboard layout...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Create List of Static Device Nodes...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Load Kernel Module chromeos_pstore...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Load Kernel Module configfs...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Load Kernel Module drm...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Load Kernel Module efi_pstore...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Load Kernel Module fuse...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Load Kernel Module pstore_blk...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Load Kernel Module pstore_zone...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Load Kernel Module ramoops...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Condition check resulted in OpenVSwitch configuration for cleanup being skipped.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting File System Check on Root Device...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Load Kernel Modules...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Coldplug All udev Devices...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Mounted Huge Pages File System.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Mounted POSIX Message Queue File System.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Mounted Kernel Debug File System.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Mounted Kernel Trace File System.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Create List of Static Device Nodes.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  modprobe@chromeos_pstore.service: Deactivated successfully.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Load Kernel Module chromeos_pstore.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  modprobe@configfs.service: Deactivated successfully.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Load Kernel Module configfs.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  modprobe@efi_pstore.service: Deactivated successfully.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Load Kernel Module efi_pstore.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  modprobe@fuse.service: Deactivated successfully.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Load Kernel Module fuse.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished File System Check on Root Device.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Mounting FUSE Control File System...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Mounting Kernel Configuration File System...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started File System Check Daemon to report status.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Remount Root and Kernel File Systems...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  modprobe@pstore_zone.service: Deactivated successfully.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Load Kernel Module pstore_zone.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Load Kernel Modules.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Mounted FUSE Control File System.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Apply Kernel Variables...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Mounted Kernel Configuration File System.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  modprobe@ramoops.service: Deactivated successfully.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Load Kernel Module ramoops.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  EXT4-fs (dm-0): re-mounted. Opts: errors=remount-ro. Quota mode: none.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  modprobe@pstore_blk.service: Deactivated successfully.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Load Kernel Module pstore_blk.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Remount Root and Kernel File Systems.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Condition check resulted in Platform Persistent Storage Archival being skipped.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Load/Save Random Seed...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Create System Users...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Coldplug All udev Devices.
      Nov 03 21:17:10 ubuntu2204 systemd-journald[1623]:  Journal started
      Nov 03 21:17:10 ubuntu2204 systemd-journald[1623]:  Runtime Journal (/run/log/journal/b184ee684aa14b2c84d630c3ba938847) is 4.0M, max 32.0M, 28.0M free.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Flush Journal to Persistent Storage...
      Nov 03 21:17:10 ubuntu2204 systemd-fsck[1623]:  /dev/mapper/luks-7006b45c-452a-4138-af93-842ceeb387dc: clean, 26377/6291456 files, 808671/25161728 blocks
      Nov 03 21:17:10 ubuntu2204 systemd-journald[1623]:  Time spent on flushing to /var/log/journal/b184ee684aa14b2c84d630c3ba938847 is 1.769ms for 268 entries.
      Nov 03 21:17:10 ubuntu2204 systemd-journald[1623]:  System Journal (/var/log/journal/b184ee684aa14b2c84d630c3ba938847) is 8.0M, max 4.0G, 3.9G free.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Journal Service.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Load/Save Random Seed.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Condition check resulted in First Boot Complete being skipped.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Create System Users.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Create Static Device Nodes in /dev...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Apply Kernel Variables.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Create Static Device Nodes in /dev.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Rule-based Manager for Device Events and Files...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  modprobe@drm.service: Deactivated successfully.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Load Kernel Module drm.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Rule-based Manager for Device Events and Files.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Network Configuration...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Flush Journal to Persistent Storage.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  VFIO - User Level meta-driver version: 0.3
      Nov 03 21:17:10 ubuntu2204 systemd-networkd[1623]:  lo: Link UP
      Nov 03 21:17:10 ubuntu2204 systemd-networkd[1623]:  lo: Gained carrier
      Nov 03 21:17:10 ubuntu2204 systemd-networkd[1623]:  Enumeration completed
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Network Configuration.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Wait for Network to be Configured...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Set the console keyboard layout.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Preparation for Local File Systems.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Wait for Network to be Configured.
      Nov 03 21:17:10 ubuntu2204 systemd-udevd[1623]:  Using default interface naming scheme 'v249'.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  virtio_net virtio2 enc1: renamed from eth0
      Nov 03 21:17:10 ubuntu2204 systemd-networkd[1623]:  eth0: Interface name change detected, renamed to enc1.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Found device /dev/disk/by-uuid/4d7e976d-b69c-48ec-9a8a-a47cd2e28e70.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting File System Check on /dev/disk/by-uuid/4d7e976d-b69c-48ec-9a8a-a47cd2e28e70...
      Nov 03 21:17:10 ubuntu2204 systemd-udevd[1623]:  Using default interface naming scheme 'v249'.
      Nov 03 21:17:10 ubuntu2204 systemd-networkd[1623]:  enc1: Link UP
      Nov 03 21:17:10 ubuntu2204 systemd-fsck[1623]:  /dev/vda1: clean, 13/262144 files, 140195/1048064 blocks
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished File System Check on /dev/disk/by-uuid/4d7e976d-b69c-48ec-9a8a-a47cd2e28e70.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Mounting /boot...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Mounted /boot.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Local File Systems.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Load AppArmor profiles...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Set console font and keymap...
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  EXT4-fs (vda1): mounted filesystem with ordered data mode. Opts: (null). Quota mode: none.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Set Up Additional Binary Formats...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Condition check resulted in Store a System Token in an EFI Variable being skipped.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Commit a transient machine-id on disk...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Create Volatile Files and Directories...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Set console font and keymap.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  proc-sys-fs-binfmt_misc.automount: Got automount request for /proc/sys/fs/binfmt_misc, triggered by 707 (systemd-binfmt)
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Mounting Arbitrary Executable File Formats File System...
      Nov 03 21:17:10 ubuntu2204 apparmor.systemd[1623]:  Restarting AppArmor
      Nov 03 21:17:10 ubuntu2204 apparmor.systemd[1623]:  Reloading AppArmor profiles
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Create Volatile Files and Directories.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Network Name Resolution...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Network Time Synchronization...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Record System Boot/Shutdown in UTMP...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Record System Boot/Shutdown in UTMP.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Mounted Arbitrary Executable File Formats File System.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Set Up Additional Binary Formats.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Commit a transient machine-id on disk.
      Nov 03 21:17:10 ubuntu2204 systemd-resolved[1623]:  Positive Trust Anchors:
      Nov 03 21:17:10 ubuntu2204 systemd-resolved[1623]:  . IN DS 20326 8 2 e06d44b80b8f1d39a95c0b0d7c65d08458e880409bbc683457104237c7f8ec8d
      Nov 03 21:17:10 ubuntu2204 systemd-resolved[1623]:  Negative trust anchors: home.arpa 10.in-addr.arpa 16.172.in-addr.arpa 17.172.in-addr.arpa 18.172.in-addr.arpa 19.172.in-addr.arpa 20.172.in-addr.arpa 21.172.in-addr.arpa 22.172.in-addr.arpa 23.172.in-addr.arpa 24.172.in-addr.arpa 25.172.in-addr.arpa 26.172.in-addr.arpa 27.172.in-addr.arpa 28.172.in-addr.arpa 29.172.in-addr.arpa 30.172.in-addr.arpa 31.172.in-addr.arpa 168.192.in-addr.arpa d.f.ip6.arpa corp home internal intranet lan local private test
      Nov 03 21:17:10 ubuntu2204 systemd-resolved[1623]:  Using system hostname 'student03-grep11server'.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Network Name Resolution.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Network.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Network is Online.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Host and Network Name Lookups.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Network Time Synchronization.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target System Time Set.
      Nov 03 21:17:10 ubuntu2204 audit[1623]:  AVC apparmor="STATUS" operation="profile_load" profile="unconfined" name="nvidia_modprobe" pid=721 comm="apparmor_parser"
      Nov 03 21:17:10 ubuntu2204 audit[1623]:  AVC apparmor="STATUS" operation="profile_load" profile="unconfined" name="nvidia_modprobe//kmod" pid=721 comm="apparmor_parser"
      Nov 03 21:17:10 ubuntu2204 audit[1623]:  AVC apparmor="STATUS" operation="profile_load" profile="unconfined" name="lsb_release" pid=720 comm="apparmor_parser"
      Nov 03 21:17:10 ubuntu2204 apparmor.systemd[1623]:  Skipping profile in /etc/apparmor.d/disable: usr.sbin.rsyslogd
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  audit: type=1400 audit(1699046227.987:2): apparmor="STATUS" operation="profile_load" profile="unconfined" name="nvidia_modprobe" pid=721 comm="apparmor_parser"
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  audit: type=1400 audit(1699046227.987:3): apparmor="STATUS" operation="profile_load" profile="unconfined" name="nvidia_modprobe//kmod" pid=721 comm="apparmor_parser"
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  audit: type=1400 audit(1699046227.987:4): apparmor="STATUS" operation="profile_load" profile="unconfined" name="lsb_release" pid=720 comm="apparmor_parser"
      Nov 03 21:17:10 ubuntu2204 audit[1623]:  AVC apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/NetworkManager/nm-dhcp-client.action" pid=723 comm="apparmor_parser"
      Nov 03 21:17:10 ubuntu2204 audit[1623]:  AVC apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/NetworkManager/nm-dhcp-helper" pid=723 comm="apparmor_parser"
      Nov 03 21:17:10 ubuntu2204 audit[1623]:  AVC apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/connman/scripts/dhclient-script" pid=723 comm="apparmor_parser"
      Nov 03 21:17:10 ubuntu2204 audit[1623]:  AVC apparmor="STATUS" operation="profile_load" profile="unconfined" name="/{,usr/}sbin/dhclient" pid=723 comm="apparmor_parser"
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Load AppArmor profiles.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target System Initialization.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Daily apt download activities.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Daily apt upgrade and clean activities.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Daily dpkg database backup timer.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Periodic ext4 Online Metadata Check for All Filesystems.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Discard unused blocks once a week.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Daily rotation of log files.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Message of the Day.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Podman auto-update timer.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Daily Cleanup of Temporary Directories.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Condition check resulted in Ubuntu Pro Timer for running repeated jobs being skipped.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Timer for calling verify disk encryption invoker service.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Basic System.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Timer Units.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Listening on D-Bus System Message Bus Socket.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Listening on Podman API Socket.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  audit: type=1400 audit(1699046228.267:5): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/NetworkManager/nm-dhcp-client.action" pid=723 comm="apparmor_parser"
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  audit: type=1400 audit(1699046228.267:6): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/NetworkManager/nm-dhcp-helper" pid=723 comm="apparmor_parser"
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  audit: type=1400 audit(1699046228.267:7): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/connman/scripts/dhclient-script" pid=723 comm="apparmor_parser"
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  audit: type=1400 audit(1699046228.267:8): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/{,usr/}sbin/dhclient" pid=723 comm="apparmor_parser"
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting containerd container runtime...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started D-Bus System Message Bus.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Save initial kernel messages after boot.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Remove Stale Online ext4 Metadata Check Snapshots...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Condition check resulted in getty on tty2-tty6 if dbus and logind are not available being skipped.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Login Prompts.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Dispatcher daemon for systemd-networkd...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Podman auto-update service...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Podman Start All Containers With Restart Policy Set To Always...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Podman API Service...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Logging Configuration...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting User Login Management...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Permit User Sessions...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Condition check resulted in Ubuntu Pro reboot cmds being skipped.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Condition check resulted in Ubuntu Pro Background Auto Attach being skipped.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Podman API Service.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Permit User Sessions.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Set console scheme...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Set console scheme.
      Nov 03 21:17:10 ubuntu2204 dbus-daemon[1623]:  [system] AppArmor D-Bus mediation is enabled
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  e2scrub_reap.service: Deactivated successfully.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Remove Stale Online ext4 Metadata Check Snapshots.
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.397232251Z" level=info msg="starting containerd" revision= version=1.7.2
      Nov 03 21:17:10 ubuntu2204 systemd-logind[1623]:  New seat seat0.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started User Login Management.
      Nov 03 21:17:10 ubuntu2204 networkd-dispatcher[1623]:  No valid path found for iwconfig
      Nov 03 21:17:10 ubuntu2204 networkd-dispatcher[1623]:  No valid path found for iw
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Dispatcher daemon for systemd-networkd.
      Nov 03 21:17:10 ubuntu2204 podman[1623]:  time="2023-11-03T21:17:08Z" level=info msg="/usr/bin/podman filtering at log level info"
      Nov 03 21:17:10 ubuntu2204 podman[1623]:  time="2023-11-03T21:17:08Z" level=info msg="/usr/bin/podman filtering at log level info"
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.458819954Z" level=info msg="loading plugin \"io.containerd.snapshotter.v1.btrfs\"..." type=io.containerd.snapshotter.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.458933679Z" level=info msg="skip loading plugin \"io.containerd.snapshotter.v1.btrfs\"..." error="path /var/lib/containerd/io.containerd.snapshotter.v1.btrfs (ext4) must be a btrfs filesystem to be used with the btrfs snapshotter: skip plugin" type=io.containerd.snapshotter.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.458946844Z" level=info msg="loading plugin \"io.containerd.content.v1.content\"..." type=io.containerd.content.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.459001557Z" level=info msg="loading plugin \"io.containerd.snapshotter.v1.native\"..." type=io.containerd.snapshotter.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.459047059Z" level=info msg="loading plugin \"io.containerd.snapshotter.v1.overlayfs\"..." type=io.containerd.snapshotter.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.459165530Z" level=info msg="loading plugin \"io.containerd.metadata.v1.bolt\"..." type=io.containerd.metadata.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.459199711Z" level=info msg="metadata content store policy set" policy=shared
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.468338746Z" level=info msg="loading plugin \"io.containerd.differ.v1.walking\"..." type=io.containerd.differ.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.468359708Z" level=info msg="loading plugin \"io.containerd.event.v1.exchange\"..." type=io.containerd.event.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.468370061Z" level=info msg="loading plugin \"io.containerd.gc.v1.scheduler\"..." type=io.containerd.gc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.468395192Z" level=info msg="loading plugin \"io.containerd.lease.v1.manager\"..." type=io.containerd.lease.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.468406115Z" level=info msg="loading plugin \"io.containerd.nri.v1.nri\"..." type=io.containerd.nri.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.468413904Z" level=info msg="NRI interface is disabled by configuration."
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.468422012Z" level=info msg="loading plugin \"io.containerd.runtime.v2.task\"..." type=io.containerd.runtime.v2
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.468479297Z" level=info msg="loading plugin \"io.containerd.runtime.v2.shim\"..." type=io.containerd.runtime.v2
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.468489651Z" level=info msg="loading plugin \"io.containerd.sandbox.store.v1.local\"..." type=io.containerd.sandbox.store.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.468499792Z" level=info msg="loading plugin \"io.containerd.sandbox.controller.v1.local\"..." type=io.containerd.sandbox.controller.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.468509604Z" level=info msg="loading plugin \"io.containerd.streaming.v1.manager\"..." type=io.containerd.streaming.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.468519809Z" level=info msg="loading plugin \"io.containerd.service.v1.introspection-service\"..." type=io.containerd.service.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.468747972Z" level=info msg="loading plugin \"io.containerd.service.v1.containers-service\"..." type=io.containerd.service.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.472324256Z" level=info msg="loading plugin \"io.containerd.service.v1.content-service\"..." type=io.containerd.service.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.472337832Z" level=info msg="loading plugin \"io.containerd.service.v1.diff-service\"..." type=io.containerd.service.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.472348826Z" level=info msg="loading plugin \"io.containerd.service.v1.images-service\"..." type=io.containerd.service.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.472360634Z" level=info msg="loading plugin \"io.containerd.service.v1.namespaces-service\"..." type=io.containerd.service.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.472370176Z" level=info msg="loading plugin \"io.containerd.service.v1.snapshots-service\"..." type=io.containerd.service.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.472382214Z" level=info msg="loading plugin \"io.containerd.runtime.v1.linux\"..." type=io.containerd.runtime.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.473948655Z" level=info msg="loading plugin \"io.containerd.monitor.v1.cgroups\"..." type=io.containerd.monitor.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.474155862Z" level=info msg="loading plugin \"io.containerd.service.v1.tasks-service\"..." type=io.containerd.service.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.474179083Z" level=info msg="loading plugin \"io.containerd.grpc.v1.introspection\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.474190243Z" level=info msg="loading plugin \"io.containerd.transfer.v1.local\"..." type=io.containerd.transfer.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.474209104Z" level=info msg="loading plugin \"io.containerd.internal.v1.restart\"..." type=io.containerd.internal.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.474247865Z" level=info msg="loading plugin \"io.containerd.grpc.v1.containers\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.474258782Z" level=info msg="loading plugin \"io.containerd.grpc.v1.content\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.474272365Z" level=info msg="loading plugin \"io.containerd.grpc.v1.diff\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.474282507Z" level=info msg="loading plugin \"io.containerd.grpc.v1.events\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.474293747Z" level=info msg="loading plugin \"io.containerd.grpc.v1.healthcheck\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.474305823Z" level=info msg="loading plugin \"io.containerd.grpc.v1.images\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.474314783Z" level=info msg="loading plugin \"io.containerd.grpc.v1.leases\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.474324198Z" level=info msg="loading plugin \"io.containerd.grpc.v1.namespaces\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.474337486Z" level=info msg="loading plugin \"io.containerd.internal.v1.opt\"..." type=io.containerd.internal.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527101685Z" level=info msg="loading plugin \"io.containerd.grpc.v1.sandbox-controllers\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527118835Z" level=info msg="loading plugin \"io.containerd.grpc.v1.sandboxes\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527129089Z" level=info msg="loading plugin \"io.containerd.grpc.v1.snapshots\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527138210Z" level=info msg="loading plugin \"io.containerd.grpc.v1.streaming\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527147657Z" level=info msg="loading plugin \"io.containerd.grpc.v1.tasks\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527158396Z" level=info msg="loading plugin \"io.containerd.grpc.v1.transfer\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527167768Z" level=info msg="loading plugin \"io.containerd.grpc.v1.version\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527175905Z" level=info msg="loading plugin \"io.containerd.grpc.v1.cri\"..." type=io.containerd.grpc.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527262827Z" level=info msg="Start cri plugin with config {PluginConfig:{ContainerdConfig:{Snapshotter:overlayfs DefaultRuntimeName:runc DefaultRuntime:{Type: Path: Engine: PodAnnotations:[] ContainerAnnotations:[] Root: Options:map[] PrivilegedWithoutHostDevices:false PrivilegedWithoutHostDevicesAllDevicesAllowed:false BaseRuntimeSpec: NetworkPluginConfDir: NetworkPluginMaxConfNum:0 Snapshotter: SandboxMode:} UntrustedWorkloadRuntime:{Type: Path: Engine: PodAnnotations:[] ContainerAnnotations:[] Root: Options:map[] PrivilegedWithoutHostDevices:false PrivilegedWithoutHostDevicesAllDevicesAllowed:false BaseRuntimeSpec: NetworkPluginConfDir: NetworkPluginMaxConfNum:0 Snapshotter: SandboxMode:} Runtimes:map[runc:{Type:io.containerd.runc.v2 Path: Engine: PodAnnotations:[] ContainerAnnotations:[] Root: Options:map[BinaryName: CriuImagePath: CriuPath: CriuWorkPath: IoGid:0 IoUid:0 NoNewKeyring:false NoPivotRoot:false Root: ShimCgroup: SystemdCgroup:false] PrivilegedWithoutHostDevices:false PrivilegedWithoutHostDevicesAllDevicesAllowed:false BaseRuntimeSpec: NetworkPluginConfDir: NetworkPluginMaxConfNum:0 Snapshotter: SandboxMode:podsandbox}] NoPivot:false DisableSnapshotAnnotations:true DiscardUnpackedLayers:false IgnoreBlockIONotEnabledErrors:false IgnoreRdtNotEnabledErrors:false} CniConfig:{NetworkPluginBinDir:/opt/cni/bin NetworkPluginConfDir:/etc/cni/net.d NetworkPluginMaxConfNum:1 NetworkPluginSetupSerially:false NetworkPluginConfTemplate: IPPreference:} Registry:{ConfigPath: Mirrors:map[] Configs:map[] Auths:map[] Headers:map[]} ImageDecryption:{KeyModel:node} DisableTCPService:true StreamServerAddress:127.0.0.1 StreamServerPort:0 StreamIdleTimeout:4h0m0s EnableSelinux:false SelinuxCategoryRange:1024 SandboxImage:registry.k8s.io/pause:3.8 StatsCollectPeriod:10 SystemdCgroup:false EnableTLSStreaming:false X509KeyPairStreaming:{TLSCertFile: TLSKeyFile:} MaxContainerLogLineSize:16384 DisableCgroup:false DisableApparmor:false RestrictOOMScoreAdj:false MaxConcurrentDownloads:3 DisableProcMount:false UnsetSeccompProfile: TolerateMissingHugetlbController:true DisableHugetlbController:true DeviceOwnershipFromSecurityContext:false IgnoreImageDefinedVolumes:false NetNSMountsUnderStateDir:false EnableUnprivilegedPorts:false EnableUnprivilegedICMP:false EnableCDI:false CDISpecDirs:[/etc/cdi /var/run/cdi] ImagePullProgressTimeout:1m0s DrainExecSyncIOTimeout:0s} ContainerdRootDir:/var/lib/containerd ContainerdEndpoint:/run/containerd/containerd.sock RootDir:/var/lib/containerd/io.containerd.grpc.v1.cri StateDir:/run/containerd/io.containerd.grpc.v1.cri}"
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527307831Z" level=info msg="Connect containerd service"
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527324794Z" level=info msg="using legacy CRI server"
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527329246Z" level=info msg="using experimental NRI integration - disable nri plugin to prevent this"
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527345561Z" level=info msg="Get image filesystem path \"/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs\""
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527775095Z" level=info msg="loading plugin \"io.containerd.tracing.processor.v1.otlp\"..." type=io.containerd.tracing.processor.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527792640Z" level=info msg="skip loading plugin \"io.containerd.tracing.processor.v1.otlp\"..." error="no OpenTelemetry endpoint: skip plugin" type=io.containerd.tracing.processor.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527802232Z" level=info msg="loading plugin \"io.containerd.internal.v1.tracing\"..." type=io.containerd.internal.v1
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527811011Z" level=info msg="skipping tracing processor initialization (no tracing plugin)" error="no OpenTelemetry endpoint: skip plugin"
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527863354Z" level=info msg="Start subscribing containerd event"
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527904732Z" level=info msg="Start recovering state"
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527948018Z" level=info msg="Start event monitor"
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527960047Z" level=info msg="Start snapshots syncer"
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527965955Z" level=info msg=serving... address=/run/containerd/containerd.sock.ttrpc
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527984042Z" level=info msg=serving... address=/run/containerd/containerd.sock
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527967814Z" level=info msg="Start cni network conf syncer for default"
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.527999417Z" level=info msg="Start streaming server"
      Nov 03 21:17:10 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:08.528355247Z" level=info msg="containerd successfully booted in 0.138453s"
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started containerd container runtime.
      Nov 03 21:17:10 ubuntu2204 podman[1623]:  time="2023-11-03T21:17:08Z" level=info msg="[graphdriver] using prior storage driver: overlay"
      Nov 03 21:17:10 ubuntu2204 podman[1623]:  time="2023-11-03T21:17:08Z" level=info msg="Found CNI network podman (type=bridge) at /etc/cni/net.d/87-podman-bridge.conflist"
      Nov 03 21:17:10 ubuntu2204 podman[1623]:  time="2023-11-03T21:17:08Z" level=info msg="Found CNI network podman (type=bridge) at /etc/cni/net.d/87-podman-bridge.conflist"
      Nov 03 21:17:10 ubuntu2204 podman[1623]:  2023-11-03 21:17:08.56829009 +0000 UTC m=+0.254283797 system refresh
      Nov 03 21:17:10 ubuntu2204 podman[1623]:  time="2023-11-03T21:17:08Z" level=info msg="Setting parallel job count to 7"
      Nov 03 21:17:10 ubuntu2204 podman[1623]:  time="2023-11-03T21:17:08Z" level=info msg="Setting parallel job count to 7"
      Nov 03 21:17:10 ubuntu2204 podman[1623]:  time="2023-11-03T21:17:08Z" level=info msg="using systemd socket activation to determine API endpoint"
      Nov 03 21:17:10 ubuntu2204 podman[1623]:  time="2023-11-03T21:17:08Z" level=info msg="using API endpoint: ''"
      Nov 03 21:17:10 ubuntu2204 podman[1623]:  time="2023-11-03T21:17:08Z" level=info msg="API service listening on \"/run/podman/podman.sock\""
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  etc-machine\x2did.mount: Deactivated successfully.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  var-lib-containers-storage-overlay.mount: Deactivated successfully.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  var-lib-containers-storage-overlay.mount: Deactivated successfully.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Podman Start All Containers With Restart Policy Set To Always.
      Nov 03 21:17:10 ubuntu2204 systemd-networkd[1623]:  enc1: Gained carrier
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  IPv6: ADDRCONF(NETDEV_CHANGE): enc1: link becomes ready
      Nov 03 21:17:10 ubuntu2204 systemd-networkd[1623]:  enc1: DHCPv4 address 172.16.0.63/24 via 172.16.0.1
      Nov 03 21:17:10 ubuntu2204 dbus-daemon[1623]:  [system] Activating via systemd: service name='org.freedesktop.hostname1' unit='dbus-org.freedesktop.hostname1.service' requested by ':1.2' (uid=100 pid=641 comm="/lib/systemd/systemd-networkd " label="unconfined")
      Nov 03 21:17:10 ubuntu2204 systemd-timesyncd[1623]:  Network configuration changed, trying to establish connection.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Hostname Service...
      Nov 03 21:17:10 ubuntu2204 dbus-daemon[1623]:  [system] Successfully activated service 'org.freedesktop.hostname1'
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Hostname Service.
      Nov 03 21:17:10 ubuntu2204 systemd-networkd[1623]:  Could not set hostname: Access denied
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  podman-auto-update.service: Deactivated successfully.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Podman auto-update service.
      Nov 03 21:17:10 ubuntu2204 systemd-timesyncd[1623]:  Initial synchronization to time server 185.125.190.58:123 (ntp.ubuntu.com).
      Nov 03 21:17:10 ubuntu2204 hpcr-dnslookup[1623]:  HPL14000I: Network connectivity check completed successfully.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Logging Configuration.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Early Initialization.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Logging to remote monitoring server is initiated..
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Logging Configuration...
      Nov 03 21:17:10 ubuntu2204 hpcr-logging[1623]:  Configuring logging ...
      Nov 03 21:17:10 ubuntu2204 hpcr-logging[1623]:  Version [1.1.145]
      Nov 03 21:17:10 ubuntu2204 hpcr-logging[1623]:  Configuring logging, input [/var/hyperprotect/user-data.decrypted] ...
      Nov 03 21:17:10 ubuntu2204 hpcr-logging[1623]:  ValidateContractE ...
      Nov 03 21:17:10 ubuntu2204 hpcr-logging[1623]:  config written: /etc/rsyslog.d/22-logging.conf
      Nov 03 21:17:10 ubuntu2204 hpcr-logging[1623]:  HPL01010I: Logging has been setup successfully.
      Nov 03 21:17:10 ubuntu2204 hpcr-logging[1623]:  Logging has been configured
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Logging Configuration.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting System Logging Service...
      Nov 03 21:17:10 ubuntu2204 rsyslogd[1623]:  rsyslogd's groupid changed to 111
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started System Logging Service.
      Nov 03 21:17:10 ubuntu2204 rsyslogd[1623]:  rsyslogd's userid changed to 104
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Synchronizes the Logging Target.
      Nov 03 21:17:10 ubuntu2204 rsyslogd[1623]:  [origin software="rsyslogd" swVersion="8.2112.0" x-pid="882" x-info="https://www.rsyslog.com"] start
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Logging to remote log server is initiated..
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Service that does validation of contract...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting HPCR Registry Authentication...
      Nov 03 21:17:10 ubuntu2204 rsyslogd[1623]:  imjournal: No statefile exists, /var/spool/rsyslog/journal_state will be created (ignore if this is first run): No such file or directory [v8.2112.0 try https://www.rsyslog.com/e/2040 ]
      Nov 03 21:17:10 ubuntu2204 hpcr-registry-auth[1623]:  Starting Registry Authentication ...
      Nov 03 21:17:10 ubuntu2204 hpcr-registry-auth[1623]:  Version [1.0.70]
      Nov 03 21:17:10 ubuntu2204 hpcr-registry-auth[1623]:  Writing auth config: /root/.docker/config.json
      Nov 03 21:17:10 ubuntu2204 hpcr-registry-auth[1623]:  Registry Authentication started
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished HPCR Registry Authentication.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Docker Socket for the API...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Listening on Docker Socket for the API.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Docker Application Container Engine...
      Nov 03 21:17:10 ubuntu2204 hpcr-contract[1623]:  Welcome to SE Contract Validator
      Nov 03 21:17:10 ubuntu2204 hpcr-contract[1623]:  Contract file passed is:  /var/hyperprotect/user-data.decrypted
      Nov 03 21:17:10 ubuntu2204 rsyslogd[1623]:  imjournal: journal files changed, reloading...  [v8.2112.0 try https://www.rsyslog.com/e/0 ]
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:09.575115627Z" level=info msg="Starting up"
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:09.576881278Z" level=info msg="detected 127.0.0.53 nameserver, assuming systemd-resolved, so using resolv.conf: /run/systemd/resolve/resolv.conf"
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  var-lib-containers-storage-overlay.mount: Deactivated successfully.
      Nov 03 21:17:10 ubuntu2204 audit[1623]:  AVC apparmor="STATUS" operation="profile_load" profile="unconfined" name="docker-default" pid=907 comm="apparmor_parser"
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:09.666341245Z" level=info msg="parsed scheme: \"unix\"" module=grpc
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:09.666350343Z" level=info msg="scheme \"unix\" not registered, fallback to default scheme" module=grpc
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:09.666366055Z" level=info msg="ccResolverWrapper: sending update to cc: {[{unix:///run/containerd/containerd.sock  <nil> 0 <nil>}] <nil> <nil>}" module=grpc
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:09.666373355Z" level=info msg="ClientConn switching balancer to \"pick_first\"" module=grpc
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  audit: type=1400 audit(1699046229.648:9): apparmor="STATUS" operation="profile_load" profile="unconfined" name="docker-default" pid=907 comm="apparmor_parser"
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:09.667559573Z" level=info msg="parsed scheme: \"unix\"" module=grpc
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:09.667571887Z" level=info msg="scheme \"unix\" not registered, fallback to default scheme" module=grpc
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:09.667582900Z" level=info msg="ccResolverWrapper: sending update to cc: {[{unix:///run/containerd/containerd.sock  <nil> 0 <nil>}] <nil> <nil>}" module=grpc
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:09.667593568Z" level=info msg="ClientConn switching balancer to \"pick_first\"" module=grpc
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:09.739086044Z" level=info msg="Loading containers: start."
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  bridge: filtering via arp/ip/ip6tables is no longer available by default. Update your scripts to load br_netfilter if you need this.
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Bridge firewalling registered
      Nov 03 21:17:10 ubuntu2204 hpcr-contract[1623]:  Contract file is valid.
      Nov 03 21:17:10 ubuntu2204 hpcr-contract[1623]:  Extracting workload from /var/hyperprotect/user-data.decrypted to /var/hyperprotect/workload-data.decrypted
      Nov 03 21:17:10 ubuntu2204 hpcr-contract[1623]:  Extraction completed
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Service that does validation of contract.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Service that does signature validation of Env Workload of contract...
      Nov 03 21:17:10 ubuntu2204 hpcr-signature[1623]:  Welcome to SE ENV Workload Signature Validator
      Nov 03 21:17:10 ubuntu2204 hpcr-signature[1623]:  Decrypted Contract file passed is:  /var/hyperprotect/workload-data.decrypted
      Nov 03 21:17:10 ubuntu2204 hpcr-signature[1623]:  Encrypted Contract file passed is:  /var/hyperprotect/cidata/user-data
      Nov 03 21:17:10 ubuntu2204 hpcr-signature[1623]:  Check Dependency params Public key and EnvWorkload signature
      Nov 03 21:17:10 ubuntu2204 hpcr-signature[1623]:  Access Public key and EnvWorkload signature
      Nov 03 21:17:10 ubuntu2204 hpcr-signature[1623]:  Create combined EnvWorkload contract content
      Nov 03 21:17:10 ubuntu2204 hpcr-signature[1623]:  Verify signing key, signature and combined EnvWorkload contract
      Nov 03 21:17:10 ubuntu2204 hpcr-signature[1623]:  Verified OK
      Nov 03 21:17:10 ubuntu2204 hpcr-signature[1623]:  Successfully verified contract with signature and signing key
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Service that does signature validation of Env Workload of contract.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Contract is unpacked and ready for consumption..
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Service that waits until the user devices are ready...
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Set podman image policy...
      Nov 03 21:17:10 ubuntu2204 hpcr-disk-standby[1623]:  Waiting for devices ...
      Nov 03 21:17:10 ubuntu2204 hpcr-disk-standby[1623]:  Version [1.0.112]
      Nov 03 21:17:10 ubuntu2204 hpcr-disk-standby[1623]:  WaitForDevices input=[/var/hyperprotect/user-data.decrypted], timeout=[2023-11-03 21:32:09.840358244 +0000 UTC m=+900.009364631]
      Nov 03 21:17:10 ubuntu2204 hpcr-disk-standby[1623]:  ParseContract ...
      Nov 03 21:17:10 ubuntu2204 hpcr-disk-standby[1623]:  ValidateContract ...
      Nov 03 21:17:10 ubuntu2204 hpcr-disk-standby[1623]:  MergeVolumes ...
      Nov 03 21:17:10 ubuntu2204 hpcr-disk-standby[1623]:  Waiting for devices is completed
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Service that waits until the user devices are ready.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Service that mounts the data volumes after they are ready...
      Nov 03 21:17:10 ubuntu2204 hpcr-disk-mount[1623]:  Mounting volumes ...
      Nov 03 21:17:10 ubuntu2204 hpcr-disk-mount[1623]:  Version [1.0.112]
      Nov 03 21:17:10 ubuntu2204 hpcr-disk-mount[1623]:  MountVolumes input=[/var/hyperprotect/user-data.decrypted]
      Nov 03 21:17:10 ubuntu2204 hpcr-disk-mount[1623]:  ParseContract ...
      Nov 03 21:17:10 ubuntu2204 hpcr-disk-mount[1623]:  ValidateContract ...
      Nov 03 21:17:10 ubuntu2204 hpcr-disk-mount[1623]:  MergeVolumes ...
      Nov 03 21:17:10 ubuntu2204 hpcr-disk-mount[1623]:  Mounting volumes ...
      Nov 03 21:17:10 ubuntu2204 hpcr-disk-mount[1623]:  Volume config ..
      Nov 03 21:17:10 ubuntu2204 hpcr-disk-mount[1623]:  HPL07003I: Mounting volumes done
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Service that mounts the data volumes after they are ready.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Reached target Data volumes are mounted ready to be used..
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Service that verifies all disks are encrypted and logs output to systemd journal.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Service that periodically logs entry to trigger verify disk encryption service.
      Nov 03 21:17:10 ubuntu2204 verify-disk-encryption[1623]:  Verify disk encryption started...
      Nov 03 21:17:10 ubuntu2204 kernel[1623]:  Initializing XFRM netlink socket
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:09.941735406Z" level=info msg="Default bridge (docker0) is assigned with an IP address 172.17.0.0/16. Daemon option --bip can be used to set a preferred IP address"
      Nov 03 21:17:10 ubuntu2204 networkd-dispatcher[1623]:  WARNING:Unknown index 3 seen, reloading interface list
      Nov 03 21:17:10 ubuntu2204 hpcr-image-play[1623]:  Getting image source signatures
      Nov 03 21:17:10 ubuntu2204 hpcr-image-play[1623]:  Copying blob sha256:66d62867ae2452322f4769f943913be00b22e73039d1902e8f785b9f49838193
      Nov 03 21:17:10 ubuntu2204 systemd-networkd[1623]:  docker0: Link UP
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:10.008006157Z" level=info msg="Loading containers: done."
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:10.053088827Z" level=info msg="Docker daemon" commit="20.10.25-0ubuntu1~22.04.2" graphdriver(s)=overlay2 version=20.10.25
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:10.053138638Z" level=info msg="Daemon has completed initialization"
      Nov 03 21:17:10 ubuntu2204 hpcr-image-play[1623]:  Copying config sha256:9eca761232387055827db0a9f2232f2635bc8c6d5f23ecfb39d34bb4ab0dca09
      Nov 03 21:17:10 ubuntu2204 hpcr-image-play[1623]:  Writing manifest to image destination
      Nov 03 21:17:10 ubuntu2204 hpcr-image-play[1623]:  Storing signatures
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Started Docker Application Container Engine.
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:10.081309751Z" level=info msg="API listen on /run/docker.sock"
      Nov 03 21:17:10 ubuntu2204 hpcr-image-play[1623]:  Loaded image(s): k8s.gcr.io/pause:3.5
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Set docker image policy...
      Nov 03 21:17:10 ubuntu2204 hpcr-image[1623]:  Starting image service...
      Nov 03 21:17:10 ubuntu2204 hpcr-image[1623]:  Contract yaml file: /var/hyperprotect/workload-data.decrypted
      Nov 03 21:17:10 ubuntu2204 hpcr-image[1623]:  Extracting image contract
      Nov 03 21:17:10 ubuntu2204 hpcr-image[1623]:  Successfully extracted Image contract
      Nov 03 21:17:10 ubuntu2204 hpcr-image[1623]:  Extracting container contract
      Nov 03 21:17:10 ubuntu2204 hpcr-image[1623]:  Checking for image with digest
      Nov 03 21:17:10 ubuntu2204 hpcr-image[1623]:  No image for DCT verification
      Nov 03 21:17:10 ubuntu2204 hpcr-image[1623]:  Image service completed successfully
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Set docker image policy.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Service that creates a set of containers...
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  Starting container service...
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  Validating contract...
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  Compose folder /data1/compose created
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  Contract yaml file: /var/hyperprotect/workload-data.decrypted
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  Compose folder: /data1/compose
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  Validation completed
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  Parsing contract...
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  Parsing of the Contract File completed successfully
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  Extracting compose...
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  Extracting done...
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  Extracting the ENV Contents...
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  Writing new env file /data1/compose/.env ...
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  Reading existing env file /data1/compose/.env ...
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  Extracting of environment contents done
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  Check if docker is ready
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  docker-compose.yml file is present in the directory
      Nov 03 21:17:10 ubuntu2204 hpcr-container[1623]:  Starting workload containers...
      Nov 03 21:17:10 ubuntu2204 podman[1623]:  2023-11-03 21:17:09.987464201 +0000 UTC m=+0.156960970 image loadfromarchive  /usr/local/se-image-play/pause.tar
      Nov 03 21:17:10 ubuntu2204 sudo[1623]:      root : PWD=/ ; USER=nobody ; COMMAND=/usr/local/bin/se-image-play
      Nov 03 21:17:10 ubuntu2204 sudo[1623]:  pam_unix(sudo:session): session opened for user nobody(uid=65534) by (uid=0)
      Nov 03 21:17:10 ubuntu2204 hpcr-image-play[1623]:  Version [1.1.112]
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Set podman image policy.
      Nov 03 21:17:10 ubuntu2204 sudo[1623]:  pam_unix(sudo:session): session closed for user nobody
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Starting Service that creates a set of containers...
      Nov 03 21:17:10 ubuntu2204 sudo[1623]:      root : PWD=/ ; USER=nobody ; COMMAND=/usr/local/bin/se-container-play
      Nov 03 21:17:10 ubuntu2204 sudo[1623]:  pam_unix(sudo:session): session opened for user nobody(uid=65534) by (uid=0)
      Nov 03 21:17:10 ubuntu2204 hpcr-container-play[1623]:  Version [1.1.116]
      Nov 03 21:17:10 ubuntu2204 sudo[1623]:  pam_unix(sudo:session): session closed for user nobody
      Nov 03 21:17:10 ubuntu2204 hpcr-container-play[1623]:  HPL15004I: The pod started successfully.
      Nov 03 21:17:10 ubuntu2204 hpcr-container-play[1623]:  HPL15006I: No pod definitions found.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  Finished Service that creates a set of containers.
      Nov 03 21:17:10 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:10.342435060Z" level=warning msg="reference for unknown type: " digest="sha256:1ebda8a7124c99735f5e7743dfc7ff335dd3e68f7b75f5ca9a41fed6e409d513" remote="quay.io/bsilliman/grep11server@sha256:1ebda8a7124c99735f5e7743dfc7ff335dd3e68f7b75f5ca9a41fed6e409d513"
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  var-lib-docker-overlay2-opaque\x2dbug\x2dcheck224199160-merged.mount: Deactivated successfully.
      Nov 03 21:17:10 ubuntu2204 systemd[1623]:  var-lib-containers-storage-overlay.mount: Deactivated successfully.
      Nov 03 21:17:10 ubuntu2204 systemd-networkd[1623]:  enc1: Gained IPv6LL
      Nov 03 21:17:12 ubuntu2204 networkd-dispatcher[1623]:  WARNING:Unknown index 4 seen, reloading interface list
      Nov 03 21:17:12 ubuntu2204 systemd-networkd[1623]:  br-cd39c66a7dab: Link UP
      Nov 03 21:17:13 ubuntu2204 systemd[1623]:  var-lib-docker-overlay2-77e9a4f23d5acf016c56589279b3f0e99df2c1d147692202f00c7c2e138ec96c\x2dinit-merged.mount: Deactivated successfully.
      Nov 03 21:17:13 ubuntu2204 systemd-udevd[1623]:  Using default interface naming scheme 'v249'.
      Nov 03 21:17:13 ubuntu2204 networkd-dispatcher[1623]:  WARNING:Unknown index 5 seen, reloading interface list
      Nov 03 21:17:13 ubuntu2204 systemd-networkd[1623]:  veth2cbfbb7: Link UP
      Nov 03 21:17:13 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:13.141237481Z" level=info msg="No non-localhost DNS nameservers are left in resolv.conf. Using default external servers: [nameserver 8.8.8.8 nameserver 8.8.4.4]"
      Nov 03 21:17:13 ubuntu2204 dockerd[1623]:  time="2023-11-03T21:17:13.141386294Z" level=info msg="IPv6 enabled; Adding default IPv6 external servers: [nameserver 2001:4860:4860::8888 nameserver 2001:4860:4860::8844]"
      Nov 03 21:17:13 ubuntu2204 kernel[1623]:  br-cd39c66a7dab: port 1(veth2cbfbb7) entered blocking state
      Nov 03 21:17:13 ubuntu2204 kernel[1623]:  br-cd39c66a7dab: port 1(veth2cbfbb7) entered disabled state
      Nov 03 21:17:13 ubuntu2204 kernel[1623]:  device veth2cbfbb7 entered promiscuous mode
      Nov 03 21:17:13 ubuntu2204 kernel[1623]:  br-cd39c66a7dab: port 1(veth2cbfbb7) entered blocking state
      Nov 03 21:17:13 ubuntu2204 kernel[1623]:  br-cd39c66a7dab: port 1(veth2cbfbb7) entered forwarding state
      Nov 03 21:17:13 ubuntu2204 kernel[1623]:  br-cd39c66a7dab: port 1(veth2cbfbb7) entered disabled state
      Nov 03 21:17:13 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:13.191880223Z" level=info msg="loading plugin \"io.containerd.internal.v1.shutdown\"..." runtime=io.containerd.runc.v2 type=io.containerd.internal.v1
      Nov 03 21:17:13 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:13.191936390Z" level=info msg="loading plugin \"io.containerd.ttrpc.v1.pause\"..." runtime=io.containerd.runc.v2 type=io.containerd.ttrpc.v1
      Nov 03 21:17:13 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:13.191960133Z" level=info msg="loading plugin \"io.containerd.event.v1.publisher\"..." runtime=io.containerd.runc.v2 type=io.containerd.event.v1
      Nov 03 21:17:13 ubuntu2204 containerd[1623]:  time="2023-11-03T21:17:13.191969961Z" level=info msg="loading plugin \"io.containerd.ttrpc.v1.task\"..." runtime=io.containerd.runc.v2 type=io.containerd.ttrpc.v1
      Nov 03 21:17:13 ubuntu2204 systemd[1623]:  Started libcontainer container 17cd821b28ff5ce5d19935577579a4a50a9bc2dc169d76f5e1dfc954069a6296.
      Nov 03 21:17:13 ubuntu2204 systemd[1623]:  dmesg.service: Deactivated successfully.
      Nov 03 21:17:13 ubuntu2204 kernel[1623]:  eth0: renamed from veth9c911d0
      Nov 03 21:17:13 ubuntu2204 systemd-networkd[1623]:  veth2cbfbb7: Gained carrier
      Nov 03 21:17:13 ubuntu2204 systemd-networkd[1623]:  br-cd39c66a7dab: Gained carrier
      Nov 03 21:17:13 ubuntu2204 kernel[1623]:  IPv6: ADDRCONF(NETDEV_CHANGE): veth2cbfbb7: link becomes ready
      Nov 03 21:17:13 ubuntu2204 kernel[1623]:  br-cd39c66a7dab: port 1(veth2cbfbb7) entered blocking state
      Nov 03 21:17:13 ubuntu2204 kernel[1623]:  br-cd39c66a7dab: port 1(veth2cbfbb7) entered forwarding state
      Nov 03 21:17:13 ubuntu2204 kernel[1623]:  IPv6: ADDRCONF(NETDEV_CHANGE): br-cd39c66a7dab: link becomes ready
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  Docker Compose Logs:
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:   student03-ep11server Pulling
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  21e3243b9c65 Pulling fs layer
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d6b978c9eb5e Pulling fs layer
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  36f5e25998cd Pulling fs layer
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  476f8c03ea25 Pulling fs layer
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  880f5aff73f5 Pulling fs layer
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  811cce5aca48 Pulling fs layer
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  419807452f87 Pulling fs layer
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  cd975f3ecadc Pulling fs layer
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  a77a1a63fd9a Pulling fs layer
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d4c9ed9a08b7 Pulling fs layer
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  476f8c03ea25 Waiting
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  880f5aff73f5 Waiting
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  811cce5aca48 Waiting
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  419807452f87 Waiting
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  cd975f3ecadc Waiting
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  a77a1a63fd9a Waiting
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d4c9ed9a08b7 Waiting
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  21e3243b9c65 Downloading [=================================>                 ]     602B/891B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  21e3243b9c65 Downloading [==================================================>]     891B/891B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  21e3243b9c65 Verifying Checksum
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  21e3243b9c65 Download complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  21e3243b9c65 Extracting [==================================================>]     891B/891B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  21e3243b9c65 Extracting [==================================================>]     891B/891B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  21e3243b9c65 Pull complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d6b978c9eb5e Downloading [>                                                  ]   1.98kB/121.3kB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d6b978c9eb5e Downloading [==================================================>]  121.3kB/121.3kB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d6b978c9eb5e Verifying Checksum
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d6b978c9eb5e Download complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d6b978c9eb5e Extracting [=============>                                     ]  32.77kB/121.3kB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d6b978c9eb5e Extracting [==================================================>]  121.3kB/121.3kB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d6b978c9eb5e Extracting [==================================================>]  121.3kB/121.3kB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d6b978c9eb5e Pull complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  880f5aff73f5 Downloading [==================================================>]     167B/167B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  880f5aff73f5 Verifying Checksum
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  880f5aff73f5 Download complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  476f8c03ea25 Downloading [>                                                  ]  73.99kB/7.055MB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  36f5e25998cd Downloading [>                                                  ]    148kB/14.8MB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  36f5e25998cd Downloading [=========>                                         ]  2.881MB/14.8MB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  811cce5aca48 Downloading [==================================================>]     173B/173B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  811cce5aca48 Verifying Checksum
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  811cce5aca48 Download complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  36f5e25998cd Downloading [==================================>                ]  10.07MB/14.8MB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  36f5e25998cd Verifying Checksum
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  36f5e25998cd Download complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  419807452f87 Downloading [==================================================>]     156B/156B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  419807452f87 Verifying Checksum
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  419807452f87 Download complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  36f5e25998cd Extracting [>                                                  ]  163.8kB/14.8MB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  36f5e25998cd Extracting [=========>                                         ]  2.949MB/14.8MB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  cd975f3ecadc Downloading [==================================================>]     138B/138B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  cd975f3ecadc Verifying Checksum
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  cd975f3ecadc Download complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  36f5e25998cd Extracting [===================>                               ]  5.898MB/14.8MB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  476f8c03ea25 Downloading [==============>                                    ]  2.111MB/7.055MB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  36f5e25998cd Extracting [===================================>               ]  10.49MB/14.8MB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  36f5e25998cd Extracting [==================================================>]   14.8MB/14.8MB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  476f8c03ea25 Downloading [================================================>  ]  6.793MB/7.055MB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  476f8c03ea25 Verifying Checksum
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  476f8c03ea25 Download complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  36f5e25998cd Pull complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  476f8c03ea25 Extracting [>                                                  ]   98.3kB/7.055MB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d4c9ed9a08b7 Downloading [==================================================>]     141B/141B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d4c9ed9a08b7 Verifying Checksum
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d4c9ed9a08b7 Download complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  a77a1a63fd9a Downloading [==================================================>]     144B/144B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  a77a1a63fd9a Verifying Checksum
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  a77a1a63fd9a Download complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  476f8c03ea25 Extracting [===================>                               ]  2.753MB/7.055MB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  476f8c03ea25 Extracting [=============================================>     ]  6.488MB/7.055MB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  476f8c03ea25 Extracting [==================================================>]  7.055MB/7.055MB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  476f8c03ea25 Extracting [==================================================>]  7.055MB/7.055MB
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  476f8c03ea25 Pull complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  880f5aff73f5 Extracting [==================================================>]     167B/167B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  880f5aff73f5 Extracting [==================================================>]     167B/167B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  880f5aff73f5 Pull complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  811cce5aca48 Extracting [==================================================>]     173B/173B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  811cce5aca48 Extracting [==================================================>]     173B/173B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  811cce5aca48 Pull complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  419807452f87 Extracting [==================================================>]     156B/156B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  419807452f87 Extracting [==================================================>]     156B/156B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  419807452f87 Pull complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  cd975f3ecadc Extracting [==================================================>]     138B/138B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  cd975f3ecadc Extracting [==================================================>]     138B/138B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  cd975f3ecadc Pull complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  a77a1a63fd9a Extracting [==================================================>]     144B/144B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  a77a1a63fd9a Extracting [==================================================>]     144B/144B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  a77a1a63fd9a Pull complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d4c9ed9a08b7 Extracting [==================================================>]     141B/141B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d4c9ed9a08b7 Extracting [==================================================>]     141B/141B
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  d4c9ed9a08b7 Pull complete
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  student03-ep11server Pulled
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  Network compose_default  Creating
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  Network compose_default  Created
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  Container compose-student03-ep11server-1  Creating
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  Container compose-student03-ep11server-1  Created
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  Container compose-student03-ep11server-1  Starting
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  Container compose-student03-ep11server-1  Started
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.568] Setting log level [debug] for module config  
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.568] Setting log level [debug] for module keyprotect 
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.568] Setting log level [debug] for module util    
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.568] Setting log level [debug] for module pgx     
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.568] Setting log level [debug] for module ep11server 
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.568] Setting log level [debug] for module storeserver 
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.568] Setting log level [debug] for module pgnotify 
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.568] Setting log level [debug] for module storage 
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.568] Setting log level [debug] for module entry   
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.568] Setting log level [debug] for module rotation 
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.568] Setting log level [debug] for module main    
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.568] Setting log level [debug] for module postgres 
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.569] Service directiamauthtemplate not found       #033[37mmodule#033[0m=entry
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.569] Service ep11manager not found                 #033[37mmodule#033[0m=entry
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.569] Service connectiontemplate not found          #033[37mmodule#033[0m=entry
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.569] Service postgrestemplate not found            #033[37mmodule#033[0m=entry
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.569] Service recoverykeyseedtemplate not found     #033[37mmodule#033[0m=entry
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.569] Service domaintemplate not found              #033[37mmodule#033[0m=entry
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.569] Service logging not found                     #033[37mmodule#033[0m=entry
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.569] Service basevoteridtemplate not found         #033[37mmodule#033[0m=entry
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.569] Service clientconnectiontemplate not found    #033[37mmodule#033[0m=entry
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.569] Service remoteconfig not found                #033[37mmodule#033[0m=entry
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.569] Service tls not found                         #033[37mmodule#033[0m=entry
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[36mINFO#033[0m[2023-11-03 21:17:13.579] Starting GREP11 server [dev]                  #033[36mmodule#033[0m=entry
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[36mINFO#033[0m[2023-11-03 21:17:13.579] TLS is enabled                                #033[36mmodule#033[0m=config
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.579] Creating new listener for *config.EP11CryptoOpts  #033[37mmodule#033[0m=entry
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] hostname:port=192.168.22.80:9001
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16OpenAdapter: Entering ...
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16OpenAdapter: server_idx=0
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::makeNewC16ClientStub: target_str=192.168.22.80:9001
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::C16ClientStub::check server certificate in stub...
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::ServeIdentityChecking: Checking server identity...
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  Docker compose result:
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:   CONTAINER ID   IMAGE                            COMMAND                 CREATED        STATUS                  PORTS                                                  NAMES
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  17cd821b28ff   quay.io/bsilliman/grep11server   "/usr/bin/ep11server"   1 second ago   Up Less than a second   0.0.0.0:9876->9876/tcp, :::9876->9876/tcp, 50052/tcp   compose-student03-ep11server-1
      Nov 03 21:17:13 ubuntu2204 hpcr-container[1623]:  Container service completed successfully
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::GetEP11Targets::try to get server certificate.
      Nov 03 21:17:13 ubuntu2204 systemd[1623]:  Finished Service that creates a set of containers.
      Nov 03 21:17:13 ubuntu2204 systemd[1623]:  Reached target Workload is up and running..
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::GetEP11Targets::get server certificate during ssl connection.
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::GetEP11Targets: Success
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::GetEP11Targets: Adding module_id=10, domain_id=22
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::GetEP11Targets: Adding module_id=8, domain_id=22
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::ServerIdentityChecking:: get server certificate in config file 
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:   -----BEGIN CERTIFICATE-----
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  MIID7jCCAtagAwIBAgICH5YwDQYJKoZIhvcNAQELBQAwgYAxCzAJBgNVBAYTAlVT
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  MREwDwYDVQQIDAhWaXJnaW5pYTEQMA4GA1UEBwwHSGVybmRvbjEMMAoGA1UECgwD
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  SUJNMRAwDgYDVQQLDAdJQk0gV1NDMRIwEAYDVQQDDAljMTZzZXJ2ZXIxGDAWBgkq
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  hkiG9w0BCQEWCWMxNnNlcnZlcjAeFw0yMzEwMzEyMDA5MDZaFw0yNTEwMzAyMDA5
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  MDZaMF4xCzAJBgNVBAYTAlVTMRMwEQYDVQQIDApDYWxpZm9ybmlhMRQwEgYDVQQH
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  DAtMb3MgQW5nZWxlczEMMAoGA1UECgwDSUJNMRYwFAYDVQQDDA0xOTIuMTY4LjIy
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  LjgwMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAy0TpNwhJcUYoSN6u
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  bPZsME0j+FpT+kAihG/anTx53Q6W+BhJWh+uhT296p6NSxT4T/YdI2fwIL8g6wN2
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  T9knyvZboijJwgwoG5t/gn5ACvY2vN5wWp075xHAEdF9zSrutoDySjhBil/wuLVy
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  QNojY8X5IyhHmbUi1wb2mb4EDD1vudNsvNMt4AAWdCF9QK7z7619tGlUEkKDu9IT
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  LZMR+AwoNVlYx5dmK5yUTMhozjpBWIzSOkQ6LAbQxMxQYmjlJ+YPMZ0qPbCgV/Dg
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  TIJoCF+8LXsbw4ngOxw98r8vlHf3TyfLE5xFaShRSJJGv1YmW6xbvXQ6ENr3DsB3
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  346WJQIDAQABo4GSMIGPMAkGA1UdEwQCMAAwCwYDVR0PBAQDAgSwMBMGA1UdJQQM
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  MAoGCCsGAQUFBwMBMBEGCWCGSAGG+EIBAQQEAwIGQDAoBgNVHR8EITAfMB2gG6AZ
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  hhdodHRwOi8vbG9jYWxob3N0L2NhLmNybDAjBgNVHREEHDAaghIxOTIuMTY4LjIy
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  LjgwOjkwMDGHBMCoFlAwDQYJKoZIhvcNAQELBQADggEBAGurn+547jZBscqema0S
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  Qd0wUn7Hc0NN/px0Y+OiS1BT3SgUNSysRsX4yqnMM2qZAoRGL+v+FsIDdCAKSt46
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  4oRc5X9l6pAGd/qIf9oDvIYPjIKtznCt/JT+sg+bdQYuypPo+cknjta5IJNZGtsq
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  ZMR5wClAbbAUSrronp3MpFZ5xB0treRZtvgpdcdDzZLF4iJ83wMJP5lHS0ocfSlq
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  fnCUbtuloLYPvJc4XHKJDKaf1tvCFkgFrquDK36e+YobTBw+wk4s4c9Bj2ih4xWu
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  Vo1ndBJ/ppta1szgPqoSgQsUK1kVcD8w99FmeaSD1ujBeeHd3ynAe5BkZR9j54na
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  +lE=
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  -----END CERTIFICATE-----
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::ServerIdentityChecking:: get server certificate in stub 
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:   -----BEGIN CERTIFICATE-----
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  MIID7jCCAtagAwIBAgICH5YwDQYJKoZIhvcNAQELBQAwgYAxCzAJBgNVBAYTAlVT
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  MREwDwYDVQQIDAhWaXJnaW5pYTEQMA4GA1UEBwwHSGVybmRvbjEMMAoGA1UECgwD
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  SUJNMRAwDgYDVQQLDAdJQk0gV1NDMRIwEAYDVQQDDAljMTZzZXJ2ZXIxGDAWBgkq
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  hkiG9w0BCQEWCWMxNnNlcnZlcjAeFw0yMzEwMzEyMDA5MDZaFw0yNTEwMzAyMDA5
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  MDZaMF4xCzAJBgNVBAYTAlVTMRMwEQYDVQQIDApDYWxpZm9ybmlhMRQwEgYDVQQH
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  DAtMb3MgQW5nZWxlczEMMAoGA1UECgwDSUJNMRYwFAYDVQQDDA0xOTIuMTY4LjIy
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  LjgwMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAy0TpNwhJcUYoSN6u
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  bPZsME0j+FpT+kAihG/anTx53Q6W+BhJWh+uhT296p6NSxT4T/YdI2fwIL8g6wN2
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  T9knyvZboijJwgwoG5t/gn5ACvY2vN5wWp075xHAEdF9zSrutoDySjhBil/wuLVy
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  QNojY8X5IyhHmbUi1wb2mb4EDD1vudNsvNMt4AAWdCF9QK7z7619tGlUEkKDu9IT
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  LZMR+AwoNVlYx5dmK5yUTMhozjpBWIzSOkQ6LAbQxMxQYmjlJ+YPMZ0qPbCgV/Dg
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  TIJoCF+8LXsbw4ngOxw98r8vlHf3TyfLE5xFaShRSJJGv1YmW6xbvXQ6ENr3DsB3
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  346WJQIDAQABo4GSMIGPMAkGA1UdEwQCMAAwCwYDVR0PBAQDAgSwMBMGA1UdJQQM
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  MAoGCCsGAQUFBwMBMBEGCWCGSAGG+EIBAQQEAwIGQDAoBgNVHR8EITAfMB2gG6AZ
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  hhdodHRwOi8vbG9jYWxob3N0L2NhLmNybDAjBgNVHREEHDAaghIxOTIuMTY4LjIy
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  LjgwOjkwMDGHBMCoFlAwDQYJKoZIhvcNAQELBQADggEBAGurn+547jZBscqema0S
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  Qd0wUn7Hc0NN/px0Y+OiS1BT3SgUNSysRsX4yqnMM2qZAoRGL+v+FsIDdCAKSt46
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  4oRc5X9l6pAGd/qIf9oDvIYPjIKtznCt/JT+sg+bdQYuypPo+cknjta5IJNZGtsq
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  ZMR5wClAbbAUSrronp3MpFZ5xB0treRZtvgpdcdDzZLF4iJ83wMJP5lHS0ocfSlq
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  fnCUbtuloLYPvJc4XHKJDKaf1tvCFkgFrquDK36e+YobTBw+wk4s4c9Bj2ih4xWu
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  Vo1ndBJ/ppta1szgPqoSgQsUK1kVcD8w99FmeaSD1ujBeeHd3ynAe5BkZR9j54na
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  +lE=
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  -----END CERTIFICATE-----
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::ServerIdentityChecking: Success to verify server identity
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16OpenAdapter: Done.
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Entering ...
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Checking target i=0, ap_id=8, dom_id=22
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target still on same server
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target list check passed. (server_idx=0)
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: targets_num: 1
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: req_len: 37
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: Setting resp_len: 54
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Done.
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Entering ...
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Checking target i=0, ap_id=8, dom_id=22
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target still on same server
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target list check passed. (server_idx=0)
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: targets_num: 1
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: req_len: 46
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: Setting resp_len: 438
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Done.
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Entering ...
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Checking target i=0, ap_id=8, dom_id=22
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target still on same server
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target list check passed. (server_idx=0)
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: targets_num: 1
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: req_len: 58
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: Setting resp_len: 254
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Done.
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Entering ...
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Checking target i=0, ap_id=8, dom_id=22
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target still on same server
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target list check passed. (server_idx=0)
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: targets_num: 1
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: req_len: 74
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: Setting resp_len: 339
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Done.
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[36mINFO#033[0m[2023-11-03 21:17:13.623] admin.ep11.go:ep11server.(*CryptoServer).rawQueryDomainImporterCert:387: m_admin returned an error within the QueryNextWK  #033[36merror code#033[0m=CKR_KEY_HANDLE_INVALID #033[36mmodule#033[0m=util
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Entering ...
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Checking target i=0, ap_id=8, dom_id=22
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target still on same server
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target list check passed. (server_idx=0)
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: targets_num: 1
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: req_len: 74
      Nov 03 21:17:13 ubuntu2204 systemd[1623]:  Starting Phase2 Catch Service...
      Nov 03 21:17:13 ubuntu2204 hpcr-catch-success[1623]:  VSI has started successfully.
      Nov 03 21:17:13 ubuntu2204 hpcr-catch-success[1623]:  HPL10001I: Services succeeded -> systemd triggered hpl-catch-success service
      Nov 03 21:17:13 ubuntu2204 systemd[1623]:  Finished Phase2 Catch Service.
      Nov 03 21:17:13 ubuntu2204 systemd[1623]:  Reached target Multi-User System.
      Nov 03 21:17:13 ubuntu2204 systemd[1623]:  Starting Record Runlevel Change in UTMP...
      Nov 03 21:17:13 ubuntu2204 systemd[1623]:  systemd-update-utmp-runlevel.service: Deactivated successfully.
      Nov 03 21:17:13 ubuntu2204 systemd[1623]:  Finished Record Runlevel Change in UTMP.
      Nov 03 21:17:13 ubuntu2204 systemd[1623]:  Startup finished in 26.473s (kernel) + 6.172s (userspace) = 32.646s.
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: Setting resp_len: 374
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Done.
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Entering ...
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Checking target i=0, ap_id=8, dom_id=22
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target still on same server
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target list check passed. (server_idx=0)
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: targets_num: 1
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: req_len: 74
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: Setting resp_len: 375
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Done.
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Entering ...
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Checking target i=0, ap_id=8, dom_id=22
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target still on same server
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target list check passed. (server_idx=0)
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: targets_num: 1
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: req_len: 74
      Nov 03 21:17:13 ubuntu2204 systemd[1623]:  podman.service: Deactivated successfully.
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: Setting resp_len: 350
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Done.
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Entering ...
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Checking target i=0, ap_id=8, dom_id=22
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target still on same server
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target list check passed. (server_idx=0)
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: targets_num: 1
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: req_len: 74
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: Setting resp_len: 338
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Done.
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Entering ...
      Nov 03 21:17:13 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Checking target i=0, ap_id=8, dom_id=22
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target list check passed. (server_idx=0)
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: targets_num: 1
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: req_len: 74
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target still on same server
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: Setting resp_len: 432
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Done.
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Entering ...
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Checking target i=0, ap_id=8, dom_id=22
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target still on same server
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target list check passed. (server_idx=0)
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: targets_num: 1
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: req_len: 74
      Nov 03 21:17:14 ubuntu2204 systemd[1623]:  run-docker-runtime\x2drunc-moby-17cd821b28ff5ce5d19935577579a4a50a9bc2dc169d76f5e1dfc954069a6296-runc.WEpO8O.mount: Deactivated successfully.
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: Setting resp_len: 339
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Done.
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Entering ...
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Checking target i=0, ap_id=8, dom_id=22
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target still on same server
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target list check passed. (server_idx=0)
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: targets_num: 1
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: req_len: 74
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: Setting resp_len: 397
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Done.
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Entering ...
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Checking target i=0, ap_id=8, dom_id=22
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target still on same server
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target list check passed. (server_idx=0)
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: targets_num: 1
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: req_len: 74
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: Setting resp_len: 338
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Done.
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Entering ...
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Checking target i=0, ap_id=8, dom_id=22
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target still on same server
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target list check passed. (server_idx=0)
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: targets_num: 1
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: req_len: 74
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: Setting resp_len: 367
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Done.
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Entering ...
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Checking target i=0, ap_id=8, dom_id=22
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target still on same server
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target list check passed. (server_idx=0)
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: targets_num: 1
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: req_len: 74
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: Setting resp_len: 338
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Done.
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[36mINFO#033[0m[2023-11-03 21:17:13.864] admin.ep11.go:ep11server.(*CryptoServer).rawQueryWKOrigins:547: m_admin returned an error within the QueryNextWK  #033[36merror code#033[0m=CKR_KEY_HANDLE_INVALID #033[36mmodule#033[0m=util
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Entering ...
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Checking target i=0, ap_id=8, dom_id=22
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target still on same server
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target list check passed. (server_idx=0)
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: targets_num: 1
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: req_len: 74
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: Setting resp_len: 411
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Done.
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Entering ...
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Checking target i=0, ap_id=8, dom_id=22
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target still on same server
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client:c16DoEP11Request: Target list check passed. (server_idx=0)
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: targets_num: 1
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: req_len: 74
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] C16ClientStub::DoRequest: Setting resp_len: 338
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  [c16client][debug] c16client::c16DoEP11Request: Done.
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[36mINFO#033[0m[2023-11-03 21:17:13.911] admin.ep11.go:ep11server.(*CryptoServer).rawQueryImporterCert:667: Received error after deserializing query response: EOF  #033[36merror code#033[0m=CKR_IBM_GREP11_CANNOT_UNMARSHAL #033[36mmodule#033[0m=util
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[37mDEBU#033[0m[2023-11-03 21:17:13.911] Creating service backing server for *config.EP11CryptoOpts  #033[37mmodule#033[0m=entry
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[36mINFO#033[0m[2023-11-03 21:17:13.912] Loading ep11crypto service                    #033[36mmodule#033[0m=entry
      Nov 03 21:17:14 ubuntu2204 compose-student03-ep11server-1[1623]:  #033[36mINFO#033[0m[2023-11-03 21:17:13.912] GRPC Server listening on [::]:9876 with TLS enabled  #033[36mmodule#033[0m=entry
      Nov 03 21:17:14 ubuntu2204 systemd-networkd[1623]:  br-cd39c66a7dab: Gained IPv6LL
      Nov 03 21:17:14 ubuntu2204 systemd-networkd[1623]:  veth2cbfbb7: Gained IPv6LL
      Nov 03 21:17:14 ubuntu2204 verify-disk-encryption[1623]:  HPL13000I: Verify LUKS Encryption
      Nov 03 21:17:14 ubuntu2204 systemd[1623]:  verify-disk-encryption-invoker.service: Deactivated successfully.
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Return value for disk-encrypt: 0
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Executed cmd: ('lsblk', '-b', '-n', '-o', 'NAME,SIZE')
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Return value: 0
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Stdout: vda                                           107374182400
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  ├─vda1                                          4292870144
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  └─vda2                                        103079215104
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:    └─luks-7006b45c-452a-4138-af93-842ceeb387dc 103062437888
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  vdb                                                 417792
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  List of volumes greater than or equal to 10GB are: ['/dev/vda']
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Updated Volumes list: ['/dev/vda2']
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Executed cmd: ('lsblk', '/dev/vda2', '-b', '-n', '-o', 'NAME,MOUNTPOINT')
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Return value: 0
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Stdout: vda2
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  └─luks-7006b45c-452a-4138-af93-842ceeb387dc /
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Boot volume is /dev/vda2
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Volume /dev/vda2 has mount point /
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  List of mounted volumes are: ['/dev/vda2']
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Verifying the boot disk /dev/vda2 is encrypted or not
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Executed cmd: ('lsblk', '/dev/vda2', '-b', '-n', '-o', 'NAME,TYPE')
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Return value: 0
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Stdout: vda2                                        part
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  └─luks-7006b45c-452a-4138-af93-842ceeb387dc crypt
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Executed cmd: ('cryptsetup', 'isLuks', '/dev/vda2')
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Return value: 0
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Executed cmd: ('cryptsetup', 'luksDump', '/dev/vda2')
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  Return value: 0
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  HPL13003I: Checked for mount point /, LUKS encryption with 1 key slot found
      Nov 03 21:17:15 ubuntu2204 verify-disk-encryption[1623]:  HPL13001I: Boot volume and all the mounted data volumes are encrypted
      Nov 03 21:17:38 ubuntu2204 systemd[1623]:  systemd-fsckd.service: Deactivated successfully.
      Nov 03 21:17:39 ubuntu2204 systemd[1623]:  systemd-hostnamed.service: Deactivated successfully.
      ```

*Congratulations!* You have reached a significant milestone in the lab.  You have successfully configured and launched your HPVS 2.1.x GREP11 Server. Now all that is left is to test its functionality with some sample GREP11 client code.  You will set that up on your Ubuntu KVM guest.  Click _Next_ at the bottom right of the page to continue.
