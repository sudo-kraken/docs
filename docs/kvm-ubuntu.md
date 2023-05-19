#:fontawesome-brands-ubuntu: Kernel-Based Virtualization - Deploys Virtual Machines on Ubuntu Server OS

## Prerequisites 
Before you continue you must have enabled CPU virtualization in the bios, this will vary from manufacturer to manufacturer so ensure that you check your motherboard manual for the correct setting. Here is a [link](https://www.bleepingcomputer.com/tutorials/how-to-enable-cpu-virtualization-in-your-computer-bios/) to a handy guide to get you started with this.
## Install Dependancies 
Install Kvm and all required dependencies to setup a virtualization environment on your :fontawesome-brands-ubuntu: Ubuntu 20.04 LTS sever using command:

=== ":material-bash: bash"
    !!! info "Info"
        Here is what you are installing.

        `qemu` - A generic machine emulator and virtualizer.

        `qemu-kvm` - QEMU metapackage for KVM support (i.e. QEMU Full virtualization on x86 hardware).

        `libvirt-clients` - programs for the libvirt library.

        `libvirt-daemon-system` - Libvirt daemon configuration files.

        `virtinst` - programs to create and clone virtual machines.

        `bridge-utils` - utilities for configuring the Linux Ethernet bridge.

    ```bash
    sudo apt install qemu qemu-kvm libvirt-clients libvirt-daemon-system virtinst bridge-utils
    ```
With the above packages installed, before you can continue you must check your hosts virtualisation capabilities, execute the following command to make sure your processor supports virtualisation capabilities and it has been enabled properly in the BIOS:

=== ":material-bash: bash"
    ``` bash
    kvm-ok
    ```
Sample Output
    ``` bash
    INFO: /dev/kvm exists
    KVM acceleration can be used
    ```
!!! warning "If you do not get the above output, you need to check the BIOS settings mentioned in the prerequisites section"

Once KVM is installed and you have verified that KVM is happy with the virtualization capabilites, start the `libvertd` service (If it is not started already):
=== ":material-bash: bash"
    ``` bash
    sudo systemctl enable libvirtd
    sudo systemctl start libvirtd
    ```
Check the status of libvirtd service with command:
=== ":material-bash: bash"
    ``` bash
    systemctl status libvirtd
    ```
    Sample output:

    ``` bash
     ● libvirtd.service - Virtualization daemon    
         Loaded: loaded (/lib/systemd/system/libvirtd.service; enabled; vendor preset: enabled)
         Active: active (running) since Sat 2020-07-04 08:13:41 UTC; 7min ago
    TriggeredBy: ● libvirtd-ro.socket
                 ● libvirtd-admin.socket
                 ● libvirtd.socket
           Docs: man:libvirtd(8)
                 https://libvirt.org
       Main PID: 4492 (libvirtd)
          Tasks: 19 (limit: 32768)
         Memory: 12.9M
         CGroup: /system.slice/libvirtd.service
                 ├─4492 /usr/sbin/libvirtd
                 ├─4641 /usr/sbin/dnsmasq --conf-file=/var/lib/libvirt/dnsmasq/default.conf --l>
                 └─4642 /usr/sbin/dnsmasq --conf-file=/var/lib/libvirt/dnsmasq/default.conf --l>
    
     Jul 04 08:13:42 ubuntuserver dnsmasq[4641]: compile time options: IPv6 GNU-getopt DBus i18n>
     Jul 04 08:13:42 ubuntuserver dnsmasq-dhcp[4641]: DHCP, IP range 192.168.122.2 -- 192.168.12>
     Jul 04 08:13:42 ubuntuserver dnsmasq-dhcp[4641]: DHCP, sockets bound exclusively to interfa>
     Jul 04 08:13:42 ubuntuserver dnsmasq[4641]: reading /etc/resolv.conf
     Jul 04 08:13:42 ubuntuserver dnsmasq[4641]: using nameserver 127.0.0.53#53
     Jul 04 08:13:42 ubuntuserver dnsmasq[4641]: read /etc/hosts - 7 addresses
     Jul 04 08:13:42 ubuntuserver dnsmasq[4641]: read /var/lib/libvirt/dnsmasq/default.addnhosts>
     Jul 04 08:13:42 ubuntuserver dnsmasq-dhcp[4641]: read /var/lib/libvirt/dnsmasq/default.host>
     Jul 04 08:13:42 ubuntuserver dnsmasq[4641]: reading /etc/resolv.conf
     Jul 04 08:13:42 ubuntuserver dnsmasq[4641]: using nameserver 127.0.0.53#53
    ```
The `libvertd` service has been enabled and started! Let us continue witht the networking now.

## Networking

We shall be using bridged networking with KVM. A bridged network shares the real network interface of the host computer with other VMs to connect to the outside network. Therefore each VM can bind directly to any available IPv4 or IPv6 addresses, just like a physical computer would do.

By default KVM setups a private virtual bridge, so that all VMs can communicate with each another, within the host computer internally. It provides its own subnet and DHCP to configure the guest’s network and uses NAT to access the host network.

The KVM default network `virbr0` uses `192.168.122.1/24` IP address. All the VMs will use an IP address in the `192.168.122.0/24` IP range and the host OS will be reachable at `192.168.122.1`. You should be able to ssh into the host OS (at `192.168.122.1`) from inside the guest OS and use scp to copy files back and forth.

It is OK if you only access the VMs inside from the host itself. However we can't access the VMs from other remote systems in the hosts attached network.

Because they use different IP range i.e. `10.0.180.0/24` in my case. In order to access the VMs from other remote hosts, we must setup a public bridge that runs on the host network and uses whatever external DHCP server is on the host network.

To put this in layman terms, we are going to make all VMs to use the same subnet used by the host system.

Before setting up a public bridged network, we should disable Netfilter for performance and security reasons. Netfilter is currently enabled on bridges by default.

To disable netfilter, create a file called `/etc/sysctl.d/bridge.conf`
=== ":material-bash: bash"
    ``` bash
    sudo nano /etc/sysctl.d/bridge.conf
    ```
Add the following lines:
=== ":material-bash: bash"
    ``` bash
    net.bridge.bridge-nf-call-ip6tables=0
    net.bridge.bridge-nf-call-iptables=0
    net.bridge.bridge-nf-call-arptables=0
    ```
Save and close the file.

Then create another file called `/etc/udev/rules.d/99-bridge.rules`
=== ":material-bash: bash"
    ``` bash
    sudo nano /etc/udev/rules.d/99-bridge.rules
    ```

Add the following line:
=== ":material-bash: bash"
    ``` bash
    ACTION=="add", SUBSYSTEM=="module", KERNEL=="br_netfilter", RUN+="/sbin/sysctl -p /etc/sysctl.d/bridge.conf"
    ```

This will set the necessary flags to disable netfilter on bridges at the appropriate place in system start-up. Save and close the file. 

Reboot your system to take effect these changes.

Next, we should disable the default networking that KVM installed for itself.

Find the name of KVM default network interfaces using "ip link" command:
=== ":material-bash: bash"
    ``` bash
    ip link
    ```
Sample output:
=== ":material-bash: bash"
    ``` bash
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
        link/ether 08:00:27:8a:52:94 brd ff:ff:ff:ff:ff:ff
    3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
        link/ether 08:00:27:10:7c:c1 brd ff:ff:ff:ff:ff:ff
    4: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
        link/ether 08:00:27:5d:61:28 brd ff:ff:ff:ff:ff:ff
    5: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default qlen 100    0
        link/ether 52:54:00:1f:a2:e7 brd ff:ff:ff:ff:ff:ff
    6: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc fq_codel master virbr0 state DOWN mode DEFAULT group default qlen 1000
        link/ether 52:54:00:1f:a2:e7 brd ff:ff:ff:ff:ff:ff
    ```
As you see in the above output, the entries `virbr0` and `virbr0-nic` are the KVM networks.

Let us remove the default KVM network with command:
=== ":material-bash: bash"
    ``` bash
    virsh net-destroy default
    ```
Sample output:
=== ":material-bash: bash"
    ``` bash
    Network default destroyed
    Undefine the default network with command: virsh net-undefine default
    ```
Now run the command it displayed above
=== ":material-bash: bash"
    ``` bash
    virsh net-undefine default
    ```

Sample output:
=== ":material-bash: bash"
    ``` bash
    Network default has been undefined
    ```

???+ tip "Tip"
    If the above commands didn't work for any reason, you can use these commands to disable and undefine KVM default network.
    === ":material-bash: bash"
        ``` bash
        sudo ip link delete virbr0 type bridge
        sudo ip link delete virbr0-nic
        ```
Now run `ip link` again to verify if the virbr0 and virbr0-nic interfaces are actually deleted.

$ ip link=== ":material-bash: bash"
    ``` bash
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
        link/ether 08:00:27:8a:52:94 brd ff:ff:ff:ff:ff:ff
    3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
        link/ether 08:00:27:10:7c:c1 brd ff:ff:ff:ff:ff:ff
    4: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
        link/ether 08:00:27:5d:61:28 brd ff:ff:ff:ff:ff:ff
    ```
!!! success "See? Now the KVM default networks are gone."

Now, let us setup the KVM public bridge to use when creating a new VM.

!!! note
    Do not use wireless network interface cards for bridges. Most wireless interlaces do not support bridging. Always use wired network interfaces for seamless connectivity!

To create a network bridge in host, edit `/etc/netplan/00-installer-config.yaml` file and add the bridge details.

Here is the default contents of the `00-installer-config.yaml` file in my Ubuntu 20.04 LTS server.
=== ":material-bash: bash"
    ``` bash
    cat /etc/netplan/00-installer-config.yaml
    ```
    ``` yaml
    # This is the network config written by 'subiquity'
    network:
      ethernets:
        enp3s0:
          dhcp4: false
          addresses:
          - 10.0.180.4/24
          gateway4: 10.0.180.1
          nameservers:
            addresses:
            - 10.0.180.1
            - 172.64.36.1
            search: []
      version: 2
      renderer: NetworkManager
    ```
As you see, I have a wired network interface namely enp3s0 in my Ubuntu server.
!!! tip 
    Before editing this file, backup your existing `/etc/netplan/00-installer-config.yaml` file.
    === ":material-bash: bash"
        ``` bash
        sudo cp /etc/netplan/00-installer-config.yaml{,.backup}
        ```
Then edit the default config file using your favorite editor, for me this is nano.
=== ":material-bash: bash"
    ``` bash
    sudo nano /etc/netplan/00-installer-config.yaml
    ```
Add/modify it like below you should also note that the subnet for you will likely differ from mine so replace the below values that matches with your network:
=== ":material-bash: bash"
    ``` bash
    # This is the network config written by 'subiquity'
    network:
      ethernets:
        enp3s0:
          dhcp4: false
      bridges:
        br0:
          interfaces: [ enp3s0 ]
          addresses: [10.0.180.4/24]
          gateway4: 10.0.180.1
          mtu: 1500
          nameservers:
            addresses: [10.0.180.1,1.1.1.1]
          parameters:
            stp: true
            forward-delay: 4
          dhcp4: no
          dhcp6: no
      version: 2
      renderer: NetworkManager
    ```
Here, the bridge network interface `br0` is attached to host's network interface `enp0s3`. The ip address of `br0` is `10.0.180.4`. The gateway is `10.0.180.1`. I use Cloudflare DNS servers (`1.1.1.1` and `1.0.0.1`) to connect to Internet.

Make sure the space indentation are exactly same as above. If the line indentations are not correct, the bridged network interface will not activate. 

!!! danger "Warning!"
    `Again to reiterate replace the above values that matches with your network`.

After modifying the network config file, save and close it. Apply the changes by running the following command:
=== ":material-bash: bash"
    ``` bash
    sudo netplan --debug  apply
    ```

You can also use the `brctl` command to show the bridge status:

Sample output:
=== ":material-bash: bash"
    ``` bash
    brctl show br0

    bridge name    bridge id            STP    enabled interfaces
    br0            8000.0800278a5294    yes    enp0s3
    ```
Now we should configure KVM to use this bridge. To do that, create a an XML file called `host-bridge.xml`.

It doesn't really matter where you create this file but I made it in `~/` (which is `/home/username/`).
=== ":material-bash: bash"
    ``` bash
    nano host-bridge.xml
    ```

Add the following lines:
=== ":material-xml: xml"
    ``` xml
    <network>
      <name>host-bridge</name>
      <forward mode="bridge"/>
      <bridge name="br0"/>
    </network>
    ```
Run the following commands to start the newly created bridge and make it as default bridge for all VMs.
=== ":material-bash: bash"
    ``` bash
    virsh net-define host-bridge.xml
    virsh net-start host-bridge
    virsh net-autostart host-bridge
    ```

To verify if the bridge is active and started, run:
=== ":material-bash: bash"
    ``` bash
    virsh net-list --all
    ```
Sample output:
=== ":material-bash: bash"
    ``` bash
    Name          State    Autostart   Persistent
    ------------------------------------------------
    host-bridge   active   yes         yes
    ```
!!! success "Congratulations! You have successfully setup KVM bridge and it is active now."

## Installing a VM
To install a VM you will need to have an ISO on hand for the installation process, as well as for an easy way to access a console session to install the virtual machine you should try [cockpit](https://cockpit-project.org/running.html). Cockpit is an amazing open source tool that comes included in Ubuntu `17.04` and later. To activate cockpit you should run the below commands, included in these are the additional addons for virtual machine management and package updates etc.
=== ":material-bash: bash"
    ``` bash
    sudo apt install cockpit cockpit-machines cockpit-packagekit -y
    sudo systemctl enable cockpit
    sudo systemctl start cockpit
    ```

`cockpit` will no be accessible on `ip-address-of-machine:9090` via a webbrowser.

Now once you have installed cockpit you are ready to deploy your first virtual machine!

=== ":material-bash: bash"
    !!! info "Important Information"
        For the command `virt-install` there are a lot of arguments that we will be adding, here is a brief explanation of these arguments, ensure you change the values to suit your needs.
    
        `--name` - Is the name of the virtual machine you wish to create.
    
        `--ram` - Is the amount of RAM in MB's that you want to allocate to the VM.
    
        `--vcpus` - Is the amount of CPU threads that you want to allocate to the VM.
    
        `--cpu` - Expose the host CPUs configuration to the guest. This enables the guest to take advantage of many of the host CPUs features (better performance), but may cause issues if migrating the guest to a host without an identical CPU.
    
        `--hvm` - Requests the use of full virtualization, if both para & full virtualization are available on the host. This parameter may not be available if connecting to a Xen hypervisor on a machine without hardware virtualization support. This parameter is implied if connecting to a `QEMU` based hypervisor.
    
        `--disk path=` - Is the path to the location in which you wish to stor the VM's hard disk.
    
        `,size` - Is the size of the VM's hard disk in GB's.
    
        `--cdrom` - Is the path to the ISO file you wish to install the VM OS from.
    
        `--network` - Is the network that you wan to put the VM on, in our case the bridge network we created earlier.
    
        `--graphics` - Specifies the graphical display configuration. This does not configure any virtual hardware, just how the guest's graphical display can be accessed.
    
        `--autorestart` -  Set the `autostart` flag for the VM. This causes the VM to be started on host boot up / reboot.


    ```bash
    sudo virt-install --name VM-NAME-HERE --ram=4096 --vcpus=4 --cpu host --hvm --disk path=/var/lib/libvirt/images/VM-NAME-HERE,size=50 --cdrom /path/to/iso/vm-os.iso --network bridge=br0 --graphics vnc --autostart
    ```
This will now execute and install your VM, the above command in my case was used to install a `Windows 10` VM, which needs user input to install unless you made youe own auto install image. So to setup the VM myself, I use the aforementioned application `cockpit`.

Now head over to Cockpit (`http://host-ip-address:9090`) login with your user, from the main page you will notice a tab for `Virtual Machines` and from there drop down the menu of your VM. You will see a `Consoles` tab. Select this and you should see the output of the VM's console, allowing you to view and setup the VM. Once this is done you can (for Windows) setup remote access via RDP, SSH or otherwise.

!!! success "CONGRATULATIONS! Enjoy your new KVM hypervisor."
