# Setting up network bridge in Fedora 40 for VMs without modifying your system's network configuration.

Use case: Make VM accessible on the network. I wanted to achieve this to use Windows VM as Sunshine host to stream games on local network.

Create a network bridge interface

```sudo nmcli connection add type bridge ifname br0```


Bring it online

```sudo nmcli connection modify bridge-br0 ipv4.method auto```

```sudo nmcli connection up bridge-br0```


Attach network card to that bridge

```sudo nmcli connection modify "Wired connection 1" master br0```

```sudo nmcli connection up "Wired connection 1"```

(change Wired connection 1 with name of yours, use 'nmcli connection show' to check your network card name)

Run DHCP client on Bridge

```dhclient br0```

Now guest VM can be attached to the br0 interface and VM's MAC address will have a real layer2 presence on the local network. The router will give it a real rechable IP address and the desktop will act as a switch for packet delivery.

Now to mannually set up networking for KVM VM using QEMU using TAP (network tap) interface and adding it to a bridge which allows VM to communicate with the host network. The following collands set up this environment without using automatic scrips.

Manually create TAP interface

```sudo ip tuntap add dev tap0 mode tap```

Add TAP interface to the bridge

```sudo ip link set tap0 master br0```

Bring up the TAP interface

```sudo ip link set tap0 up```

Configure networking in Virt-Manager
Add NIC
Set 'Source device' to bridge
Type in the name of the bridge device as 'br0'
Set device model to virtio for better performance
Apply changes and boot to the VM



Thanks to [this reddit user](https://www.reddit.com/r/VFIO/comments/l1h3im/comment/gjzyv1v/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button). I modified his steps for Fedora
