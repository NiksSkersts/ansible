[Screenshot](..\files\Screenshot 2025-05-04 at 10-24-05 Replication needs so much additional storage space Proxmox Support Forum.png)
Switch volume from thick provisioning to thin provisioning:

```
> zfs list -o space
NAME                           AVAIL   USED  USEDSNAP  USEDDS  USEDREFRESERV  USEDCHILD
PVE_ZFS_POOL                96.2G  1.66T        0B     96K             0B      1.66T
PVE_ZFS_POOL/vm-215-disk-2   148G  88.4G     2.44G   34.4G          51.6G         0B

> zfs get reservation,refreserv PVE_ZFS_POOL/vm-215-disk-2
NAME                           PROPERTY        VALUE      SOURCE
PVE_ZFS_POOL/vm-215-disk-2  reservation     none       default
PVE_ZFS_POOL/vm-215-disk-2  refreservation  51.6G      local

> zfs set refreservation=none PVE_ZFS_POOL/vm-215-disk-2

> zfs get reservation,refreserv PVE_ZFS_POOL/vm-215-disk-2
NAME                           PROPERTY        VALUE      SOURCE
PVE_ZFS_POOL/vm-215-disk-2  reservation     none       default
PVE_ZFS_POOL/vm-215-disk-2  refreservation  none       local

>zfs list -o space
NAME                           AVAIL   USED  USEDSNAP  USEDDS  USEDREFRESERV  USEDCHILD
PVE_ZFS_POOL                 148G  1.61T        0B     96K             0B      1.61T
PVE_ZFS_POOL/vm-215-disk-2   148G  36.8G     2.44G   34.4G             0B         0B
```


This releases all the reserved space.

In Proxmox i can only set thin provisioning for the whole pool.

Q1: Is it safe to mix thick and thin provisioning that way?


I leave important VMs thick provisioned and change not so important VMs to thin provisioning with zfs set refreservation=none [volume]?

A: yes



Q2: The reservation property is also set to none, this is correct for a thin provisioned volume, right?
A: yes

Q3: What will happen if i set the reservation property?

```
zfs set reservation=51.6G  PVE_ZFS_POOL/vm-215-disk-2
> zfs get reservation,refreserv PVE_ZFS_POOL/vm-215-disk-2
NAME                           PROPERTY        VALUE      SOURCE
PVE_ZFS_POOL/vm-215-disk-2  reservation     51.6G      local
PVE_ZFS_POOL/vm-215-disk-2  refreservation  none       local

zfs list -o space
NAME                           AVAIL   USED  USEDSNAP  USEDDS  USEDREFRESERV  USEDCHILD
PVE_ZFS_POOL                 133G  1.63T        0B     96K             0B      1.63T
PVE_ZFS_POOL/vm-215-disk-2   148G  36.8G     2.44G   34.4G             0B         0B
```



Q4: Does it mean the disk is thick provisioned but replication might fail because of missing reservation?
Is this an advisable configuration to sacrifice reliable replication to avoid data corruption because of thin provisioning in case the pool runs out of space?

Sorry for so many more questions :cool: and thank you so much for your great help!

Q3/Q4: reservation is also used to reserve space - but its used for reserving space for the dataset/volume and descendants, where refreservation is used just for the dataset/volume itself - see man zfsprops. for PVE, it doesn't make much sense to set reservation. you can only either reserve the space or be thin-provisioned, there is no meaningful "halfway" there.