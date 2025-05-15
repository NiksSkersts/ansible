# zfs send with ssh

You could make use of Remote Migration, too, though that is still experimental [1] and may not be available on your older PVE host (tho, you should probably update that anyway).

Alternatively, to move the disks, you can use zfs send/receive like so:

    Shut down the VM on the sending node and create a snapshot of each disk: zfs snapshot rpool/vm-X-disk-Y@now.
    Create the volume on the receiving side.
    Send the volume via: zfs send -Rpv rpool/vm-X-disk-Y@now | ssh -o BatchMode=yes root@$IP zfs recv -Fv rpool/vm-X-disk-Y.

If you want, you can destroy the snapshot after like so: zfs destroy rpool/vm-X-disk-Y@now.

[1]: https://pve.proxmox.com/pve-docs/api-viewer/index.html#/nodes/{node}/qemu/{vmid}/remote_migrate
 

# zfs send with netcat revsited (Much faster, less secure)


technotes.seastrom.com

    technotes.seastrom.com | About 

zfs send with netcat revsited

    Sun 11 March 2018 misc 

Some years ago I wrote about using zfs send with netcat (only across trusted networks! to overcome the performance issues (cipher speed, internal window size, etc) inherent in sending the zfs send over an ssh connection.

Month before last, or maybe it was the month before that, Gaige and I were migrating some big datasets using the aforementioned recipe, but experiencing problems with the zfs send croaking mid-stream for no apparent reason.

Upon investigation, we discovered that the problem was one of the usual inherent issues with blindly copying from someone else's how-to and declaring victory without fully understanding what all the command line flags mean.

Refresher: I was advocating the following:

Receiving machine:

nc -l -p 9999 | zfs receive zones/med

Sending machine:

    zfs send -v zones/med@now | nc -w 20 192.0.2.10 9999

For some reason I had it in my head that "-w 20" meant time to wait for an initial connection (despite the flag being on the wrong end for a listener) and 20 seconds seemed long enough to me, so win right?

Well, the difficulty is that the actual semantics of "-w 20" is "if you don't see any input for 20 seconds, consider things to have failed and nuke the transfer".

Gaige and I generally run relatively wimpy disk subsystems; they are optimized for storage capacity and price, not transfer speed and with their LFF NL/NAS drives certainly not for IOPS.

The result? If some other process is hammering the bejesus out of the disks while you're trying to do your copies, it's entirely possible for the zfs send (which operates at a lower priority anyway) to just hang for 20 seconds without anything being wrong.

The commands you probably want are more like:

Receiving machine:

    nc -l -p 9999 | zfs receive zones/med

Sending machine:

    zfs send -v zones/med@now | nc -w 1800 192.0.2.10 9999

so that if nothing happens for a half hour you declare failure.
