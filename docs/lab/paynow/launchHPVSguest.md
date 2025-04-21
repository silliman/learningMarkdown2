# Launch HPVS guest for PayNow


You will start this section from your login session on the RHEL host. Start from this familiar window or tab:

<img src="../../../images/RHELHost.png" width="351" height="216" >

## launch the HPVS 2.1.x KVM guest

This fancy command figures out (and displays) the last two characters of your assigned userid and is used in other commands in this section, so that the lab instructions will work for everybody:

   ``` bash
   suffix=$(temp=$(whoami) && echo ${temp: -2}) \
   && echo Your student suffix is ${suffix}

   ```

You aren't going to change anything here since it's already been defined for you by the instructors, but you can display the definition of your HPVS 2.1.x KVM guest for the PayNow demo:

   ``` bash
   sudo virsh dumpxml paynowse${suffix}
   ```

???- example "Definition of HPVS KVM guest for PayNow Demo"

      ``` hl_lines="25 32"
      <domain type='kvm'>
        <name>paynowse04</name>
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
            <source file='/var/lib/libvirt/images/labs/paynow/student04/ibm-hyper-protect-container-runtime-23.11.1.qcow2'/>
            <backingStore/>
            <target dev='vda' bus='virtio'/>
            <address type='ccw' cssid='0xfe' ssid='0x0' devno='0x0000'/>
          </disk>
          <disk type='file' device='disk'>
            <driver name='qemu' type='raw' cache='none' io='native' iommu='on'/>
            <source file='/var/lib/libvirt/images/labs/paynow/student04/ciiso.iso'/>
            <target dev='vdc' bus='virtio'/>
            <readonly/>
            <address type='ccw' cssid='0xfe' ssid='0x0' devno='0x0002'/>
          </disk>
          <controller type='pci' index='0' model='pci-root'/>
          <interface type='network'>
            <mac address='52:54:00:fc:6c:a8'/>
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

Start your HPVS Guest for the PayNow Demo and attach to its console.  Watch the messages carefully.  You should not see any failures:

   ``` bash
   sudo virsh start paynowse${suffix} --console
   
   ```

???- example "This is what success looks like"

      ```
      Domain 'paynowse02' started
      Connected to domain 'paynowse02'
      Escape character is ^] (Ctrl + ])
      # HPL11 build:23.11.0 enabler:23.6.0
      # Tue Sep  5 22:22:00 UTC 2023
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
      # Tue Sep  5 22:22:27 UTC 2023
      # HPL11 build:23.11.0 enabler:23.6.0
      # HPL11099I: bootloader end
      hpcr-dnslookup[890]: HPL14000I: Network connectivity check completed successfully.
      hpcr-logging[1038]: Configuring logging ...
      hpcr-logging[1039]: Version [1.1.147]
      hpcr-logging[1039]: Configuring logging, input [/var/hyperprotect/user-data.decrypted] ...
      hpcr-logging[1039]: HPL01010I: Logging has been setup successfully.
      hpcr-logging[1038]: Logging has been configured
      hpcr-catch-success[1541]: VSI has started successfully.
      hpcr-catch-success[1541]: HPL10001I: Services succeeded -> systemd triggered hpl-catch-success service
      ```

You will have to enter the ++control+bracket-right++ key-combination to break out of the console and return to your command prompt.

## verify that messages from your HPVS KVM guest are received by rsyslog

**The logging of the HPVS KVM guest is going to the _rsyslog_ service that you configured on your Ubuntu guest, so switch to the terminal tab or window for your KVM standard guest.**

You should still be comfortably logged in on this tab or window:

<img src="../../../images/KVMGuest.png" width="351" height="217" />

The arguments to the _journalctl_ command here aren't the most elegant in the world, but, unless midnight passed since you started your HPVS KVM guest for PayNow, you will be able to see messages in rsyslog from when you just started up your HPVS KVM guest:

   ``` bash
   journalctl --since today --no-pager
   ```

There are a lot of messages logged, a veritable trove of treasure for the curious.  Here is an example of what you should be able to see:

???- example "Log messages in rsyslog from starting your HPVS KVM guest for the PayNow demo"

      ``` hl_lines="989-992"
      Sep 05 22:22:29 ubuntu2204 vpcnode[26262]: authentication probe
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Linux version 5.15.0-79-generic (buildd@bos02-s390x-016) (gcc (Ubuntu 11.3.0-1ubuntu1~22.04.1) 11.3.0, GNU ld (GNU Binutils for Ubuntu) 2.38) #86-Ubuntu SMP Mon Jul 10 16:19:54 UTC 2023 (Ubuntu 5.15.0-79.86-generic 5.15.111)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  setup: Linux is running under KVM in 64-bit mode
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  setup: Relocating AMODE31 section of size 0x00003000
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  setup: The maximum memory size is 3812MB
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  cpu: 2 configured CPUs, 0 standby CPUs
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Write protected kernel read-only data: 18692k
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Zone ranges:
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:    DMA      [mem 0x0000000000000000-0x000000007fffffff]
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:    Normal   [mem 0x0000000080000000-0x00000000ee3fffff]
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Movable zone start for each node
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Early memory node ranges
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:    node   0: [mem 0x0000000000000000-0x00000000ee3fffff]
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Initmem setup node 0 [mem 0x0000000000000000-0x00000000ee3fffff]
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  On node 0, zone Normal: 7168 pages in unavailable ranges
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  percpu: Embedded 32 pages/cpu s91904 r8192 d30976 u131072
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  pcpu-alloc: s91904 r8192 d30976 u131072 alloc=32*4096
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  pcpu-alloc: [0] 0 [0] 1 
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Built 1 zonelists, mobility grouping on.  Total pages: 960624
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Policy zone: Normal
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Kernel command line: panic=0 blacklist=virtio_rng swiotlb=262144 cloud-init=disabled console=ttyS0 printk.time=0 systemd.getty_auto=0 systemd.firstboot=0 module.sig_enforce=1 quiet loglevel=0 systemd.show_status=0
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Unknown kernel command line parameters "blacklist=virtio_rng cloud-init=disabled", will be passed to user space.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Dentry cache hash table entries: 524288 (order: 10, 4194304 bytes, linear)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Inode-cache hash table entries: 262144 (order: 9, 2097152 bytes, linear)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  mem auto-init: stack:off, heap alloc:on, heap free:off
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  software IO TLB: mapped [mem 0x00000000435a4000-0x00000000635a4000] (512MB)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Memory: 3266284K/3903488K available (11988K kernel code, 3212K rwdata, 6704K rodata, 5200K init, 1252K bss, 637204K reserved, 0K cma-reserved)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  SLUB: HWalign=256, Order=0-3, MinObjects=0, CPUs=2, Nodes=1
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  ftrace: allocating 34120 entries in 134 pages
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  ftrace: allocated 134 pages with 3 groups
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  rcu: Hierarchical RCU implementation.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  rcu: #011RCU restricting CPUs from NR_CPUS=512 to nr_cpu_ids=2.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  #011Rude variant of Tasks RCU enabled.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  #011Tracing variant of Tasks RCU enabled.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  rcu: RCU calculated value of scheduler-enlistment delay is 10 jiffies.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  rcu: Adjusting geometry for rcu_fanout_leaf=16, nr_cpu_ids=2
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  NR_IRQS: 3, nr_irqs: 3, preallocated irqs: 3
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  clocksource: tod: mask: 0xffffffffffffffff max_cycles: 0x3b0a9be803b0a9, max_idle_ns: 1805497147909793 ns
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  random: crng init done
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Console: colour dummy device 80x25
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  printk: console [ttyS0] enabled
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  printk: console [ttysclp0] enabled
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Calibrating delay loop (skipped)... 24038.00 BogoMIPS preset
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  pid_max: default: 32768 minimum: 301
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  LSM: Security Framework initializing
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  landlock: Up and running.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Yama: becoming mindful.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  AppArmor: AppArmor initialized
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Mount-cache hash table entries: 8192 (order: 4, 65536 bytes, linear)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Mountpoint-cache hash table entries: 8192 (order: 4, 65536 bytes, linear)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  rcu: Hierarchical SRCU implementation.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  smp: Bringing up secondary CPUs ...
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  smp: Brought up 1 node, 2 CPUs
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  devtmpfs: initialized
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  clocksource: jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 19112604462750000 ns
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  futex hash table entries: 512 (order: 5, 131072 bytes, linear)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  NET: Registered PF_NETLINK/PF_ROUTE protocol family
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  audit: initializing netlink subsys (disabled)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  audit: type=2000 audit(1693952520.204:1): state=initialized audit_enabled=0 res=1
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Spectre V2 mitigation: etokens
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  HugeTLB registered 1.00 MiB page size, pre-allocated 0 pages
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  iommu: Default domain type: Translated 
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  iommu: DMA domain TLB invalidation policy: strict mode 
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  SCSI subsystem initialized
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  NetLabel: Initializing
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  NetLabel:  domain hash size = 128
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  NetLabel:  protocols = UNLABELED CIPSOv4 CALIPSO
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  NetLabel:  unlabeled traffic allowed by default
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  zpci: PCI is not supported because CPU facilities 69 or 71 are not available
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  VFS: Disk quotas dquot_6.6.0
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  VFS: Dquot-cache hash table entries: 512 (order 0, 4096 bytes)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  AppArmor: AppArmor Filesystem Enabled
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  NET: Registered PF_INET protocol family
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  IP idents hash table entries: 65536 (order: 7, 524288 bytes, linear)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  tcp_listen_portaddr_hash hash table entries: 2048 (order: 3, 32768 bytes, linear)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Table-perturb hash table entries: 65536 (order: 6, 262144 bytes, linear)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  TCP established hash table entries: 32768 (order: 6, 262144 bytes, linear)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  TCP bind hash table entries: 32768 (order: 7, 524288 bytes, linear)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  TCP: Hash tables configured (established 32768 bind 32768)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  MPTCP token hash table entries: 4096 (order: 4, 98304 bytes, linear)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  UDP hash table entries: 2048 (order: 4, 65536 bytes, linear)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  UDP-Lite hash table entries: 2048 (order: 4, 65536 bytes, linear)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  NET: Registered PF_UNIX/PF_LOCAL protocol family
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  NET: Registered PF_XDP protocol family
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Trying to unpack rootfs image as initramfs...
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  kvm-s390: SIE is not available
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  hypfs: The hardware system does not support hypfs
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Initialise system trusted keyrings
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Key type blacklist registered
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  workingset: timestamp_bits=45 max_order=20 bucket_order=0
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  zbud: loaded
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  squashfs: version 4.0 (2009/01/31) Phillip Lougher
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  fuse: init (API version 7.34)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  integrity: Platform Keyring initialized
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Key type asymmetric registered
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Asymmetric key parser 'x509' registered
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Block layer SCSI generic (bsg) driver version 0.4 loaded (major 249)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  io scheduler mq-deadline registered
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  hvc_iucv: The z/VM IUCV HVC device driver cannot be used without z/VM
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  loop: module loaded
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  tun: Universal TUN/TAP device driver, 1.6
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  device-mapper: core: CONFIG_IMA_DISABLE_HTABLE is disabled. Duplicate IMA measurements will not be recorded in the IMA log.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  device-mapper: uevent: version 1.0.3
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  device-mapper: ioctl: 4.45.0-ioctl (2021-03-22) initialised: dm-devel@redhat.com
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  drop_monitor: Initializing network drop monitor service
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  NET: Registered PF_INET6 protocol family
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Freeing initrd memory: 9828K
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Segment Routing with IPv6
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  In-situ OAM (IOAM) with IPv6
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  NET: Registered PF_PACKET protocol family
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Key type dns_resolver registered
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  cio: Channel measurement facility initialized using format extended (mode autodetected)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  sclp_sd: Store Data request failed (eq=2, di=3, response=0x40f0, flags=0x00, status=0, rc=-5)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  ap: The hardware system does not support AP instructions
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  registered taskstats version 1
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Loading compiled-in X.509 certificates
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Loaded X.509 cert 'Build time autogenerated kernel key: 033cfe156234b615233dffd1cb0a66d4b6280b04'
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Loaded X.509 cert 'Canonical Ltd. Live Patch Signing: 14df34d1a87cf37625abec039ef2bf521249b969'
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Loaded X.509 cert 'Canonical Ltd. Kernel Module Signing: 88f752e560a1e0737e31163a466ad7b70a850c19'
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  blacklist: Loading compiled-in revocation X.509 certificates
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing: 61482aa2830d0ab2ad5af10b7250da9033ddcef0'
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (2017): 242ade75ac4a15e50d50c84b0d45ff3eae707a03'
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (ESM 2018): 365188c1d374d6b07c3c8f240f8ef722433d6a8b'
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (2019): c0746fd6c5da3ae827864651ad66ae47fe24b3e8'
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (2021 v1): a8d54bbb3825cfb94fa13c9f8a594a195c107b8d'
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (2021 v2): 4cf046892d6fd3c9a5b03f98d845f90851dc6a8c'
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (2021 v3): 100437bb6de6e469b581e61cd66bce3ef4ed53af'
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Loaded X.509 cert 'Canonical Ltd. Secure Boot Signing (Ubuntu Core 2019): c1d57b8f6b743f23ee41f4f7ee292f06eecadfb9'
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  zswap: loaded using pool lzo/zbud
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Key type .fscrypt registered
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Key type fscrypt-provisioning registered
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Key type encrypted registered
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  AppArmor: AppArmor sha1 policy hashing enabled
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  ima: No TPM chip found, activating TPM-bypass!
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Loading compiled-in module X.509 certificates
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Loaded X.509 cert 'Build time autogenerated kernel key: 033cfe156234b615233dffd1cb0a66d4b6280b04'
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  ima: Allocated hash algorithm: sha1
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  ima: No architecture policies found
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  evm: Initialising EVM extended attributes:
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  evm: security.selinux
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  evm: security.SMACK64
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  evm: security.SMACK64EXEC
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  evm: security.SMACK64TRANSMUTE
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  evm: security.SMACK64MMAP
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  evm: security.apparmor
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  evm: security.ima
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  evm: security.capability
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  evm: HMAC attrs: 0x1
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Freeing unused kernel image (initmem) memory: 5200K
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Write protected read-only-after-init data: 136k
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Checked W+X mappings: passed, no unexpected W+X pages found
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Run /init as init process
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:    with arguments:
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:      /init
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:    with environment:
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:      HOME=/
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:      TERM=linux
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:      blacklist=virtio_rng
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:      cloud-init=disabled
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  virtio_blk virtio0: [vda] 209715200 512-byte logical blocks (107 GB/100 GiB)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  GPT:Primary header thinks Alt. header is not at the end of the disk.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  GPT:8388607 != 209715199
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  GPT:Alternate GPT header not at the end of the disk.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  GPT:8388607 != 209715199
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  GPT: Use GNU Parted to correct GPT errors.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:   vda: vda1
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  virtio_blk virtio1: [vdb] 760 512-byte logical blocks (389 kB/380 KiB)
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  EXT4-fs (dm-0): mounted filesystem with ordered data mode. Opts: (null). Quota mode: none.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  EXT4-fs (vda1): mounted filesystem with ordered data mode. Opts: (null). Quota mode: none.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  ISO 9660 Extensions: Microsoft Joliet Level 3
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  ISO 9660 Extensions: RRIP_1991A
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  EXT4-fs (dm-0): re-mounted. Opts: (null). Quota mode: none.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  systemd 249.11-0ubuntu3.9 running in system mode (+PAM +AUDIT +SELINUX +APPARMOR +IMA +SMACK +SECCOMP +GCRYPT +GNUTLS +OPENSSL +ACL +BLKID +CURL +ELFUTILS +FIDO2 +IDN2 -IDN +IPTC +KMOD +LIBCRYPTSETUP +LIBFDISK +PCRE2 -PWQUALITY -P11KIT -QRENCODE +BZIP2 +LZ4 +XZ +ZLIB +ZSTD -XKBCOMMON +UTMP +SYSVINIT default-hierarchy=unified)
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Detected virtualization kvm.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Detected architecture s390x.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Hostname set to <student02-paynowdemo>.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Initializing machine ID from random generator.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Installed transient /etc/machine-id file.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  /lib/systemd/system/verify-disk-encryption-invoker.service:6: Special user nobody configured, this is not safe!
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  /lib/systemd/system/se-dnslookup.service:10: Special user nobody configured, this is not safe!
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  /lib/systemd/system/hpl-catch-success.service:13: Special user nobody configured, this is not safe!
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  /lib/systemd/system/hpl-catch-failed.service:10: Special user nobody configured, this is not safe!
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  se-registry-auth.service: Found ordering cycle on basic.target/start
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  se-registry-auth.service: Found dependency on sockets.target/start
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  se-registry-auth.service: Found dependency on docker.socket/start
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  se-registry-auth.service: Found dependency on se-registry-auth.service/start
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  se-registry-auth.service: Job sockets.target/start deleted to break ordering cycle starting with se-registry-auth.service/start
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Queued start job for default target Multi-User System.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Created slice Slice /system/modprobe.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Created slice Slice /system/systemd-fsck.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Created slice User and Session Slice.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Dispatch Password Requests to Console Directory Watch.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Forward Password Requests to Wall Directory Watch.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Set up automount Arbitrary Executable File Formats File System Automount Point.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Local Encrypted Volumes.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Path Units.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Remote File Systems.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Slice Units.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Swaps.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Local Verity Protected Volumes.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Listening on Syslog Socket.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Listening on fsck to fsckd communication Socket.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Listening on initctl Compatibility Named Pipe.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Listening on Journal Audit Socket.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Listening on Journal Socket (/dev/log).
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Listening on Journal Socket.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Listening on Network Service Netlink Socket.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Listening on udev Control Socket.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Listening on udev Kernel Socket.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Mounting Huge Pages File System...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Mounting POSIX Message Queue File System...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Mounting Kernel Debug File System...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Mounting Kernel Trace File System...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Journal Service...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Set the console keyboard layout...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Create List of Static Device Nodes...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Load Kernel Module chromeos_pstore...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Load Kernel Module configfs...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Load Kernel Module drm...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Load Kernel Module efi_pstore...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Load Kernel Module fuse...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Load Kernel Module pstore_blk...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Load Kernel Module pstore_zone...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Load Kernel Module ramoops...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Condition check resulted in OpenVSwitch configuration for cleanup being skipped.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting File System Check on Root Device...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Load Kernel Modules...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Coldplug All udev Devices...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Mounted Huge Pages File System.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Mounted POSIX Message Queue File System.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Mounted Kernel Debug File System.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Mounted Kernel Trace File System.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Create List of Static Device Nodes.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  modprobe@chromeos_pstore.service: Deactivated successfully.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Load Kernel Module chromeos_pstore.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  modprobe@configfs.service: Deactivated successfully.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Load Kernel Module configfs.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  modprobe@fuse.service: Deactivated successfully.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Load Kernel Module fuse.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  modprobe@pstore_zone.service: Deactivated successfully.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Load Kernel Module pstore_zone.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Mounting FUSE Control File System...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Mounting Kernel Configuration File System...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Load Kernel Modules.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Mounted FUSE Control File System.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Mounted Kernel Configuration File System.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Apply Kernel Variables...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  modprobe@pstore_blk.service: Deactivated successfully.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Load Kernel Module pstore_blk.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  modprobe@ramoops.service: Deactivated successfully.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Load Kernel Module ramoops.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  modprobe@efi_pstore.service: Deactivated successfully.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Load Kernel Module efi_pstore.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Coldplug All udev Devices.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started File System Check Daemon to report status.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Apply Kernel Variables.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished File System Check on Root Device.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Remount Root and Kernel File Systems...
      Sep 05 22:22:30 ubuntu2204 systemd-journald[26262]:  Journal started
      Sep 05 22:22:30 ubuntu2204 systemd-journald[26262]:  Runtime Journal (/run/log/journal/96ad71740ae743c79acd51c6f69413fb) is 4.0M, max 32.0M, 28.0M free.
      Sep 05 22:22:30 ubuntu2204 systemd-fsck[26262]:  /dev/mapper/luks-655145dd-f4d0-4127-93ce-e1906a668299: clean, 26377/6291456 files, 808668/25161728 blocks
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Journal Service.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  EXT4-fs (dm-0): re-mounted. Opts: errors=remount-ro. Quota mode: none.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Remount Root and Kernel File Systems.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  modprobe@drm.service: Deactivated successfully.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Load Kernel Module drm.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Flush Journal to Persistent Storage...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Condition check resulted in Platform Persistent Storage Archival being skipped.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Load/Save Random Seed...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Create System Users...
      Sep 05 22:22:30 ubuntu2204 systemd-journald[26262]:  Time spent on flushing to /var/log/journal/96ad71740ae743c79acd51c6f69413fb is 2.551ms for 269 entries.
      Sep 05 22:22:30 ubuntu2204 systemd-journald[26262]:  System Journal (/var/log/journal/96ad71740ae743c79acd51c6f69413fb) is 8.0M, max 4.0G, 3.9G free.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Create System Users.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Create Static Device Nodes in /dev...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Load/Save Random Seed.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Condition check resulted in First Boot Complete being skipped.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Create Static Device Nodes in /dev.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Rule-based Manager for Device Events and Files...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Flush Journal to Persistent Storage.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Set the console keyboard layout.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Preparation for Local File Systems.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Rule-based Manager for Device Events and Files.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Network Configuration...
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  VFIO - User Level meta-driver version: 0.3
      Sep 05 22:22:30 ubuntu2204 systemd-networkd[26262]:  lo: Link UP
      Sep 05 22:22:30 ubuntu2204 systemd-networkd[26262]:  lo: Gained carrier
      Sep 05 22:22:30 ubuntu2204 systemd-networkd[26262]:  Enumeration completed
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Network Configuration.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Wait for Network to be Configured...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Wait for Network to be Configured.
      Sep 05 22:22:30 ubuntu2204 systemd-udevd[26262]:  Using default interface naming scheme 'v249'.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  virtio_net virtio2 enc1: renamed from eth0
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Found device /dev/disk/by-uuid/4d7e976d-b69c-48ec-9a8a-a47cd2e28e70.
      Sep 05 22:22:30 ubuntu2204 systemd-networkd[26262]:  eth0: Interface name change detected, renamed to enc1.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting File System Check on /dev/disk/by-uuid/4d7e976d-b69c-48ec-9a8a-a47cd2e28e70...
      Sep 05 22:22:30 ubuntu2204 systemd-udevd[26262]:  Using default interface naming scheme 'v249'.
      Sep 05 22:22:30 ubuntu2204 systemd-networkd[26262]:  enc1: Link UP
      Sep 05 22:22:30 ubuntu2204 systemd-fsck[26262]:  /dev/vda1: clean, 13/262144 files, 140195/1048064 blocks
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished File System Check on /dev/disk/by-uuid/4d7e976d-b69c-48ec-9a8a-a47cd2e28e70.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Mounting /boot...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Mounted /boot.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Local File Systems.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Load AppArmor profiles...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Set console font and keymap...
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  EXT4-fs (vda1): mounted filesystem with ordered data mode. Opts: (null). Quota mode: none.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Set Up Additional Binary Formats...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Condition check resulted in Store a System Token in an EFI Variable being skipped.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Commit a transient machine-id on disk...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Create Volatile Files and Directories...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Set console font and keymap.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  proc-sys-fs-binfmt_misc.automount: Got automount request for /proc/sys/fs/binfmt_misc, triggered by 863 (systemd-binfmt)
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Mounting Arbitrary Executable File Formats File System...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Mounted Arbitrary Executable File Formats File System.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Set Up Additional Binary Formats.
      Sep 05 22:22:30 ubuntu2204 apparmor.systemd[26262]:  Restarting AppArmor
      Sep 05 22:22:30 ubuntu2204 apparmor.systemd[26262]:  Reloading AppArmor profiles
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Create Volatile Files and Directories.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Network Name Resolution...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Network Time Synchronization...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Record System Boot/Shutdown in UTMP...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Record System Boot/Shutdown in UTMP.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Commit a transient machine-id on disk.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Network Time Synchronization.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target System Time Set.
      Sep 05 22:22:30 ubuntu2204 systemd-resolved[26262]:  Positive Trust Anchors:
      Sep 05 22:22:30 ubuntu2204 systemd-resolved[26262]:  . IN DS 20326 8 2 e06d44b80b8f1d39a95c0b0d7c65d08458e880409bbc683457104237c7f8ec8d
      Sep 05 22:22:30 ubuntu2204 systemd-resolved[26262]:  Negative trust anchors: home.arpa 10.in-addr.arpa 16.172.in-addr.arpa 17.172.in-addr.arpa 18.172.in-addr.arpa 19.172.in-addr.arpa 20.172.in-addr.arpa 21.172.in-addr.arpa 22.172.in-addr.arpa 23.172.in-addr.arpa 24.172.in-addr.arpa 25.172.in-addr.arpa 26.172.in-addr.arpa 27.172.in-addr.arpa 28.172.in-addr.arpa 29.172.in-addr.arpa 30.172.in-addr.arpa 31.172.in-addr.arpa 168.192.in-addr.arpa d.f.ip6.arpa corp home internal intranet lan local private test
      Sep 05 22:22:30 ubuntu2204 systemd-resolved[26262]:  Using system hostname 'student02-paynowdemo'.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Network Name Resolution.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Network.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Network is Online.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Host and Network Name Lookups.
      Sep 05 22:22:30 ubuntu2204 audit[26262]:  AVC apparmor="STATUS" operation="profile_load" profile="unconfined" name="nvidia_modprobe" pid=879 comm="apparmor_parser"
      Sep 05 22:22:30 ubuntu2204 audit[26262]:  AVC apparmor="STATUS" operation="profile_load" profile="unconfined" name="nvidia_modprobe//kmod" pid=879 comm="apparmor_parser"
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  audit: type=1400 audit(1693952548.024:2): apparmor="STATUS" operation="profile_load" profile="unconfined" name="nvidia_modprobe" pid=879 comm="apparmor_parser"
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  audit: type=1400 audit(1693952548.024:3): apparmor="STATUS" operation="profile_load" profile="unconfined" name="nvidia_modprobe//kmod" pid=879 comm="apparmor_parser"
      Sep 05 22:22:30 ubuntu2204 audit[26262]:  AVC apparmor="STATUS" operation="profile_load" profile="unconfined" name="lsb_release" pid=878 comm="apparmor_parser"
      Sep 05 22:22:30 ubuntu2204 apparmor.systemd[26262]:  Skipping profile in /etc/apparmor.d/disable: usr.sbin.rsyslogd
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  audit: type=1400 audit(1693952548.034:4): apparmor="STATUS" operation="profile_load" profile="unconfined" name="lsb_release" pid=878 comm="apparmor_parser"
      Sep 05 22:22:30 ubuntu2204 audit[26262]:  AVC apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/NetworkManager/nm-dhcp-client.action" pid=880 comm="apparmor_parser"
      Sep 05 22:22:30 ubuntu2204 audit[26262]:  AVC apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/NetworkManager/nm-dhcp-helper" pid=880 comm="apparmor_parser"
      Sep 05 22:22:30 ubuntu2204 audit[26262]:  AVC apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/connman/scripts/dhclient-script" pid=880 comm="apparmor_parser"
      Sep 05 22:22:30 ubuntu2204 audit[26262]:  AVC apparmor="STATUS" operation="profile_load" profile="unconfined" name="/{,usr/}sbin/dhclient" pid=880 comm="apparmor_parser"
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Load AppArmor profiles.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target System Initialization.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Daily apt download activities.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Daily apt upgrade and clean activities.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Daily dpkg database backup timer.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Periodic ext4 Online Metadata Check for All Filesystems.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  audit: type=1400 audit(1693952548.294:5): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/NetworkManager/nm-dhcp-client.action" pid=880 comm="apparmor_parser"
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  audit: type=1400 audit(1693952548.294:6): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/NetworkManager/nm-dhcp-helper" pid=880 comm="apparmor_parser"
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  audit: type=1400 audit(1693952548.294:7): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/connman/scripts/dhclient-script" pid=880 comm="apparmor_parser"
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  audit: type=1400 audit(1693952548.294:8): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/{,usr/}sbin/dhclient" pid=880 comm="apparmor_parser"
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Discard unused blocks once a week.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Daily rotation of log files.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Message of the Day.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Podman auto-update timer.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Daily Cleanup of Temporary Directories.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Condition check resulted in Ubuntu Pro Timer for running repeated jobs being skipped.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Timer for calling verify disk encryption invoker service.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Basic System.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Timer Units.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Listening on D-Bus System Message Bus Socket.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Listening on Podman API Socket.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting containerd container runtime...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started D-Bus System Message Bus.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Save initial kernel messages after boot.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Remove Stale Online ext4 Metadata Check Snapshots...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Condition check resulted in getty on tty2-tty6 if dbus and logind are not available being skipped.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Login Prompts.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Dispatcher daemon for systemd-networkd...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Podman auto-update service...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Podman Start All Containers With Restart Policy Set To Always...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Podman API Service...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Logging Configuration...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting User Login Management...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Permit User Sessions...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Condition check resulted in Ubuntu Pro reboot cmds being skipped.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Condition check resulted in Ubuntu Pro Background Auto Attach being skipped.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Podman API Service.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Permit User Sessions.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Set console scheme...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Set console scheme.
      Sep 05 22:22:30 ubuntu2204 dbus-daemon[26262]:  [system] AppArmor D-Bus mediation is enabled
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.429144732Z" level=info msg="starting containerd" revision= version=1.7.2
      Sep 05 22:22:30 ubuntu2204 systemd-logind[26262]:  New seat seat0.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started User Login Management.
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.481841275Z" level=info msg="loading plugin \"io.containerd.snapshotter.v1.btrfs\"..." type=io.containerd.snapshotter.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.481966874Z" level=info msg="skip loading plugin \"io.containerd.snapshotter.v1.btrfs\"..." error="path /var/lib/containerd/io.containerd.snapshotter.v1.btrfs (ext4) must be a btrfs filesystem to be used with the btrfs snapshotter: skip plugin" type=io.containerd.snapshotter.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.481980901Z" level=info msg="loading plugin \"io.containerd.content.v1.content\"..." type=io.containerd.content.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.482038824Z" level=info msg="loading plugin \"io.containerd.snapshotter.v1.native\"..." type=io.containerd.snapshotter.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.482094169Z" level=info msg="loading plugin \"io.containerd.snapshotter.v1.overlayfs\"..." type=io.containerd.snapshotter.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.482208824Z" level=info msg="loading plugin \"io.containerd.metadata.v1.bolt\"..." type=io.containerd.metadata.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.482262894Z" level=info msg="metadata content store policy set" policy=shared
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.490944229Z" level=info msg="loading plugin \"io.containerd.differ.v1.walking\"..." type=io.containerd.differ.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.490961416Z" level=info msg="loading plugin \"io.containerd.event.v1.exchange\"..." type=io.containerd.event.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.490975325Z" level=info msg="loading plugin \"io.containerd.gc.v1.scheduler\"..." type=io.containerd.gc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.490997777Z" level=info msg="loading plugin \"io.containerd.lease.v1.manager\"..." type=io.containerd.lease.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491010169Z" level=info msg="loading plugin \"io.containerd.nri.v1.nri\"..." type=io.containerd.nri.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491043531Z" level=info msg="NRI interface is disabled by configuration."
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491053578Z" level=info msg="loading plugin \"io.containerd.runtime.v2.task\"..." type=io.containerd.runtime.v2
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491119236Z" level=info msg="loading plugin \"io.containerd.runtime.v2.shim\"..." type=io.containerd.runtime.v2
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491134201Z" level=info msg="loading plugin \"io.containerd.sandbox.store.v1.local\"..." type=io.containerd.sandbox.store.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491145659Z" level=info msg="loading plugin \"io.containerd.sandbox.controller.v1.local\"..." type=io.containerd.sandbox.controller.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491156719Z" level=info msg="loading plugin \"io.containerd.streaming.v1.manager\"..." type=io.containerd.streaming.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491167561Z" level=info msg="loading plugin \"io.containerd.service.v1.introspection-service\"..." type=io.containerd.service.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491181095Z" level=info msg="loading plugin \"io.containerd.service.v1.containers-service\"..." type=io.containerd.service.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491193104Z" level=info msg="loading plugin \"io.containerd.service.v1.content-service\"..." type=io.containerd.service.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491203846Z" level=info msg="loading plugin \"io.containerd.service.v1.diff-service\"..." type=io.containerd.service.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491214358Z" level=info msg="loading plugin \"io.containerd.service.v1.images-service\"..." type=io.containerd.service.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491224386Z" level=info msg="loading plugin \"io.containerd.service.v1.namespaces-service\"..." type=io.containerd.service.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491237314Z" level=info msg="loading plugin \"io.containerd.service.v1.snapshots-service\"..." type=io.containerd.service.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491247146Z" level=info msg="loading plugin \"io.containerd.runtime.v1.linux\"..." type=io.containerd.runtime.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491294104Z" level=info msg="loading plugin \"io.containerd.monitor.v1.cgroups\"..." type=io.containerd.monitor.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491477745Z" level=info msg="loading plugin \"io.containerd.service.v1.tasks-service\"..." type=io.containerd.service.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491502174Z" level=info msg="loading plugin \"io.containerd.grpc.v1.introspection\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491513583Z" level=info msg="loading plugin \"io.containerd.transfer.v1.local\"..." type=io.containerd.transfer.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491529769Z" level=info msg="loading plugin \"io.containerd.internal.v1.restart\"..." type=io.containerd.internal.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491570098Z" level=info msg="loading plugin \"io.containerd.grpc.v1.containers\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491581805Z" level=info msg="loading plugin \"io.containerd.grpc.v1.content\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491591976Z" level=info msg="loading plugin \"io.containerd.grpc.v1.diff\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491640808Z" level=info msg="loading plugin \"io.containerd.grpc.v1.events\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491653827Z" level=info msg="loading plugin \"io.containerd.grpc.v1.healthcheck\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491663601Z" level=info msg="loading plugin \"io.containerd.grpc.v1.images\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491672810Z" level=info msg="loading plugin \"io.containerd.grpc.v1.leases\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491681803Z" level=info msg="loading plugin \"io.containerd.grpc.v1.namespaces\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.491691621Z" level=info msg="loading plugin \"io.containerd.internal.v1.opt\"..." type=io.containerd.internal.v1
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  e2scrub_reap.service: Deactivated successfully.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Remove Stale Online ext4 Metadata Check Snapshots.
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.494518335Z" level=info msg="loading plugin \"io.containerd.grpc.v1.sandbox-controllers\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.494533930Z" level=info msg="loading plugin \"io.containerd.grpc.v1.sandboxes\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.494545431Z" level=info msg="loading plugin \"io.containerd.grpc.v1.snapshots\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.494555781Z" level=info msg="loading plugin \"io.containerd.grpc.v1.streaming\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.494569019Z" level=info msg="loading plugin \"io.containerd.grpc.v1.tasks\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.494580297Z" level=info msg="loading plugin \"io.containerd.grpc.v1.transfer\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.494589947Z" level=info msg="loading plugin \"io.containerd.grpc.v1.version\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.494598932Z" level=info msg="loading plugin \"io.containerd.grpc.v1.cri\"..." type=io.containerd.grpc.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.494691551Z" level=info msg="Start cri plugin with config {PluginConfig:{ContainerdConfig:{Snapshotter:overlayfs DefaultRuntimeName:runc DefaultRuntime:{Type: Path: Engine: PodAnnotations:[] ContainerAnnotations:[] Root: Options:map[] PrivilegedWithoutHostDevices:false PrivilegedWithoutHostDevicesAllDevicesAllowed:false BaseRuntimeSpec: NetworkPluginConfDir: NetworkPluginMaxConfNum:0 Snapshotter: SandboxMode:} UntrustedWorkloadRuntime:{Type: Path: Engine: PodAnnotations:[] ContainerAnnotations:[] Root: Options:map[] PrivilegedWithoutHostDevices:false PrivilegedWithoutHostDevicesAllDevicesAllowed:false BaseRuntimeSpec: NetworkPluginConfDir: NetworkPluginMaxConfNum:0 Snapshotter: SandboxMode:} Runtimes:map[runc:{Type:io.containerd.runc.v2 Path: Engine: PodAnnotations:[] ContainerAnnotations:[] Root: Options:map[BinaryName: CriuImagePath: CriuPath: CriuWorkPath: IoGid:0 IoUid:0 NoNewKeyring:false NoPivotRoot:false Root: ShimCgroup: SystemdCgroup:false] PrivilegedWithoutHostDevices:false PrivilegedWithoutHostDevicesAllDevicesAllowed:false BaseRuntimeSpec: NetworkPluginConfDir: NetworkPluginMaxConfNum:0 Snapshotter: SandboxMode:podsandbox}] NoPivot:false DisableSnapshotAnnotations:true DiscardUnpackedLayers:false IgnoreBlockIONotEnabledErrors:false IgnoreRdtNotEnabledErrors:false} CniConfig:{NetworkPluginBinDir:/opt/cni/bin NetworkPluginConfDir:/etc/cni/net.d NetworkPluginMaxConfNum:1 NetworkPluginSetupSerially:false NetworkPluginConfTemplate: IPPreference:} Registry:{ConfigPath: Mirrors:map[] Configs:map[] Auths:map[] Headers:map[]} ImageDecryption:{KeyModel:node} DisableTCPService:true StreamServerAddress:127.0.0.1 StreamServerPort:0 StreamIdleTimeout:4h0m0s EnableSelinux:false SelinuxCategoryRange:1024 SandboxImage:registry.k8s.io/pause:3.8 StatsCollectPeriod:10 SystemdCgroup:false EnableTLSStreaming:false X509KeyPairStreaming:{TLSCertFile: TLSKeyFile:} MaxContainerLogLineSize:16384 DisableCgroup:false DisableApparmor:false RestrictOOMScoreAdj:false MaxConcurrentDownloads:3 DisableProcMount:false UnsetSeccompProfile: TolerateMissingHugetlbController:true DisableHugetlbController:true DeviceOwnershipFromSecurityContext:false IgnoreImageDefinedVolumes:false NetNSMountsUnderStateDir:false EnableUnprivilegedPorts:false EnableUnprivilegedICMP:false EnableCDI:false CDISpecDirs:[/etc/cdi /var/run/cdi] ImagePullProgressTimeout:1m0s DrainExecSyncIOTimeout:0s} ContainerdRootDir:/var/lib/containerd ContainerdEndpoint:/run/containerd/containerd.sock RootDir:/var/lib/containerd/io.containerd.grpc.v1.cri StateDir:/run/containerd/io.containerd.grpc.v1.cri}"
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.494737417Z" level=info msg="Connect containerd service"
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.494756121Z" level=info msg="using legacy CRI server"
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.494761467Z" level=info msg="using experimental NRI integration - disable nri plugin to prevent this"
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.494807386Z" level=info msg="Get image filesystem path \"/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs\""
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.495959683Z" level=info msg="loading plugin \"io.containerd.tracing.processor.v1.otlp\"..." type=io.containerd.tracing.processor.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.495974989Z" level=info msg="skip loading plugin \"io.containerd.tracing.processor.v1.otlp\"..." error="no OpenTelemetry endpoint: skip plugin" type=io.containerd.tracing.processor.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.495985324Z" level=info msg="loading plugin \"io.containerd.internal.v1.tracing\"..." type=io.containerd.internal.v1
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.496006364Z" level=info msg="Start subscribing containerd event"
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.496037713Z" level=info msg="Start recovering state"
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.496080616Z" level=info msg="Start event monitor"
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.496090938Z" level=info msg="Start snapshots syncer"
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.496097604Z" level=info msg="Start cni network conf syncer for default"
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.496102877Z" level=info msg="Start streaming server"
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.496256044Z" level=info msg="skipping tracing processor initialization (no tracing plugin)" error="no OpenTelemetry endpoint: skip plugin"
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.496399091Z" level=info msg=serving... address=/run/containerd/containerd.sock.ttrpc
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.496419905Z" level=info msg=serving... address=/run/containerd/containerd.sock
      Sep 05 22:22:30 ubuntu2204 networkd-dispatcher[26262]:  No valid path found for iwconfig
      Sep 05 22:22:30 ubuntu2204 networkd-dispatcher[26262]:  No valid path found for iw
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Dispatcher daemon for systemd-networkd.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started containerd container runtime.
      Sep 05 22:22:30 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:28.508349412Z" level=info msg="containerd successfully booted in 0.079960s"
      Sep 05 22:22:30 ubuntu2204 podman[26262]:  time="2023-09-05T22:22:28Z" level=info msg="/usr/bin/podman filtering at log level info"
      Sep 05 22:22:30 ubuntu2204 podman[26262]:  time="2023-09-05T22:22:28Z" level=info msg="/usr/bin/podman filtering at log level info"
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  etc-machine\x2did.mount: Deactivated successfully.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  var-lib-containers-storage-overlay-metacopy\x2dcheck1945055672-merged.mount: Deactivated successfully.
      Sep 05 22:22:30 ubuntu2204 podman[26262]:  time="2023-09-05T22:22:28Z" level=info msg="[graphdriver] using prior storage driver: overlay"
      Sep 05 22:22:30 ubuntu2204 podman[26262]:  time="2023-09-05T22:22:28Z" level=info msg="Found CNI network podman (type=bridge) at /etc/cni/net.d/87-podman-bridge.conflist"
      Sep 05 22:22:30 ubuntu2204 podman[26262]:  time="2023-09-05T22:22:28Z" level=info msg="Found CNI network podman (type=bridge) at /etc/cni/net.d/87-podman-bridge.conflist"
      Sep 05 22:22:30 ubuntu2204 podman[26262]:  2023-09-05 22:22:28.729521205 +0000 UTC m=+0.354964756 system refresh
      Sep 05 22:22:30 ubuntu2204 podman[26262]:  time="2023-09-05T22:22:28Z" level=info msg="Setting parallel job count to 7"
      Sep 05 22:22:30 ubuntu2204 podman[26262]:  time="2023-09-05T22:22:28Z" level=info msg="Setting parallel job count to 7"
      Sep 05 22:22:30 ubuntu2204 podman[26262]:  time="2023-09-05T22:22:28Z" level=info msg="using systemd socket activation to determine API endpoint"
      Sep 05 22:22:30 ubuntu2204 podman[26262]:  time="2023-09-05T22:22:28Z" level=info msg="using API endpoint: ''"
      Sep 05 22:22:30 ubuntu2204 podman[26262]:  time="2023-09-05T22:22:28Z" level=info msg="API service listening on \"/run/podman/podman.sock\""
      Sep 05 22:22:30 ubuntu2204 systemd-networkd[26262]:  enc1: Gained carrier
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  IPv6: ADDRCONF(NETDEV_CHANGE): enc1: link becomes ready
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Podman Start All Containers With Restart Policy Set To Always.
      Sep 05 22:22:30 ubuntu2204 systemd-networkd[26262]:  enc1: DHCPv4 address 172.16.0.82/24 via 172.16.0.1
      Sep 05 22:22:30 ubuntu2204 dbus-daemon[26262]:  [system] Activating via systemd: service name='org.freedesktop.hostname1' unit='dbus-org.freedesktop.hostname1.service' requested by ':1.0' (uid=100 pid=806 comm="/lib/systemd/systemd-networkd " label="unconfined")
      Sep 05 22:22:30 ubuntu2204 systemd-timesyncd[26262]:  Network configuration changed, trying to establish connection.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Hostname Service...
      Sep 05 22:22:30 ubuntu2204 dbus-daemon[26262]:  [system] Successfully activated service 'org.freedesktop.hostname1'
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Hostname Service.
      Sep 05 22:22:30 ubuntu2204 systemd-networkd[26262]:  Could not set hostname: Access denied
      Sep 05 22:22:30 ubuntu2204 systemd-timesyncd[26262]:  Initial synchronization to time server 185.125.190.58:123 (ntp.ubuntu.com).
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  podman-auto-update.service: Deactivated successfully.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Podman auto-update service.
      Sep 05 22:22:30 ubuntu2204 hpcr-dnslookup[26262]:  HPL14000I: Network connectivity check completed successfully.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Logging Configuration.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Early Initialization.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Logging to remote monitoring server is initiated..
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Logging Configuration...
      Sep 05 22:22:30 ubuntu2204 hpcr-logging[26262]:  Configuring logging ...
      Sep 05 22:22:30 ubuntu2204 hpcr-logging[26262]:  Version [1.1.145]
      Sep 05 22:22:30 ubuntu2204 hpcr-logging[26262]:  Configuring logging, input [/var/hyperprotect/user-data.decrypted] ...
      Sep 05 22:22:30 ubuntu2204 hpcr-logging[26262]:  ValidateContractE ...
      Sep 05 22:22:30 ubuntu2204 hpcr-logging[26262]:  config written: /etc/rsyslog.d/22-logging.conf
      Sep 05 22:22:30 ubuntu2204 hpcr-logging[26262]:  HPL01010I: Logging has been setup successfully.
      Sep 05 22:22:30 ubuntu2204 hpcr-logging[26262]:  Logging has been configured
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Logging Configuration.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting System Logging Service...
      Sep 05 22:22:30 ubuntu2204 rsyslogd[26262]:  rsyslogd's groupid changed to 111
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started System Logging Service.
      Sep 05 22:22:30 ubuntu2204 rsyslogd[26262]:  rsyslogd's userid changed to 104
      Sep 05 22:22:30 ubuntu2204 rsyslogd[26262]:  [origin software="rsyslogd" swVersion="8.2112.0" x-pid="1044" x-info="https://www.rsyslog.com"] start
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Synchronizes the Logging Target.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Logging to remote log server is initiated..
      Sep 05 22:22:30 ubuntu2204 rsyslogd[26262]:  imjournal: No statefile exists, /var/spool/rsyslog/journal_state will be created (ignore if this is first run): No such file or directory [v8.2112.0 try https://www.rsyslog.com/e/2040 ]
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Service that does validation of contract...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting HPCR Registry Authentication...
      Sep 05 22:22:30 ubuntu2204 hpcr-contract[26262]:  Welcome to SE Contract Validator
      Sep 05 22:22:30 ubuntu2204 hpcr-contract[26262]:  Contract file passed is:  /var/hyperprotect/user-data.decrypted
      Sep 05 22:22:30 ubuntu2204 hpcr-registry-auth[26262]:  Starting Registry Authentication ...
      Sep 05 22:22:30 ubuntu2204 hpcr-registry-auth[26262]:  Version [1.0.70]
      Sep 05 22:22:30 ubuntu2204 rsyslogd[26262]:  imjournal: journal files changed, reloading...  [v8.2112.0 try https://www.rsyslog.com/e/0 ]
      Sep 05 22:22:30 ubuntu2204 hpcr-registry-auth[26262]:  Writing auth config: /root/.docker/config.json
      Sep 05 22:22:30 ubuntu2204 hpcr-registry-auth[26262]:  Registry Authentication started
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished HPCR Registry Authentication.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Docker Socket for the API...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Listening on Docker Socket for the API.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Docker Application Container Engine...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  var-lib-containers-storage-overlay.mount: Deactivated successfully.
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:29.672740594Z" level=info msg="Starting up"
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:29.684275456Z" level=info msg="detected 127.0.0.53 nameserver, assuming systemd-resolved, so using resolv.conf: /run/systemd/resolve/resolv.conf"
      Sep 05 22:22:30 ubuntu2204 audit[26262]:  AVC apparmor="STATUS" operation="profile_load" profile="unconfined" name="docker-default" pid=1068 comm="apparmor_parser"
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:29.749209491Z" level=info msg="parsed scheme: \"unix\"" module=grpc
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:29.749278826Z" level=info msg="scheme \"unix\" not registered, fallback to default scheme" module=grpc
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:29.749315855Z" level=info msg="ccResolverWrapper: sending update to cc: {[{unix:///run/containerd/containerd.sock  <nil> 0 <nil>}] <nil> <nil>}" module=grpc
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:29.749349509Z" level=info msg="ClientConn switching balancer to \"pick_first\"" module=grpc
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:29.751021396Z" level=info msg="parsed scheme: \"unix\"" module=grpc
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:29.751057665Z" level=info msg="scheme \"unix\" not registered, fallback to default scheme" module=grpc
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:29.751086981Z" level=info msg="ccResolverWrapper: sending update to cc: {[{unix:///run/containerd/containerd.sock  <nil> 0 <nil>}] <nil> <nil>}" module=grpc
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:29.751114718Z" level=info msg="ClientConn switching balancer to \"pick_first\"" module=grpc
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  audit: type=1400 audit(1693952549.735:9): apparmor="STATUS" operation="profile_load" profile="unconfined" name="docker-default" pid=1068 comm="apparmor_parser"
      Sep 05 22:22:30 ubuntu2204 hpcr-contract[26262]:  Contract file is valid.
      Sep 05 22:22:30 ubuntu2204 hpcr-contract[26262]:  Extracting workload from /var/hyperprotect/user-data.decrypted to /var/hyperprotect/workload-data.decrypted
      Sep 05 22:22:30 ubuntu2204 hpcr-contract[26262]:  Extraction completed
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Service that does validation of contract.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Service that does signature validation of Env Workload of contract...
      Sep 05 22:22:30 ubuntu2204 hpcr-signature[26262]:  Welcome to SE ENV Workload Signature Validator
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:29.835870657Z" level=info msg="Loading containers: start."
      Sep 05 22:22:30 ubuntu2204 hpcr-signature[26262]:  Decrypted Contract file passed is:  /var/hyperprotect/workload-data.decrypted
      Sep 05 22:22:30 ubuntu2204 hpcr-signature[26262]:  Encrypted Contract file passed is:  /var/hyperprotect/cidata/user-data
      Sep 05 22:22:30 ubuntu2204 hpcr-signature[26262]:  Check Dependency params Public key and EnvWorkload signature
      Sep 05 22:22:30 ubuntu2204 hpcr-signature[26262]:  Access Public key and EnvWorkload signature
      Sep 05 22:22:30 ubuntu2204 hpcr-signature[26262]:  Create combined EnvWorkload contract content
      Sep 05 22:22:30 ubuntu2204 hpcr-signature[26262]:  Verify signing key, signature and combined EnvWorkload contract
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  bridge: filtering via arp/ip/ip6tables is no longer available by default. Update your scripts to load br_netfilter if you need this.
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Bridge firewalling registered
      Sep 05 22:22:30 ubuntu2204 hpcr-signature[26262]:  Verified OK
      Sep 05 22:22:30 ubuntu2204 hpcr-signature[26262]:  Successfully verified contract with signature and signing key
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Service that does signature validation of Env Workload of contract.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Contract is unpacked and ready for consumption..
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Service that waits until the user devices are ready...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Set podman image policy...
      Sep 05 22:22:30 ubuntu2204 hpcr-disk-standby[26262]:  Waiting for devices ...
      Sep 05 22:22:30 ubuntu2204 hpcr-disk-standby[26262]:  Version [1.0.112]
      Sep 05 22:22:30 ubuntu2204 hpcr-disk-standby[26262]:  WaitForDevices input=[/var/hyperprotect/user-data.decrypted], timeout=[2023-09-05 22:37:29.889691719 +0000 UTC m=+900.024319178]
      Sep 05 22:22:30 ubuntu2204 hpcr-disk-standby[26262]:  ParseContract ...
      Sep 05 22:22:30 ubuntu2204 hpcr-disk-standby[26262]:  ValidateContract ...
      Sep 05 22:22:30 ubuntu2204 systemd-networkd[26262]:  enc1: Gained IPv6LL
      Sep 05 22:22:30 ubuntu2204 hpcr-disk-standby[26262]:  MergeVolumes ...
      Sep 05 22:22:30 ubuntu2204 hpcr-disk-standby[26262]:  Waiting for devices is completed
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Service that waits until the user devices are ready.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Service that mounts the data volumes after they are ready...
      Sep 05 22:22:30 ubuntu2204 hpcr-disk-mount[26262]:  Mounting volumes ...
      Sep 05 22:22:30 ubuntu2204 hpcr-disk-mount[26262]:  Version [1.0.112]
      Sep 05 22:22:30 ubuntu2204 hpcr-disk-mount[26262]:  MountVolumes input=[/var/hyperprotect/user-data.decrypted]
      Sep 05 22:22:30 ubuntu2204 hpcr-disk-mount[26262]:  ParseContract ...
      Sep 05 22:22:30 ubuntu2204 hpcr-disk-mount[26262]:  ValidateContract ...
      Sep 05 22:22:30 ubuntu2204 hpcr-disk-mount[26262]:  MergeVolumes ...
      Sep 05 22:22:30 ubuntu2204 hpcr-disk-mount[26262]:  Mounting volumes ...
      Sep 05 22:22:30 ubuntu2204 hpcr-disk-mount[26262]:  Volume config ..
      Sep 05 22:22:30 ubuntu2204 hpcr-disk-mount[26262]:  HPL07003I: Mounting volumes done
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Service that mounts the data volumes after they are ready.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Reached target Data volumes are mounted ready to be used..
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Service that verifies all disks are encrypted and logs output to systemd journal.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Service that periodically logs entry to trigger verify disk encryption service.
      Sep 05 22:22:30 ubuntu2204 verify-disk-encryption[26262]:  Verify disk encryption started...
      Sep 05 22:22:30 ubuntu2204 hpcr-image-play[26262]:  Getting image source signatures
      Sep 05 22:22:30 ubuntu2204 hpcr-image-play[26262]:  Copying blob sha256:66d62867ae2452322f4769f943913be00b22e73039d1902e8f785b9f49838193
      Sep 05 22:22:30 ubuntu2204 kernel[26262]:  Initializing XFRM netlink socket
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:30.064508356Z" level=info msg="Default bridge (docker0) is assigned with an IP address 172.17.0.0/16. Daemon option --bip can be used to set a preferred IP address"
      Sep 05 22:22:30 ubuntu2204 networkd-dispatcher[26262]:  WARNING:Unknown index 3 seen, reloading interface list
      Sep 05 22:22:30 ubuntu2204 hpcr-image-play[26262]:  Copying config sha256:9eca761232387055827db0a9f2232f2635bc8c6d5f23ecfb39d34bb4ab0dca09
      Sep 05 22:22:30 ubuntu2204 hpcr-image-play[26262]:  Writing manifest to image destination
      Sep 05 22:22:30 ubuntu2204 hpcr-image-play[26262]:  Storing signatures
      Sep 05 22:22:30 ubuntu2204 systemd-networkd[26262]:  docker0: Link UP
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:30.140157723Z" level=info msg="Loading containers: done."
      Sep 05 22:22:30 ubuntu2204 hpcr-image-play[26262]:  Loaded image(s): k8s.gcr.io/pause:3.5
      Sep 05 22:22:30 ubuntu2204 podman[26262]:  2023-09-05 22:22:29.991201384 +0000 UTC m=+0.127793409 image loadfromarchive  /usr/local/se-image-play/pause.tar
      Sep 05 22:22:30 ubuntu2204 sudo[26262]:      root : PWD=/ ; USER=nobody ; COMMAND=/usr/local/bin/se-image-play
      Sep 05 22:22:30 ubuntu2204 sudo[26262]:  pam_unix(sudo:session): session opened for user nobody(uid=65534) by (uid=0)
      Sep 05 22:22:30 ubuntu2204 hpcr-image-play[26262]:  Version [1.1.112]
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:30.212277099Z" level=info msg="Docker daemon" commit="20.10.25-0ubuntu1~22.04.2" graphdriver(s)=overlay2 version=20.10.25
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:30.212332016Z" level=info msg="Daemon has completed initialization"
      Sep 05 22:22:30 ubuntu2204 sudo[26262]:  pam_unix(sudo:session): session closed for user nobody
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Set podman image policy.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Service that creates a set of containers...
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Started Docker Application Container Engine.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Set docker image policy...
      Sep 05 22:22:30 ubuntu2204 hpcr-image[26262]:  Starting image service...
      Sep 05 22:22:30 ubuntu2204 hpcr-image[26262]:  Contract yaml file: /var/hyperprotect/workload-data.decrypted
      Sep 05 22:22:30 ubuntu2204 hpcr-image[26262]:  Extracting image contract
      Sep 05 22:22:30 ubuntu2204 hpcr-image[26262]:  Successfully extracted Image contract
      Sep 05 22:22:30 ubuntu2204 hpcr-image[26262]:  Extracting container contract
      Sep 05 22:22:30 ubuntu2204 hpcr-image[26262]:  Checking for image with digest
      Sep 05 22:22:30 ubuntu2204 hpcr-image[26262]:  No image for DCT verification
      Sep 05 22:22:30 ubuntu2204 hpcr-image[26262]:  Image service completed successfully
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Set docker image policy.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Starting Service that creates a set of containers...
      Sep 05 22:22:30 ubuntu2204 sudo[26262]:      root : PWD=/ ; USER=nobody ; COMMAND=/usr/local/bin/se-container-play
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  Starting container service...
      Sep 05 22:22:30 ubuntu2204 sudo[26262]:  pam_unix(sudo:session): session opened for user nobody(uid=65534) by (uid=0)
      Sep 05 22:22:30 ubuntu2204 hpcr-container-play[26262]:  Version [1.1.116]
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  Validating contract...
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  Compose folder /data1/compose created
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  Contract yaml file: /var/hyperprotect/workload-data.decrypted
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  Compose folder: /data1/compose
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  Validation completed
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  Parsing contract...
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  Parsing of the Contract File completed successfully
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  Extracting compose...
      Sep 05 22:22:30 ubuntu2204 sudo[26262]:  pam_unix(sudo:session): session closed for user nobody
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  Extracting done...
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  Extracting the ENV Contents...
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  Writing new env file /data1/compose/.env ...
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  Reading existing env file /data1/compose/.env ...
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:30.270893886Z" level=info msg="API listen on /run/docker.sock"
      Sep 05 22:22:30 ubuntu2204 hpcr-container-play[26262]:  HPL15004I: The pod started successfully.
      Sep 05 22:22:30 ubuntu2204 hpcr-container-play[26262]:  HPL15006I: No pod definitions found.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  Finished Service that creates a set of containers.
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  Extracting of environment contents done
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  Check if docker is ready
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  docker-compose.yml file is present in the directory
      Sep 05 22:22:30 ubuntu2204 hpcr-container[26262]:  Starting workload containers...
      Sep 05 22:22:30 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:30.471972387Z" level=warning msg="reference for unknown type: " digest="sha256:b0921f4009b33b926aeae931fef2b0536514e7a62ae013cee6c345b1ac7f11bb" remote="quay.io/bsilliman/paynow@sha256:b0921f4009b33b926aeae931fef2b0536514e7a62ae013cee6c345b1ac7f11bb"
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  var-lib-docker-overlay2-opaque\x2dbug\x2dcheck3647899609-merged.mount: Deactivated successfully.
      Sep 05 22:22:30 ubuntu2204 systemd[26262]:  var-lib-containers-storage-overlay.mount: Deactivated successfully.
      Sep 05 22:22:33 ubuntu2204 systemd[26262]:  dmesg.service: Deactivated successfully.
      Sep 05 22:22:34 ubuntu2204 systemd[26262]:  var-lib-containers-storage-overlay.mount: Deactivated successfully.
      Sep 05 22:22:34 ubuntu2204 systemd[26262]:  podman.service: Deactivated successfully.
      Sep 05 22:22:35 ubuntu2204 verify-disk-encryption[26262]:  HPL13000I: Verify LUKS Encryption
      Sep 05 22:22:35 ubuntu2204 systemd[26262]:  verify-disk-encryption-invoker.service: Deactivated successfully.
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Return value for disk-encrypt: 0
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Executed cmd: ('lsblk', '-b', '-n', '-o', 'NAME,SIZE')
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Return value: 0
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Stdout: vda                                           107374182400
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  ├─vda1                                          4292870144
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  └─vda2                                        103079215104
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:    └─luks-655145dd-f4d0-4127-93ce-e1906a668299 103062437888
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  vdb                                                 389120
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  List of volumes greater than or equal to 10GB are: ['/dev/vda']
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Updated Volumes list: ['/dev/vda2']
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Executed cmd: ('lsblk', '/dev/vda2', '-b', '-n', '-o', 'NAME,MOUNTPOINT')
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Return value: 0
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Stdout: vda2
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  └─luks-655145dd-f4d0-4127-93ce-e1906a668299 /
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Boot volume is /dev/vda2
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Volume /dev/vda2 has mount point /
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  List of mounted volumes are: ['/dev/vda2']
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Verifying the boot disk /dev/vda2 is encrypted or not
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Executed cmd: ('lsblk', '/dev/vda2', '-b', '-n', '-o', 'NAME,TYPE')
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Return value: 0
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Stdout: vda2                                        part
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  └─luks-655145dd-f4d0-4127-93ce-e1906a668299 crypt
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Executed cmd: ('cryptsetup', 'isLuks', '/dev/vda2')
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Return value: 0
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Executed cmd: ('cryptsetup', 'luksDump', '/dev/vda2')
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  Return value: 0
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  HPL13003I: Checked for mount point /, LUKS encryption with 1 key slot found
      Sep 05 22:22:36 ubuntu2204 verify-disk-encryption[26262]:  HPL13001I: Boot volume and all the mounted data volumes are encrypted
      Sep 05 22:22:51 ubuntu2204 systemd-udevd[26262]:  Using default interface naming scheme 'v249'.
      Sep 05 22:22:51 ubuntu2204 networkd-dispatcher[26262]:  WARNING:Unknown index 4 seen, reloading interface list
      Sep 05 22:22:51 ubuntu2204 systemd-networkd[26262]:  br-18668bffa6c4: Link UP
      Sep 05 22:22:51 ubuntu2204 systemd[26262]:  var-lib-docker-overlay2-f3c1a612a8c718d9bedf6660b26f19d716c7209957913735e391abaf24f79bfe\x2dinit-merged.mount: Deactivated successfully.
      Sep 05 22:22:52 ubuntu2204 systemd-udevd[26262]:  Using default interface naming scheme 'v249'.
      Sep 05 22:22:52 ubuntu2204 networkd-dispatcher[26262]:  WARNING:Unknown index 5 seen, reloading interface list
      Sep 05 22:22:52 ubuntu2204 systemd-udevd[26262]:  Using default interface naming scheme 'v249'.
      Sep 05 22:22:52 ubuntu2204 systemd-networkd[26262]:  veth5f369da: Link UP
      Sep 05 22:22:52 ubuntu2204 kernel[26262]:  br-18668bffa6c4: port 1(veth5f369da) entered blocking state
      Sep 05 22:22:52 ubuntu2204 kernel[26262]:  br-18668bffa6c4: port 1(veth5f369da) entered disabled state
      Sep 05 22:22:52 ubuntu2204 kernel[26262]:  device veth5f369da entered promiscuous mode
      Sep 05 22:22:52 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:52.390932760Z" level=info msg="No non-localhost DNS nameservers are left in resolv.conf. Using default external servers: [nameserver 8.8.8.8 nameserver 8.8.4.4]"
      Sep 05 22:22:52 ubuntu2204 dockerd[26262]:  time="2023-09-05T22:22:52.391093655Z" level=info msg="IPv6 enabled; Adding default IPv6 external servers: [nameserver 2001:4860:4860::8888 nameserver 2001:4860:4860::8844]"
      Sep 05 22:22:52 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:52.444170398Z" level=info msg="loading plugin \"io.containerd.internal.v1.shutdown\"..." runtime=io.containerd.runc.v2 type=io.containerd.internal.v1
      Sep 05 22:22:52 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:52.444224119Z" level=info msg="loading plugin \"io.containerd.ttrpc.v1.pause\"..." runtime=io.containerd.runc.v2 type=io.containerd.ttrpc.v1
      Sep 05 22:22:52 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:52.444243857Z" level=info msg="loading plugin \"io.containerd.event.v1.publisher\"..." runtime=io.containerd.runc.v2 type=io.containerd.event.v1
      Sep 05 22:22:52 ubuntu2204 containerd[26262]:  time="2023-09-05T22:22:52.444258768Z" level=info msg="loading plugin \"io.containerd.ttrpc.v1.task\"..." runtime=io.containerd.runc.v2 type=io.containerd.ttrpc.v1
      Sep 05 22:22:52 ubuntu2204 systemd[26262]:  Started libcontainer container dc86fe6e55dae4e9803d3362e55ed084ee06c785e89bcd1dd547eb0e058cbfe1.
      Sep 05 22:22:52 ubuntu2204 kernel[26262]:  eth0: renamed from veth8e0c9a3
      Sep 05 22:22:53 ubuntu2204 systemd-networkd[26262]:  veth5f369da: Gained carrier
      Sep 05 22:22:53 ubuntu2204 systemd-networkd[26262]:  br-18668bffa6c4: Gained carrier
      Sep 05 22:22:53 ubuntu2204 kernel[26262]:  IPv6: ADDRCONF(NETDEV_CHANGE): veth5f369da: link becomes ready
      Sep 05 22:22:53 ubuntu2204 kernel[26262]:  br-18668bffa6c4: port 1(veth5f369da) entered blocking state
      Sep 05 22:22:53 ubuntu2204 kernel[26262]:  br-18668bffa6c4: port 1(veth5f369da) entered forwarding state
      Sep 05 22:22:53 ubuntu2204 kernel[26262]:  IPv6: ADDRCONF(NETDEV_CHANGE): br-18668bffa6c4: link becomes ready
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  Docker Compose Logs:
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:   paynow Pulling
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Pulling fs layer
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  6404e912b557 Pulling fs layer
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0561ee66ff6a Pulling fs layer
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Pulling fs layer
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Pulling fs layer
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  65a3fb6c13d7 Pulling fs layer
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Pulling fs layer
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  5c31cf8345fa Pulling fs layer
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7b32651b0169 Pulling fs layer
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0d117aef64cc Pulling fs layer
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  197acf2cade1 Pulling fs layer
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  d78e4d77283b Pulling fs layer
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  8b3975218acc Pulling fs layer
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Waiting
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Waiting
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  65a3fb6c13d7 Waiting
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Waiting
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  5c31cf8345fa Waiting
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7b32651b0169 Waiting
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0d117aef64cc Waiting
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  197acf2cade1 Waiting
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  d78e4d77283b Waiting
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  8b3975218acc Waiting
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Downloading [>                                                  ]  540.7kB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Downloading [=========>                                         ]  9.697MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  6404e912b557 Downloading [>                                                  ]  51.93kB/5.149MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Downloading [=================>                                 ]  18.79MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0561ee66ff6a Downloading [>                                                  ]  110.1kB/10.77MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  6404e912b557 Downloading [=================>                                 ]  1.767MB/5.149MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Downloading [========================>                          ]  25.78MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  6404e912b557 Downloading [==================================================>]  5.149MB/5.149MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  6404e912b557 Download complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0561ee66ff6a Downloading [=========>                                         ]  2.066MB/10.77MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Downloading [=================================>                 ]  35.99MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0561ee66ff6a Downloading [=====================>                             ]  4.708MB/10.77MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Downloading [==========================================>        ]  45.67MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0561ee66ff6a Downloading [================================>                  ]  6.891MB/10.77MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Verifying Checksum
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Download complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0561ee66ff6a Downloading [=========================================>         ]   8.96MB/10.77MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [>                                                  ]  557.1kB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Downloading [>                                                  ]  540.7kB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0561ee66ff6a Verifying Checksum
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0561ee66ff6a Download complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [===>                                               ]  3.342MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Downloading [=======>                                           ]  8.059MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [======>                                            ]  6.685MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Downloading [=============>                                     ]  15.02MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [========>                                          ]   9.47MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Downloading [====================>                              ]  21.96MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [>                                                  ]  538.4kB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [===========>                                       ]  12.26MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [=>                                                 ]   3.75MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  65a3fb6c13d7 Downloading [======>                                            ]     581B/4.203kB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  65a3fb6c13d7 Downloading [==================================================>]  4.203kB/4.203kB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  65a3fb6c13d7 Verifying Checksum
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  65a3fb6c13d7 Download complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Downloading [=========================>                         ]  27.85MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [=============>                                     ]  14.48MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [===>                                               ]  10.73MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [===============>                                   ]  16.71MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Downloading [================================>                  ]  34.85MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [====>                                              ]  17.14MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Downloading [=====================================>             ]  40.77MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [==================>                                ]  20.05MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [======>                                            ]  22.51MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Downloading [=========================================>         ]  44.54MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [=====================>                             ]   23.4MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Downloading [>                                                  ]  475.1kB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [========================>                          ]  26.18MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Downloading [==============================================>    ]  49.92MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Downloading [======>                                            ]  6.134MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Verifying Checksum
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Download complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [=======>                                           ]   26.8MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [==========================>                        ]  27.85MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Downloading [===============>                                   ]  14.14MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [=========>                                         ]  33.23MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [===================================>               ]  37.32MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Downloading [=======================>                           ]  21.22MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [==========>                                        ]  37.55MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Downloading [=========================>                         ]   23.1MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [======================================>            ]  40.67MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [============>                                      ]  44.53MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [========================================>          ]  42.89MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [===============>                                   ]  53.66MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [========================================>          ]  43.45MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [=================>                                 ]  60.63MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Downloading [==========================>                        ]  24.03MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [===================>                               ]  68.69MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Downloading [=============================>                     ]  26.86MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [=========================================>         ]  44.01MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [======================>                            ]  76.22MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Downloading [===================================>               ]  32.52MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [===========================================>       ]  46.24MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [========================>                          ]  83.18MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Downloading [=======================================>           ]  36.29MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [=============================================>     ]  48.46MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [=========================>                         ]  89.63MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Downloading [=============================================>     ]  41.99MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [===============================================>   ]  50.69MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [=============================>                     ]  100.3MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Downloading [==============================================>    ]  42.45MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [===============================>                   ]  108.4MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [================================================>  ]  51.81MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Verifying Checksum
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Download complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [==================================>                ]  118.1MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  5c31cf8345fa Downloading [>                                                  ]  23.84kB/2.277MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [====================================>              ]  127.7MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  5c31cf8345fa Downloading [=====>                                             ]    260kB/2.277MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [=================================================> ]  52.36MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [========================================>          ]  138.5MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7b32651b0169 Downloading [==================================================>]     452B/452B
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7b32651b0169 Verifying Checksum
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7b32651b0169 Download complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  5c31cf8345fa Downloading [================>                                  ]  767.9kB/2.277MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Extracting [==================================================>]  53.28MB/53.28MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [==========================================>        ]  147.6MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  5c31cf8345fa Downloading [======================================>            ]  1.763MB/2.277MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  5c31cf8345fa Verifying Checksum
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  5c31cf8345fa Download complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [=============================================>     ]  157.2MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Downloading [================================================>  ]  168.5MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Verifying Checksum
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Download complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  197acf2cade1 Downloading [====>                                              ]     613B/6.827kB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  197acf2cade1 Downloading [==================================================>]  6.827kB/6.827kB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  197acf2cade1 Verifying Checksum
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  197acf2cade1 Download complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0d117aef64cc Downloading [==================================================>]     126B/126B
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0d117aef64cc Download complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  d78e4d77283b Downloading [>                                                  ]   21.9kB/2.143MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  d78e4d77283b Verifying Checksum
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  d78e4d77283b Download complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  8b3975218acc Downloading [>                                                  ]  9.583kB/902.5kB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  8b3975218acc Downloading [=============>                                     ]  243.6kB/902.5kB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  8b3975218acc Verifying Checksum
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  8b3975218acc Download complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  51f6de0debe6 Pull complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  6404e912b557 Extracting [>                                                  ]  65.54kB/5.149MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  6404e912b557 Extracting [====================================>              ]  3.736MB/5.149MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  6404e912b557 Extracting [==================================================>]  5.149MB/5.149MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  6404e912b557 Pull complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0561ee66ff6a Extracting [>                                                  ]  131.1kB/10.77MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0561ee66ff6a Extracting [==============>                                    ]  3.146MB/10.77MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0561ee66ff6a Extracting [===============================================>   ]  10.22MB/10.77MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0561ee66ff6a Extracting [==================================================>]  10.77MB/10.77MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0561ee66ff6a Pull complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [>                                                  ]  557.1kB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [====>                                              ]  4.456MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [=======>                                           ]  8.356MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [==========>                                        ]   11.7MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [=============>                                     ]  15.04MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [================>                                  ]  17.83MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [===================>                               ]  20.61MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [=====================>                             ]  22.84MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [=======================>                           ]  25.62MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [==========================>                        ]  28.41MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [============================>                      ]   31.2MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [===============================>                   ]  34.54MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [===================================>               ]  37.88MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [======================================>            ]  41.78MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [=========================================>         ]  45.12MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [============================================>      ]  48.46MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [==============================================>    ]  50.69MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [================================================>  ]  52.92MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [=================================================> ]  53.48MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Extracting [==================================================>]  54.06MB/54.06MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7a6c7ccf7cb5 Pull complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [>                                                  ]  557.1kB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=>                                                 ]  3.899MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [==>                                                ]  7.242MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [===>                                               ]  11.14MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [===>                                               ]  12.26MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [====>                                              ]  14.48MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [====>                                              ]  16.15MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=====>                                             ]  17.83MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=====>                                             ]   19.5MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [======>                                            ]  21.17MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=======>                                           ]  25.07MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [========>                                          ]  28.97MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=========>                                         ]  32.31MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [==========>                                        ]  36.21MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [===========>                                       ]  39.55MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [============>                                      ]  42.34MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=============>                                     ]  46.24MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [==============>                                    ]  50.14MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [===============>                                   ]  52.36MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [===============>                                   ]  55.15MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=================>                                 ]  59.05MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [==================>                                ]  62.39MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [===================>                               ]  65.73MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [===================>                               ]  69.07MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [====================>                              ]  72.42MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=====================>                             ]  75.76MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [======================>                            ]   79.1MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [========================>                          ]     83MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=========================>                         ]   86.9MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [==========================>                        ]   90.8MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [===========================>                       ]   94.7MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [============================>                      ]   98.6MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=============================>                     ]  101.9MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [==============================>                    ]  105.3MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [===============================>                   ]  108.6MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [================================>                  ]  110.9MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [================================>                  ]    112MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [================================>                  ]  113.6MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=================================>                 ]  116.4MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [==================================>                ]  119.8MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [===================================>               ]  123.1MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [====================================>              ]  126.5MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=====================================>             ]  129.2MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=======================================>           ]  135.4MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=========================================>         ]  144.8MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [============================================>      ]  153.2MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=============================================>     ]  157.1MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [==============================================>    ]  160.4MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [===============================================>   ]  163.2MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [================================================>  ]    166MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [================================================>  ]  169.3MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=================================================> ]    171MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [=================================================> ]  172.7MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Extracting [==================================================>]  172.8MB/172.8MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  2854c5c8fd87 Pull complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  65a3fb6c13d7 Extracting [==================================================>]  4.203kB/4.203kB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  65a3fb6c13d7 Extracting [==================================================>]  4.203kB/4.203kB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  65a3fb6c13d7 Pull complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [>                                                  ]  491.5kB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [====>                                              ]  3.932MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [========>                                          ]  7.373MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [===========>                                       ]  10.81MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [===============>                                   ]  14.25MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [===================>                               ]  17.69MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [=======================>                           ]  21.63MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [============================>                      ]  26.05MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [================================>                  ]  29.49MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [===================================>               ]  32.93MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [======================================>            ]  35.39MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [========================================>          ]  37.36MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [==========================================>        ]  39.32MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [===========================================>       ]   40.3MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [=============================================>     ]  41.78MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [===============================================>   ]  43.75MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [================================================>  ]  44.73MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [=================================================> ]  45.71MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Extracting [==================================================>]  46.04MB/46.04MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  c9fe958b3ae4 Pull complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  5c31cf8345fa Extracting [>                                                  ]  32.77kB/2.277MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  5c31cf8345fa Extracting [================================================>  ]  2.195MB/2.277MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  5c31cf8345fa Extracting [==================================================>]  2.277MB/2.277MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  5c31cf8345fa Pull complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7b32651b0169 Extracting [==================================================>]     452B/452B
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7b32651b0169 Extracting [==================================================>]     452B/452B
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  7b32651b0169 Pull complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0d117aef64cc Extracting [==================================================>]     126B/126B
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0d117aef64cc Extracting [==================================================>]     126B/126B
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  0d117aef64cc Pull complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  197acf2cade1 Extracting [==================================================>]  6.827kB/6.827kB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  197acf2cade1 Extracting [==================================================>]  6.827kB/6.827kB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  197acf2cade1 Pull complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  d78e4d77283b Extracting [>                                                  ]  32.77kB/2.143MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  d78e4d77283b Extracting [=========================>                         ]  1.114MB/2.143MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  d78e4d77283b Extracting [==================================================>]  2.143MB/2.143MB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  d78e4d77283b Pull complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  8b3975218acc Extracting [=>                                                 ]  32.77kB/902.5kB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  8b3975218acc Extracting [==================================================>]  902.5kB/902.5kB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  8b3975218acc Extracting [==================================================>]  902.5kB/902.5kB
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  8b3975218acc Pull complete
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  paynow Pulled
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  Network compose_default  Creating
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  Network compose_default  Created
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  Container compose-paynow-1  Creating
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  Container compose-paynow-1  Created
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  Container compose-paynow-1  Starting
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  Container compose-paynow-1  Started
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  Docker compose result:
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:   CONTAINER ID   IMAGE                      COMMAND                  CREATED         STATUS                  PORTS                                       NAMES
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  dc86fe6e55da   quay.io/bsilliman/paynow   "docker-entrypoint.s…"   2 seconds ago   Up Less than a second   0.0.0.0:8443->8443/tcp, :::8443->8443/tcp   compose-paynow-1
      Sep 05 22:22:53 ubuntu2204 hpcr-container[26262]:  Container service completed successfully
      Sep 05 22:22:53 ubuntu2204 systemd[26262]:  Finished Service that creates a set of containers.
      Sep 05 22:22:53 ubuntu2204 systemd[26262]:  Reached target Workload is up and running..
      Sep 05 22:22:53 ubuntu2204 systemd[26262]:  Starting Phase2 Catch Service...
      Sep 05 22:22:53 ubuntu2204 hpcr-catch-success[26262]:  VSI has started successfully.
      Sep 05 22:22:53 ubuntu2204 hpcr-catch-success[26262]:  HPL10001I: Services succeeded -> systemd triggered hpl-catch-success service
      Sep 05 22:22:53 ubuntu2204 systemd[26262]:  Finished Phase2 Catch Service.
      Sep 05 22:22:53 ubuntu2204 systemd[26262]:  Reached target Multi-User System.
      Sep 05 22:22:53 ubuntu2204 systemd[26262]:  Starting Record Runlevel Change in UTMP...
      Sep 05 22:22:53 ubuntu2204 systemd[26262]:  systemd-update-utmp-runlevel.service: Deactivated successfully.
      Sep 05 22:22:53 ubuntu2204 systemd[26262]:  Finished Record Runlevel Change in UTMP.
      Sep 05 22:22:53 ubuntu2204 systemd[26262]:  Startup finished in 28.387s (kernel) + 25.442s (userspace) = 53.830s.
      Sep 05 22:22:53 ubuntu2204 compose-paynow-1[26262]:  
      Sep 05 22:22:53 ubuntu2204 compose-paynow-1[26262]:  > hyper-protect-pay-now@1.0.0 start
      Sep 05 22:22:53 ubuntu2204 compose-paynow-1[26262]:  > node app.js
      Sep 05 22:22:53 ubuntu2204 compose-paynow-1[26262]:  
      Sep 05 22:22:54 ubuntu2204 systemd-networkd[26262]:  br-18668bffa6c4: Gained IPv6LL
      Sep 05 22:22:54 ubuntu2204 systemd-networkd[26262]:  veth5f369da: Gained IPv6LL
      ```

Please click the *Next* link at the bottom right of the page to continue with the lab.
