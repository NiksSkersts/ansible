Add post-up commands to prevent e1000 driver crash after while.
```
iface eno2 inet manual

auto vmbr0
iface vmbr0 inet static
        #address x
        #gateway y
        #bridge-ports eno2
        #bridge-stp off
        #bridge-fd 0
        #bridge-vlan-aware yes
        #bridge-vids 2-4094
        post-up ethtool -K eno2 gso off gro off tso off tx off rx off rxvlan off txvlan off sg off
        post-up ethtool -G eno2 rx 4096
        post-up ethtool -G eno2 tx 4096

iface wlo1 inet manual

source /etc/network/interfaces.d/*
```