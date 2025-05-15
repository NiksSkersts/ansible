
You could make use of Remote Migration, too, though that is still experimental [1] and may not be available on your older PVE host (tho, you should probably update that anyway).

Alternatively, to move the disks, you can use zfs send/receive like so:

    Shut down the VM on the sending node and create a snapshot of each disk: zfs snapshot rpool/vm-X-disk-Y@now.
    Create the volume on the receiving side.
    Send the volume via: zfs send -Rpv rpool/vm-X-disk-Y@now | ssh -o BatchMode=yes root@$IP zfs recv -Fv rpool/vm-X-disk-Y.

If you want, you can destroy the snapshot after like so: zfs destroy rpool/vm-X-disk-Y@now.

[1]: https://pve.proxmox.com/pve-docs/api-viewer/index.html#/nodes/{node}/qemu/{vmid}/remote_migrate
 
