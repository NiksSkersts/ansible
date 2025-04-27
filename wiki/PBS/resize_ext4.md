
two ways to resize ext4 datastore:
(I did this)
```
sgdisk -e /dev/sdb
parted /dev/sdb resizepart 1 100$
reboot 
resize2fs /dev/sdb1
```

Another that might work:
```
sdb1 is backup repository disk. Add disk space to you sdb in your environment.

In PBS:
apt install lsof
apt install parted
lsof /dev/sdb1
Kill 847 (process number(s) occupying the disk, PBS might die on your SSH)

In console:
Umount /dev/sdb1
parted /dev/sdb
(parted) print
Warning: Not all of the space available to /dev/sdb appears to be used, you can
fix the GPT to use all of the space (an extra ########### blocks) or continue
with the current setting?
Fix/Ignore? F
(parted) resizepart 1 Yes 100%
(parted) quit
Reboot

After reboot:
resize2fs /dev/sdb1
```