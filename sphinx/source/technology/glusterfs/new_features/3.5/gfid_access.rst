-----------
GFID Access
-----------

A new method for accessing data directly by GFID. With this method, we can consume the data in changelog translator, which is logging ‘gfid’ internally, very efficiently. This feature yet again extends the methods by which you can access Gluster, and should be well-received by members of the developer community, who will have a simple way to perform file operations programmatically within a Gluster volume.

::
  [2014-05-19 2:55:33]# brick="/mnt/lv3/vol-gfid-access";gluster volume create vol-gfid-access eins:$brick zwei:$brick
  volume create: vol-gfid-access: success: please start the volume to access data
  [2014-05-19 2:57:25]# gluster volume start vol-gfid-access
  volume start: vol-gfid-access: success
  [2014-05-19 2:58:20]# mkdir /mnt/glusterfs/vol-gfid-access
  [2014-05-19 2:58:29]# mount.glusterfs -o aux-gfid-mount localhost:/vol-gfid-access /mnt/glusterfs/vol-gfid-access
  
  # for i in `seq 0 9`; do dd if=/dev/zero of=/mnt/glusterfs/vol-gfid-access/$i.dat bs=1M count=1; done
  
  # ls -a /mnt/glusterfs/vol-gfid-access/.gfid
  ls: cannot open directory /mnt/glusterfs/vol-gfid-access/.gfid: Stale file handle
  
  # ls -a '/mnt/glusterfs/vol-gfid-access/.gfid/0svu9Cc1wVRLOBiu5NqF3ncw=='
  ls: cannot access /mnt/glusterfs/vol-gfid-access/.gfid/0svu9Cc1wVRLOBiu5NqF3ncw==: No such file or directory
  
  [2014-05-19 3:11:52]# ls -ld /mnt/glusterfs/vol-gfid-access/.gfid/
  drwxr-xr-x 3 root root 166 May 19 03:03 /mnt/glusterfs/vol-gfid-access/.gfid/
  [2014-05-19 3:11:58]# stat /mnt/glusterfs/vol-gfid-access/.gfid/
    File: `/mnt/glusterfs/vol-gfid-access/.gfid/'
    Size: 166       	Blocks: 0          IO Block: 131072 directory
  Device: 16h/22d	Inode: 13          Links: 3
  Access: (0755/drwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
  Access: 2014-05-19 03:03:14.146605880 +0900
  Modify: 2014-05-19 03:03:04.968605874 +0900
  Change: 2014-05-19 03:03:04.968605874 +0900
  
  strace ls -a /mnt/glusterfs/vol-gfid-access/.gfid
  ...
  stat("/mnt/glusterfs/vol-gfid-access/.gfid", {st_mode=S_IFDIR|0755, st_size=166, ...}) = 0
  open("/mnt/glusterfs/vol-gfid-access/.gfid", O_RDONLY|O_NONBLOCK|O_DIRECTORY|O_CLOEXEC) = -1 ESTALE (Stale file handle)
  ...
  
